Chapter 3: Architecture
=======================

The previous chapter established a substantial set of requirements for
network design—a computer network must provide general,
cost-effective, fair, secure, and robust connectivity among a large
number of computers. As if this weren’t enough, networks do not remain
fixed at any single point in time but must evolve to accommodate
changes in both the underlying technologies upon which they are based
as well as changes in the demands placed on them by application
programs. Furthermore, networks must be manageable by humans of
varying levels of skill. Designing a network to meet these
requirements is no small task.

To help deal with this complexity, network designers have developed
general blueprints—usually called *network architectures*—that guide
the design and implementation of networks. This section defines more
carefully what we mean by a network architecture by introducing the
central ideas that are common to all network architectures. It also
introduces two of the most historically important architectures—the
OSI (or 7-layer) architecture and the Internet architecture—as well as
the defacto cloud architecture that is in wide-spread use today.

3.1 Layering
------------

Abstraction—the hiding of implementation details behind a well-defined
interface—is the fundamental tool used by system designers to manage
complexity. The idea of an abstraction is to define a model that can
capture some important aspect of the system, encapsulate this model in
an object that provides an interface that can be manipulated by other
components of the system, and hide the details of how the object is
implemented from the users of the object. The challenge is to identify
abstractions that simultaneously provide a service that proves useful
in a large number of situations and that can be efficiently
implemented in the underlying system. This is exactly what we were
doing when we introduced the idea of a channel in the previous
section: we were providing an abstraction for applications that hides
the complexity of the network from application writers.

.. _fig-layers1:
.. figure:: figures/f01-08-9780123850591.png
   :width: 300px
   :align: center

   Example of a layered network system.

Abstractions naturally lead to layering, especially in network
systems.  The general idea is that you start with the services offered
by the underlying hardware and then add a sequence of layers, each
providing a higher (more abstract) level of service. The services
provided at the high layers are implemented in terms of the services
provided by the low layers. Drawing on the discussion of requirements
given in the previous section, for example, we might imagine a simple
network as having two layers of abstraction sandwiched between the
application program and the underlying hardware, as illustrated in
:numref:`Figure %s <fig-layers1>`. The layer immediately above the
hardware in this case might provide host-to-host connectivity,
abstracting away the fact that there may be an arbitrarily complex
network topology between any two hosts. The next layer up builds on
the available host-to-host communication service and provides support
for process-to-process channels, abstracting away the fact that the
network occasionally loses messages, for example.

Layering provides two useful features. First, it decomposes the
problem of building a network into more manageable components. Rather
than implementing a monolithic piece of software that does everything
you will ever want, you can implement several layers, each of which
solves one part of the problem. Second, it provides a more modular
design. If you decide that you want to add some new service, you may
only need to modify the functionality at one layer, reusing the
functions provided at all the other layers.

Thinking of a system as a linear sequence of layers is an
oversimplification, however. Many times there are multiple abstractions
provided at any given level of the system, each providing a different
service to the higher layers but building on the same low-level
abstractions. To see this, consider the two types of channels discussed
in the previous section. One provides a request/reply service and one
supports a message stream service. These two channels might be
alternative offerings at some level of a multilevel networking system,
as illustrated in :numref:`Figure %s <fig-layers2>`.

.. _fig-layers2:
.. figure:: figures/f01-09-9780123850591.png
   :width: 300px
   :align: center
   
   Layered system with alternative abstractions available
   at a given layer.

3.1.1 Protocols
~~~~~~~~~~~~~~~

Using this discussion of layering as a foundation, we are now ready to
discuss the architecture of a network more precisely. For starters, the
abstract objects that make up the layers of a network system are called
*protocols*. That is, a protocol provides a communication service that
higher-level objects (such as application processes, or perhaps
higher-level protocols) use to exchange messages. For example, we could
imagine a network that supports a request/reply protocol and a message
stream protocol, corresponding to the request/reply and message stream
channels discussed above.

Each protocol defines two different interfaces. First, it defines a
*service interface* to the other objects on the same computer that want
to use its communication services. This service interface defines the
operations that local objects can perform on the protocol. For example,
a request/reply protocol would support operations by which an
application can send and receive messages. An implementation of the HTTP
protocol could support an operation to fetch a page of hypertext from a
remote server. An application such as a web browser would invoke such an
operation whenever the browser needs to obtain a new page (e.g., when
the user clicks on a link in the currently displayed page).

Second, a protocol defines a *peer interface* to its counterpart (peer)
on another machine. This second interface defines the form and meaning
of messages exchanged between protocol peers to implement the
communication service. This would determine the way in which a
request/reply protocol on one machine communicates with its peer on
another machine. In the case of HTTP, for example, the protocol
specification defines in detail how a *GET* command is formatted, what
arguments can be used with the command, and how a web server should
respond when it receives such a command.

To summarize, a protocol defines a communication service that it exports
locally (the service interface), along with a set of rules governing the
messages that the protocol exchanges with its peer(s) to implement this
service (the peer interface). This situation is illustrated in :numref:`Figure
%s <fig-interfaces>`.

.. _fig-interfaces:
.. figure:: figures/f01-10-9780123850591.png
   :width: 500px
   :align: center
   
   Service interfaces and peer interfaces.

Except at the hardware level, where peers directly communicate with each
other over a physical medium, peer-to-peer communication is
indirect—each protocol communicates with its peer by passing messages to
some lower-level protocol, which in turn delivers the message to *its*
peer. In addition, there are potentially more than one protocol at any
given level, each providing a different communication service. We
therefore represent the suite of protocols that make up a network system
with a *protocol graph*. The nodes of the graph correspond to protocols,
and the edges represent a *depends on* relation. For example, :numref:`Figure
%s <fig-protgraph>` illustrates a protocol graph for the hypothetical
layered system we have been discussing—protocols RRP (Request/Reply
Protocol) and MSP (Message Stream Protocol) implement two different
types of process-to-process channels, and both depend on the
Host-to-Host Protocol (HHP) which provides a host-to-host connectivity
service.

.. _fig-protgraph:
.. figure:: figures/f01-11-9780123850591.png
   :width: 500px
   :align: center
   
   Example of a protocol graph.

In this example, suppose that the file access program on host 1 wants to
send a message to its peer on host 2 using the communication service
offered by RRP. In this case, the file application asks RRP to send the
message on its behalf. To communicate with its peer, RRP invokes the
services of HHP, which in turn transmits the message to its peer on the
other machine. Once the message has arrived at the instance of HHP on
host 2, HHP passes the message up to RRP, which in turn delivers the
message to the file application. In this particular case, the
application is said to employ the services of the *protocol stack*
RRP/HHP.

Note that the term *protocol* is used in two different ways. Sometimes
it refers to the abstract interfaces—that is, the operations defined by
the service interface and the form and meaning of messages exchanged
between peers, and sometimes it refers to the module that actually
implements these two interfaces. To distinguish between the interfaces
and the module that implements these interfaces, we generally refer to
the former as a *protocol specification*. Specifications are generally
expressed using a combination of prose, pseudocode, state transition
diagrams, pictures of packet formats, and other abstract notations. It
should be the case that a given protocol can be implemented in different
ways by different programmers, as long as each adheres to the
specification. The challenge is ensuring that two different
implementations of the same specification can successfully exchange
messages. Two or more protocol modules that do accurately implement a
protocol specification are said to *interoperate* with each other.

We can imagine many different protocols and protocol graphs that satisfy
the communication requirements of a collection of applications.
Fortunately, there exist standardization bodies, such as the Internet
Engineering Task Force (IETF) and the International Standards
Organization (ISO), that establish policies for a particular protocol
graph. We call the set of rules governing the form and content of a
protocol graph a *network architecture*. Although beyond the scope of
this book, standardization bodies have established well-defined
procedures for introducing, validating, and finally approving protocols
in their respective architectures. We briefly describe the architectures
defined by the IETF and ISO shortly, but first there are two additional
things we need to explain about the mechanics of protocol layering.

3.1.2 Encapsulation
~~~~~~~~~~~~~~~~~~~

Consider what happens in when one of the application programs sends a
message to its peer by passing the message to RRP. From RRP’s
perspective, the message it is given by the application is an
uninterpreted string of bytes. RRP does not care that these bytes
represent an array of integers, an email message, a digital image, or
whatever; it is simply charged with sending them to its peer. However,
RRP must communicate control information to its peer, instructing it how
to handle the message when it is received. RRP does this by attaching a
*header* to the message. Generally speaking, a header is a small data
structure—from a few bytes to a few dozen bytes—that is used among peers
to communicate with each other. As the name suggests, headers are
usually attached to the front of a message. In some cases, however, this
peer-to-peer control information is sent at the end of the message, in
which case it is called a *trailer*. The exact format for the header
attached by RRP is defined by its protocol specification. The rest of
the message—that is, the data being transmitted on behalf of the
application—is called the message’s *body* or *payload*. We say that the
application’s data is *encapsulated* in the new message created by RRP.

.. _fig-encapsulation:
.. figure:: figures/f01-12-9780123850591.png
   :width: 500px
   :align: center
   
   High-level messages are encapsulated inside of low-level messages.

This process of encapsulation is then repeated at each level of the
protocol graph; for example, HHP encapsulates RRP’s message by
attaching a header of its own. If we now assume that HHP sends the
message to its peer over some network, then when the message arrives
at the destination host, it is processed in the opposite order: HHP
first interprets the HHP header at the front of the message (i.e.,
takes whatever action is appropriate given the contents of the header)
and passes the body of the message (but not the HHP header) up to RRP,
which takes whatever action is indicated by the RRP header that its
peer attached and passes the body of the message (but not the RRP
header) up to the application program. The message passed up from RRP
to the application on host 2 is exactly the same message as the
application passed down to RRP on host 1; the application does not see
any of the headers that have been attached to it to implement the
lower-level communication services. This whole process is illustrated
in :numref:`Figure %s <fig-encapsulation>`. Note that in this example,
nodes in the network (e.g., switches and routers) may inspect the HHP
header at the front of the message.

Note that when we say a low-level protocol does not interpret the
message it is given by some high-level protocol, we mean that it does
not know how to extract any meaning from the data contained in the
message. It is sometimes the case, however, that the low-level protocol
applies some simple transformation to the data it is given, such as to
compress or encrypt it. In this case, the protocol is transforming the
entire body of the message, including both the original application’s
data and all the headers attached to that data by higher-level
protocols.

3.2 Virtualization
------------------

Virtualization as a special kind of "recursive" layering...

For almost as long as there have been packet-switched networks, there
have been ideas about how to virtualize them, starting with virtual
circuits. But what exactly does it mean to virtualize a network?

Virtual memory is a helpful example. Virtual memory creates an
abstraction of a large and private pool of memory, even though the
underlying physical memory may be shared by many applications and
considerably smaller that the apparent pool of virtual memory. This
abstraction enables programmers to operate under the illusion that there
is plenty of memory and that no-one else is using it, while under the
covers the memory management system takes care of things like mapping
the virtual memory to physical resources and avoiding conflict between
users.

Similarly, server virtualization presents the abstraction of a virtual
machine (VM), which has all the features of a physical machine. Again,
there may be many VMs supported on a single physical server, and the
operating system and users on the virtual machine are happily unaware
that the VM is being mapped onto physical resources.

A key point is the virtualization of computing resources preserves the
abstractions and interfaces that existed before they were virtualized.
This is important because it means that users of those abstractions
don’t need to change—they see a faithful reproduction of the resource
being virtualized. Virtualization also means that the different users
(sometimes called *tenants*) cannot interfere with each other. So what
happens when we try to virtualize a network?

VPNs, as described in **Section 3.3**,
were one early success for virtual networking. They allowed carriers
to present corporate customers with the illusion that they had their
own private network, even though in reality they were sharing
underlying links and switches with many other users. VPNs, however,
only virtualize a few resources, notably addressing and routing
tables. Network virtualization as commonly understood today goes
further, virtualizing every aspect of networking. That means that a
virtual network should support all the basic abstractions of a
physical network. In this sense, they are analogous to the virtual
machine, with its support of all the resources of a server: CPU,
storage, I/O, and so on.

To this end, VLANS, as described in **Section 3.2**, are how we
typically virtualize an L2 network. VLANs proved to be quite useful to
enterprises that wanted to isolate different internal groups (e.g.,
departments, labs), giving each of them the appearance of having their
own private LAN. VLANs were also seen as a promising way to virtualize
L2 networks in cloud datacenters, making it possible to give each
tenant their own L2 network so as to isolate their traffic from the
traffic of all other tenants. But there was a problem: the 4096
possible VLANs was not sufficient to account for all the tenants that
a cloud might host, and to complicate matters, in a cloud the network
needs to connect *virtual machines* rather than the physical machines
that those VMs run on.

To address this problem, another standard called *Virtual Extensible
LAN* (VXLAN) was introduced. Unlike the original approach, which
effectively encapsulated a virtualized ethernet frame inside another
ethernet frame, VXLAN encapsulates a virtual ethernet frame inside a UDP
packet. This means a VXLAN-based virtual network (which is often
referred to as an *overlay network*) runs on top of an IP-based network,
which in turn runs on an underlying ethernet (or perhaps in just one
VLAN of the underlying ethernet). VXLAN also makes it possible for one
cloud tenant to have multiple VLANs of their own, which allows them to
segregate their own internal traffic. This means it is ultimately
possible to have a VLAN encapsulated in a VXLAN overlay encapsulated in
a VLAN.

The powerful thing about virtualization is that when done right, it
should be possible to nest one virtualized resource inside another
virtualized resource, since after all, a virtual resource should behave
just like a physical resources and we know how to virtualize physical
resources! Said another way, being able to virtualize a virtual resource
is the best proof that you have done a good job of virtualizing the
original physical resource. To re-purpose the mythology of the World
Turtle: It’s virtual networks all the way down.

.. _fig-vxlan:
.. figure:: figures/impl-Slide8.png
   :width: 500px
   :align: center

   VXLAN Header encapsulated in a UDP/IP packet. 
   header.

The actual VXLAN header is simple, as shown in :numref:`Figure %s
<fig-vxlan>`. It includes a 24-bit *Virtual Network Id* (VNI), plus
some flag and reserved bits. It also implies a particular setting of
the UDP source and destination port fields (see **Section 5.1**), with
the destination port 4789 officially reserved for VXLANs. Figuring out
how to uniquely identify virtual LANs (VLAN tags) and virtual networks
(VXLAN VIDs) is the easy part. This is because encapsulation is the
fundamental cornerstone of virtualization; all you need to add is an
identifier that tells you which of many possible users this
encapsulated packet belongs to.

The hard part is grappling with the idea of virtual networks being
nested (encapsulated) inside virtual networks, which is networking’s
version of recursion. The other challenge is understanding how to
automate the creation, management, migration, and deletion of virtual
networks, and on this front there is still a lot of room for
improvement. Mastering this challenge will be at the heart of networking
in the next decade, and while some of this work will undoubtedly happen
in proprietary settings, there are open source network virtualization
platforms (e.g., the Linux Foundation’s *Tungsten Fabric* project)
leading the way.

3.4 Reference Architectures
---------------------------

We conclude this chapter by looking at three example network
architectures. The first two are historically important, and were the
result of explicit efforts to define a coherent architecture. The
third is our characterization of the *de facto* architecture in use
today. It evolved out of the Internet architecture (and some would
claim it as the updated version of the Internet architecture), but in
truth, no one has explicitly defined it, santioned it, or even named
it. It is just the natural consequence of a system evolving over time.

3.4.1 OSI Model
~~~~~~~~~~~~~~~~~~~~

The ISO was one of the first organizations to formally define a common
way to connect computers. Their architecture, called the *Open Systems
Interconnection* (OSI) architecture and illustrated in :numref:`Figure
%s <fig-osi>`, defines a partitioning of network functionality into
seven layers, where one or more protocols implement the functionality
assigned to a given layer. In this sense, the schematic given in is
not a protocol graph, *per se*, but rather a *reference model* for a
protocol graph. It is often referred to as the 7-layer model.  While
there is no OSI-based network running today, the terminology it
defined is still widely used, so it is still worth a cursory look.

.. _fig-osi:
.. figure:: figures/f01-13-9780123850591.png
   :width: 600px
   :align: center
   
   The OSI 7-layer model.

Starting at the bottom and working up, the *physical* layer handles the
transmission of raw bits over a communications link. The *data link*
layer then collects a stream of bits into a larger aggregate called a
*frame*. Network adaptors, along with device drivers running in the
node’s operating system, typically implement the data link level. This
means that frames, not raw bits, are actually delivered to hosts. The
*network* layer handles routing among nodes within a packet-switched
network. At this layer, the unit of data exchanged among nodes is
typically called a *packet* rather than a frame, although they are
fundamentally the same thing. The lower three layers are implemented on
all network nodes, including switches within the network and hosts
connected to the exterior of the network. The *transport* layer then
implements what we have up to this point been calling a
*process-to-process channel*. Here, the unit of data exchanged is
commonly called a *message* rather than a packet or a frame. The
transport layer and higher layers typically run only on the end hosts
and not on the intermediate switches or routers.

Skipping ahead to the top (seventh) layer and working our way back
down, we find the *application* layer. Application layer protocols
include things like the Hypertext Transfer Protocol (HTTP), which is
the basis of the World Wide Web and is what enables web browsers to
request pages from web servers. Below that, the *presentation* layer
is concerned with the format of data exchanged between peers—for
example, whether an integer is 16, 32, or 64 bits long, whether the
most significant byte is transmitted first or last, or how a video
stream is formatted. Finally, the *session* layer provides a name
space that is used to tie together the potentially different transport
streams that are part of a single application. For example, it might
manage an audio stream and a video stream that are being combined in a
teleconferencing application.

3.4.2 Internet Architecture
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The Internet architecture, which is also sometimes called the TCP/IP
architecture after its two main protocols, is depicted in
:numref:`Figure %s <fig-internet1>`. An alternative representation is
given in :numref:`Figure %s <fig-internet2>`. The Internet
architecture evolved out of experiences with an earlier
packet-switched network called the ARPANET. Both the Internet and the
ARPANET were funded by the Advanced Research Projects Agency (ARPA),
one of the research and development funding agencies of the
U.S. Department of Defense. The Internet and ARPANET were around
before the OSI architecture, and the experience gained from building
them was a major influence on the OSI reference model.
	
.. _fig-internet1:
.. figure:: figures/f01-14-9780123850591.png
   :width: 300px
   :align: center
   
   Internet protocol graph.

.. _fig-internet2:
.. figure:: figures/f01-15-9780123850591.png
   :width: 250px
   :align: center
   
   Alternative view of the Internet architecture. The "subnetwork" layer
   was historically referred to as the “network” layer and is now often
   referred to as “Layer 2” (influenced by the OSI model). 

While the 7-layer OSI model can, with some imagination, be applied to
the Internet, a simpler stack is often used instead. At the lowest
level is a wide variety of network protocols, denoted NET\ :sub:`1`,
NET\ :sub:`2`, and so on. In practice, these protocols are implemented
by a combination of hardware (e.g., a network adaptor) and software
(e.g., a network device driver). For example, you might find Ethernet
or wireless protocols (such as the 802.11 Wi-Fi standards) at this
layer. (These protocols in turn may actually involve several
sublayers, but the Internet architecture does not presume anything
about them.) The next layer consists of a single protocol—the
*Internet Protocol* (IP). This is the protocol that supports the
interconnection of multiple networking technologies into a single,
logical internetwork. The layer on top of IP contains two main
protocols—the *Transmission Control Protocol* (TCP) and the *User
Datagram Protocol* (UDP). TCP and UDP provide alternative logical
channels to application programs: TCP provides a reliable byte-stream
channel, and UDP provides an unreliable datagram delivery channel
(*datagram* may be thought of as a synonym for message). In the
language of the Internet, TCP and UDP are sometimes called
*end-to-end* protocols, although it is equally correct to refer to
them as *transport* protocols.

Running above the transport layer is a range of application protocols,
such as HTTP, FTP, Telnet (remote login), and the Simple Mail Transfer
Protocol (SMTP), that enable the interoperation of popular applications.
To understand the difference between an application layer protocol and
an application, think of all the different World Wide Web browsers that
are or have been available (e.g., Firefox, Chrome, Safari, Netscape,
Mosaic, Internet Explorer). There is a similarly large number of
different implementations of web servers. The reason that you can use
any one of these application programs to access a particular site on the
Web is that they all conform to the same application layer protocol:
HTTP. Confusingly, the same term sometimes applies to both an
application and the application layer protocol that it uses (e.g., FTP
is often used as the name of an application that implements the FTP
protocol).

Most people who work actively in the networking field are familiar with
both the Internet architecture and the 7-layer OSI architecture, and
there is general agreement on how the layers map between architectures.
The Internet’s application layer is considered to be at layer 7, its
transport layer is layer 4, the IP (internetworking or just network)
layer is layer 3, and the link or subnet layer below IP is layer 2.

.. sidebar:: IETF and Standardization
	     
   Although we call it the "Internet architecture" rather than the
   "IETF architecture," it's fair to say that the IETF is the primary
   standardization body responsible for its definition, as well as the
   specification of many of its protocols, such as TCP, UDP, IP,
   DNS, and BGP. But the Internet architecture also embraces many
   protocols defined by other organizations, including IEEE's
   802.11 ethernet and Wi-Fi standards, W3C's HTTP/HTML web
   specifications, 3GPP's 4G and 5G cellular networks standards,
   and ITU-T's H.232 video encoding standards, to name a few.

   In addition to defining architectures and specifying protocols,
   there are yet other organizations that support the larger goal of
   interoperability. One example is the IANA (Internet Assigned
   Numbers Authority), which as its name impies, is responsible for
   handing out the unique identifiers needed to make the protocols
   work. IANA, in turn, is a department within the ICANN (Internt
   Corporation for Assigned Names and Numbers), a non-profit
   organization that's responsible for the overall stewardship of the
   Internet.
   
The Internet architecture has three features that are worth
highlighting. First, as best illustrated by :numref:`Figure %s
<fig-internet2>`, the Internet architecture does not imply strict
layering. The application is free to bypass the defined transport
layers and to directly use IP or one of the underlying networks. In
fact, programmers are free to define new channel abstractions or
applications that run on top of any of the existing protocols.

Second, if you look closely at the protocol graph in :numref:`Figure
%s <fig-internet1>`, you will notice an hourglass shape—wide at the top,
narrow in the middle, and wide at the bottom. This shape actually
reflects the central philosophy of the architecture. That is, IP serves
as the focal point for the architecture—it defines a common method for
exchanging packets among a wide collection of networks. Above IP there
can be arbitrarily many transport protocols, each offering a different
channel abstraction to application programs. Thus, the issue of
delivering messages from host to host is completely separated from the
issue of providing a useful process-to-process communication service.
Below IP, the architecture allows for arbitrarily many different network
technologies, ranging from Ethernet to wireless to single point-to-point
links.

A final attribute of the Internet architecture (or more accurately, of
the IETF culture) is that in order for a new protocol to be officially
included in the architecture, there must be both a protocol
specification and at least one (and preferably two) representative
implementations of the specification. The existence of working
implementations is required for standards to be adopted by the
IETF. This cultural assumption of the design community helps to ensure
that the architecture’s protocols can be efficiently implemented.
Perhaps the value the Internet culture places on working software is
best exemplified by a quote on T-shirts commonly worn at IETF
meetings:

   *We reject kings, presidents, and voting. We believe in rough
   consensus and running code.* **(David Clark)**

.. _key-hourglass:
.. admonition:: Key Takeaway

   Of these three attributes of the Internet architecture, the hourglass
   design philosophy is important enough to bear repeating. The
   hourglass’s narrow waist represents a minimal and carefully chosen
   set of global capabilities that allows both higher-level applications
   and lower-level communication technologies to coexist, share
   capabilities, and evolve rapidly. The narrow-waisted model is
   critical to the Internet’s ability to adapt to new user
   demands and changing technologies.

3.4.3 Cloud Architecture
~~~~~~~~~~~~~~~~~~~~~~~~~

The Internet has been described as having a *narrow waist* architecture,
with one universal protocol in the middle (IP), widening to support many
transport and application protocols above it (e.g., TCP, UDP, RTP,
SunRPC, DCE-RPC, gRPC, SMTP, HTTP, SNMP) and able to run on top of many
network technologies below (e.g., Ethernet, PPP, WiFi, SONET, ATM). This
general structure has been a key to the Internet becoming ubiquitous: by
keeping the IP layer that everyone has to agree to minimal, a thousand
flowers were allowed to bloom both above and below. This is now a widely
understood strategy for any platform trying to achieve universal
adoption.

But something else has happened over the last 30 years. By not
addressing all the issues the Internet would eventually face as it grew
(e.g., security, congestion, mobility, real-time responsiveness, and so
on) it became necessary to introduce a series of additional features
into the Internet architecture. Having IP’s universal addresses and
best-effort service model was a necessary condition for adoption, but
not a sufficient foundation for all the applications people wanted to
build.

We’re yet to see some of these solutions—future chapters will describe
how the Internet manages congestion (**Chapter 6**, provides security
(**Chapter 8**), and supports real-time multimedia applications
(**Chapters 7** and **Chapter 9**)—but it is informative to take this
opportunity to reconcile the value of a universal narrow waist with
the evolution that inevitably happens in any long-lived system: the
“fixed point” around which the rest of the architecture evolves has
moved to a new spot in the software stack. In short, HTTP has become
the new narrow waist; the one shared/assumed piece of the global
infrastructure that makes everything else possible. This didn’t happen
overnight or by proclamation, although some did anticipate it would
happen. The narrow waist drifted slowly up the protocol stack as a
consequence of an evolution (to mix geoscience and biological
metaphors).
 
.. _fig-waist:
.. figure:: figures/rpc-Slide3.png
   :width: 500px
   :align: center

   HTTP (plus TLS, TCP, and IP) forming the narrow
   waist of today's Internet architecture.

Putting the narrow waist label purely on HTTP is an over simplification.
It’s actually a team effort, with the HTTP/TLS/TCP/IP combination now
serving as the Internet’s common platform.

-  HTTP provides global object identifiers (URIs) and a simple GET/PUT
   interface.

-  TLS provides end-to-end communication security.

-  TCP provides connection management, reliable transmission, and
   congestion control.

-  IP provides global host addresses and a network abstraction layer.

In other words, even though you are free to invent your own congestion
control algorithm, TCP solves this problem quite well, so it makes sense
to reuse that solution. Similarly, even though you are free to invent
your own RPC protocol, HTTP provides a perfectly serviceable one (which
because it comes bundled with proven security, has the added feature of
not being blocked by enterprise firewalls), so again, it makes sense to
reuse it rather than reinvent the wheel.

Somewhat less obviously, HTTP also provides a good foundation for
dealing with mobility. If the resource you want to access has moved,
you can have HTTP return a *redirect response* that points the client
to a new location. Similarly, HTTP enables injecting *caching proxies*
between the client and server, making it possible to replicate popular
content in multiple locations and save clients the delay of going all
the way across the Internet to retrieve some piece of information.
Finally, HTTP has been used to deliver real-time multimedia, in an
approach known as *adaptive streaming*. 

.. admonition:: Broader Perspective

   To learn more about the centrality of HTTP, we recommend: `HTTP:
   An Evolvable Narrow Waist for the Future
   Internet <https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-5.pdf>`__,
   January 2012.
   
