---
layout: post
title: "First Native OSGi Developers Meeting"
category: Programming
tags: [osgi, c++]
published: true
---
{% include JB/setup %}

On the 22nd of May 2012 the first Native OSGi developers meeting was hosted by [Thales](http://www.thalesgroup.com/NLHome/)
(Hengelo, Netherlands). Detailed notes about the meeting can be found [here](https://github.com/abroekhuis/NativeOSGi/wiki/Meeting-20120522-Hengelo).

After a general introduction round, we discussed the current state of the available open-source frameworks
([Celix][celix], [SOF][sof], [nOSGi][nosgi], and [CTK][ctk]) and tried to identify common needs and goals.
The projects had a lot in common, but also differed in some interesting ways.

Another very important discussion was centered about two questions which pop up frequently.

**Why is a native (C or C++) OSGi framework needed?**

- C and C++ as programming languages are here to stay (whether you like it or not).
- Traditional application domains (for example in medical imaging, embedded devices, sensor networks, etc.) often use native languages
  and possibly rely on a large native (legacy) code-base.
- Native developers designing scalable, modular platforms for a dynamically reconfigurable (embedded) environment
  need a light-weight yet powerful module system (just like Java developers).

**What are the benefits of a native (C or C++) OSGi framework?**

- Builds upon a mature API design for a dynamic environment, supporting native developers in creating modular platforms.
- Allows the creation of a hybrid Java and C/C++ architecture (via remote services) as an alternative to JNI.
- Maximizes the performance per watt ratio for (embedded) devices.
- Eases future software migrations of native components to a Java OSGi-based system.

We strongly believe that there is a market (community) for *Native OSGi* and quickly agreed on trying to draft a C and C++ API in the
near future and have it reviewed by the community.

Having a common API would allow us to re-use bundles originating from different projects,
as it is common in the Java OSGi community. Additionally, it would hopefully help us in growing a community and to foster collaboration
between the existing native OSGi framework implementations.

A large part of the afternoon discussions focused on the [meaning of a *Module Layer*](https://github.com/abroekhuis/NativeOSGi/wiki/Draft-Module-Layer)
in a native setting and about [C and C++ interoperability](https://github.com/abroekhuis/NativeOSGi/wiki/Draft-C-Cpp-Interoperability).
I think we are heading in the right directions but there are still many details to work out.

You can follow our collaborative efforts on our [NativeOSGi GitHub project](https://github.com/abroekhuis/NativeOSGi) and on the
[Celix Mailinglist](http://incubator.apache.org/celix/support/mailinglist.html).

[celix]: http://incubator.apache.org/celix/
[ctk]: http://www.commontk.org/index.php/Documentation/Plugin_Framework
[nosgi]: http://www-vs.informatik.uni-ulm.de/proj/nosgi/
[sof]: http://sof.tiddlyspot.com/
