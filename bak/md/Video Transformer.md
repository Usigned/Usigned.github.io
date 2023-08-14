# Video Transformer

## DETR

> 不是video transformer，而是transformer做detection的，后续几篇video transformer文章都有借鉴该文章

**参考文献**

- [(4条消息) 深度学习之目标检测（十一）--DETR详解_木卯_THU的博客-CSDN博客_detr](https://blog.csdn.net/baidu_36913330/article/details/120495817)
- [2005.12872.pdf (arxiv.org)](https://arxiv.org/pdf/2005.12872.pdf)

![img](https://img-blog.csdnimg.cn/1d037964ca704405b70e0ccbbb00dbef.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5pyo5Y2vX1RIVQ==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

特点

- 将目标检测视为set prediction问题

- 端到端

- 无NMS后续步骤
- 无anchor

**整体流程**

![image-20220611231841886](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220611231841886.png)

- cnn特征抽取+transformer结构
- 直接预测一组（N个）detections
  - (class + box)或者'no object'
  - N是事先定好的

**Loss设计**

>  bipartie loss

loss计算是ground truth集合到prediction集合

假设集合大小是N(参照上文，N是事先规定好的)

1. 首先将ground truth集合和prediction集合配对
   - 配对为最小的，因为集合是无序的(permutation-invariant)
2. 计算配对后的loss

![image-20220611234747983](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220611234747983.png)

![image-20220611235255088](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220611235255088.png)

> $\sigma$是配对函数，p是每个类别的概率

DETR架构

![img](https://img-blog.csdnimg.cn/1d8438c30d184b989f53047d0f57315c.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5pyo5Y2vX1RIVQ==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

1. CNN接收$3\times H_0 \times W_0$的收入，输出$C\times H \times W$
   - 每一批中所有图片都会被padding到最大图片大小

2. 使用1x1卷积将$C\times H \times W$降维为$d\times H \times W$

3. 将HW作为序列长度加上位置信息后输入encoder

   - spatial分解

   - 即输入为$d\times HW$，d为向量长度，$HW$为序列长度
   - 位置信息为每个像素的encoding，文中采用的正弦

4. decoder接收N个d维的postional embedding输入，又称object queries，加上位置信息后输出N个embedding。这个过程是并行的，而不是auto regressive的
   - 由于输入也是permutation-invariant，故object queries是需要训练的

5. N个embedding通过FFN输出预测

> object queries是怎么训练的？

## TransTrack

> 没看原论文，看的[(4条消息) TransTrack解读_周先森爱吃素的博客-CSDN博客_transtrack](https://zhouchen.blog.csdn.net/article/details/112427217)

**背景**

1. 经典单目标跟踪(SOT)pipeline

   ![image-20220612002910670](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220612002910670.png)

   - 目标对象是query
   - 整个图片是key

2. SOT 2 MOT

   ![image-20220612002950776](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220612002950776.png)

   - 延伸SOT的思想，前一帧的目标特征为query，当前帧的图像特征为key
   - 无法解决目标中途出现的问题 -- 无法获取ket

**pipeline**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210110134339971.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pob3VjaGVuMTk5OA==,size_16,color_FFFFFF,t_70#pic_center)

- 下方基本是dert的思路，得到当前帧的detections
  - 新增目标

- 上方是SOT的思路，得到上一帧的目标特征对当前图片的query
  - 历史目标

**整体过程**

1. 当前帧通过CNN提取特征，和object query查询(det query)，得到当前帧的object feature
   - 当前帧的object feature会用于下一帧查询历史目标
2. 当前帧的特征再和上一帧的object feature做query(track query)，得到历史目标
3. 后处理

## TrackFormer

> 没看论文，看的是[(4条消息) TrackFormer解读_周先森爱吃素的博客-CSDN博客_trackformer](https://blog.csdn.net/zhouchen1998/article/details/114778192)

>  和TransTrack非常类似，改进之处是把track query部分变成auto regressive的

- 在上一篇TransTrack里每次的track query是通过上一帧的det query生成的向量来生成的
  - 即每次的使用的特征是object feature
- 在这篇文章里把track query设置成auto regressive的，即当前帧的track query是通过上一帧的track query(而不是det query)生成的
  - 好处是track query带时序信息，可以自动避免重复信息等