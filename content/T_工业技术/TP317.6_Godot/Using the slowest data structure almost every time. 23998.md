---
type: web-bookmark
date: 2023-11-29 15:29
publish: false
archive: 
tags: 
---
原文：[Using the slowest data structure almost every time. · Issue #23998 · godotengine/godot (github.com)](https://github.com/godotengine/godot/issues/23998)

---

**[vblanco20-1](https://github.com/vblanco20-1)** commented [on Nov 27, 2018](https://github.com/godotengine/godot/issues/23998#issue-384444386) • edited by reduz

**MODERATOR NOTE**: This issue is over five years old and discusses a reality that no longer matches the status quo of the codebase. For more information, scroll to [this comment](https://github.com/godotengine/godot/issues/23998#issuecomment-1727501892).

--

I was reviewing the code of godot, and i've found complete disregard for any kind of cpu side performance, down to the core data structures. Its using List everywhere, when a linked list is the slowest data structure you can use on modern PCs. Almost any other data structure would be faster, specially on small sized structures. Its specially egregious on things like the light list or reflection captures in renderables, where you are storing 3 pointers every time, instead of just giving it a stack array of 8 max lights (for example) + an extra for the cases where it would be more than that.

Same thing with RID_Owner being a tree where it could be a hash map, or a slotmap. Also the Octree implementation for culling has the exact same issue.

I want to ask about the design intention behind that total and absolute overuse of linked lists and dreadful performance pointer heavy data structures everywhere in the code. Is that for a specific reason? In most of the cases a "chunked" linked list, where you do a linked list of arrays would automatically bring performance gains on the same code.

The use of these data structures also prevents any kind of "easy" parallelism in a good chunk of the code and completely trashes the cache.

I am working on making a proof of concept implementation of refactoring some of the internals to use better data structures. At this moment i am working on a rewrite of the culling code which bypasses the current octree and just uses a flat array with optional parallelism. Ill use the tps-demo as benchmark and come back with results, which in fact i have benchmarked to go 25 levels deep on that octree...

On another more happy note, i am very impressed with the code style quality, everything is easy to follow and understand, and quite well commented.

---

**[reduz](https://github.com/reduz)** commented [on Sep 20](https://github.com/godotengine/godot/issues/23998#issuecomment-1727501892) • edited

Just to note on my side. This is a very old issue, almost five years old. But somehow it keeps resurfacing in social media, so I think its a good time to add some clarifications.

Everything mentioned originally has been 100% corrected (and I want to thank [@vblanco20-1](https://github.com/vblanco20-1) who provided many of the original suggestions):

- Linked Lists no longer used in hot paths
- RID Owner uses O(1) allocation (array)
- We have a superb implementation of HashMap and HashSet in Godot 4, we even took care to ensure the hashing functions for each Godot datatype generate the best entropy, and hence have the best distribution possible.
- We use worst case memory allocators (despite the name this is a good thing, means memory grows to accommodate the game needs on demand and stays there) and pools almost everywhere performance matters.
- Data oriented structures are used now extensively where it matters.
- Original issues raised regarding culling have both been resolved in both Godot 3 and Godot 4.
- Threading now is used extensively.
- The list of improvements since this issue was opened is far too long. Five years is a long time.

Even though what is mentioned on this issue has already been taken care of, there still is work pending to do optimization wise in Godot 4:

- Multithreading in many areas still needs work (mainly on the scene side, this is very new).
- Some locking in a few areas could be better.
- Godot still does not support texture/mesh streaming, which should help a lot with I/O performance.
- And really many other things.

But the situation now is day and night compared to 2018. There has been literally hundreds of pull requests geared to optimize Godot in the meantime, to the point that Godot 4 is pretty much all new code rewritten from scratch.

There is also a lot of continued work being done to optimize different areas and this has not stopped.