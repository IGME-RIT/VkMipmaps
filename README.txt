Mipmap generation + Anisotropic filtering

Vulkan-tutorial inspired me to write this ATLAS tutorial
https://vulkan-tutorial.com/Generating_Mipmaps

In TextureGPU.cpp, we use the function vkCmdBlitImage
to make a duplicate of an image that is in GPU memory.

VkImageBlit is a structure that is designed to describe
the source image (being copied) and the destination image
(result of the copy)

By setting the destination image resolution to half of
the width and height of the source, the destination 
image becomes a downsampled verison of the original.

We downsample the original image, then we downsample
the downsampled version of the original, then we downsample
that, and the pattern continues until the mipmap chain is 
complete.

We use vkCmdPipelineBarrier to put a stall in the 
initialization command buffer. With this barrier, 
the command buffer will not continue until 
vkCmdBlitImage is finished executing

===================================================

In Texture.h we have a function to count the number
of desired mipmap layers (CountMps)

In Texture.cpp, we have to set the number of miplevels
in VkImageCreateInfo

If we want to use mipmaps, we need to change the
uasge of VkImageCreateInfo to include TRANSFER_SRC,
to allow us to make duplicates of the original image

==================================================

Demo.cpp

To enable anisotropic filtering, we have to see if
our GPU supports the feature, we check VkPhysicalDeviceProperties
to see if this is supported

vkGetPhysicalDeviceFeatures(gpu, &supportedFeatures);

After we see that it is supported, we need to enable the feature.
None of the features in supportedFeatures are enabled by default.

Right before vkCreateDevice is called, we add anisotropic
filtering to a list of desiredFeatures, which is put into
VkDeviceCreateInfo

Finally, we set the VkSamplerCreateInfo in prepare_sampler
to use anisotropic filtering, only if it is supported