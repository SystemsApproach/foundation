Chapter 2:  Requirements
===============================

We have established an ambitious goal for ourselves: to understand how
to build a computer network from the ground up. Our approach to
accomplishing this goal will be to start from first principles and then
ask the kinds of questions we would naturally ask if building an actual
network. The first question is what do we want from our network: *What
are our requirements?*

Any systems-oriented discussion of requirements starts with the
*-ities:* reliability, scalability, availability, scalability,
useability, security, manageability, and so on. These are all
requirements of the network we want to build, but it's not
particularly helpful to simply state the obvious. Our goal in this
chapter is frame the discussion of requirements to the particular
system we are trying to build. In doing so, we will touch on all the
-ities, but in a way that brings the most important factors into
sharper focus.

2.1 Generality
-------------------

Most people know the Internet through its applications: web browsing,
email, social media, streaming music or movies, videoconferencing,
instant messaging, and file-sharing, to name just a few. The most
important statement we can make about these applications is that they
know no bound. The Internet is a "universial communications platform"
that enables a wide-range of applications. Unlike networks that came
before it, the Internet was not purpose-built to support just one
application, such as making voice calls or watching TV.

We take it for granted today, but generality is the foremost
requirement for the network we are building. This is an easy claim to
make, but has deeper implications that are best understood by a closer
look at applications in use today. In practice, identifying a
representative set of applications you want to support—these are
sometimes call *use cases*—is a necessary first step for any system
you want to build. We have the luxury of 30+ years of hard-earned
experience building Internet applications, but for illustrative
purposes, let's suppose we decide we want to (minimally) support the
following three services: email, web surfing and video streaming.

.. sidebar:: Killer Apps

	We are approaching this discussion as a design exercise, but
	of course with 20/20 hindsight. Our history of predicting what
	will and won't be a popular application is not very
	impressive, but you have to start somewhere. In the early days
	of the Internet, there were three main apps (use cases).

	The main application justifying the investment in building the
	Internet was support for logging into remote computers, which
	were scarce at the time. Today, remote login—now typically
	done using SSH—is primarily the domain of network
	administrators who need to access a machine for diagnostic or
	operational purposes.

	Another early application was file transfer, which continues
	today, but looks much different than the original FTP. We now
	store our files in Dropbox or Google Drive, but that's just a
	better user interface. Under the covers, files of various
	sizes and content, organized into a hierarchical collection of
	folders, are moved between local machines (our laptops) and
	remote storage servers (the cloud).

	The third main application of the original Internet was email,
	which we briefly describe in this section. Noticably absent
	were any audio or video streaming applications. At the time,
	one limiting factor was that computers connected to the
	Internet were not powerful enough to encode/decode multimedia
	streams, nor did they have the necessary graphical displays.
	In many ways, it's been advances in computing and graphics
	that has spurred the development of new Internet apps. But
	dispite this blind spot, the Internet was designed with enough
	foresight to be able to evolve to support them.
	     

2.1.1 Email
~~~~~~~~~~~

Start with email... (a) name people, (b) rendez-vous/asynchronous delivery, (c)
arbitrary sized messages (may include attachements).

2.1.2 Web Browsing
~~~~~~~~~~~~~~~~~~

(a) uniquely name/identify objects, (b) arbitrary-sized, (c) arbitrary
    types, (d) get (and put) operations... REST? Relationship to
    Facebook and Phone Apps About history of hypertext and Bush.

The World Wide Web is the Internet application that catapulted the
Internet from a somewhat obscure tool used mostly by scientists and
engineers to the mainstream phenomenon that it is today. The Web itself
has become such a powerful platform that many people confuse it with the
Internet, and it's a bit of a stretch to say that the Web is a single
application.

In its basic form, the Web presents an intuitively simple interface.
Users view pages full of textual and graphical objects and click on
objects that they want to learn more about, and a corresponding new
page appears. We clearly need a way to name (identify) the objects we
want to download, and as most people are aware, today this identifier
is called a Uniform Resource Locator (URL), and they provide a way of
uniquely identifying all the possible objects that can be viewed from
your web browser. For example,

.. code-block:: html

   http://www.cs.princeton.edu/llp/index.html

is the URL for a page providing information about one of this book's
authors: the string ``http`` indicates that the Hypertext Transfer
Protocol (HTTP) should be used to download the page,
``www.cs.princeton.edu`` is the name of the machine that serves the
page, and ``/llp/index.html`` uniquely identifies Larrys home page at
this site.

Mabye generalize to web services... cloud services (perhaps hint at it
and revisit in Implementation section).

2.1.3 Audio/Video Streaming
~~~~~~~~~~~~~~~~~~~~~~~~~~~

A third widespread application class is the delivery of "streaming"
audio and video. Services such as video on demand and Internet radio
use this technology. While we frequently start at a website to
initiate a streaming session, the delivery of audio and video has some
important differences from fetching a simple web page of text and
images. For example, you often don't want to download an entire video
file—a process that might take a few minutes—before watching the first
scene. Streaming audio and video implies a more timely transfer of
messages from sender to receiver, and the receiver displays the video
or plays the audio pretty much as it arrives.

Note that the difference between streaming applications and the more
traditional delivery of text, graphics, and images is that humans
consume audio and video streams in a continuous manner, and
discontinuity—in the form of skipped sounds or stalled video—is not
acceptable. By contrast, a regular (non-streaming) page can be
delivered and read in bits and pieces. This difference affects how the
network supports these different classes of applications.

A subtly different application class is *real-time* audio and video.
These applications have considerably tighter timing constraints than
streaming applications. When using a voice-over-IP application such as
Skype or a videoconferencing application, the interactions among the
participants must be timely. When a person at one end gestures, then
that action must be displayed at the other end as quickly as possible.\ [#]_

.. [#] Not quite "as soon as possible"... Human factors research
       indicates 300 ms is a reasonable upper bound for how much
       round-trip delay can be tolerated in a telephone call before
       humans complain, and a 100-ms delay sounds very good.

When one person tries to interrupt another, the interrupted person needs
to hear that as soon as possible and decide whether to allow the
interruption or to keep talking over the interrupter. Too much delay in
this sort of environment makes the system unusable. Contrast this with
video on demand where, if it takes several seconds from the time the
user starts the video until the first image is displayed, the service is
still deemed satisfactory. Also, interactive applications usually entail
audio and/or video flows in both directions, while a streaming
application is most likely sending video or audio in only one direction.

.. _fig-vic:
.. figure:: figures/f01-01-9780123850591.png
   :width: 600px
   :align: center

   A multimedia application including videoconferencing.

Videoconferencing tools that run over the Internet have been around now
since the early 1990s but have achieved widespread use in the last few
years, with several commercial products on the market. An example of one
such system is shown in :numref:`Figure %s <fig-vic>`.  Just as
downloading a web page involves a bit more than meets the eye, so too
with video applications. Fitting the video content into a relatively
low bandwidth network, for example, or making sure that the video and
audio remain in sync and arrive in time for a good user experience are
all problems that network and protocol designers have to worry
about. We'll look at these and many other issues related to multimedia
applications later in the book.

Although they are just three examples, sending email, downloading web
pages, and participating in a videoconference demonstrate the
diversity of applications that can be built on top of the Internet and
hint at the complexity of the Internet's design. Later in the book we
will develop a more complete taxonomy of application types to help
guide our discussion of key design decisions as we seek to build,
operate, and use networks that such a wide range of applications. The
book concludes by revisiting these two specific applications, as well
as several others that illustrate the breadth of what is possible on
today's Internet.

For now, this quick look at a few typical applications will suffice to
enable us to start looking at the problems that must be addressed if we
are to build a network that supports such application diversity.

2.1.4 Design Lessons

What are some of the takeaways that have done us in good stead? Don't
restrict data types (decouple)... Separation of concerns: inside vs
outside (on top of) the network. Avoid baking assumptions into the
network.

2.2 Scalable Connectivity 
----------------------------

Just as important as generality, a network must provide connectivity
among a set of computers. The more we can scale the network to include
more and more computers, devices, and ultimately people, the more
powerful it will be. This is the idea behind the term "network effect."

It can be enough to build a limited network that connects only a few
select machines. In fact, for reasons of privacy and security, many
private (corporate) networks have the explicit goal of limiting the
set of machines that are connected. In contrast, other networks (of
which the Internet is the prime example) are designed to grow in a way
that allows them the potential to connect all the computers in the
world. A system that is designed to support growth to an arbitrarily
large size is said to *scale*. Using the Internet as a model, this
book addresses the challenge of scalability.

To understand the requirements of connectivity more fully, we need to
take a closer look at possible ways to interconnect computers, keeping
in mind that connectivity occurs at many different levels. Said
another way, connectivity can be provided incrementally, through
multiple levels of abstraction.

2.2.1 Direct Links
~~~~~~~~~~~~~~~~~~

At the lowest level, a network can consist of two or more computers
directly connected by some physical medium, such as a coaxial cable or
an optical fiber. We call such a physical medium a *link*, and we
often refer to the computers it connects as *nodes*. (Sometimes a node
is a more specialized piece of hardware rather than a computer, but we
overlook that distinction for the purposes of this discussion.) As
illustrated in :numref:`Figure %s <fig-direct>`, physical links are
sometimes limited to a pair of nodes (such a link is said to be
*point-to-point*), while in other cases more than two nodes may share
a single physical link (such a link is said to be
*multiple-access*). Wireless links, such as those provided by cellular
networks and Wi-Fi networks, are an important class of multiple-access
links. It is always the case that multiple-access links are limited in
size, in terms of both the geographical distance they can cover and
the number of nodes they can connect. For this reason, they often
implement the so-called *last mile*, connecting end users to the rest
of the network.

.. _fig-direct:
.. figure:: figures/f01-02-9780123850591.png
   :width: 500px
   :align: center
   
   Direct links: (a) point-to-point; (b) multiple-access.

2.2.2 Packet Switch Networks
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If computer networks were limited to situations in which all nodes are
directly connected to each other over a common physical medium, then
either networks would be very limited in the number of computers they
could connect, or the number of wires coming out of the back of each
node would quickly become both unmanageable and very expensive.
Fortunately, connectivity between two nodes does not necessarily imply a
direct physical connection between them—indirect connectivity may be
achieved among a set of cooperating nodes. Consider the following
example of how a collection of computers can be indirectly connected.

:numref:`Figure %s <fig-psn>` shows a pair of shows a set of nodes,
each of which is attached to one or more point-to-point links. Those
nodes that are attached to at least two links run software that
forwards data received on one link out on another. If organized in a
systematic way, these forwarding nodes form a *switched
network*. There are numerous types of switched networks, of which the
two most common are *circuit switched* and *packet switched*. The
former is most notably employed by the telephone system, while the
latter is used for the overwhelming majority of computer networks and
will be the focus of this book. (Circuit switching is, however, making
a bit of a comeback in the optical networking realm, which turns out
to be important as demand for network capacity constantly grows.) The
important feature of packet-switched networks is that the nodes in
such a network send discrete blocks of data to each other. Think of
these blocks of data as corresponding to some piece of application
data such as a file, a piece of email, or an image. We call each block
of data either a *packet* or a *message*, and for now we use these
terms interchangeably.

.. _fig-psn:
.. figure:: figures/f01-03-9780123850591.png
   :width: 500px
   :align: center
   
   Switched network.

Packet-switched networks typically use a strategy called
*store-and-forward*. As the name suggests, each node in a
store-and-forward network first receives a complete packet over some
link, stores the packet in its internal memory, and then forwards the
complete packet to the next node. In contrast, a circuit-switched
network first establishes a dedicated circuit across a sequence of links
and then allows the source node to send a stream of bits across this
circuit to a destination node. The major reason for using packet
switching rather than circuit switching in a computer network is
efficiency, discussed in the next subsection.

The cloud in :numref:`Figure %s <fig-psn>` distinguishes between the
nodes on the inside that *implement* the network (they are commonly
called *switches*, and their primary function is to store and forward
packets) and the nodes on the outside of the cloud that *use* the
network (they are traditionally called *hosts*, and they support users
and run application programs). Also note that the cloud is one of the
most important icons of computer networking. In general, we use a
cloud to denote any type of network, whether it is a single
point-to-point link, a multiple-access link, or a switched
network. Thus, whenever you see a cloud used in a figure, you can
think of it as a placeholder for any of the networking technologies
covered in this book.\ [#]_

.. [#] The use of clouds to represent networks predates the term
       *cloud computing* by at least a couple of decades, but there an
       increasingly rich connection between these two usages, which
       we explore in the next section.

2.2.3 Federated Internet
~~~~~~~~~~~~~~~~~~~~~~~~

Thousands of networks, of dozens of different types and designs, have
been built over the years, each owned and operated by a different
organization. But to build a truely global networks, we need to find a
way for all of those networks to federate with each other. This
naturally leads to a a second way in which a set of computers can be
indirectly connected is shown in :numref:`Figure %s
<fig-internet-cloud>`. In this situation, a set of independent
networks (clouds) are interconnected to form an *internetwork*, or
internet for short. We adopt the Internet’s convention of referring to
a generic internetwork of networks as a lowercase *i* internet, and
the TCP/IP Internet we all use every day as the capital *I*
Internet. A node that is connected to two or more networks is commonly
called a *router* or *gateway*, and it plays much the same role as a
switch—it forwards messages from one network to another.

.. _fig-internet-cloud:
.. figure:: figures/f01-04-9780123850591.png
   :width: 500px
   :align: center
   
   Interconnection of networks.

Note that an internet can itself be viewed as another kind of network,
which means that an internet can be built from a set of internets.
Thus, we can recursively build arbitrarily large networks by
interconnecting clouds to form larger clouds. It can reasonably be
argued that this idea of interconnecting widely differing networks was
the fundamental innovation of the Internet and that the successful
growth of the Internet to global size and billions of nodes was the
result of some very good design decisions by the early Internet
architects, which we will discuss later.

Just because a set of hosts are directly or indirectly connected to
each other does not mean that we have succeeded in providing
host-to-host connectivity. The related requirement is that each node
must be able to say which of the other nodes on the network it wants
to communicate with. This is done by assigning an *address* to each
node. An address is a byte string that identifies a node; that is, the
network can use a node’s address to distinguish it from the other
nodes connected to the network. When a source node wants the network
to deliver a message to a certain destination node, it specifies the
address of the destination node. If the sending and receiving nodes
are not directly connected, then the switches and routers of the
network use this address to decide how to forward the message toward
the destination. The process of determining systematically how to
forward messages toward the destination node based on its address is
called *routing*.

This brief introduction to addressing and routing has presumed that the
source node wants to send a message to a single destination node
(*unicast*). While this is the most common scenario, it is also possible
that the source node might want to *broadcast* a message to all the
nodes on the network. Or, a source node might want to send a message to
some subset of the other nodes but not all of them, a situation called
*multicast*. Thus, in addition to node-specific addresses, another
requirement of a network is that it supports multicast and broadcast
addresses.

.. _key-nested:
.. admonition:: Key Takeaway

  The main idea to take away from this discussion is that we can
  define a *network* recursively as consisting of two or more nodes
  connected by a physical link, or as two or more networks connected
  by a node. In other words, a network can be constructed from a
  nesting of networks, where at the bottom level, the network is
  implemented by some physical medium. Among the key challenges in
  providing network connectivity are the definition of an address for
  each node that is reachable on the network (be it logical or
  physical), and the use of such addresses to forward messages toward
  the appropriate destination node(s). :ref:`[Next] <key-stat-mux>`

2.2.4 Example Network Technologies
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

2.3 Cost-Effective Resource Sharing
----------------------------------------

As stated above, we focus on packet-switched networks. This section
explains the key requirement of computer networks—efficiency—that
leads us to packet switching as the strategy of choice.

Given a collection of nodes indirectly connected by a nesting of
networks, it is possible for any pair of hosts to send messages to each
other across a sequence of links and nodes. Of course, we want to do
more than support just one pair of communicating hosts—we want to
provide all pairs of hosts with the ability to exchange messages. The
question, then, is how do all the hosts that want to communicate share
the network, especially if they want to use it at the same time? And, as
if that problem isn’t hard enough, how do several hosts share the same
*link* when they all want to use it at the same time?

To understand how hosts share a network, we need to introduce a
fundamental concept, *multiplexing*, which means that a system resource
is shared among multiple users. At an intuitive level, multiplexing can
be explained by analogy to a timesharing computer system, where a single
physical processor is shared (multiplexed) among multiple jobs, each of
which believes it has its own private processor. Similarly, data being
sent by multiple users can be multiplexed over the physical links that
make up a network.

To see how this might work, consider the simple network illustrated in
:numref:`Figure %s <fig-mux>`, where the three hosts on the left side
of the network (senders S1-S3) are sending data to the three hosts on
the right (receivers R1-R3) by sharing a switched network that
contains only one physical link. (For simplicity, assume that host S1
is sending data to host R1, and so on.) In this situation, three flows
of data—corresponding to the three pairs of hosts—are multiplexed onto
a single physical link by switch 1 and then *demultiplexed* back into
separate flows by switch 2. Note that we are being intentionally vague
about exactly what a “flow of data” corresponds to. For the purposes
of this discussion, assume that each host on the left has a large
supply of data that it wants to send to its counterpart on the right.

.. _fig-mux:
.. figure:: figures/f01-05-9780123850591.png
   :width: 500px
   :align: center
   
   Multiplexing multiple logical flows over a single
   physical link.

There are several different methods for multiplexing multiple flows
onto one physical link. One common method is *time-division
multiplexing* (TDM). The idea of TDM is to divide time into
equal-sized quanta and, in a round-robin fashion, give each flow a
chance to send its data over the physical link. In other words, during
time quantum 1, data from S1 to R1 is transmitted; during time quantum
2, data from S2 to R2 is transmitted; in quantum 3, S3 sends data to
R3.  At this point, the first flow (S1 to R1) gets to go again, and
the process repeats. Another method is *frequency-division
multiplexing* (FDM). The idea of FDM is to transmit each flow over the
physical link at a different frequency, much the same way that the
signals for different TV stations are transmitted at a different
frequency over the airwaves or on a coaxial cable TV link.

Although simple to understand, both TDM and FDM are limited in two
ways. First, if one of the flows (host pairs) does not have any data
to send, its share of the physical link—that is, its time quantum or
its frequency—remains idle, even if one of the other flows has data to
transmit. For example, S3 had to wait its turn behind S1 and S2 in the
previous paragraph, even if S1 and S2 had nothing to send. For
computer communication, the amount of time that a link is idle can be
very large—for example, consider the amount of time you spend reading
a web page (leaving the link idle) compared to the time you spend
fetching the page. Second, both TDM and FDM are limited to situations
in which the maximum number of flows is fixed and known ahead of
time. It is not practical to resize the quantum or to add additional
quanta in the case of TDM or to add new frequencies in the case of
FDM.

The form of multiplexing that addresses these shortcomings, and of which
we make most use in this book, is called *statistical multiplexing*.
Although the name is not all that helpful for understanding the concept,
statistical multiplexing is really quite simple, with two key ideas.
First, it is like TDM in that the physical link is shared over
time—first data from one flow is transmitted over the physical link,
then data from another flow is transmitted, and so on. Unlike TDM,
however, data is transmitted from each flow on demand rather than during
a predetermined time slot. Thus, if only one flow has data to send, it
gets to transmit that data without waiting for its quantum to come
around and thus without having to watch the quanta assigned to the other
flows go by unused. It is this avoidance of idle time that gives packet
switching its efficiency.

As defined so far, however, statistical multiplexing has no mechanism to
ensure that all the flows eventually get their turn to transmit over the
physical link. That is, once a flow begins sending data, we need some
way to limit the transmission, so that the other flows can have a turn.
To account for this need, statistical multiplexing defines an upper
bound on the size of the block of data that each flow is permitted to
transmit at a given time. This limited-size block of data is typically
referred to as a *packet*, to distinguish it from the arbitrarily large
*message* that an application program might want to transmit. Because a
packet-switched network limits the maximum size of packets, a host may
not be able to send a complete message in one packet. The source may
need to fragment the message into several packets, with the receiver
reassembling the packets back into the original message.

.. _fig-statmux:
.. figure:: figures/f01-06-9780123850591.png
   :width: 500px
   :align: center
   
   A switch multiplexing packets from multiple sources
   onto one shared link.

In other words, each flow sends a sequence of packets over the
physical link, with a decision made on a packet-by-packet basis as to
which flow’s packet to send next. Notice that, if only one flow has
data to send, then it can send a sequence of packets back-to-back;
however, should more than one of the flows have data to send, then
their packets are interleaved on the link. :numref:`Figure %s
<fig-statmux>` depicts a switch multiplexing packets from multiple
sources onto a single shared link.

The decision as to which packet to send next on a shared link can be
made in a number of different ways. For example, in a network consisting
of switches interconnected by links such as the one in :numref:`Figure
%s <fig-mux>`, the decision would be made by the switch that transmits
packets onto the shared link. (As we will see later, not all
packet-switched networks actually involve switches, and they may use
other mechanisms to determine whose packet goes onto the link next.)
Each switch in a packet-switched network makes this decision
independently, on a packet-by-packet basis. One of the issues that faces
a network designer is how to make this decision in a fair manner. For
example, a switch could be designed to service packets on a first-in,
first-out (FIFO) basis. Another approach would be to transmit the
packets from each of the different flows that are currently sending data
through the switch in a round-robin manner. This might be done to ensure
that certain flows receive a particular share of the link’s bandwidth or
that they never have their packets delayed in the switch for more than a
certain length of time. A network that attempts to allocate bandwidth to
particular flows is sometimes said to support *quality of service*
(QoS).

Also, notice in :numref:`Figure %s <fig-statmux>` that since the
switch has to multiplex three incoming packet streams onto one
outgoing link, it is possible that the switch will receive packets
faster than the shared link can accommodate. In this case, the switch
is forced to buffer these packets in its memory. Should a switch
receive packets faster than it can send them for an extended period of
time, then the switch will eventually run out of buffer space, and
some packets will have to be dropped. When a switch is operating in
this state, it is said to be *congested*.

.. _key-stat-mux:
.. admonition:: Key Takeaway

  The bottom line is that statistical multiplexing defines a
  cost-effective way for multiple users (e.g., host-to-host flows of
  data) to share network resources (links and nodes) in a fine-grained
  manner. It defines the packet as the granularity with which the
  links of the network are allocated to different flows, with each
  switch able to schedule the use of the physical links it is
  connected to on a per-packet basis. Fairly allocating link capacity
  to different flows and dealing with congestion when it occurs are
  the key challenges of statistical multiplexing. :ref:`[Next]
  <key-semantic-gap>`

Need to mention *best-effort* in here some place...

Is there anything in Ch6 to include here?

2.4 Support for Application Developers
--------------------------------------

The discussion up this this point focuses on the challenges providing
cost-effective connectivity among a group of hosts, but it is overly
simplistic to view a computer network as simply delivering packets
among a collection of computers. It is more accurate to think of a
network as providing the means for a set of application processes that
are distributed over those computers to communicate with each other to
implement some application (with email, web surfing, and video being
examples we know we want to support).

The requirement is that the network provide application developers
with powerful, easy-to-use communication abstractions, shielding them
from the low-level details, limitations, and failures of host-to-host
packet delivery. The alternative would be for every application
developer to build all the necessary functionality for themselves,
effectively reinventing the wheel over and over again.

Fortunately, because many applications have similar needs, it is much
more logical to implement those common services once, and then to let
the application designer build the application using them. The
challenge for a network designer is to identify the right set of
common services.  The goal is to hide the complexity of the network
from the application without overly constraining the application
designer.

.. _fig-channel:
.. figure:: figures/f01-07-9780123850591.png
   :width: 500px
   :align: center
   
   Processes communicating over an abstract channel.

Intuitively, we view the network as providing logical *channels* over
which application-level processes can communicate with each other; each
channel provides the set of services required by that application. In
other words, just as we use a cloud to abstractly represent connectivity
among a set of computers, we now think of a channel as connecting one
process to another. :numref:`Figure %s <fig-channel>` shows a pair of
application-level processes communicating over a logical channel that
is, in turn, implemented on top of a cloud that connects a set of hosts.
We can think of the channel as being like a pipe connecting two
applications, so that a sending application can put data in one end and
expect that data to be delivered by the network to the application at
the other end of the pipe.

Multi-dimensional: how name, how secure, how format, how make reliable...

2.4.1 Semantic Gap
~~~~~~~~~~~~~~~~~~

The challenge is to develop algorithms that turn the less-than-perfect
properties of the underlying network into the high level of service
required by application programs. Two forces shape the design of these
so called *end-to-end* communication servcies (abstractions). From
above, the application-level processes that use its services have
certain requirements. The following list itemizes some of the common
properties that a transport protocol can be expected to provide:

-  Guarantees message delivery

-  Delivers messages in the same order they are sent

-  Delivers at most one copy of each message

-  Supports arbitrarily large messages

-  Supports synchronization between the sender and the receiver

-  Allows the receiver to apply flow control to the sender

-  Supports multiple application processes on each host

-  Identify end-points with human readable names   

Note that this list does not include all the functionality that
application processes might want from the network. For example, it
does not include security features like authentication or encryption,
so in reality the network provides a collection of abstract
end-to-end communication services, but for now let's focus on
issues apart from secrity (which we deal with separately in a later
section). It also doesn't address how processes identify each other
and name the resources they want to access...

From below, the underlying network upon which the end-to-end
communication service operates has certain limitations in the level of
service it can provide.  Some of the more typical limitations of the
network are that it may

-  Drop messages

-  Reorder messages

-  Deliver duplicate copies of a given message

-  Limit messages to some finite size

-  Deliver messages after an arbitrarily long delay

-  Identify end-points with machine-optimized addresses 

Such a network is said to provide a *best-effort* level of service, as
exemplified by the Internet.

The challenge, therefore, is to develop algorithms that turn the
less-than-perfect properties of the underlying network into the high
level of service required by application programs.

.. _key-semantic-gap:
.. admonition:: Key Takeaway

   The key idea to take away from this discussion is that defining
   useful channels involves both understanding the applications’
   requirements and recognizing the limitations of the underlying
   technology. The challenge is to fill in the gap between what the
   application expects and what the underlying technology can provide.
   This is sometimes called the *semantic gap.*


2.4.2 Common Communication Patterns
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Unfortunately, this is not a one-size-fits-all situation. Some
applications need a guarantee that all data is delivered, even if it
means waiting to retransmit a packet that the network dropped, while
others prefer missing an occasional message over the delay incurred by
a retransmission. This leads to a suite of end-to-end communication
abstractions, each tailored for a particular class of applications.

The challenge is to recognize what functionality the channels should
provide to application programs. For example, does the application
require a guarantee that messages sent over the channel are delivered,
or is it acceptable if some messages fail to arrive? Is it necessary
that messages arrive at the recipient process in the same order in which
they are sent, or does the recipient not care about the order in which
messages arrive? Does the network need to ensure that no third parties
are able to eavesdrop on the channel, or is privacy not a concern? In
general, a network provides a variety of different types of channels,
with each application selecting the type that best meets its needs. The
rest of this section illustrates the thinking involved in defining
useful channels.

Designing abstract channels involves first understanding the
communication needs of a representative collection of applications, then
extracting their common communication requirements, and finally
incorporating the functionality that meets these requirements in the
network.

One of the earliest applications supported on any network is a file
access program like the File Transfer Protocol (FTP) or Network File
System (NFS). Although many details vary—for example, whether whole
files are transferred across the network or only single blocks of the
file are read/written at a given time—the communication component of
remote file access is characterized by a pair of processes, one that
requests that a file be read or written and a second process that honors
this request. The process that requests access to the file is called the
*client*, and the process that supports access to the file is called the
*server*.

Reading a file involves the client sending a small request message to a
server and the server responding with a large message that contains the
data in the file. Writing works in the opposite way—the client sends a
large message containing the data to be written to the server, and the
server responds with a small message confirming that the write to disk
has taken place.

A digital library is a more sophisticated application than file
transfer, but it requires similar communication services. For example,
the *Association for Computing Machinery* (ACM) operates a large digital
library of computer science literature at

.. code-block:: html

   http://portal.acm.org/dl.cfm

This library has a wide range of searching and browsing features to help
users find the articles they want, but ultimately much of what it does
is respond to user requests for files, such as electronic copies of
journal articles.

Using file access, a digital library, and the two video applications
described in the introduction (videoconferencing and video on demand) as
a representative sample, we might decide to provide the following two
types of channels: *request/reply* channels and *message stream*
channels. The request/reply channel would be used by the file transfer
and digital library applications. It would guarantee that every message
sent by one side is received by the other side and that only one copy of
each message is delivered. The request/reply channel might also protect
the privacy and integrity of the data that flows over it, so that
unauthorized parties cannot read or modify the data being exchanged
between the client and server processes.

The message stream channel could be used by both the video on demand and
videoconferencing applications, provided it is parameterized to support
both one-way and two-way traffic and to support different delay
properties. The message stream channel might not need to guarantee that
all messages are delivered, since a video application can operate
adequately even if some video frames are not received. It would,
however, need to ensure that those messages that are delivered arrive in
the same order in which they were sent, to avoid displaying frames out
of sequence. Like the request/reply channel, the message stream channel
might want to ensure the privacy and integrity of the video data.
Finally, the message stream channel might need to support multicast, so
that multiple parties can participate in the teleconference or view the
video.

While it is common for a network designer to strive for the smallest
number of abstract channel types that can serve the largest number of
applications, there is a danger in trying to get away with too few
channel abstractions. Simply stated, if you have a hammer, then
everything looks like a nail. For example, if all you have are message
stream and request/reply channels, then it is tempting to use them for
the next application that comes along, even if neither type provides
exactly the semantics needed by the application. Thus, network designers
will probably be inventing new types of channels—and adding options to
existing channels—for as long as application programmers are inventing
new applications.

Also note that independent of exactly *what* functionality a given
channel provides, there is the question of *where* that functionality is
implemented. In many cases, it is easiest to view the host-to-host
connectivity of the underlying network as simply providing a *bit pipe*,
with any high-level communication semantics provided at the end hosts.
The advantage of this approach is that it keeps the switches in the
middle of the network as simple as possible—they simply forward
packets—but it requires the end hosts to take on much of the burden of
supporting semantically rich process-to-process channels. The
alternative is to push additional functionality onto the switches,
thereby allowing the end hosts to be “dumb” devices (e.g., telephone
handsets). We will see this question of how various network services are
partitioned between the packet switches and the end hosts (devices) as a
recurring issue in network design.

2.4.3 Reliable Message Delivery
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

As suggested by the examples just considered, reliable message delivery
is one of the most important functions that a network can provide. It is
difficult to determine how to provide this reliability, however, without
first understanding how networks can fail. The first thing to recognize
is that computer networks do not exist in a perfect world. Machines
crash and later are rebooted, fibers are cut, electrical interference
corrupts bits in the data being transmitted, switches run out of buffer
space, and, as if these sorts of physical problems aren’t enough to
worry about, the software that manages the hardware may contain bugs and
sometimes forwards packets into oblivion. Thus, a major requirement of a
network is to recover from certain kinds of failures, so that
application programs don’t have to deal with them or even be aware of
them.

There are three general classes of failure that network designers have
to worry about. First, as a packet is transmitted over a physical link,
*bit errors* may be introduced into the data; that is, a 1 is turned
into a 0 or *vice versa*. Sometimes single bits are corrupted, but more
often than not a *burst error* occurs—several consecutive bits are
corrupted. Bit errors typically occur because outside forces, such as 
lightning strikes, power surges, and microwave ovens, interfere with the
transmission of data. The good news is that such bit errors are fairly 
rare, affecting on average only one out of every 10\ :sup:`6` to 
10\ :sup:`7` bits on a typical copper-based cable and one out of every 
10\ :sup:`12` to 10\ :sup:`14` bits on a typical optical fiber. 
As we will see, there are techniques that detect these bit errors with 
high probability. Once detected, it is sometimes possible to correct for 
such errors—if we know which bit or bits are corrupted, we can simply 
flip them—while in other cases the damage is so bad that it is necessary
to discard the entire packet. In such a case, the sender may be expected 
to retransmit the packet.

The second class of failure is at the packet, rather than the bit,
level; that is, a complete packet is lost by the network. One reason
this can happen is that the packet contains an uncorrectable bit error
and therefore has to be discarded. A more likely reason, however, is
that one of the nodes that has to handle the packet—for example, a
switch that is forwarding it from one link to another—is so overloaded
that it has no place to store the packet and therefore is forced to drop
it. This is the problem of congestion just discussed. Less commonly, the
software running on one of the nodes that handles the packet makes a
mistake. For example, it might incorrectly forward a packet out on the
wrong link, so that the packet never finds its way to the ultimate
destination. As we will see, one of the main difficulties in dealing
with lost packets is distinguishing between a packet that is indeed lost
and one that is merely late in arriving at the destination.

The third class of failure is at the node and link level; that is, a
physical link is cut, or the computer it is connected to crashes. This
can be caused by software that crashes, a power failure, or a reckless
backhoe operator. Failures due to misconfiguration of a network device
are also common. While any of these failures can eventually be
corrected, they can have a dramatic effect on the network for an
extended period of time. However, they need not totally disable the
network. In a packet-switched network, for example, it is sometimes
possible to route around a failed node or link. One of the difficulties
in dealing with this third class of failure is distinguishing between a
failed computer and one that is merely slow or, in the case of a link,
between one that has been cut and one that is very flaky and therefore
introducing a high number of bit errors.

2.4.4 Example End-to-End Abstractions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

2.5 Security
--------------

Computer networks are typically a shared resource used by many
applications representing different interests. The Internet is
particularly widely shared, being used by competing businesses, mutually
antagonistic governments, and opportunistic criminals. Unless security
measures are taken, a network conversation or a distributed application
may be compromised by an adversary.

Consider, for example, some threats to secure use of the web. Suppose
you are a customer using a credit card to order an item from a website.
An obvious threat is that an adversary would eavesdrop on your network
communication, reading your messages to obtain your credit card
information. How might that eavesdropping be accomplished? It is trivial
on a broadcast network such as an Ethernet or Wi-Fi, where any node can
be configured to receive all the message traffic on that network. More
elaborate approaches include wiretapping and planting spy software on
any of the chain of nodes involved. Only in the most extreme cases
(e.g.,national security) are serious measures taken to prevent such
monitoring, and the Internet is not one of those cases. It is possible
and practical, however, to encrypt messages so as to prevent an
adversary from understanding the message contents. A protocol that does
so is said to provide *confidentiality*. Taking the concept a step
farther, concealing the quantity or destination of communication is
called *traffic confidentiality*—because merely knowing how much
communication is going where can be useful to an adversary in some
situations.

Even with confidentiality there still remains threats for the website
customer. An adversary who can’t read the contents of your encrypted
message might still be able to change a few bits in it, resulting in a
valid order for, say, a completely different item or perhaps 1000 units
of the item. There are techniques to detect, if not prevent, such
tampering. A protocol that detects such message tampering is said to
provide *integrity*.

Another threat to the customer is unknowingly being directed to a false
website. This can result from a Domain Name System (DNS) attack, in
which false information is entered in a DNS server or the name service
cache of the customer’s computer. This leads to translating a correct
URL into an incorrect IP address—the address of a false website. A
protocol that ensures that you really are talking to whom you think
you’re talking is said to provide *authentication*. Authentication
entails integrity, since it is meaningless to say that a message came
from a certain participant if it is no longer the same message.

The owner of the website can be attacked as well. Some websites have
been defaced; the files that make up the website content have been
remotely accessed and modified without authorization. That is an issue
of *access control*: enforcing the rules regarding who is allowed to do
what. Websites have also been subject to denial of service (DoS)
attacks, during which would-be customers are unable to access the
website because it is being overwhelmed by bogus requests. Ensuring a
degree of access is called *availability*.

In addition to these issues, the Internet has notably been used as a
means for deploying malicious code, generally called *malware*, that
exploits vulnerabilities in end systems. *Worms*, pieces of
self-replicating code that spread over networks, have been known for
several decades and continue to cause problems, as do their relatives,
*viruses*, which are spread by the transmission of infected files.
Infected machines can then be arranged into *botnets*, which can be used
to inflict further harm, such as launching DoS attacks.

2.6 Manageability
-------------------

A final requirement, which seems to be neglected or left till last all
too often (as we do here), is that networks need to be managed. Managing
a network includes upgrading equipment as the network grows to carry
more traffic or reach more users, troubleshooting the network when
things go wrong or performance isn’t as desired, and adding new features
in support of new applications. Network management has historically
been a human-intensive aspect of networking, and while it is ulikely
we'll get people entirely out of the loop, it is increasingly being
addressed by automation and self-healing designs.

This requirement is partly related to the issue of scalability discussed
above—as the Internet has scaled up to support billions of users and at
least hundreds of millions of hosts, the challenges of keeping the whole
thing running correctly and correctly configuring new devices as they
are added have become increasingly problematic. Configuring a single
router in a network is often a task for a trained expert; configuring
thousands of routers and figuring out why a network of such a size is
not behaving as expected can become a task beyond any single human.
This is why automation is becoming so important.

One way to make a network easier to manage is to avoid change. Once the
network is working, simply *do not touch it!* This mindset exposes the
fundamental tension between *stability* and *feature velocity*: the rate
at which new capabilities are introduced into the network. Favoring
stability is the approach the telecommunications industry (not to
mention University system administrators and corporate IT departments)
adopted for many years, making it one of the most slow moving and risk
averse industries you will find anywhere. But the recent explosion of
the cloud has changed that dynamic, making it necessary to bring
stability and feature velocity more into balance. The impact of the
cloud on the network is a topic that comes up over and over throughout
the book, and one we pay particular attention to in the *Perspectives*
section at the end of each chapter. For now, suffice it to say that
managing a rapidly evolving network is arguably *the* central challenge
in networking today.

