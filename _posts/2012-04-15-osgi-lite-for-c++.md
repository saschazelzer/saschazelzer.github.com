---
layout: post
title: "OSGi Lite for C++"
tagline: "A micro services model for C++"
category: Programming
tags: [osgi, c++, micro services]
published: true
---
{% include JB/setup %}

OSGi solves the Java modularity problem. This is the major feat of OSGi, as it is claimed by its advocates.
While I am definitely pro OSGi, I can also see the difficulties when trying to migrate an existing code base
with complex dependencies to an OSGi based system. This applies both to Java projects as well as C/C++ projects
trying to migrate to one of the [C/C++ OSGi frameworks]({{site.production_url}}/2012/03/29/osgi-and-c++/).

In this blog post, I will discuss the idea of *OSGi Lite* and how it may be translated to the *Native OSGi* world
of C/C++ OSGi frameworks.

The OSGi Migration Problem
--------------------------

Large software systems usually consist of multiple components (in whatever form) having non trivial dependencies.
Modularization promises better encapsulation and re-use of components as well as cleaner dependency management. Using an established
framework like OSGi which is especially designed for enabling the creation of modularized applications seems like a no-brainer.

However, modularization itself is hard and the transition of an existing code base to OSGi is a complex task. This has been
discussed a lot in the past, for example here:

- [OSGi Discontent - No Migration Path!](http://techdistrict.kirkk.com/2009/03/25/osgi-discontent-no-migration-path/) (25/3/09)
- [OSGi - Feast or Famine?](http://java.dzone.com/articles/osgi-feast-or-famine) (13/4/10)
- [OSGi: Complexity is NOT the Problem](http://techdistrict.kirkk.com/2010/04/15/osgi-complexity-is-not-the-problem/) (15/4/10)
- [OSGi? No Thanks](http://blogs.mulesoft.org/osgi-no-thanks/) (9/11/10)

Arguably the biggest obstacle when transitioning an existing Java application to OSGi is the (possible) existence of class loader hacks.
Another one is the fact that OSGi tends to expose the inherent complexity of a code base, leading to the false impression
that OSGi increases complexity instead of helping to manage it.

Usually, the reason for a project to move to OSGi is to achieve cleaner modularity by controlling the
visibility of Java packages between bundles. This is where OSGi excels with its module layer specification.
A second typical reason is the desire to use the OSGi service layer to realize a service oriented architecture. Unfortunately,
the service layer is tightly coupled to the module layer. This means that in order to benefit from OSGi principles, you
have to completely migrate your project to OSGi. While this might pay off in the long run, it is not a smooth migration
path and might even be a show-stopper because of possible large refactoring requirements.

OSGi Lite to the Rescue?
------------------------

People like the OSGi service layer so much that recently (in the year 2011) the idea of
[*OSGi Lite*](http://www.osgi.org/blog/2011/04/osgi-lite.html) started coming up again (here is
[another blog post](http://www.osgi.org/blog/2011/04/osgi-lite_05.html "OSGi Lite") from Peter Kriens about it). It essentially
is a service registry (like specified in the OSGi specs) without an OSGi framework, especially without the module layer. A Java
project based on this idea has been created by Karl Pauls, called [PojoSR][pojosr] (Pojo Service Registry).
You might also be interested in these slides: [PojoSR or (OSGi) uServices for the rest of us](http://www.slideshare.net/OSGiUsers/pojosr-or-osgi-services-for-the-rest-of-us).

By not requireing a running (OSGi) framework, the OSGi service layer can now be put to good use in existing projects step-by-step.
After having converted direct method calls between Jar files to a service model, the migration to a full
blown OSGi framework should be much smoother.

In my previous post about existing [C/C++ OSGi frameworks]({{site.production_url}}/2012/03/29/osgi-and-c++/) I ranted about the
lack of a C/C++ OSGi community. There are certainly many reasons for that: Most frameworks are still rather young and the
target audience is not the average Joe programmer. Another reason I can think of is that C/C++ programmers tend to be more
conservative in a sense that introducing a new framework (requiring [Inversion of Control](http://stackoverflow.com/questions/3057526/framework-vs-toolkit-vs-library "Framework vs. Toolkit vs. Library")) needs much more convincing. However, the OSGi module layer solves a
Java specific problem which does not directly map to C/C++. In C/C++, the visibility of symbols defined in shared libraries
can be effectively controlled by various means (for example on Windows, all symbols are hidden by default anyway). The symbol search
path for each library is determined by the link-time dependencies (in reality, it may be a bit more complicated),
as opposed to the global class path in Java. Symbol versioning is still
a problem, but many projects have full control over the set of deployed libraries and may not need that level of complexity. Let alone the
need for a dynamic module system or updates at runtime. These projects would probably benefit the most from a C/C++ OSGi framework without
the module layer. This is what the [C++ Micro Services][cppms] library aims to provide.


C++ Micro Services
------------------

The [C++ Micro Services][cppms] library is to C++ roughly what [PojoSR][pojosr] is to Java. It provides a full implementation
of the OSGi service layer and just enough of the module layer to be able to create a *module context* for each shared or static
library. It leaves the handling of library/symbol dependencies and loading of libraries at runtime completely to the dynamic linker
of the operating system.

Originally, the main motivation for creating the C++ Micro Services library was the need to gain control over the life-cycle
and dependencies of singletons in an existing large code base I was working on. Over the years, the number of singletons grew and dependencies
between them were introduced, which lead to a static (de-)initialization order fiasco. Migrating the whole code base
to a OSGi-like C++ framework to replace singletons with services was not an option (due to several reasons) but the power of
the OSGi service model was still intriguing. After realizing that a complete OSGi framework would probably be overkill for now anyway,
we decided that having just the service layer available would be a perfect match for our needs. Building upon our experience in
developing the [CTK Plugin Framework](http://www.commontk.org/index.php/Documentation/Plugin_Framework) (a Qt-based C++ OSGi framework),
creating the C++ Micro Services library took just a couple of weeks.

Using the library is as simple as using any other C++ library. There is no need to call certain methods to start a framework or
to think about deployment issues. Here is a simple code snippet demonstrating how to register and query service objects:

{% highlight cpp %}
#include <SomeInterface.h>
#include <usGetModuleContext.h>

class MyService : public us::Base, public SomeInterface
{
  void DoSomething() { std::cout << "My service implementation"; }
};

void RegisterService(MyService* myService)
{
  us::ModuleContext* context = us::GetModuleContext();
  context->RegisterService<SomeInterface>(myService);
}

void UseService()
{
  us::ModuleContext* context = us::GetModuleContext();
  
  // Get the highest ranking service, if any
  us::ServiceReference serviceRef = context->GetServiceReference<SomeInterface>();
  if (serviceRef)
  {
    SomeInterface* service = context->GetService<SomeInterface>(serviceRef);
    if (service)
    {
      service->DoSomething();
    }
  }
}
{% endhighlight %}

Note that the service implementations base class `us::Base` can actually be any class, specified during the configuration of the C++ Micro
Services library. For details, please see the [Build Instructions](http://cppmicroservices.org/doc_latest/BuildInstructions.html).

Hopefully, the C++ Micro Services library will lower the adoption barrier for a OSGi service model. If a project later on realizes that
it needs a full blown C++ OSGi framework, the migration should be more straight-forward.

[pojosr]: http://code.google.com/p/pojosr/
[cppms]: http://cppmicroservices.org/
