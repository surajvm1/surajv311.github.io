---
layout: post 
title: Neural Networks Basics
category: technicalArticles
---

> Tried to get sense of AI hype going around. 

With AI advancing around, I tried to get a sense of the fundamentals on which it is built and touched some of the trending terms watching their youtube videos, reading articles, chatting with GPT, like - RAG, MCP, LLMs, VectorDBs, ML/DL/Neural Networks, etc. 

Things make a little more sense now, I found neural networks quite interesting. Below is a condensed snippet of an interesting conversation I was having with GPT making me understand neural networks. 

```
Explain me neural network with a mathematical example. 

Let's work through a concrete, simplified example with actual numbers. I'll make this small enough that you can verify every calculation by hand.
Setup: Recognizing the digit "1" from a blurry 4×4 image
The Input Image
Let's say our blurry image is 4×4 pixels (grayscale, 0=black, 1=white):
Image of "1":
0.1  0.7  0.2  0.1
0.2  0.8  0.2  0.1
0.1  0.6  0.3  0.2
0.1  0.7  0.1  0.1
We flatten this into a vector of 16 input values:
Input = [0.1, 0.7, 0.2, 0.1, 0.2, 0.8, 0.2, 0.1, 0.1, 0.6, 0.3, 0.2, 0.1, 0.7, 0.1, 0.1]

Network Architecture
Input layer: 16 neurons (one per pixel)
Hidden layer 1: 4 neurons
Hidden layer 2: 3 neurons
Output layer: 2 neurons (outputs: "is it 1?" and "is it 0?")

Layer 1: Input → Hidden Layer 1
Each neuron computes: output = activation(weighted_sum + bias)
Neuron 1 in Hidden Layer 1
Weights (I'll give you 16 weights, one for each input):
w = [0.5, 0.8, 0.1, 0.0, 
     0.3, 0.9, 0.2, 0.0,
     0.4, 0.7, 0.1, 0.1,
     0.2, 0.8, 0.0, 0.0]
Bias: b = -0.5
Calculation:
z₁ = (0.1×0.5) + (0.7×0.8) + (0.2×0.1) + (0.1×0.0) +
     (0.2×0.3) + (0.8×0.9) + (0.2×0.2) + (0.1×0.0) +
     (0.1×0.4) + (0.6×0.7) + (0.3×0.1) + (0.2×0.1) +
     (0.1×0.2) + (0.7×0.8) + (0.1×0.0) + (0.1×0.0) - 0.5
z₁ = 0.05 + 0.56 + 0.02 + 0.0 + 0.06 + 0.72 + 0.04 + 0.0 +
     0.04 + 0.42 + 0.03 + 0.02 + 0.02 + 0.56 + 0.0 + 0.0 - 0.5
z₁ = 2.54 - 0.5 = 2.04

Activation function (ReLU: max(0, x)):
a₁ = ReLU(2.04) = 2.04
Let me give you simpler weights for the remaining 3 neurons:

Neuron 2:
Weights: [0.1, 0.2, 0.3, 0.1, 0.2, 0.1, 0.2, 0.1, 0.3, 0.2, 0.1, 0.1, 0.2, 0.3, 0.1, 0.1]
Bias: -0.2
Calculate: z₂ = sum of (input × weight) - 0.2 = 0.31 - 0.2 = 0.11
a₂ = ReLU(0.11) = 0.11

Neuron 3:
Weights: [0.2, 0.1, 0.4, 0.2, 0.1, 0.2, 0.3, 0.1, 0.2, 0.1, 0.3, 0.2, 0.1, 0.2, 0.2, 0.1]
Bias: -0.3
Calculate: z₃ = 0.49 - 0.3 = 0.19
a₃ = ReLU(0.19) = 0.19

Neuron 4:
Weights: [0.1, 0.3, 0.2, 0.1, 0.1, 0.2, 0.1, 0.1, 0.1, 0.2, 0.2, 0.1, 0.1, 0.3, 0.1, 0.1]
Bias: -0.1
Calculate: z₄ = 0.36 - 0.1 = 0.26
a₄ = ReLU(0.26) = 0.26

Hidden Layer 1 Output: [2.04, 0.11, 0.19, 0.26]

Layer 2: Hidden Layer 1 → Hidden Layer 2
Now these 4 values become inputs to the next layer.
Neuron 1 in Hidden Layer 2:
Weights: [0.6, 0.3, 0.2, 0.1]
Bias: -0.2
z = (2.04×0.6) + (0.11×0.3) + (0.19×0.2) + (0.26×0.1) - 0.2
z = 1.224 + 0.033 + 0.038 + 0.026 - 0.2 = 1.121
a = ReLU(1.121) = 1.121

Neuron 2:
Weights: [0.2, 0.5, 0.4, 0.3]
Bias: -0.1
z = (2.04×0.2) + (0.11×0.5) + (0.19×0.4) + (0.26×0.3) - 0.1
z = 0.408 + 0.055 + 0.076 + 0.078 - 0.1 = 0.517
a = ReLU(0.517) = 0.517

Neuron 3:
Weights: [0.1, 0.4, 0.3, 0.5]
Bias: -0.15
z = (2.04×0.1) + (0.11×0.4) + (0.19×0.3) + (0.26×0.5) - 0.15
z = 0.204 + 0.044 + 0.057 + 0.13 - 0.15 = 0.285
a = ReLU(0.285) = 0.285

Hidden Layer 2 Output: [1.121, 0.517, 0.285]

Layer 3: Hidden Layer 2 → Output Layer
Final layer! 2 outputs: "probability it's a 1" and "probability it's a 0"
Output Neuron 1 (for digit "1"):
Weights: [0.8, 0.4, 0.2]
Bias: -0.3
z = (1.121×0.8) + (0.517×0.4) + (0.285×0.2) - 0.3
z = 0.897 + 0.207 + 0.057 - 0.3 = 0.861

Output Neuron 2 (for digit "0"):
Weights: [0.1, 0.3, 0.5]
Bias: -0.2
z = (1.121×0.1) + (0.517×0.3) + (0.285×0.5) - 0.2
z = 0.112 + 0.155 + 0.143 - 0.2 = 0.210
Raw outputs: [0.861, 0.210]
Apply Softmax (converts to probabilities):
exp(0.861) = 2.366
exp(0.210) = 1.234
Sum = 2.366 + 1.234 = 3.600
P(digit is "1") = 2.366 / 3.600 = 0.657 = 65.7%
P(digit is "0") = 1.234 / 3.600 = 0.343 = 34.3%

Result: The network predicts "1" with 65.7% confidence!

Takeaways:

- Each neuron multiplies inputs by weights, adds them up, adds a bias, then applies activation. Weights are learned during training to recognize patterns (like vertical edges for "1"). Layers stack: outputs of one layer become inputs to the next. The network transforms raw pixels → abstract features → decision

- How Are Weights Defined? (Training Process)
You DON'T manually set weights! That would be impossible for large networks. Instead, the network learns them automatically through a process called training.
The Training Process:
     Step 1: Start with Random Weights
     Initially, all weights are random small numbers like:
     w = [0.03, -0.12, 0.08, ...]

     Step 2: Forward Pass (What We Just Did)
     Feed an image through the network
     Get a prediction (e.g., "65.7% it's a 1")

     Step 3: Calculate Error (Loss)
     True label: "1" → [1, 0] (100% sure it's 1, 0% it's 0)
     Network prediction: [0.657, 0.343]
     Error = How wrong are we?
     Loss = (1 - 0.657)² + (0 - 0.343)² = 0.235

     Step 4: Backpropagation (The Magic) This is where calculus comes in! The algorithm:
     Calculates: "If I change weight w₁ by a tiny bit, how much does the error change?"
     Adjusts weights in the direction that reduces error
     Mathematical update rule:
     new_weight = old_weight - (learning_rate × gradient)
     Example:
     w₁ = 0.5 - (0.01 × 0.3) = 0.497

     Step 5: Repeat Thousands of Times
     Show the network 1000s of images of digits
     Each time, adjust weights slightly
     Over time, weights learn to recognize patterns:
     Some weights become sensitive to vertical edges (good for detecting "1")
     Some learn curves (good for detecting "0", "6", "8")

- Do You Retrain for Dogs vs Cats?: 
Yes, but: 
Option A: Train from Scratch
Start with random weights. Feed 10,000 images of dogs and cats. Let the network learn weights that detect: Fur textures, Ear shapes (pointy cat ears vs floppy dog ears), Nose patterns, Eye positions
Option B: Transfer Learning (Much Faster)
Why retrain everything? Low-level features are similar across tasks. 
Layer 1 learns: edges, corners, colors
Layer 2 learns: textures, simple shapes
Layer 3 learns: fur patterns, facial features
Layer 4 learns: "this is a dog face" or "this is a cat face"
Process:
Take a network already trained on 1 million images
Freeze the first 3 layers (keep their weights)
Only retrain the last layer for dogs vs cats
Done in 100s of images instead of 10,000s. 

- Why Activation Functions? (ReLU, Softmax)
Without Activation Functions:
Layer 1: z₁ = w₁×input + b₁
Layer 2: z₂ = w₂×z₁ + b₂
Layer 3: z₃ = w₃×z₂ + b₃
Substitute:
z₃ = w₃×(w₂×(w₁×input + b₁) + b₂) + b₃
   = (w₃×w₂×w₁)×input + (combined biases)
   = W×input + B  ← Just a single linear equation!
Problem: Multiple layers collapse into ONE layer. Deep networks become useless.

ReLU (Rectified Linear Unit)
Definition: ReLU(x) = max(0, x)
Graph:
  |
  |     /
  |    /
  |   /
__|__/______
  |
Why use it?
Introduces Non-linearity
If neuron gets negative input → outputs 0 (neuron "turns off")
If positive → passes through unchanged
This lets networks learn complex patterns like:
- "Activate only if there's a vertical edge AND in the center"
- "Detect curves but ignore straight lines"
Biological Inspiration Real neurons fire only when stimulated enough (threshold behavior)
Computationally Simple
ReLU: just compare to 0 vs Sigmoid: e^x / (1 + e^x)  ← expensive
Hand Calculation Example:

z = -1.5  → ReLU(-1.5) = 0
z = 0.3   → ReLU(0.3) = 0.3
z = 2.7   → ReLU(2.7) = 2.7

Softmax (Output Layer)
Purpose: Convert raw numbers into probabilities that sum to 1.
Formula:
For outputs [z₁, z₂, ..., zₙ]:
softmax(z₁) = e^z₁ / (e^z₁ + e^z² + ... + e^zₙ)
Example (Dog vs Cat):
Raw outputs: [2.3, 0.8]
              dog  cat
Step 1: Exponentiate
e^2.3 = 9.97
e^0.8 = 2.23
Step 2: Normalize
P(dog) = 9.97 / (9.97 + 2.23) = 9.97 / 12.2 = 0.817 = 81.7%
P(cat) = 2.23 / 12.2 = 0.183 = 18.3%
Check: 81.7% + 18.3% = 100% 
Why Exponential?
Amplifies differences: high scores get MUCH higher probability
Always positive: probabilities can't be negative
Differentiable: needed for backpropagation

In further detail: 
The neural network is fundamentally a mathematical function. You feed it numbers (the pixel values of your image), and it performs a series of calculations, giving you back numbers that represent probabilities. The entire process is just multiplication, addition, and a few simple functions applied over and over.
The beautiful part is that we do not tell the network what makes a dog a dog or what makes a cat a cat. Instead, we show it thousands of examples, and it figures out the patterns by itself through a process called training.

Think of a neuron as a tiny decision maker. It looks at several inputs, weighs their importance, and produces one output.
Here is the mathematical formula for one neuron:
output = activation_function(w₁×x₁ + w₂×x₂ + w₃×x₃ + ... + wₙ×xₙ + b)
Let me break down each component:
The inputs (x₁, x₂, x₃, etc.) are the data coming into this neuron. For the first layer, these might be pixel brightness values. For deeper layers, these are outputs from previous neurons.
The weights (w₁, w₂, w₃, etc.) represent how important each input is. A large positive weight means "this input strongly influences me to activate." A large negative weight means "this input strongly influences me to stay quiet." A weight near zero means "I don't really care about this input."
The bias (b) is like a threshold that shifts the neuron's sensitivity. If the bias is negative, the neuron needs more total input to activate. If positive, it activates more easily.
The activation function introduces non-linearity, which I will explain in detail shortly.

Hand Calculation Example - Single Neuron
Let me give you a tiny example you can verify right now:
Inputs: x₁ = 0.8, x₂ = 0.3, x₃ = 0.5
Weights: w₁ = 0.5, w₂ = 0.7, w₃ = 0.2
Bias: b = -0.3
Calculate the weighted sum (called z):
z = (0.5 × 0.8) + (0.7 × 0.3) + (0.2 × 0.5) + (-0.3)
z = 0.4 + 0.21 + 0.1 - 0.3
z = 0.41
Now apply the ReLU activation function (which I will explain soon):
ReLU(z) = max(0, z) = max(0, 0.41) = 0.41
So this neuron outputs 0.41. 

Understanding Activation Functions
Why Do We Need Them?
This is crucial to understand. Let me show you what happens without activation functions.
Imagine a three-layer network where each layer just does weighted sums:
Layer 1: y₁ = W₁×input + b₁
Layer 2: y₂ = W₂×y₁ + b₂ = W₂×(W₁×input + b₁) + b₂
Layer 3: y₃ = W₃×y₂ + b₃ = W₃×(W₂×(W₁×input + b₁) + b₂) + b₃
If you multiply this all out (which you can try on paper), you get:
y₃ = (W₃×W₂×W₁)×input + (some combined biases)
This simplifies to just one big weighted sum! All three layers collapse into a single operation. This means your deep network gains no benefit from having multiple layers. It is like stacking multiple straight ramps, you still just get one straight ramp in the end.
Activation functions curve these ramps, allowing the network to learn complex, curved decision boundaries. This is what lets neural networks approximate virtually any function.

ReLU (Rectified Linear Unit)
The ReLU function is beautifully simple:
ReLU(x) = x    if x > 0
ReLU(x) = 0    if x ≤ 0
Or more compactly: ReLU(x) = max(0, x)
When a neuron's weighted sum is negative, ReLU makes it output zero. The neuron is essentially "off" or "silent." When positive, the neuron passes the value through unchanged. The neuron is "on."
This creates a threshold effect similar to real biological neurons. A neuron fires when it receives enough stimulation, and stays silent otherwise.
Sample calculations:
ReLU(-2.3) = 0
ReLU(0.0) = 0
ReLU(1.7) = 1.7
ReLU(5.2) = 5.2

Sigmoid Function
Another common activation function is the sigmoid, which squashes any input into a range between zero and one: σ(x) = 1 / (1 + e^(-x))
The sigmoid has a characteristic S-shaped curve. For large negative numbers, it outputs values close to zero. For large positive numbers, it outputs values close to one. For numbers near zero, it produces values near 0.5.
Sample calculations:
σ(0) = 1 / (1 + e^0) = 1 / (1 + 1) = 0.5
σ(2) = 1 / (1 + e^(-2)) = 1 / (1 + 0.135) = 1 / 1.135 = 0.88
σ(-2) = 1 / (1 + e^2) = 1 / (1 + 7.389) = 1 / 8.389 = 0.12
The sigmoid was popular historically, but ReLU has largely replaced it in hidden layers because ReLU is simpler to compute and helps networks train faster.

Softmax (For Output Layer)
When we want to classify into multiple categories (dog, cat, bird, etc.), we need outputs that represent probabilities. The softmax function converts a vector of numbers into a probability distribution:
For inputs [z₁, z₂, ..., zₙ]:
softmax(zᵢ) = e^zᵢ / (e^z₁ + e^z² + ... + e^zₙ)
The key properties are that all outputs are positive, and they sum to exactly one.
Hand calculation example:
Raw scores: [2.0, 1.0, 0.1]
             dog  cat  bird
Step 1: Exponentiate each score
e^2.0 = 7.389
e^1.0 = 2.718
e^0.1 = 1.105
Step 2: Sum them
Sum = 7.389 + 2.718 + 1.105 = 11.212
Step 3: Divide each by the sum
P(dog) = 7.389 / 11.212 = 0.659 = 65.9%
P(cat) = 2.718 / 11.212 = 0.242 = 24.2%
P(bird) = 1.105 / 11.212 = 0.099 = 9.9%
Check: 65.9% + 24.2% + 9.9% = 100.0% 
The exponential function amplifies differences, so the highest score gets an even higher probability.

---

Let me design an extremely small network again (another example) that you can fully calculate by hand. This will be unrealistically small (real networks are much larger), but it captures all the essential concepts.

Network Architecture:
Input Layer: 4 pixels (our "image" is just 2×2 pixels, grayscale)
Hidden Layer: 3 neurons
Output Layer: 2 neurons (dog probability and cat probability)
Our image is represented as four numbers between zero and one, where zero is black and one is white.

Complete Forward Pass (Prediction)
Let me walk you through predicting whether an image is a dog or cat with actual numbers you can verify.
Step 1: The Input Image
Our 2×2 grayscale image:
[0.9  0.2]
[0.8  0.3]
Flattened input vector: x = [0.9, 0.2, 0.8, 0.3]
Imagine this represents a simple dark blob on the right (maybe part of a dog's nose) and brightness on the left (maybe fur).

Weights and Biases (First Hidden Layer)
I am going to give you the weights for all three neurons in the hidden layer. In a real network, these start random and are learned. For now, pretend we already trained the network and these are the learned values.
Neuron 1 in Hidden Layer:
Weights: w₁ = [0.6, 0.4, 0.5, 0.3]
Bias: b₁ = -0.4
Calculate the weighted sum:
z₁ = (0.6 × 0.9) + (0.4 × 0.2) + (0.5 × 0.8) + (0.3 × 0.3) - 0.4
z₁ = 0.54 + 0.08 + 0.40 + 0.09 - 0.4
z₁ = 1.11 - 0.4
z₁ = 0.71
Apply ReLU activation:
a₁ = ReLU(0.71) = 0.71
Neuron 2 in Hidden Layer:
Weights: w₂ = [0.2, 0.7, 0.3, 0.6]
Bias: b₂ = -0.3
Calculate:
z₂ = (0.2 × 0.9) + (0.7 × 0.2) + (0.3 × 0.8) + (0.6 × 0.3) - 0.3
z₂ = 0.18 + 0.14 + 0.24 + 0.18 - 0.3
z₂ = 0.74 - 0.3
z₂ = 0.44
a₂ = ReLU(0.44) = 0.44
Neuron 3 in Hidden Layer:
Weights: w₃ = [0.3, 0.5, 0.2, 0.8]
Bias: b₃ = -0.5
Calculate:
z₃ = (0.3 × 0.9) + (0.5 × 0.2) + (0.2 × 0.8) + (0.8 × 0.3) - 0.5
z₃ = 0.27 + 0.10 + 0.16 + 0.24 - 0.5
z₃ = 0.77 - 0.5
z₃ = 0.27
a₃ = ReLU(0.27) = 0.27
Output of Hidden Layer: [0.71, 0.44, 0.27]
Output Layer
Now these three hidden neuron outputs become inputs to our final two output neurons.
Output Neuron 1 (Dog):
Weights: w_dog = [0.8, 0.6, 0.3]
Bias: b_dog = -0.2
Calculate:
z_dog = (0.8 × 0.71) + (0.6 × 0.44) + (0.3 × 0.27) - 0.2
z_dog = 0.568 + 0.264 + 0.081 - 0.2
z_dog = 0.913 - 0.2
z_dog = 0.713
Output Neuron 2 (Cat):
Weights: w_cat = [0.3, 0.7, 0.9]
Bias: b_cat = -0.3
Calculate:
z_cat = (0.3 × 0.71) + (0.7 × 0.44) + (0.9 × 0.27) - 0.3
z_cat = 0.213 + 0.308 + 0.243 - 0.3
z_cat = 0.764 - 0.3
z_cat = 0.464
Raw outputs: [0.713, 0.464]
Step 4: Apply Softmax
Convert these raw scores to probabilities:
e^0.713 = 2.040
e^0.464 = 1.590
Sum = 2.040 + 1.590 = 3.630
P(dog) = 2.040 / 3.630 = 0.562 = 56.2%
P(cat) = 1.590 / 3.630 = 0.438 = 43.8%
Final prediction: DOG (56.2% confidence)
This is the entire forward pass through the network.

Training - How Weights Are Learned
Now comes the most important question: where did those weights come from? This is where training happens.
The Training Dataset: Before training, we need labeled data. Imagine we have collected one thousand images:
Five hundred images of dogs, each labeled "dog"
Five hundred images of cats, each labeled "cat"
Each image is our input, and the label is what we want the network to output.
Training Process Overview: Training happens in iterations called epochs. In each epoch, we show the network every image in our training set, and we adjust the weights to make predictions better.
Here is the cycle:
First: Initialize all weights randomly (small numbers like 0.01, negative 0.03, 0.05, etc.)
Second: For each training image, do a forward pass (like we just did) and get a prediction.
Third: Calculate how wrong the prediction was. This is called the loss or error.
Fourth: Use calculus to figure out how to adjust each weight to reduce the error. This is called backpropagation.
Fifth: Update all weights slightly in the direction that reduces error.
Sixth: Repeat for all images, then repeat the entire process for many epochs until the network gets good at predictions.

Calculating Loss (Mean Squared Error)
Let me show you how we measure how wrong our prediction was.
Suppose our network predicted [0.562, 0.438] for dog and cat, but the true label was "dog", which we represent as [1.0, 0.0] (one hundred percent dog, zero percent cat).
The loss function measures the difference:
Loss = (1/2) × [(predicted_dog - true_dog)² + (predicted_cat - true_cat)²]
Loss = (1/2) × [(0.562 - 1.0)² + (0.438 - 0.0)²]
Loss = (1/2) × [(-0.438)² + (0.438)²]
Loss = (1/2) × [0.192 + 0.192]
Loss = (1/2) × 0.384
Loss = 0.192
A perfect prediction (outputting exactly [1.0, 0.0]) would give us a loss of zero. The larger the loss, the worse our prediction.

Gradient Descent and Backpropagation
This is where calculus enters the picture, but I will explain it conceptually first, then mathematically.
The Concept: Imagine you are standing on a hilly landscape in thick fog. You cannot see where the lowest point is, but you can feel the slope under your feet. If you always walk downhill, eventually you will reach a valley (a low point). That is gradient descent.
The "landscape" is actually a mathematical surface where the height represents the loss (error). Each weight in your network corresponds to one dimension in this landscape. Our goal is to find the combination of weights that gives us the minimum loss.
The Mathematics: For each weight, we calculate the derivative of the loss with respect to that weight. This derivative tells us: "if I increase this weight by a tiny amount, how much does the loss change?"
Let me show you a simplified example with one weight:
Suppose we have one weight w = 0.5
After forward pass, loss L = 0.192
We compute: dL/dw (the derivative of loss with respect to w)
Suppose dL/dw = 0.35
This means: "if I increase w slightly, the loss will increase by about 0.35 times that amount"
The update rule is:
new_w = old_w - (learning_rate × dL/dw)
The learning rate (often 0.01 or 0.001) controls how big our steps are. Let's say learning rate equals 0.01:
new_w = 0.5 - (0.01 × 0.35)
new_w = 0.5 - 0.0035
new_w = 0.4965
We moved the weight slightly in the direction that decreases loss!

Backpropagation: The Chain Rule
Computing these derivatives for all weights is complex because the network has many layers. Backpropagation uses the chain rule from calculus to efficiently compute all derivatives.
The chain rule states:
If y depends on z, and z depends on x, then:
dy/dx = (dy/dz) × (dz/dx)
For our network:
Loss depends on output layer activations
Output activations depend on output layer weights
Output activations also depend on hidden layer activations
Hidden activations depend on hidden layer weights
Hidden activations depend on input
By applying the chain rule backwards through the network (hence "back" propagation), we can compute the derivative of the loss with respect to every single weight.
Let me show you a concrete calculation for one weight in the output layer. This requires some calculus, but I will go step by step.
Computing Gradient for Output Weight:
Remember our output neuron for "dog":
z_dog = w₁×h₁ + w₂×h₂ + w₃×h₃ + b
      = (0.8 × 0.71) + (0.6 × 0.44) + (0.3 × 0.27) - 0.2
      = 0.713
After softmax: p_dog = 0.562
True label: y_dog = 1.0
The derivative of the loss with respect to the output (before softmax) is:
dL/dz_dog = p_dog - y_dog = 0.562 - 1.0 = -0.438
Now, to find how much the loss changes with respect to the first weight (connecting hidden neuron one to the dog output):
dL/dw₁ = dL/dz_dog × dz_dog/dw₁
       = dL/dz_dog × h₁
       = -0.438 × 0.71
       = -0.311
Update the weight:
w₁_new = w₁_old - (learning_rate × dL/dw₁)
       = 0.8 - (0.01 × (-0.311))
       = 0.8 + 0.00311
       = 0.80311
Notice the weight increased slightly! That is because the derivative was negative, meaning increasing this weight will decrease the loss. The network wants to predict "dog" more strongly (which makes sense since the true label was dog).
You would repeat this calculation for every single weight in the network.

Complete Training Loop
Pseudocode that shows the entire training process:
Initialize all weights randomly
For each epoch (let's say 100 epochs):
    For each image in training set:
        # Forward Pass
        1. Feed image through network
        2. Get prediction (probabilities)
        # Calculate Loss
        3. Compare prediction to true label
        4. Calculate error (loss)
        # Backward Pass (Backpropagation)
        5. For each weight in output layer:
           - Calculate derivative dL/dw
           - Update: w_new = w_old - (learning_rate × dL/dw)
        6. For each weight in hidden layers (going backwards):
           - Use chain rule to calculate derivative dL/dw
           - Update: w_new = w_old - (learning_rate × dL/dw)
    # After seeing all images once:
    Calculate average loss across all training images
    Print progress: "Epoch 10: Average Loss = 0.143"
    If loss is low enough, stop training
After many epochs, the loss decreases, and the network gets better at distinguishing dogs from cats.

What Is the Network Actually Learning?
The weights encode patterns:
First layer weights might learn to detect simple features like edges, corners, or color blobs. One neuron might activate strongly when it sees a vertical edge. Another might respond to horizontal edges.
Second layer weights combine these simple features into more complex patterns. They might detect textures (fur), shapes (triangular ears), or patterns (stripes, spots).
Third layer weights combine complex features into complete concepts. They learn to recognize "this combination of features means dog" versus "this combination means cat."
The network builds a hierarchy of understanding, from simple to complex, all automatically from the data!

The Complete ML Lifecycle
Now let me walk you through the entire process from start to finish, as you would do in a real project.
Phase 1: Problem Definition and Data Collection
First, clearly define what you want to predict. In our case: given an image, classify it as dog or cat. Next, collect your dataset. You might scrape images from the internet, use an existing dataset like ImageNet, or take your own photos. You need thousands of images, ideally balanced (equal numbers of dogs and cats). Each image must be labeled. This is often done manually or using crowdsourcing platforms. The quality of your labels directly affects your model's quality.
Phase 2: Data Preprocessing
Real-world data is messy. You need to clean and standardize it:
Resize images to a consistent size (say 224×224 pixels). Neural networks expect fixed-size inputs. Normalize pixel values from the range [0, 255] to [0, 1] by dividing by 255. This helps training converge faster. Split your data into three sets:
Training set (70% of data): used to train the network
Validation set (15% of data): used to tune hyperparameters
Test set (15% of data): used only at the end to evaluate final performance
Augment your data: Create variations of training images by randomly flipping, rotating, cropping, or adjusting brightness. This helps the network generalize better.
Phase 3: Model Architecture Design
Decide on your network structure. For image classification, convolutional neural networks (CNNs) work best, but the principles are the same as what we discussed.
Choose:
Number of layers
Number of neurons per layer
Activation functions
Output layer structure (softmax with two outputs for dog vs cat)
Phase 4: Training
Initialize weights randomly. Set hyperparameters like learning rate (0.001 is a common starting point), batch size (how many images to process before updating weights, often 32 or 64), and number of epochs (how many times to go through the entire dataset, often 50-200).
Run the training loop I described earlier. Monitor two metrics:
Training loss: Error on the training set. This should decrease steadily.
Validation loss: Error on the validation set (data the network has never seen during training). This should also decrease, but might level off or increase if the network starts overfitting (memorizing training data instead of learning general patterns).
If validation loss stops decreasing while training loss keeps dropping, you are overfitting. Solutions include:
Getting more training data
Simplifying the model (fewer layers or neurons)
Using regularization techniques like dropout
Stopping training earlier
Phase 5: Hyperparameter Tuning
Use the validation set to experiment with different settings. Try different learning rates (0.01, 0.001, 0.0001). Try different architectures (more or fewer layers). Try different batch sizes.
This phase is iterative. You train many models with different settings and pick the one that performs best on the validation set.
Phase 6: Final Evaluation
Once you are satisfied with your model's performance on the validation set, evaluate it one final time on the test set. This gives you an unbiased estimate of how well the model will perform on completely new data in the real world.
Calculate metrics like:
Accuracy: What percentage of images did you classify correctly?
Precision: Of the images you labeled as "dog," what percentage were actually dogs?
Recall: Of all the actual dog images, what percentage did you correctly identify?
Phase 7: Deployment
If the test performance is good enough for your use case, deploy the model. This might mean:
Creating an API that accepts images and returns predictions
Embedding the model in a mobile app
Running the model on a web server
Phase 8: Monitoring and Maintenance
After deployment, monitor the model's performance on real-world data. Performance might degrade over time if the data distribution changes (for example, if people start uploading different types of dog breeds).
Periodically retrain the model with new data to keep it accurate.
Part 9: Key Insights and Common Pitfalls
Neural networks are universal function approximators. Given enough neurons and layers, they can theoretically approximate any continuous function. This is why they work for such diverse tasks.
More data usually beats better algorithms. A simple neural network trained on one million images will often outperform a sophisticated network trained on ten thousand images.
Training is expensive, inference is cheap. Training might take hours or days on powerful computers. But once trained, making predictions is fast, often milliseconds per image.
Overfitting is the main challenge. Networks are so powerful they can memorize training data perfectly, but then fail on new data. Regularization, dropout, data augmentation, and early stopping help combat this.
Deeper networks learn hierarchical features. Early layers learn simple patterns, later layers learn complex combinations. This is why deep learning works so well for images, where natural hierarchies exist (pixels → edges → shapes → objects).
```

------------------------------------------------
