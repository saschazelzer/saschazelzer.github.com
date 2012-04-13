---
layout: post
title: "OSGi Lite for C++"
tagline: "A micro services model for C++"
category: Programming
tags: [osgi, c++, micro services]
published: false
---
{% include JB/setup %}

OSGi solves the Java modularity problem. This is the major feat of OSGi, as it is claimed by its advocates.
While I am definitely pro OSGi, I can also see the difficulties when trying to migrate an existing code base
with complex dependencies to an OSGi based system. This applies both to Java projects as well as C/C++ projects
trying to migrate to one of the [C/C++ OSGi frameworks]({{site.production_url}}/2012/03/29/osgi-and-c++/).

The OSGi Migration Problem
--------------------------

Large software systems usually consist of multiple components (in whatever form) having non trivial dependencies.
Modularization promises better encapsulation and re-use of components as well as cleaner dependency management. Using an established
framework like OSGi which is especially designed for enabling the creation of modularized applications seems like a no-brainer.

However, modularization itself is hard and the transition of an existing code base to OSGi is complex. This has been
discussed a lot in the past, for example here:

- [OSGi Discontent - No Migration Path!](http://techdistrict.kirkk.com/2009/03/25/osgi-discontent-no-migration-path/) (25/3/09)
- [OSGi - Feast or Famine?](http://java.dzone.com/articles/osgi-feast-or-famine) (13/4/10)
- [OSGi: Complexity is NOT the Problem](http://techdistrict.kirkk.com/2010/04/15/osgi-complexity-is-not-the-problem/) (15/4/10)
- [OSGi? No Thanks](http://blogs.mulesoft.org/osgi-no-thanks/) (9/11/10)

Arguably the biggest obstacle when transitioning an existing Java application to OSGi is the (possible) existence of class loader hacks.
Another one is the fact that OSGi tends to expose the inherent complexity of a code base, leading to the false impression
that OSGi increases complexity instead of helping to manage it.

So far, the (implicitly assumed) reason for a project to move to OSGi was to achieve better modularity by controlling the
visibility of Java packages between bundles. This is where OSGi excels with its module layer specification.
A second typical reason is the desire to use the OSGi service layer. 

OSGi Lite to the Rescue ?
-------------------------

C++ Micro Services
------------------


http://www.osgi.org/blog/2011/04/osgi-lite.html
http://www.osgi.org/blog/2011/04/osgi-lite_05.html
http://it-republik.de/jaxenter/news/OSGi---Nein-danke!-Zu-komplex-fuer-Otto-Normal-Entwickler-057515.html
http://code.google.com/p/pojosr/
http://www.slideshare.net/OSGiUsers/pojosr-or-osgi-services-for-the-rest-of-us


