# Title

## Introduction

In recent years, deep neural networks(DNNs) have provided state-of-the-art works in many areas such as speech recognition, computer vision, and natural language processing. However, as DNNs achieving high accuracy, they are becoming more and more computationally expensive to use. Thus the energy consumption has become a big problem that hinders the deployment of DNNs on edge devices which usually have constrained memory and battery. Under this context, there are mainly two kinds of methods to improve DNN models' performance, either to design efficient model architecture from the start([^1]), or to reduce the original model size([^2]). Among the later methods, quantization is an effective way to reduce the memory and battery cost of any model by quantizing original weights and activations to lower precision.

Currently, the most wide-adopted quantization policy is to assign uniform bit-width for all layers. However, as the importance of each layer varies from each other in DNN models, using the same number of bits for all layers is not an optimal option. To further reduce model size and energy consumption, it's necessary to use mixed-precision quantization policy to optimize the bit-width allocation.

The most important part of mixed-precision quantization is to decide the suitable bit-width of weights and activations for each layer to make the model energy-efficient while maintain certain accuracy. Naive search method is computationally expensive as the design space can be exponential and unpractical in real circumstances. In conventional methods, the design space is manually explored by domain experts with rule-based heuristics. These methods highly rely on experts' knowledge (one must have knowledge of both machine learning and hardware architecture) and cannot scale between models and hardware platforms. 







 [^3]used neural architecture search (NAS) based method to explore the quantization policy. However, these methods take little use of the hardware information to explore the optimal energy-efficient method. It's widely adopted to take some proxy signals like FLOPs or model size as the measurement of model performance and the optimization object. However, these proxy signals can not always be accurate since different hardware behaves very differently. [^4] proposed to involve directly hardware feedback into the search loop and have largely reduced the size and energy consumption of models without much accuracy loss. However, this method is tightly coupled with hardware devices since it relies on platform to provide direct feedback making it troublesome to scale between different hardware platforms. Mover, as this method takes direct hardware feedback, it brings additional time costs and thus reduces overall search efficiency. Due to these reasons, we argue that an energy model is needed to predict DNN models' performance while searching for energy-efficient mixed-precision quantization policy.

 [^paleo] proposed a framework to determine latency of DNNs on various platforms using an analytical method. This rule-based method can't flexibly extend to different platforms with different optimization libraries. To make performance model adaptive to different hardware and optimization libraries, learning-based model is adopt to predict DNNs performance[^18]. These models can give out power and energy of DNNs without actually running them on a target platform. However these models are designed for floating-point DNN models. Quantized models which mainly proceed integer-arithmetic are not supported since floating-point number and integer number are treated very differently in model computer architecture.

In this paper, we propose a novel quantization method that leverages a heuristic evolutional algorithm to find an energy-efficient mixed-precision quantization policy via an energy model for quantized DNNs. When given a DNN model and a quantization policy, the energy model will predict the energy consumption of the quantized model's inference. And the evolutional algorithm will take these predictions as heuristic information to search for the optimal energy-efficient quantization policy. This paper brings the following contributions:

- We propose a learning-based energy consumption model that is suitable to predict the energy consumption of quantized models. This can be of great help when making an energy-efficient quantization policy.

- We propose a framework that can automatically choose the optimal energy-efficient quantization policy without detailed knowledge of the hardware platform. 
- By using the energy consumption model, we decouple making quantization policy with hardware platforms. So our framework can well scale to different models and devices.
- We compare our work with existing quantization methods through various network structures on different hardware, demonstrating our framework outperforms others.

## Related Work

**Quantization**

Network quantization has been studied excessively to boost DNN models' performance. [^7]used a rule-based strategy to quantize the network weights, the bit-widths for convolution and fully-connected layers are determined manually. [^8]quantized the network into binary values, which significantly reduce the model size and eliminate most of the multiplication. [^9] binarized both weights and activations and replaced original floating-point arithmetic with XNOR and bit-count operations. [^10] used one bit for weights and 2bits for activations and quantized the gradients to low precision during training. [^11]made use of 8bit integers for both weights and activations and introduced a method to run network inference purely on integer-arithmetic. These methods either assign the same number of bits to all layers or need domain experts to determine the bit-widths for different layer, while our framework can automatically find the optimal bit-width for each layer.

**Neural architecture search**

Neural architecture search (NAS) has become an extremely hot research topic in the past two years. Relevant studies try to find optimal network structures and hyper-parameters via reinforcement learning, genetic algorithms, or gradient-based approaches. [^12]first utilized reinforcement learning to generate neural network architectures with high accuracy and efficiency. [^13]adopted evolutionary algorithms as the controller, in which genetic operations were used to modify the architecture. [^14] proposed DARTS, a differentiable architecture search framework, to search in a continuous search space. The automatic methods can generate network structures that outperform the classical hand-crafted models. In addition to architecture search, NAS also can be combined with network quantization and pruning. [^15] leveraged reinforcement learning to prune the convolution channels automatically.[^4] provided a reinforcement learning-based method to search the quantization policy with hardware feedback. [^16] proposed an effective differentiable neural architecture search (DNAS) framework to determine the bit-width allocation.[^17] introduced an evolutionary search algorithm to achieve architecture search with quantization. Few of these methods take minimizing the energy consumption as their optimization object and can scale well to different hardware devices and models.

**Performance measurement**

Most commonly used metric to measure DNN complexity is too crude to predict energy consumption for real platforms. Traditional profiling methods have limited effectiveness in in 

## Our proposal

In this work, we first build an energy model for quantized DNNs. When given a DNN model and a certain quantization policy, this energy model will predict the energy cost of the DNN model to run one time inference. Then we use an evolutionary algorithm to explore mixed precision quantization policy in a heuristic manner. Given a pre-trained floating point model M, our target is to find an optimized quantization policy  based on the heuristic information the energy model provide. The problem can be defined as:
$$
\min_{\Pi(b_1,b_2,\dots,b_n)} E(Q_{\Pi(b_1,b_2,\dots,b_n)})\\ s.t.\varepsilon(Q_{\Pi(b_1,b_2,\dots,b_n)}) \le \varepsilon_{max}
$$
where $E(\cdot)$ denotes the heuristic information provided by the energy model, quantization policy is referenced as $\Pi(b_1,\cdots, b_l)$, where $b_i$ denotes the quantization bit-width of the $i$-th layer. $\varepsilon(\cdot)$ represents the error that quantization policy introduces and $\varepsilon_{max}$ is the max error that can be tolerant.

1. **quantization**
2. **energy model**
3. **search strategy**

## Experiments

## Conclusion

[^1]: literals that design efficient models from the start -- can be found in HAQ.
[^2]: literals that reduce original models size -- can be found in Evoq
[^3]: literals that used NAS
[^4]: HAQ: Hardware-Aware Automated Quantization with Mixed Precision
[^5]: papers that don't use energy consumption as optimization object
[^6]: neural power

[^7]: Deep Compression: Compressing Deep Neural Networks with Pruning, Trained Quantization and Huffman Coding
[^8]: Binarized neural networks: Training deep neural networks with weights and activations constrained to +1 or -1.
[^9]:  XNOR-Net: ImageNet Classification Using Binary Convolutional Neural Networks
[^10]:  Dorefa-net: Training low bitwidth convolutional neural networks with low bitwidth gradients
[^11]:  Quantization and training of neural networks for efficient integer-arithmetic-only inference.
[^12]: . Neural architecture search with reinforcement learning
[^13]: Regularized evolution for image classifier architecture search
[^14]: Darts: Differentiable architecture search
[^15]: AMC: Automl for model compression and acceleration on mobile devices
[^16]: Mixed precision quantization of convnets via differentiable neural architecture search
[^17]: Optimizing the Bit Allocation for Compression of Weights and Activations of Deep Neural Networks
[^paleo]: paleo
[^18]: learning-based models

