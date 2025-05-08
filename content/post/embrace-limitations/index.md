---
title: Embrace Limitations
description: A little limitation can go a long way
slug: embrace-limitations
date: 2025-05-08 00:00:00+0000
image: image.png
tags:
    - Software Engineering
weight: 1       
---

# Limited systems can be cheaper and better

Writing a robust, general-purpose system is often quite expensive. For example, the memory allocator behind `malloc()` and `new` is the result of years of work and fine-tuning. It has to handle every weird allocation pattern, be completely thread-safe on 64-core processors, minimize memory fragmentation and overhead, etc. If the memory allocator is inadequate for some reason and you feel the urge to write your own replacement, a starting assumption is that it will take you about as long as the original took - maybe quicker if you are very smart, longer if you are unfamiliar with the problem space. In many cases that's impractical.

However if you introduce the right limitation it can massively reduce the effort required. `malloc` is partly complicated because the caller can allocate and deallocate any sized chunk of memory in any order, from any thread. An arena allocator has a different interface: the caller first creates an `arena`, then allocates many small chunks of memory inside that arena, then deletes the arena when complete, invalidating and freeing all the small chunks at once. This works well if you need to allocate a number of temporary structures to process a request, then can dispose them all at the end. Usually the implementation is a big chunk of memory and a `next` pointer. To allocate memory, just return the current value of `next` and increment it by the number of bytes requested. The implementation can be a few hundred lines, and allocation will be very fast.

Why is this cheaper to implement? Because we've embraced a limitation and produced a less general interface. The limitation here is on `free()` - the caller can't free individual memory chunks. That doesn't seem like a huge difference, but it simplifies things a lot. 

# Buy general-purpose, build special-purpose

It rarely makes sense to write your own very general-purpose subsystem (like custom programming language, database engine, memory allocator) if your main goal is to build an application - you likely can't afford to do a good job of it.

But _if_ the off-the-shelf components are not suitable, it may make sense to solve a *specialized* subset of the problem: not a programming language but a tiny DSL; not a general-purpose allocator but an arena allocator. You can take advantage of the limitations you have embraced to simplify the implementation by orders of magnitude. 

# Don't evolve your special-purpose system to make it more general

So you successfully implemented your special-purpose DSL for mangling your custom data. Maybe it's super fast and reliable because it has no looping constructs or branches so execution time is bounded. Everyone loves it. But then someone comes along and says "but you know, it would be really nice if we could do a loop here...". If you go ahead and kludge in a wonky looping construct, this is a classic blunder, like going up against a Sicilian when death is on the line. Now your system has lost its defining limitation, it is competing against general-purpose languages, and on those metrics it probably sucks - limited adoption, no debugger, no standard library, no ecosystem, and so on. 

This blunder is so classic it has its own [Wikipedia article](https://en.wikipedia.org/wiki/Greenspun%27s_tenth_rule). Don't fall for it! Either stick with your limited system and embrace the limitations, or if the problem really is more general, you have to suck it up and adopt the 'real' system.




