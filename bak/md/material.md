# Definition of Attention

~~might need this~~

Two different definition:

1. attention is the process of attending to objects
   - the attention is based on objects
   - provide the strong theory support for defining human attention as objects
2. attention is something that happens in the mind - a mental “inside” which is linked with the perceivable “outside”
   - related with the high-level mental information in human mind
   - When a human is doing a task, the task is a kind of high-level information in the mind, guiding human attention

# Importance

[Learning to infer human attention in daily activities](https://www.sciencedirect.com/science/article/pii/S0031320320301187#bib0046)

研究注意力可以使机器人更好的帮助人类

**这个研究的是视频中人注意的object感觉和我们的有些类似**

> In a typical human-robot interaction scenario in daily life, a robot is installed with a camera capturing a video, inside which a human is performing daily activities. In this kind of scenarios, inferring human attention from the robot’s view equals to inferring the attention of a human inside a video (Inside-video human attention). Elderly care is a potential and valuable application of human-robot interaction. As we know, it is laborious for the elderly people to perform some simple activities such as open the refrigerator, lift a cup, and move a bottle. To enable the robot to assist the human, it is necessary for the robot to infer human attentional objects. For example, a human is going to take an apple from a refrigerator, when the human is approaching the refrigerator, the robot could infer that human attentional object is the refrigerator, so that the robot could assist the human to open the refrigerator door in advance.

[Attentional concentration during physiotherapeutic intervention improves gait and trunk control in patients with stroke](https://www.sciencedirect.com/science/article/pii/S0304394020305619)

研究表明物理治疗干预中的注意力集中改善了脑卒中患者的步态和躯干控制

> The group with high attentional concentration showed significant differences in abdominal muscle strength and trunk control.

[Topographical Memory and the Concentration of Attention in Top Female Tennis Players](https://www.sciencedirect.com/science/article/pii/S1877042815032437)

女性网球运动员注意力越集中，场上表现更好

> If the female tennis players give more correct answers during tasks demanding concentration of attention  under fast speed conditions, this aspect is associated with a better performance of the tennis players on the court. 

[Deconstructing the effects of concentration meditation practice on interference control: The roles of controlled attention and inflammatory activity](https://www.sciencedirect.com/science/article/pii/S0889159120305870)

*冥想改善注意力*

> Our results suggest that practice of concentration meditation influences interference control by enhancing controlled attention to goal-relevant task elements, and that inflammatory activity relates to individual differences in controlled attention.

[Cognitive Aging and Long-Term Maintenance of Attentional Improvements Following Meditation Training](https://link.springer.com/article/10.1007/s41465-018-0068-1)

通过冥想训练来提升注意力以此改善年龄带来的认知水平下降

>In conclusion, the present study suggests that intensive and continued meditation is associated with enduring improvements in sustained attention, supporting the notion that the cognitive benefits of dedicated mental training may persist over the long-term when promoted by a regimen of continued practice.

[Attention and Concentration Training in Sport]()

[Mindfulness meditation practice and executive functioning: Breaking down the benefit](https://www.sciencedirect.com/science/article/pii/S1053810016300058?via%3Dihub)

集中注意力训练有助于对抗ADHD

> This may represent a promising finding for populations experiencing inhibition deficits (e.g., older adults and individuals with ADHD), as mindfulness practice may represent a possible treatment option for improving such abilities.

[Characterizing Attention with Predictive Network Models](https://www.sciencedirect.com/science/article/pii/S1364661317300177#bib0050)

注意力缺失导致各种后果

>It is hard to imagine meeting the demands of daily life without the ability to focus. Impairments in attention, which are common to clinical populations as diverse as ADHD [[4\]](https://www.sciencedirect.com/science/article/pii/S1364661317300177#bib0020), depression [[5\]](https://www.sciencedirect.com/science/article/pii/S1364661317300177#bib0025), schizophrenia [[6\]](https://www.sciencedirect.com/science/article/pii/S1364661317300177#bib0030), bipolar disorder [[7\]](https://www.sciencedirect.com/science/article/pii/S1364661317300177#bib0035), post-traumatic stress disorder [[8\]](https://www.sciencedirect.com/science/article/pii/S1364661317300177#bib0040), and traumatic brain injury [[9\]](https://www.sciencedirect.com/science/article/pii/S1364661317300177#bib0045), predict a wide range of negative outcomes, from poorer educational achievement to worse employment and job performance, peer acceptance, and family relationships [[10\]](https://www.sciencedirect.com/science/article/pii/S1364661317300177#bib0050).

# How to detect/infer attention?

直接研究脑

[Characterizing Attention with Predictive Network Models](https://www.sciencedirect.com/science/article/pii/S1364661317300177)

-  resting-state fMRI 脑电波之类的

>Recent work shows that models based on functional brain networks can predict how well individual people pay attention.

visual attention

- outside images, 通过眼睛，需要受试者戴设备

>Visual attention mainly refers to the eye fixation saliency object [[31\]](https://www.sciencedirect.com/science/article/pii/S0031320320301187#bib0030), [[53\]](https://www.sciencedirect.com/science/article/pii/S0031320320301187#bib0052), [[55\]](https://www.sciencedirect.com/science/article/pii/S0031320320301187#bib0054) or saliency map [[2\]](https://www.sciencedirect.com/science/article/pii/S0031320320301187#bib0002), [[23\]](https://www.sciencedirect.com/science/article/pii/S0031320320301187#bib0022), [[54\]](https://www.sciencedirect.com/science/article/pii/S0031320320301187#bib0053), which signals the regions of an image where the human observer would pay attention at the first glance. The ground truth of saliency map or saliency object is usually obtained by the eye-tracking equipment that records the eye fixations of the observer looking at the image. Therefore, the visual attention is the attention of a human outside images. 

Inside images

- 不用戴设备，通过视频识别人注意力目标

[Learning to infer human attention in daily activities](https://www.sciencedirect.com/science/article/pii/S0031320320301187#bib0046)