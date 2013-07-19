---
layout: post
title: "C++ Micro Services 1.0.0 released"
category: Programming
tags: [micro services, c++]
published: true
---
{% include JB/setup %}

As of today, the C++ Micro Services library version 1.0.0 is available. You can get the
sources at the [download page](http://cppmicroservices.org/download.html) or directly
from the [GitHub releases page](https://github.com/saschazelzer/CppMicroServices/releases).

During the last months a few notable improvements and additions took place. Among these are:

 * A [resources system](http://cppmicroservices.org/doc_1_0/MicroServices_Resources.html)
   for embedding arbitrary files into a module.
 * *Install* support on all supported platforms, including installation of API documentation.
 * Performance improvements in the service registry
 * Added new public class [us::SharedLibrary](http://cppmicroservices.org/doc_1_0/classSharedLibrary.html).
 * General overhaul of the high-level documentation, adding an exhaustive
   [tutorial](http://cppmicroservices.org/doc_1_0/MicroServices_Tutorials.html).

Additionally, the usual bug fixing and maintenance took place. Please report any issues you
have with this release.

What's next?
------------

The 1.0.0 release will be maintained for quite some time, but will likely not receive any more
feature additions. Work on version 2 has already started and there will be a couple of important
changes and improvements. The goal for version 2 is to get rid of the *base class requirement*,
meaning the service implementations won't need to inherit from a common base class anymore.
Further, the handling of type information of services is going to be improved, similar to the
introduction of the generics-based API in OSGi 4.3. Compare the current code for retrieving
a service

{% highlight cpp %}
ServiceReference ref = context->GetServiceReference<IDictionaryService>();
IDictionaryService* dict = context->GetService<IDictionaryService>(ref);
{% endhighlight %}

with the version 2 way:

{% highlight cpp %}
ServiceReference<IDictionaryService> ref = context->GetServiceReference<IDictionaryService>();
IDictionaryService* dict = context->GetService(ref);
{% endhighlight %}

There will be no need for dynamic casts anymore (which has been hidden in the templated
GetService method.

Another planned feature for version 2 is the implementation of *Servivce Scopes* (RFC 195)
as specified in the [OSGi Early Draft 2013.03](http://www.osgi.org/Download/File?url=/download/osgi-early-draft-2013-03.pdf).
