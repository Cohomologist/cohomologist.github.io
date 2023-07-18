---
title: Understanding exact sequences (part 1)
published: true
---

There are a lot of neural network models nowadays, but they all have one thing in common. Linear maps are still the workhorse of a neural network, but they are seasoned with a sprinkle of nonlinearity so that we can use them for decidedly nonlinear applications.

> The deep learning researchers don't get credit for this idea. **Logistic regression** uses this same idea to represent the dependent variable as a probability. The secret spice is the sigmoid function $$\sigma(x) = \frac{1}{1 + e^{-x}}$$ which squishes the result of a linear regression to $(0, 1)$.

Neural networks are *the* preferred model for large datasets. They are just linear maps with a top hat – hence the joke that deep learning is just stirring a pile of linear algebra! But we shouldn't see that as a bad thing. That means we can compute the gradients quickly and in parallel when optimizing them with gradient descent. Not only does computing the gradients only require addition and multiplication, but when running in parallel, each thread only needs to shoulder a small part of the work as differentiation is linear.

### One-layer neural networks

A one-layer neural network is a simple creature. Suppose we have $m$ input and $n$ output dimensions, and we want the neural network to have $h$ hidden units and the activation function $g$. The whole neural network is just the activation function $g$ sandwiched between two linear maps with biases (affine maps):

$$
f(x) = W_2g(W_1x + b_1) + b_2
$$

> Here, $W_1: \mathbb{R}^m \to \mathbb{R}^h, W_2: \mathbb{R}^h \to \mathbb{R}^n$ are linear maps represented by matrices, and $b_1 \in \mathbb{R}^h, b_2 \in \mathbb{R}^n$ are bias vectors. 

If we want, we can have another activation function $g_*$ in the output:

$$
f(x) = g_*(W_2g(W_1x + b_1) + b_2)
$$

which is useful for classification problems where we want a probability.

But a one-layer neural network is a deceptively simple creature. Linear maps are easy to think about: they can only scale, rotate, or shear the input. When we add the activation function $g$ to the mix, all bets are off. Thanks to $g$, neural networks can represent any continuous function on most realistic input and output spaces. Yet, also thanks to $g$, interpreting the function learned by the neural network becomes a challenge.

#### Within the brain 
We can make things easier with a bit of neuroscience. Most computer memory works like this: we associate each piece of data with an address, and we can only use that address to access that data. If we haven't organized our data correctly, then we have to perform a linear search which would require searching through every memory location that the data *might* be in.

On the other hand, our memory is **content-addressable**. Instead of an address being the key for accessing the data, the data is now its own key. Indeed, we can easily remember things that are similar to what we are perceiving *right now*, whether the similarity is syntactic ("plane" and "plain") or semantic ("plane" and "autopilot"). That's why it is easier to remember facts about a show when you are watching the show itself. All the time, we are activating the neurons that represent similar experiences to what we are experiencing at the moment. Think of content-addressable memory as a search engine: instead of typing in a site's URL, we can use a site's *content* as a way to find the site instead thanks to search engines.

To implement content-addressable memory, the brain uses a crude dot product computation. Two vectors $u, v$ in Euclidean space $\mathbb{R}^n$ lie on a planar subspace of $\mathbb{R}^n$ (as long as $n \geq 2$ of course). Within that plane, we can talk about the angle $\theta$ between $u$ and $v$. The cosine of that angle, $\cos(\theta)$, decreases as $\theta$ grows from $0$ all the way to two right angles ($\pi$ radians), where the vectors are pointing in opposite directions. So $\cos(\theta)$ measures the similarity between $u$ and $v$, and we call this measure **cosine similarity**.

The dot product

$$
u \cdot v = \|u\| \|v\| \cos(\theta)
$$

is proportional to the cosine similarity between $u$ and $v$. When $u$ and $v$ are unit vectors, we just get the cosine similarity. When $u$ and $v$ have norms other than $1$, there are the factors $\|u\| \|v\|$ we have to account for before we can call $u \cdot v$ a measure of similarity.

A neuron receives a signal from another neuron through a **synapse**. Since neurons can be connected to multiple other neurons, a neuron will have multiple synapses, one for each neuron it is connected to. Each synapse has a weight that controls how sensitive it is to input from its corresponding neuron. To determine its own activation, a neuron just takes a sum of inputs from the other neurons multiplied by their weights. If we represent the synapses as $1, \dots, m$, the inputs as a vector $x = (x_1, \dots, x_m)$, and the weights as a vector $w = (w_1, \dots, w_m)$, then the activation is simply the dot product

$$
a = \sum_{i=1}^m w_ix_i = w \cdot x
$$

And since related groups of neurons tend to operate in parallel, we can often compute the activations of $n$ neurons linearly. We can represent the weights as a matrix where each row has the weights of a single neuron. Finally, while activations are just a dot product, the actual output of a neuron might not be. For example, a neuron might only produce an output when the activation exceeds a value. Huh, all of that seems really familiar. I wonder why they called it a "neural network" in the first place!

#### The hidden layer computes similarity

With the right activation function, a one-layer neural network can approximate any continuous function on a closed and bounded space. While that guarantee is nice to know, it doesn't tell us much about how they *work*. But with a bit of the neuroscience we went over, we can now address that question!

Let's use a simple and silly example. Suppose we want a neural network that computes the product $x_1x_2$ of two numbers $x_1, x_2$. Since multiplication isn't linear, we should use a nonlinear activation function.

Let's train this little network with a few points:

$$
\begin{align}
(1, 0) &\rightarrow 0 \\
(1/\sqrt{2}, 1/\sqrt{2}) &\rightarrow 1/2 \\
(0, 2) &\rightarrow 0
\end{align}
$$

We can use PyTorch or TensorFlow or whatever the kids use nowadays to do this, but we can also do it by hand, even if only crudely. We set the weight matrix of the hidden layer to

$$
W_1 = \begin{pmatrix}
1 & 0 \\
1/\sqrt{2} & 1/\sqrt{2} \\
0 & 1
\end{pmatrix}
$$

The rows are just the inputs of our training data. When running the network, it takes the dot product of the input vector $x$ with each row. The resulting vector $W_1x$ encodes the similarity of each row to the input. For example,

$$
W_1\begin{pmatrix}
1 \\
0
\end{pmatrix} = \begin{pmatrix}
1 \\
1/\sqrt{2} \\
0
\end{pmatrix}
$$

Obviously, the vector $(1, 0)$ *is* the vector $(1, 0)$ in the first row, so it makes sense it would have maximum similarity. Here, the norms of each row vector in the weight matrix are the same, which is important to note. For if the middle row were $(10, 10)$ for example, the dot product $(10, 10) \cdot (1, 0)$ would be $10$ even though they are *far* from being similar. We'll go over how we can address this issue in a bit.

The activation function controls the relationship between the activation and the neuron's output. Perhaps the most common nowadays is the ReLU activation $g(a) = \max(0, a)$, and we will follow the tribe here. We should treat the biases as a part of the activation function itself – when using ReLU for example, the bias controls the threshold at which each neuron fires.

Indeed, using ReLU activation, when we set the bias vector to 

$$
b_1 = \begin{pmatrix}
-1/\sqrt{2} \\
-1/\sqrt{2} \\
-1/\sqrt{2}
\end{pmatrix}
$$

then each neuron will only produce an output when the activation exceeds $1/\sqrt{2}$:

$$
\begin{align}
W_1\begin{pmatrix}
1 \\
0
\end{pmatrix} + b_1 &= \begin{pmatrix}
\color{green}{1 - 1/\sqrt{2}} \\
\color{red}{0} \\
\color{red}{-1/\sqrt{2}}
\end{pmatrix} \\ g\left(W_1\begin{pmatrix}
1 \\
0
\end{pmatrix} + b_1\right) &= \begin{pmatrix}
1 - 1/\sqrt{2} \\
0 \\
0
\end{pmatrix}
\end{align}
$$

We can think of each hidden neuron as a sensor. Its weights specify the data we are even trying to match, which each row representing a specific target. Its bias and activation function controls how sensitive it is. With ReLU activation, higher biases allow it to detect more dissimilar inputs to the vector encoded by its weights. Altogether, a hidden layer **determines how similar the input is to what it remembers**: the weight matrix is its list of memories, and the bias and activation functions represent how strong each memory is. Neural networks are  an elegant way to implement content-addressable memory.

#### The output layer computes the final value by analogy

We often solve a problem by thinking about a similar problem we successfully solved in the past. Our memories of past experiences that we can retell are called **episodic memories**, and they are cruical for approaching situations that we haven't have extensive practice at doing. While we might not rely on episodic memory for turning on the light or flushing the toilet, we would rely on episodic memory for a difficult situation we have only experienced a few times before.

That is the idea behind what the output layer does in a neural network. It takes the output of the hidden layer, with each neuron's output representing the similarity to a given vector. The $j$th *column* of the output weights $W_2$ conveys what value an input vector similar to the $j$th row of $W_1$ should have. If the $j$th element of the hidden output vector $g(W_1x + b_1)$ is large, then the $j$th column of $W_2$ will contribute a lot to the final output.

But we talked about how row vectors in $W_1$ that have larger norms can skew the similarities computed by the hidden layer. If one hidden neuron has way larger weights than the others, then wouldn't its output always be large? Luckily, we can just control how large its output weight is. If we scale the $j$th column by a constant $\lambda$, then the output of the $j$th hidden neuron will contribute only $\lambda$ times as much. And we can vary the scaling factor for each dimension when there are multiple output dimensions.

Usually, we don't train neural networks by hand (thank goodness) so we don't need to worry about this. But continuing our example from the last section, the hidden neurons emit an output of at most $1 - 1/\sqrt{2}$ for the input $(1, 0)$, even when $(1, 0)$ is literally a row of the weight matrix $W_1$. It is similar for all the other inputs in our dataset.

So our output weights $W_2$ should be a row vector of the result of the multiplication for each input, scaled by

$$
\lambda = \frac{1}{1-1/\sqrt{2}} = 2 + \sqrt{2}
$$

In the end, we get

$$
W_2 = \begin{pmatrix}
\lambda(0) & \lambda(1/2) & \lambda(0)
\end{pmatrix} = \begin{pmatrix}
0 & (2 + \sqrt{2})/2 & 0
\end{pmatrix}
$$

We don't need a second bias vector $b_2$ here, so in the end, we get

$$
W_2\,g\left(W_1\begin{pmatrix}
1 \\
0
\end{pmatrix} + b_1\right) = \begin{pmatrix}
0 & (2 + \sqrt{2})/2 & 0
\end{pmatrix} \begin{pmatrix}
1 - 1/\sqrt{2} \\
0 \\
0
\end{pmatrix} = 0
$$

as expected! For inputs that sit around the three training examples, the network provides reasonable results. For example, the input $(5/6, 1/3)$ should ideally yield the output $5/18$. Let's check:

$$
W_2\,g\left(W_1\begin{pmatrix}
5/6 \\
1/3
\end{pmatrix} + b_1\right) = \begin{pmatrix}
0 & (2 + \sqrt{2})/2 & 0
\end{pmatrix} \begin{pmatrix}
5/6 - 1/\sqrt{2} \\
7/(6 \sqrt{2}) - 1/\sqrt{2} \\
1/3 - 1/\sqrt{2}
\end{pmatrix} \approx 0.201
$$

with an error of around $0.07$. Not bad (but not good).

Here, we also derived the output weights from the data, but now we can only access those vectors by providing the corresponding vector in the hidden layer – still addressing by content, but indirectly. Similarly, some of our memories cannot directly be accessed, but instead require us to activate some other knowledge to access. For example, we have unconscious **procedural memories** that encompass our memory of skills such as playing an instrument. We can only access procedural memories when we consciously plan that skill, or we have formed a habit to perform that skill which requires extensive practice. Even then, those memories are represented not by words or images, but as motor actions, so we might not be able to describe or draw that skill.

#### Training creates meaningful memories

Of course, we don't train neural networks this way. But the principles still hold regardless: the hidden layer stores the network's memory and computes a similarity score for each vector in that memory, while the output layer computes the associated value by analogy with the dataset we used to train the network.

Likewise, we don't use episodic memory for the vast majority of problems we face in life, since episodic memories are anecdotal. Just because we have a certain experience with something doesn't mean the experience will always happen. True power comes from being able to *generalize* from multiple experiences to create **meaning**.

For example, words are simply a way of categorizing things in the world. When we first learned basic words like "apple" and "door" as a child, we simply perceived examples of those words *while* seeing or hearing those words at the same time, so our brains automatically associated the thing with the word. Things and words are anything but similar at first: one is a concrete experience in the world, while the other is an abstract sequence of symbols that we had to invent. Regardless, with enough examples, we are able to perceive the thing as being similar to the word.

Whenever we use a neural network, we will most likely use fewer hidden units than training examples. But that could be a blessing in disguise, as that encourages the neural network to store vectors that generalize multiple training examples as hidden neuron weights. When we train the neural network, we are trying to find the best vectors to represent the entire dataset.

For example, suppose we have a dataset consisting of scalar inputs and outputs which *generally* associates negative inputs to $-1$ and nonnegative inputs to $1$. However, there are some *huge* outliers: there might be a training example that, say, maps $9804$ to $-1$. It is for the best that we don't have a hidden neuron with a weight of $9804$ and a corresponding output of $-1$, as when we pass *any* positive integer to the neural network, it would have a mile high activation. Without a similarly massive amount of training examples, massive neural networks can **overfit**: instead of making meaningful generalizations about the data, the neural network simply stores training examples even if they don't represent the dataset because it can. Often less is more, and this is no exception!

Alternatively, as long as we transform the data to have $0$ mean, we can also regularize the neural network during training to get away with larger neural networks. Regularization penalizes the network for storing large weights in its hidden layer – so it doesn't needlessly compare the input to outliers! – and also prevents the outputs from becoming too large. This is perfect for situations where we have to learn a complex relationship with limited data: regularization challenges the hidden layer to store vectors that are more similar to the bulk of the data (hence close to the mean of $0$).

### Further reading

In AI research and practice, neuroscience is too often neglected. This is a great book about how the brain does its computations:

> Rolls, E. T. (2016). Cerebral cortex: principles of operation. *Oxford University Press*.
