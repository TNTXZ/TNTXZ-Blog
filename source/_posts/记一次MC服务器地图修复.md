---
title: 记一次MC服务器地图修复
date: 2025-11-15 13:39:34
tags: [MC,MC服务器]
cover: https://image.starsfire.top/blog/2025/11/15/wallpaper_minecraft_bedrock_edition_800x450%20.png
---
# 起因：

当我给MC服务器加上[<u>CustomWorldHeight</u>](https://modrinth.com/plugin/customworldheight)插件后，地图高度发生改变，而在我删除插件后报错:

```
[19:54:22] [Leaves Common Worker #0/WARN]: Ignoring heightmap data for chunk [5, 6], size does not match; expected: 37, got: 52
[19:54:22] [Leaves Common Worker #0/ERROR]: [ca.spottedleaf.moonrise.patches.chunk_system.scheduling.task.ChunkLoadTask] Failed to parse chunk data for task: GenericDataLoadTask{class: ca.spottedleaf.moonrise.patches.chunk_system.scheduling.task.ChunkLoadTask$ChunkDataLoadTask, world: world, chunk: (5,6), hashcode: 1688560600, priority: NORMAL, type: CHUNK_DATA}, chunk data will be lost
java.lang.ArrayIndexOutOfBoundsException: Index 24 out of bounds for length 24
......
```

# 修复过程：

于是我使用<u>NBTExplorer</u>工具打开mca文件:

```
-Chunk [0,0]
	-Heightmaps
		-MOTION_BLOCKING: 52 long integers
		-MOTION_BLOCKING_NO_LEAVES: 52 long integers
		-OCEAN_FLOOR: 52 long integers
		-WORLD_SURFACE: 52 long integers
	-...
-...
```

而它实际应该为37位。

我用<u>同样的种子</u>生成了地图，读取每个区块的Heightmaps值并替换给服务器地图，然后启动服务器：

```
[10:59:16] [Leaves Common Worker #0/ERROR]: [ca.spottedleaf.moonrise.patches.chunk_system.io.MoonriseRegionFileIO] Failed to decompress chunk data for task: Task for world: 'world' at (5,6) type: CHUNK_DATA, hash: 1697861292
java.io.EOFException: null
......
```

也是成功地<u>失败</u>了......

然后我突发奇想，直接把这个删掉让服务器重新生成不就可以了吗？——

我使用<u>[Chunker](https://www.chunker.app/)</u>工具将地图版本转到新一级版本再转回来（当时服务器是1.21.8,最新版是1.21.10）

~~PS：为什么这么做呢？懒得写代码罢了，反正效果一样，嗯，就这么简单。~~

然后成功了~

# 结语：

对，就这么简单，结果弯弯绕绕花了我3个周末，此修复过程有部分省略，只保留了当时的主体故事。

这告诉我们一个道理：做一些危险事情之前一定要做好<u>备份</u>！！！

