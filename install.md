# 安装注意事项

安装过程中会遇到一些问题，下面给出对应的解决方案：

1. opencv的安装：
```
conda install opencv
```

2. 在centos 6.3上安装时，可能会遇到如下问题：
```
cudaCheckError() failed : invalid device function
```
参考如下链接解决：
https://github.com/smallcorgi/Faster-RCNN_TF/issues/19
不同架构GPU对应的修改如下：

```bazaar

# Which CUDA capabilities do we want to pre-build for?
# https://developer.nvidia.com/cuda-gpus
#   Compute/shader model   Cards
#   6.1		      P4, P40, Titan X so CUDA_MODEL = 61
#   6.0                    P100 so CUDA_MODEL = 60
#   5.2                    M40
#   3.7                    K80
#   3.5                    K40, K20
#   3.0                    K10, Grid K520 (AWS G2)
#   Other Nvidia shader models should work, but they will require extra startup
#   time as the code is pre-optimized for them.
CUDA_MODELS=30 35 37 52 60 61

```

3. 2中的修改代表什么？

what is difference between “-arch sm_13” and “-arch sm_20”

SM stands for Streaming Multiprocessor and the number indicates the features supported by the architecture. You can find a good description in the CUDA Programming Guide sections 3.1.2-3.1.4 and you can see the features associated with each architecture in the table in appendix F.

From the NVCC manual (also included in the Toolkit):

In order to allow for architectural evolution, NVIDIA GPUs are released in different generations. New generations introduce major improvements in functionality and/or chip architecture, while GPU models within the same generation show minor configuration differences that „moderately‟ affect functionality, performance, or both.
Your GPU has Compute Capability 2.0, so you should use sm_20 to enable the compiler to use features not available in older architectures. If you want backward compatibility, you could also target sm_13 (or sm_1x), check out the documents above for how to use the -gencode option to nvcc to target multiple architectures in a single call to nvcc.

Regarding performance, one thing to look out for is that sm_1x did not support IEEE754 floating point, so if you target sm_13 and run on a device with Compute Capability 2.0 or later then you may find that floating point runs faster since it is using the less accurate path. You can also force the less accurate path with sm_20 or later by using the -ftz=true -prec-div=false -prec-sqrt=false options, see section 5.4.1 in the CUDA Programming Guide for more information on this.


4. 
```
cd faster_rcnn_pytorch
mkdir data
cd data
ln -s $VOCdevkit VOCdevkit2007
```
做如下改动， 去掉软连接时的$符号：
```
cd faster_rcnn_pytorch
mkdir data
cd data
ln -s VOCdevkit VOCdevkit2007
```