[Source - Yet another blog explaining Vulkan synchronization](https://themaister.net/blog/2019/08/14/yet-another-blog-explaining-vulkan-synchronization/)

# Synchronization

All commands in a queue are executed in an optimized order. But that happen on the Queue level, not in command buffers. Everything submitted to a queu is just a linear stream of commands. And synchronization applies globally to a queue. THere is also command overlap, so reordering may happen between command buf

## Barriers
There are different types of Barriers

First `Execution barriers`. Note: These fall under `Memory barriers`
# Execution Barriers
vkCmdPipelineBarrier
```
void vkCmdPipelineBarrier(
    VkCommandBuffer                             commandBuffer,
    VkPipelineStageFlags                        srcStageMask,
    VkPipelineStageFlags                        dstStageMask,
    VkDependencyFlags                           dependencyFlags,
    uint32_t                                    memoryBarrierCount,
    const VkMemoryBarrier*                      pMemoryBarriers,
    uint32_t                                    bufferMemoryBarrierCount,
    const VkBufferMemoryBarrier*                pBufferMemoryBarriers,
    uint32_t                                    imageMemoryBarrierCount,
    const VkImageMemoryBarrier*                 pImageMemoryBarriers);
```
Ignore memory barriers for now:
`srcStageMask` and `dstStageMask` are left and are the heart of synchronization. This splits the command stream in two; Everything `before` the barrier and everything `after` the barrier.


