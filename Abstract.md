----------

Abstract
===================
Creating loosely coupled software using concepts of monads, functional programming, aspect oriented programming and design driven developement.

Muraad Nofal - 2014




---------

Today even programs with small domains may have to meet lots of different non domain related requirements. 

The world we are living in is getting agile more and more. 
Scrum, Canban, Lean, ...
Variable requirements are rather the norm than an exception.
It must be easy to react on new and chaning requirements and modifications to the domain and the environment. 
Being able to add new features more cost efficient (easier, faster) can be a huge advantage over a competitor, and makes developers and customers happy :-)


Keywords are "seperation of concerns, loosely coupled, micro services, SOA, DDD"

Lots of different technologies may be used even for *small* projects. 
Persistence is need (Database, XML, JSON). Webservices may be used to add new features (facebook, sharing, drobox) or to gather informations that would be otherwise hard to get (weather, maps). Maybe the project itself is a web app and/or needs to provide some kind of api that can be consumed (externaly) via a conanical format (REST, SOAP, RPC...). 
A software must be responsive (multithreading). It may have different clients accessing at the same time. Synchronization or special architectures must  be taken into account. 

---------

To have a responsive user interface or to offer the possibility to update user data to one of the widely used cloud storages could make the difference users are preffering one software over the other. Even the software is only a small smartphone "notes" app.

--------

Additionally there may be different cross cutting requirements that are needed throughout the whole software. For example diagnostic releated things like logging and profiling. Another commong requirement is input validation or business rule validation.

---------

Some time these non domain/business related requirements may be much more comprehensive than the actual business logic that is needed to solve a given problem.

---------

All these requirements should be as much transparent as possible to the domain part of a project. 

--------

It is a good design to have different logical parts of software, a domain, an infrastructure (persistence, netwokring) and so on (Layered architecture, new Hexagonal (DDD), Onion, see also [Quasar 3.0][1]. *Blutgruppen* and A-, T-, and TI- Architecture)
If things are not seperated from the beginning on its the direct way to tightly coupled software that is hard to maintain and even harder to extend. (Seperation of concerns, single responsibility, repository pattern, factorys, inversion of control, dependency injection...). The domain that the software is trying to work on is getting cluttered more and more by technical stuff the domain part should not take care about.
Software erosion is starting. Maintaining (features, bugs) is becoming harder and harder. 
Small code changes may have unpredictable impacts. Huge refactoring may safe such a software. But once a *point of no return* is reached it may become to cost-intensive. 

----------

The domain once is stable enough to go in production may not change very frequent.
But the bridges between the domain and the environment may change in a more frequent way. For example different RPC (REST, SOAP, Xml-RPC, COBRA) and database/storage technologies may come and go. The domains *lifetime* [^quasar3_lifetime] is probably much longer or at least may differe from used technologies *lifetimes*.

[^quasar3_lifetime]:[Quasar3.0](http://www.de.capgemini.com/resource-file-access/resource/pdf/quasar3.0.pdf) * A.2.1 Developement of a framework* (Page 160) 


--------

At some point in a developlement all the independent components have to be brought and constructed together to make the software alive.
The domain entities have to be pushed to and retreived from some storage. The web api, or rpc library/part has to know about the domain to create external ports that can be consumed from the outside and so on.
A GUI may have to know the domain to make it usable and visible. 
Lots of these things are technical, some of them can be made generic and/or can be automated and transparent for the domain.(OR-Mapper, GUI generators for simple CRUD applications, web frameworks like ASP.NET or JavaEE).

----------

Mobile developement becomes more and more important (Mobile first).
Say a company has an existing java program that runs on linux, and want to have an android app now. 
Because you can use java on both platforms one can get good benefits out of code reuse. But some parts may be different, and some apis that seems normal and standard for a desktop environment are different and/or not availabe on other platforms. At least the ui part will be different.

To hava a good architecture/design and an isolated domain can increase the amount of code that can be reused on different platforms. 
Ideally the complete domain/business logic may be reused on all required platforms. 
In .NET environments for example there is the concept of a "portable class library", that means the library uses only parts of the runtime environment that are available on all possible runtimes. More exactly what environments should be supported can be further specified to meet the given requirements. 
Minimal available runtime environment (standard libary) is a common language between components (of the same prog. language). Usage without mediator components possible.
(Quasar Blutgruppe 0 Software).

----------

To isolate the domain model/part of a software form the infrastructure in OO-lanugages most time new interfaces are introduces by the domain. For example an IRepository (repository pattern) to make the storage and retreival of domain entities transparent. 

During runtime a concrete IRepository implementation can be injected (Inversin of control, dependecy injection) when an object is created.
Another example i have seen quite often is an ISerialization interface. Most times it will only declare two methods to serialize and deserialize an object. 
To serialize an object is a requirement that is needed in lots of projects these days.

There may be lots of different serialization librarys/possibilites out there to fit the given needs. The api they offer will be much bigger than what one actually need. 
The ISerialization interface will also reduce the complexity of a concrete serialization apis (information hiding).
To use a concrete api a mediator component/library is introduces that is implementing the ISerialization domain interface using a concrete serialization api. Maybe this mediator is just a small wrapper class that is delegating method calls. Additionally it may setup the conrecte serializer to meet the domain requirements. The mediator component has to know the domain component and the concret serialization api(s) to fullfill its job.

Later one needs an executable of the program. The exec. has to know every component, the domain (ISerializer), the serializer mediator component (JSONSerializer class) and the concrete serializatin api component. It can inject the JSONSerializer class to the domain where ISerializer is needed. 

This is good and everything but in traditional object oriented imperative programing the serializer mediator component is needed to mediate between the domain language and the concrete serializer component even if the JSONSerialier class may be very small. 

The executable component that knows all other components and fixes everything together may be relativelly small too, if inversion of control and dependency injection frameworks and or pluginable architectures are used. 

Functional programming concepts can reduce the need for a mediator component between the domain and infrastructure components.  Most time the *Serialize* function will simply take an instance (maybe generic or base type), maybe its returning a boolean to indicate success or failure. The *Deserialize* function may simply take a Stream object an will return a concrete instance.
The point is the functions itself may take only standard runtime objects/types as arguments. These can be understood from every component without the need for a mediator component that is translating.

In programming lanugates where functions (lambdas) are first class objects like in .NET they exist on all possible platforms/environments. If the runtime is not injecting an ISerialization but only a *Serialize* function there is no need for a mediator component.

The executable component that is fixing everything together (bootstrapping) could take the mediators job using lambdas to create needed *Serialize* and *Deserialize*  functions and inject them where needed. 

---------

Like DTO´s are used to propagate structure/data to another component, first class function objects and lambdas can be seen as a common languague in OO-imperative languages to propagate functionality.

----------

Concepts like Aspect-, Subject- and Role based programming can further help to seperate concerns and to help to concentrate on the domain.

All three can be explained using monads, they add functionality /structure to existing types and their instances without chaning them. 
An apsect like logging can be applied to all methods of class without chaning the behaviour of the class itself. 
Other parts of the program may react different on an entity depending on what subject it came from or what role the the entity is currently representing. For example a Customer can be a *GoldCustomer* or a *NormalCustomer*. Maybe the role itself is adding new functionality to the underlying entity itself. This can be modelled using inheritance/subtyping, but i think this could be modelled in more generic ways using concepts of monads. 

---------

Concepts of declarative programming and/or attribute based programming can be used to describe ---------- in an intuitive an clean way. Monads that wrap entitys can reflect themself during runtime and read out different kind of attributes. These attributes can contain validation and cross cutting stuff. (PostSharp) Based on given attributes behaviour/aspects can be injected with code generation or during runtime.
For example in Java/.Net web frameworks class methods that responde to web requets are simply annotated to indicate what HTTPMethod and URL they are responding too. So the domain controllers can be programmed without thinking about HOW.

-----------

A network of generic Data flow / Stream classes is spanned during runtime. 
Components are simply subscribing for *Abilities* they need and/or are publishing *Abilities*. 
Concepts of message passing is used. *Abilities* (Messages) can be everything the given language is supporting (value objects, classes, functions...). So they can propagate data and/or functionality. Annotations (Java *Annotations*, C# *Attributes*) could be used as a lightweight common language for all components. Does the domain really needs an IRepository ? Maybe its enough to send a Message containing the entity together with an action that has to be done (save, delete...). If a common language (DDD, http://martinfowler.com/bliki/UbiquitousLanguage.html, Quasar Blutgruppe 0 Software, "Bestimmt von garnichts" außer minimal verfügbare standard umgebung) could be find that is spoken by all components everyone could just annotate itself with the kind of *Abilities* they offer and what *Abilities* they need to operate properly. A runtime could then *automagically* build up every needed dependencies. Further more even the data flow of a program itself (most time method calls) could be "programmed" declarative and build up during runtime when an object is instantiated. 
Examples could be (

Property, filed, Method validation. (used in ASP.NET web framework)
Result validation. 
Logging of changes errors
Synchronization
Error handling

These are all static, eg. they dont realize lots of dynamic. 
If *Data flow* annotations are introduced lots of different things are possible
in a generic way.

"Every time this method is called send its result value as message..."
"Send the result of this method to some storage..."
"Call this method every 50 ms.."
"I need a function that takes X and returns Y..."
" i offer a function that takes an X and returns an Y..."
"Send the result of this method via HTTP (REST) to XY..." 
"When XY happens do ..."
"When this field is changing notify/do XY..." 
" Every time X is received use function A to transform X to Y and send Y as message..."

If message (*Ability*) passing is used all the way, its transparent for a domain to operate local or remote the runtime can take care of it. 

Message passing can make validation, cross cutting aspects and multithreading easier and transparent. 
Message passing can decouple these things from concrete classes, and even from the caller (sender) itself.
Normally in interpreted lanugage like Java and .Net aspects are (most times) applied to class methods via *Il-Weaving* or *code emtting*. The first is adding code (method interception) during the project build, the later is emitting new code/types during runtime. 
Are dynamic types supported by the language, they can also be used to proxy other types to add new apsects/features. 
If method calls are decoupled via *implicit invocations (message passing)* apsects can be applied more easier and in a decoupled way. 
The aspects can simply be applied to the *Abilities (functions, class instances)* that are transported via some network (message passing, event driven,...) decoupled from the source and target instances.

------------

OO-Patterns can be made easier using functions as first class objects.
For example the *Publisher/Subscriber* pattern is normally implemented using two interfaces *IPublisher* and *ISubscriber*. At least one of them has to know the other interface.
*IPublisher<T>* has a function *Subscribe* or *ISubscriber<T>* can have a function *SubscribeAtPublisher*. The *IPublisher* is calling each registered *ISubscriber´s* *OnUpdate* function when the publisher is receiving changes. Again most times both interfaces only declare one or two functions.
The *IPublisher* only has to know one function. And a *Subscriber* does not has to know that he is an *ISubscriber*. There just needs to be someone how is giving a *Subscriber* function to the *IPublisher* that is fitting the *IPublisher´s* Type it is working with.
See Reactive Extensions available for different languages and platforms.

--------------------------------
Schichtenmodell nach Quasar http://www.vsek.org/servlet/is/26886/?print=true
http://st.inf.tu-dresden.de/Lehre/WS06-07/st1/Vorlesungen/21b-quasar.pdf
[1]:http://www.de.capgemini.com/resource-file-access/resource/pdf/quasar3.0.pdf "Quasar 3.0"
http://www.qaware.de/download.php?artid=%7B972d1c79-ea13-815e-0e57-ece33fbead46%7D  (erklärt Blutgruppen kurz und gut)

Layered vs Onion

http://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html
http://www.methodsandtools.com/archive/onionsoftwarearchitecture.php
https://github.com/danielmarbach/Onion.Factory
http://de.slideshare.net/matthidinger/onion-architecture
http://alistair.cockburn.us/Hexagonal+architecture (Ports and adapters)

Domain driven developement 
http://www.methodsandtools.com/archive/archive.php?id=97
http://de.wikipedia.org/wiki/Domain-Driven_Design
http://de.slideshare.net/Tindr/domain-driven-design-with-onion?related=1
http://de.slideshare.net/XebiaFrance/ddd-bdx-io?next_slideshow=1









