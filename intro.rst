Chapter 1:  Introduction
========================

Suppose you want to build a computer network, one that has the potential
to grow to global proportions and to support applications as diverse as
teleconferencing, video on demand, electronic commerce, distributed
computing, and digital libraries. What available technologies would
serve as the underlying building blocks, and what kind of software
architecture would you design to integrate these building blocks into an
effective communication service? Answering this question is the
overriding goal of this book—to describe the available building
materials and then to show how they can be used to construct a network
from the ground up.

Before we can understand how to design a computer network, we should
first agree on exactly what a computer network is. At one time, the term
*network* meant the set of serial lines used to attach dumb terminals to
mainframe computers. Other important networks include the voice
telephone network and the cable TV network used to disseminate video
signals. The main things these networks have in common are that they are
specialized to handle one particular kind of data (keystrokes, voice, or
video) and they typically connect to special-purpose devices (terminals,
hand receivers, and television sets).

What distinguishes a computer network from these other types of
networks? Probably the most important characteristic of a computer
network is its generality. Computer networks are built primarily from
general-purpose programmable hardware, and they are not optimized for a
particular application like making phone calls or delivering television
signals. Instead, they are able to carry many different types of data,
and they support a wide, and ever growing, range of applications.
Today’s computer networks have pretty much taken over the functions
previously performed by single-use networks. This chapter looks at some
typical applications of computer networks and discusses the requirements
that a network designer who wishes to support such applications must be
aware of.

Once we understand the requirements, how do we proceed? Fortunately, we
will not be building the first network. Others, most notably the
community of researchers responsible for the Internet, have gone before
us. We will use the wealth of experience generated from the Internet to
guide our design. This experience is embodied in a *network
architecture* that identifies the available hardware and software
components and shows how they can be arranged to form a complete network
system.

At each step, we will use today’s protocols to illustrate various
design choices available to us, but we will not accept these existing
artifacts as gospel. Instead, we will be asking (and answering) the
question of *why* networks are designed the way they are. While it is
tempting to settle for just understanding the way it’s done today, it
is important to recognize the underlying concepts because networks are
constantly changing as technology evolves and new applications are
invented. It is our experience that once you understand the
fundamental ideas, any new protocol that you are confronted with will
be relatively easy to digest.

In addition to understanding how networks are built, it is increasingly
important to understand how they are operated or managed and how network
applications are developed. Almost all of us now have computer networks
in our homes, offices, and in some cases in our cars, so operating
networks is no longer a matter only for a few specialists. And with the
proliferation of smartphones, many more of this generation are
developing networked applications than in the past. So we need to
consider networks from these multiple perspectives: builders, operators,
application developers.

This book lays the foundation for understanding how to build, operate,
and program a network. First, it explores the requirements that
different applications and different communities of people place on
the network. Second, it introduces the idea of a network architecture,
which provides a framework (blueprint) for the network we're
building. Third, it introduces some of the key elements in the
implementation of computer networks, which are necessary to translate
our paper design into real, functioning system. Finally, it identifies
the key metrics that are used to evaluate the performance of computer
networks.

1.1 Stakeholders
------------------

As we noted above, a student of networks can take several perspectives. 
When we wrote the first edition of this book, the majority of the 
population had no Internet access at all, and those who did obtained it 
while at work, at a university, or by a dial-up modem at home. The set 
of popular applications could be counted on one’s fingers. Thus, like 
most books at the time, ours focused on the perspective of someone who 
would design networking equipment and protocols. We continue to focus on 
this perspective, and our hope is that after reading this book you will 
know how to design the networking equipment and protocols of the future. 

However, we also want to cover the perspectives of two additional 
stakeholders: those who develop networked applications and those who 
manage or operate networks. Let’s consider how these three stakeholders 
might list their requirements for a network:

-  An *application programmer* would list the services that his or her 
   application needs: for example, a guarantee that each message the 
   application sends will be delivered without error within a certain 
   amount of time or the ability to switch gracefully among different 
   connections to the network as the user moves around. 

-  A *network operator* would list the characteristics of a system that 
   is easy to administer and manage: for example, in which faults can be 
   easily isolated, new devices can be added to the network and 
   configured correctly, and it is easy to account for usage. 

-  A *network designer* would list the properties of a cost-effective 
   design: for example, that network resources are efficiently utilized 
   and fairly allocated to different users. Issues of performance are 
   also likely to be important. 

The next chapter distills the requirements of different stakeholders
into a high-level introduction to the major considerations that drive
network design and, in doing so, identify the challenges addressed
throughout the rest of this book.

1.2  Cloudification of the Internet
--------------------------------------------------

Identifying the stakeholders in computer networks helps to motivate
the technical requirements that shape how networks are designed and
built. This presumes all design decisions are purely technical, but of
course, that's usually not the case. Many other factors, from market
forces, to government policy, to ethical considerations, also
influence how networks are designed and built.

Of these, the marketplace is the most influential, and corresponds to
the interplay between network operators (e.g., AT&T, Comcast, Verizon,
DT, NTT, China Unicom), network equipment venders (e.g., Cisco, Juniper,
Ericsson, Nokia, Huawei, NEC), application and service providers (e.g.,
Facebook, Google, Amazon, Microsoft, Apple, Netflix, Spotify), and of
course, subscribers and customers (i.e., individuals, but also
enterprises and businesses). The lines between these players are not
always crisp, with many companies playing multiple roles. The most
notable example of this are the large cloud providers, who (a) build
their own networking equipment using commodity components, (b) deploy
and operate their own networks, and (c) provide end-user services and
applications on top of their networks.

When you account for these other factors in the technical design
process, you realize there are a couple of implicit assumptions in the
textbook version of the story that need to be reevaluated. One is that
designing a network is a one-time activity. Build it once and use it
forever (modulo hardware upgrades so users can enjoy the benefits of the
latest performance improvements). A second is that the job of building
the network is largely divorced from the job of operating the network.
Neither of these assumptions is quite right.

The network's design is clearly evolving, and we have documented these
changes with each new edition of the textbook over the years. Doing
that on a timeline measured in years has historically been good
enough, but anyone that has downloaded and used the latest smartphone
app knows how glacially slow anything measured in years is by today's
standards.  Designing for evolution has to be part of the decision
making process.

On the second point, the companies that build networks are almost always
the same ones that operate them. They are collectively known as *network
operators*, and they include the companies listed above. But if we again
look to the cloud for inspiration, we see that develop-and-operate isn' t
true just at the company level, but it is also how the fastest moving
cloud companies organize their engineering teams: around the *DevOps*
model. (If you are unfamiliar with DevOps, we recommend you read *Site
Reliability Engineering: How Google Runs Production Systems* to see how
it is practiced.)

What this all means is that computer networks are now in the midst of a
major transformation, with network operators trying to simultaneously
accelerate the pace of innovation (sometimes known as feature velocity)
and yet continue to offer a reliable service (preserve stability). And
they are increasingly doing this by adopting the best practices of cloud
providers, which can be summarized as having two major themes: (1) take
advantage of commodity hardware and move all intelligence into software,
and (2) adopt agile engineering processes that break down barriers
between development and operations.

This transformation is sometimes called the "cloudification" or
"softwarization" of the network, and while the Internet has always had
a robust software ecosystem, it has historically been limited to the
applications running *on top of* the network (e.g., using the Socket
API described in **Section 1.4**).  What's changed
is that today these same cloud-inspired engineering practices are
being applied to the *internals* of the network. This new approach,
known as *Software Defined Networks* (SDN), is a game changer, not so
much in terms of how we address the fundamental technical challenges
of framing, routing, fragmentation/reassembly, packet scheduling,
congestion control, security, and so on, but in terms of how rapidly
the network evolves to support new features.

This transformation is so important that we take it up again in the
*Perspective* section at the end of each chapter. As these discussions
will explore, what happens in the networking industry is partly about
technology, but also partly about many other non-technical factors,
all of which is a testament to how deeply embedded the Internet
is in our lives.

.. admonition:: Broader Perspective

   To learn more about DevOps, we recommend: `Site Reliability
   Engineering: How Google Runs Production Systems
   <https://www.amazon.com/Site-Reliability-Engineering-Production-Systems/dp/149192912X/ref=pd_bxgy_14_img_2/131-5109792-2268338?_encoding=UTF8&pd_rd_i=149192912X&pd_rd_r=4b77155f-234d-11e9-944e-278ce23a35b5&pd_rd_w=qIfxg&pd_rd_wg=12dE2&pf_rd_p=6725dbd6-9917-451d-beba-16af7874e407&pf_rd_r=5GN656H9VEG4WEVGB728&psc=1&refRID=5GN656H9VEG4WEVGB728>`__,
   2016.
