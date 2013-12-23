---
layout: post
title: "C++ Micro Services 2.0.0 released"
category: Programming
tags: [micro services, c++]
published: true
---
{% include JB/setup %}

Today, the C++ Micro Services library version 2.0.0 has been released. You can get the
sources at the [download page](http://cppmicroservices.org/download.html) or directly
from the [GitHub releases page](https://github.com/saschazelzer/CppMicroServices/releases).

This release marks a significant milestone in the evolution of the C++ Micro Services API.
The most notable changes and new features are:

 * Service objects do not need to inherit from a common base class any more.
 * New templated service layer API leading to Improved compile time type checking.
 * Added module and service hooks as specified in the OSGi Core Release 5.
 * Implemented [RFC 195 Service Scopes](http://www.osgi.org/Download/File?url=/download/osgi-early-draft-2013-03.pdf).
 * Modules can retrieve a module specific file-system path for storing persistent data.

For a detailed description of the new features, please read the next section.

New features
------------

The following paragraphs demonstrate the usage of the main new features and changes in the 2.0.0 API.

### No service base class

Version 1.0.0 required service implementations to inherit from a common base class. This requirement
has been removed and services now just need to inherit from the interfaces agains which they will
be registered with the service registry:

{% highlight cpp %}
struct MyInterface
{
  virtual ~MyInterface();
  
  // API ...
};
US_DECLARE_SERVICE_INTERFACE(MyInterface, "com.mycompany.myinterface")

class MyService : public MyInterface
{
  // implementation ...
};
{% endhighlight %}

### Templated service layer API

The service layer API has been templated and classes like `ServiceReference` and `ServiceRegistration` are
now templates. This improves compile time type checking when working with (multiple) service interfaces.
Registering and retrieving services now looks like:

{% highlight cpp %}
us::ModuleContext* context = us::GetModuleContext();
MyInterface* service = new MyService();
us::ServiceRegistration<MyInterface> reg = context->RegisterService(service);

us::ServiceReference<MyInterface> ref = context->GetServiceReference<MyInterface>();
MyInterface* myInterface = context->GetService(ref);

// ...

reg.Unregister();
delete service;
{% endhighlight %}

### Module and Service Hooks

The OSGi service layer specifies module and service hooks, which offer clients an API to closely
interact with the service registry. See the [Service Hooks](http://cppmicroservices.org/doc_2_0/MicroServices_ServiceHooks.html) documentation for an overview or read the original OSGi Core specifications for an
in-depth description.

### Service Scopes

[RFC 195 Service Scopes](http://www.osgi.org/Download/File?url=/download/osgi-early-draft-2013-03.pdf)
has been implemented in the C++ Micro Services library. In version 1.0.0, services essentially had
two *scopes* - singleton and module scope - although they weren't named as such. Service objects were
either shared among all clients or module-specific service instances were provided by the service
registry if the service inherited the
[us::ServiceFactory](http://cppmicroservices.org/doc_2_0/classServiceFactory.html) class. The same
mechanism is still supported, but the additional *prototype* scope was added. Services can now support
three kind of scopes:

 * Singleton - One service instance for all clients
 * Module - One service instance per module (constructed via [us::ServiceFactory](http://cppmicroservices.org/doc_2_0/classServiceFactory.html))
 * Prototype (new) - As many instances as requested (constructed via [us::PrototypeServiceFactory](http://cppmicroservices.org/doc_2_0/structPrototypeServiceFactory.html))

The newly introduced service property [SERVICE_SOPE](http://cppmicroservices.org/doc_2_0/namespaceServiceConstants.html#a7745368b1cb8eafeafb0259822302b9b) allows clients to look up the supported scope of a ServiceReference object.

Here is an example how to provide and consume services with prototype scope:

{% highlight cpp %}
class MyServicePrototypeFactory : public us::PrototypeServiceFactory
{
public:

  us::InterfaceMap GetService(us::Module* /*caller*/, const us::ServiceRegistrationBase& /*reg*/)
  {
    MyService* service = new MyService();
    return us::MakeInterfaceMap<MyInterface>(service);
  }

  void UngetService(us::Module* /*caller*/, const us::ServiceRegistrationBase& /*reg*/, const us::InterfaceMap& service)
  {
    delete us::ExtractInterface<MyInterface>(service);
  }
};

// Register the prototype factory
us::ModuleContext* context = us::GetModuleContext();
us::ServiceFactory* myPrototypeFactory = new MyServicePrototypeFactory();
us::ServiceRegistration<MyInterface> reg = context->RegisterService<MyInterface>(myPrototypeFactory);

// Get new service instances based on the prototype
us::ServiceReference<MyInterface> ref = context->GetServiceReference<MyInterface>();
if (ref.GetProperty(us::ServiceConstants::SERVICE_SCOPE()).ToString() != us::ServiceConstants::SCOPE_PROTOTYPE())
{
  // Not a prototype scope service. Keep going or bail out ...
}

us::ServiceObjects<MyInterface> svcObjects = context->GetServiceObjects(ref);
MyInterface* myServiceInstance = svcObjects.GetService();
// Do something ...

// The us::ServiceObjects class "ungets" all instances automatically on destruction
// and all pointers to MyInterface instances become invalid. 

reg.Unregister();
delete myPrototypeFactory;
{% endhighlight %}

Breaking changes
----------------

Version 2.0.0 introduces a couple of [https://github.com/saschazelzer/CppMicroServices/wiki/API-changes-in-version-2.0.0](breaking API changes)
compared to version 1.0.0. Please ask for support in the [CppMicroservices Forum](http://forum.cppmicroservices.org/)
if you have trouble with migrating to version 2.0.0.


What's next?
------------

The C++ Micro Services library API will continue to evolve as new OSGi features are specified and fit into
the scope of this library. However, the evolution will probably be at a much slower pace when compared
to the changes between version 1.0.0 and 2.0.0. Instead, the future development focus will be on
implementing OSGi Core and Compendium specifications in separate repositories,  based on the C++ Micro
Services library. Most likely, the Config Admin and Declarative Services specifications will be
worked on first.
