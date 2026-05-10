# Model Architecture And Labelling

Consider a problem in detecting whether there is an inguana, lion, or cat in an image. One way you can do this is to treat every pixel in the image as a feature so that the image can be thought as a big feature vector with size $3 WL$

> The number of pixels in an image is it's resolution and each image has 3 channels: R,G,B

Your network design may depend on what you want to do. Consider

![alt text](<sources/Introduction to Neural Networks/Screenshot 2026-04-19 at 11.26.38 AM.png>)

This if you notice is just a multinomial regression. Here's an alternative

![alt text](<sources/Introduction to Neural Networks/Screenshot 2026-04-19 at 11.28.00 AM.png>)

This is doing multiple linear regression.

One crucial difference between the two of them is that the first model can only strictly predict one of the animal categories. This is because the output values are normalized. The best you can get is probably that if there are multiple of them, then their values would be equivalent. Additionally, the outcomes of the model are also linked e.g. If there is no lion or iguana, then the model will predict that there is likely to be a cat. This model simply does not fit the goal of finding multiple classes of the animal in an image.

> Can define an extra class for no animals

The second model on the other hand can indeed do just that. The 3 networks are independent of one another and so is not constrained to normalization. Each will do it's job as specified and produce either 0 or 1. In this case, the 2nd model can be seen as a more general version of the first because it's use case cover that of the first.

# Neural Network's Layers

Here's a typical setup

![alt text](<sources/Introduction to Neural Networks/Screenshot 2026-04-19 at 11.49.22 AM.png>)

A neural network generally has 3 types of layers: the input layer, hidden layer, and the output layer. The input layer is the layer for the input data to comes in and the output works in a similar fashion.

The hidden layer is the layer in between. There can be an arbitrary number of layers in the hidden layer. It's the layer that transform the input layer to the output layer

# Batching Inputs

Let's say we have a batch of input data instead of a singular one. Instead of passing the data one by one, we can describe a system to evaluate these input in one go

So, similar to the original problem, we have a batch of images each of size $3n$ and we have $m$ of these images. The input $X$ then would be a matrix of size $3n$ x $m$. The input layer then have a size of $3$ x $3n$ which if you perform the operation, we'll be left with a $3\times m$ matrix.

We're not done yet though, we still have the biases. We have 3 bias, and for each bias we need to add it to each element of the row. We'll have a $3\times m$ matrix of biases where the elements of each row will be exatly the same (it's just the bias replicated) or you can think of it as replicating the bias matrix $m$ times. In the end you'll end up with a $3 \times m$ matrix for your output.

> In programming a neural network, frameworks usually have something called broadcasting where it will do exactly this

You can continue this process all the way to the output layer where presumable you'll get something along the lines of $n_o \times m$ where $n_o$ is all the possible outputs of your neural network

# References

1. https://www.youtube.com/watch?v=MfIjxPh6Pys&list=PLoROMvodv4rMiGQp3WXShtMGgzqpfVfbU&index=11
