# Logistic Regression Neural Network Backprop

We'll derive the update rule for doing logistic regression with backprop and neural network. We'll assume a 3 layer network. The first layer has 3 neurons with a vector of $n$ input and the sigmoid activation function $\sigma$, and then a layer with 2 neurons and then finally 1 neuron for 3rd layer. We'll assume fully connected layers

The idea of backprop starts from the very end of the network near the ouput. Recall that for linear regression we have the objective

$$
J(\theta) = -\frac{1}{m}\sum_i^mL^{(i)}(h_\theta(x^{[i]}),y^{(i)})
$$

where $h_\theta(x) = \sigma(\theta x)$ and

$$
L(\hat{y}, y) = y\log(\hat{y}) + (1 - y)\log(1 - \hat{y})
$$

We'll make some notational note here for a bit. To indicate the output of the ith layer, we use $a^{[i]}$. This would mean that $a^{[3]} = h_\theta(x)$.

The idea of backprop is to use the derivative of the next immediate layer to know the derivative of the output of the current layer we're considering to find the derivative for the weights.

Now, we're at the last layer. The output of the last layer is seen as an input to the loss function. So the method will first have us take the derivative of our output, $a^{[3]}$, w.r.t the loss function

$$
\frac{\partial L}{\partial a^{[3]}} = \frac{y}{a^{[3]}} - \frac{1-y}{1-a^{[3]}}
$$

It's important here to note that $a^{[3]} = \sigma(w^{[3]}a^{[2]}+b^{[3]})$ is a function and not some raw input values.

Then, once we have the derivative of our output, we can use it to get the weights to our input

$$
\frac{dL}{dw^{[3]}} = \frac{dL}{da^{[3]}}\frac{da^{[3]}}{dw^{[3]}}\\
\,\\
= \left(\frac{y - a^{[3]}}{a^{[3]}(1-a^{[3]})}\right)a^{[3]}(1-a^{[3]})a^{[2]} = (y - a^{[3]})a^{[2]}
$$

> If you can't see why, take derivatives of $L$ with each weight. This means you'll have to express L with the weights. Though at this point, maybe try to subtitute those expressions with $a^{[3]}$...

Now you have the derivatives to your current output, the same predicament we were in at the start. You can think of the loss function as the final 'final' layer.

And that's the game we play with each layer. You will repeat this procedure until eventually you get every weight. What's difficult however is that the number of neurons in a layer may be more than 1. In such a case, you must consider the derivative of each output of the neuron to the loss function because each weight within that layer only correspond to one particular neuron and that from that layer onwards, the network is effectively a multivariable function. You'll see this if you consider this case further down the layer.

## The 2nd Layer

To go from the 3rd to 2nd layer, we will once again have to first find $\frac{\partial L}{\partial a^{[2]}}$. The nice thing is, we can consider the 3rd layer onward to be a function say $F$ which is a scalar valued function. This would mean

$$
L = F(a^{[3]}) = F(\sigma(w^{[3]}a^{[2]} + b^{[3]}))
$$

Here's the tricky bit. Obviously, we would like to utilize chain rule here by exploiting $a^{[3]}$. But, what does it mean to take the derivative of a scalar function w.r.t to a vector? (which $a^{[2]}$ is)

Well, we will have to consider the final goal of why we're doing this in the first place. The idea is to find the derivative for the weights in the 2nd layer. What we can do is to consider $a^{[3]}$ to be a function of each neuron in the 2nd layer

$$
a^{[3]} = \sigma(w^{[3]} \sigma(w^{[2]}a^{[1]} + b^{[2]}) + b^{[3]}) = G(a^{[2]}_1, a^{[2]}_2)
$$

where we override $\sigma$ a bit here to have it be applied on per element basis if the input is a vector. It must be that $w^{[2]}_{i,j}$ only affects the ith neuron.

If we want to take the derivative then, we simply need to only work out one of the input term i.e. $a_i^{[2]}$. You can think of each $a^{[2]}_i$ as function that takes in weights of the 2nd layer. With this, it is now clearer what taking the derivative with respect to a vector would mean to us. The derivative would be to find

$$
\frac{\partial a^{[3]}}{\partial a^{[2]}} = \begin{pmatrix}
\frac{\partial a^{[3]}}{\partial a_1^{[2]}} \\
\frac{\partial a^{[3]}}{\partial a_2^{[2]}}
\end{pmatrix} = a^{[3]}(1-a^{[3]})w^{[3]}
$$

where we can find that

$$
\frac{\partial a^{[3]}}{\partial a_i^{[2]}} = a^{[3]}(1-a^{[3]})w_i^{[3]}
$$

so that to find the derivative of the weights

$$
\frac{\partial L}{\partial w^{[2]}_{i,j}} = \frac{\partial L}{\partial a^{[3]}} \frac{\partial a^{[3]}}{\partial w_{i,j}^{[2]}} = \frac{\partial L}{\partial a^{[3]}} \frac{\partial a^{[3]}}{\partial a_i^{[2]}} \frac{\partial a_i^{[2]}}{\partial w_{i,j}^{[2]}}
$$

We can easily find that

$$
\frac{\partial a_i^{[2]}}{\partial w_{i,j}^{[2]}} = a_i^{[2]}(1-a_i^{[2]})a^{[1]}_j
$$

And so

$$
\frac{\partial L}{\partial w^{[2]}_{i,j}} = (\frac{y}{a^{[3]}} - \frac{1-y}{1-a^{[3]}}) a^{[3]}(1-a^{[3]})w_i^{[3]} a_i^{[2]}(1-a_i^{[2]})a^{[1]}_j\\
\,\\
= (y - a^{[3]})w_i^{[3]}a_i^{[2]}(1-a_i^{[2]})a_j^{[1]}
$$

Or to be more compact, we can say

$$
\frac{\partial L}{\partial w_i^{[2]}} = w_i^{[3]}a_i^{[2]}(1-a_i^{[2]})(y-a^{[3]})a^{[1]}
$$

More conveniently, we can also say

$$
\frac{\partial L}{\partial w^{[2]}} = \begin{pmatrix}
w_1^{[3]}a_1^{[2]}(1-a_1^{[2]})(y-a^{[3]})a^{[1]} \\
w_2^{[3]}a_2^{[2]}(1-a_2^{[2]})(y-a^{[3]})a^{[1]}
\end{pmatrix}
$$

# Activation Functions Necessity

Activation functions are needed to give our network more non-linearity. Without it we're essentially doing a form of linear model. Consider a neural network in which it's activation function is the identity function e.g. $f(x)=x$

Then, we can essentially model it as a recursive function. If we have 3 layers then the output can be written down as such

$$
W^{[3]}(W^{[2]}(W^{[1]}x+b^{[1]})+b^{[2]})+b^{[3]}
$$

Which if we expand the terms

$$
W^{[3]}W^{[2]}W^{[1]}x + W^{[3]}W^{[2]}b^{[1]} + W^{[3]}b^{[2]} + b^{[3]}
$$

As you recall, the $W$ are matrices. The product of matrices are another matrix and product of matrix with a vector is a vetor. Hence our final expression would be

$$
Wx + b
$$

where $W$ is the product of matrices $W_1W_2W_3$ and $b$ is the sum of the vector terms. The non linearity is the thing that makes the network

# Normalization

Here we'll go through some common practices with neural network. One you would find is with normalizing your data. The rationale behind this is that it may allow you to train faster.

Consider that you're just putting data as is. In some particular setup, say the general linear model, the contour plot of the loss function may be irregular although still a cone

![alt text](<sources/Backprop & Improving Neural Networks/Screenshot 2026-04-26 at 5.49.00 PM.png>)

This creates a jagged path in your training since the steepest direction from your current point may be going elsewhere. In comparison, if your data is normalized, your contour plot looks more regular and each steepest direction would point to the global minima. Normalization is done through usual procedure of using the mean to shift and variance to standardize

# The Vanishing/Exploding Gradient

This is a case for when you, say, have a very deep network of $N$ layers. Remember that alot of what you do is involved with matrix multipliclation. Depending on the coefficient that you have so far, the values of your network could either explode or just dies down

If your values are way bigger than 1, then imagine if you keep on multiplying with numbers that is equal to your number, then the final value may blow up. If your number is less than 1, then it will die down as $N \rightarrow \infin$. The ideal spot would be to always be near 1 this way you'll never go to either extremes as you do backprop.

> Recall the expression we got for using the identity function as the activation. All of those matrix multiplication may blow up / die down depending on their coefficient.

The idea of normalizing can be applied here. There is however one more issue we need to consider and that is the initial weights we should set up because of another issue: width of the network i.e. How many neurons you have in a layer.

Both of the problem mentiod here and before can be helped by initializing the weights properly e.g. Taking into account the size of the layer.

# References

1. https://www.youtube.com/watch?v=zUazLXZZA2U&list=PLoROMvodv4rMiGQp3WXShtMGgzqpfVfbU&index=12
