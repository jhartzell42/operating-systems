+++
title = "What is an operating system?"
date = "2019-04-28"
slug = "operating_system"
author = "Jimmy Hartzell"
tags = ["computers"]
showFullContent = false
draft = false
+++

A user of modern technology hears the term "operating system" thrown
around a lot. Most people can name a few examples: Windows and macOS on
workstations and laptops, iOS and Android on phones.  Some people might
even throw in Linux or Unix or ChromeOS.  Most people also understand
that a program or a game or even a sufficiently advanced website might
work on some operating systems but not others, and might require different
versions for different operating systems. But it's a bit less clear what
an operating system actually is, how it fits into the general model of
a computer, and how it works.


This isn't surprising, because "operating system" is a bit of an amorphous
concept. Is it a type of program? It's certainly different from most
programs we think of! I listened to a lecture recently by the lead programmer on a
project to develop a new operating system, and he spent at least the first
quarter of the lecture and many slides trying to come up with a workable
definition that jived well with most programmers' and users' intuitions.

To explore this concept, I'm going to talk about a lot of operating
systems from history. These aren't going to be the operating systems
that invented the models in question, but rather typical examples of
those models, especially very popular operating systems of their era and
ones that were direct predecessors to popular operating systems today.
All of the fundamental technologies discussed pre-date the operating
systems I discuss to typify them.

## Computers Without Operating Systems

To see what an operating system is, and why we might want one, let's
imagine a computer without an operating system, or perhaps with a very
minimal operating system. Such computers once existed; people my age
or older might remember the Apple II or the Sega Genesis. A more recent
example might include earlier versions of the Game Boy. These computers
(and a game console is a type of a computer for these purposes) could only
run one program at a time; if you wanted to run a different program or
game, you had to turn the device off, insert a new floppy or cartridge,
and turn the device back on again.

The same physical machine took on an entirely different interface based
on what software you provided. Each program has full control of the
computer while you're running it, to the extent that you have to turn
the computer off to stop running the program. Each program also managed
its own storage; you would save your Sega Genesis games on the cartridge,
not the console, and could then resume them on your neighbor's console
if you wanted to.

This is very different from how computers with operating systems work,
and leads me to the following definition of an operating system: an
operating system is a set of software that allows multiple programs to
co-exist on a computer. You need an operating system to, for example,
reasonably have a permanent hard disk, because there needs to be some
or another convention as to tell which programs should write their data
to which portions of the disk.

## A Minimal Operating System: MS-DOS

![MS-DOS icon](/images/Msdos-icon.png)

This definition includes older operating systems like MS-DOS (see the
[original source code](https://github.com/microsoft/ms-dos)), Microsoft's
flagship operating system from the 80's and early 90's. MS-DOS only could
run one application at a time, like the Apple II or the Sega Genesis. The
difference is that MS-DOS would at least let you share a hard disk between
applications and it also let you switch which application you were using
without rebooting or inserting new media. Sharing a hard disk between
programs was its defining feature, to the point where DOS actually stands
for "disk operating system." MS-DOS shared this acronym DOS with other,
similarly featured microcomptuer operating systems of its day, which
also focused on simply letting programs share a hard drive.

To share a hard drive between multiple programs over time, all the
programs have to agree on how the hard drive is organized.
It wouldn't do for a game to store its game data on sector 13 of
the hard drive when a word processing editor wanted to store its
list of documents on the same sector. The hard drive required
not only an organization scheme, but one shared between different
programs by different authors.

This was done through a *file system*, which allowed you to assign names
to long blobs of bytes, called *files*. A programmer could have a program
store whatever it wanted in the files it created, but as long as it
created files with different names from the other programs, the operating
system, with its file system, would ensure that the data could be
found again without each program having to have its own, possibly
conflicting, ideas of where to look directly on the disk.

On MS-DOS, these files had to be 12 characters long or less:
8 characters of name, a dot `.`, and an 3-character *extension*,
for example, `teleport.doc` or `taxr1998.xls`. The extension served
as a convention to indicate which program was supposed to care about
this file. Your spreadsheet program would let you save spreadsheets on
the same file system that your word processor would let you save your
documents --- some mechanism was needed to say which program should be
run to make sense of which blob of binary bytes, especially because
the first version of MS-DOS didn't even have support for directories
(which we now might call folders).

If you opened a file with the wrong program, the program might notice
you used the wrong extension --- or it might not, and give you gibberish
results from misinterpreting the data. It would certainly encourage you
to save files with the proper extension --- a concept that survives in
Windows to this day, where programs only offer to open files that have
an appropriate extension.

By modern standards, MS-DOS and its file system didn't do very much.
It didn't stop a program from modifying files intended for another
program --- or even from wiping the computer entirely; it simply created
an organizational system that allowed programs to co-exist and store their
data in an organized fashion, as long as the program's were well-behaved
and not buggy (or malicious).

It did have to define a format for programs themselves to be stored on
the disk.  You could tell which files represented runnable programs
because they had the extension `com` (for "command") or `exe` (for
executable). It also had to provide a program to launch your application
programs: This was known as a *shell*: It was the first program that
ran when you turned on the computer, and you could use it to select
other programs to run. At the time through a *command-line interface*:
It would prompt you with the text `C:\>`, and you would have to type
the name of the file that contained the program you wanted to load
(or alternatively do some very basic file management directly from the
command line through built-in commands).

![Starting MS-DOS...](/images/StartingMsdos.png)

Besides its core mission of providing a system to operate a disk, the
"disk operating system" did also have other code, to help programs
interact with the hardware. As most components besides the disk could
be used by the programs however they wanted without damaging others
(because only one ran at a time), this code wasn't as essential to its
functionality, but it did exist. Software used to interact with hardware
is called *drivers*, and they might be included in an operating system
or might be loaded separately, depending on the design. Driver code
is organized into procedures that programs invoke to do things to the
hardware (e.g. draw on the screen or print a file), or code that is
installed as *interrupt handlers* so that the processor will interrupt
the current task whenever a certain hardware event happens (e.g., what
to do when the user presses a key). Because MS-DOS was so minimal,
both types of drivers could be circumvented.

And in actuality, application programs could circumvent the driver that
was the most core to its role as a "disk" operating system --- the driver
for the hard drive, and the layer that allowed you to edit it in terms
of files.  MS-DOS couldn't even force programs to use its procedures for
the one abstraction it absolutely had to maintain.  Though the existence
of official filesystem procedures provided some stability, many programs
circumvented these procedures and modified the hard disk directly,
(hopefully) making sure to respect the conventions but not using MS-DOS's
actual code. MS-DOS, especially at first, was a little bit of code, and a
lot of "gentlemen's agreement" --- it had no security or rigor whatsoever.

This had some upsides. Every application had access to the full power of
the computer. Microcomputers were much slower then, and so every ounce
of direct hardware access could be a major performance boon, especially
for games. Furthermore, many applications supported hardware that the
operating system itself could not: In MS-DOS days, you often had to do
separate sound card or even graphics configuration for every game you had,
but at least you weren't limited by what Microsoft had chosen to provide
support for.

It also had some downsides. Obviously, securing your files was impossible:
there was a way to mark files as read-only, but it could only be advisory.
There was no system of multiuser file ownership --- though an application
could individually provide an encryption feature. These downsides weren't
too bad --- if you trusted everyone who used your computer, it wasn't
really a problem. It's generally better anyway to secure your computer
with encryption or just by putting it in a locked room.

More importantly, this was a hazard for the stability of the system. Any
program could decide to circumvent the standard ways of doing file access,
and many did, to cut corners on performance. But many different pieces
of code all interacting with the same file system is many opportunities
to mess up and have bugs instead of just one. There was a real risk of
a poorly-written program corrupting your file system, deleting files
it wasn't even supposed to touch or potentially rendering the entire
filesystem unusable.

The biggest long-term problem for Microsoft was a subtler version of
this: If Microsoft wanted to change the file system --- if they, for
example, wanted to make filenames longer than 8.3 (so you could say
`real_long_name.html` instead of `rllngnam.htm`), they couldn't just
go do it themselves. Changing a bit of code is easy. Changing a subtle
gentlemen's agreement requires all the gentlemen in question to agree.
If they had changed the format to allow more characters, programs that
used their officially recognized libraries would keep working, but
those that accessed the file system on the hard drive directly would
be following the old ways when the conventions had changed. They would
be thrown off by the long filenames like old people thrown off by how
young people dress. The software that followed the old conventions could
easily accidentally delete data that no longer follows them.

If this were just an occasional program that was doing things its own
way, then Microsoft could just break that one program. Unfortunately,
many many programs had their own ways of accessing the disk. The "disk
operating system" couldn't even keep control of its central feature.

The other major downside of MS-DOS and OSes like it is that you couldn't
run multiple programs at the same time. It allowed different programs to
run in sequence, and to share permanent resources (the filesystem). On
a modern operating system we take for granted the ability to multitask
programs. We listen to music while being ready to receive a call at any
moment --- and to return to the music when the call is finished.  We
expect to be able to look up directions or text messages while talking
to our friends while a file is downloading in the background. This takes
much more sophistication than MS-DOS could provide.

Luckily for those who wanted multitasking, many systems existed to
add multitasking to an MS-DOS installation. Because MS-DOS was so
minimalistic, an MS-DOS program took full control of the computer
when it was run. If it used that control to dispatch between multiple,
simultaneously running programs, it fits our definition of an operating
system: a software system that allows multiple programs to coexist on
a computer. Basically, operating systems existed that used DOS as their
launching point, taking over the computer and providing richer and more
modern services to the programs running under its scope.

These programs/OSes were called "DOS extenders," and the most famous
of them was written by Microsoft, DOS's vendor, to add multitasking
(and GUI, which in the personal computer world often went hand in hand)
to their otherwise primitive operating system. This was called "Windows."

![Microsoft Windows has changed a lot since 3.0](/images/windows.png)

For those of you who don't remember this era, Windows was not always
the operating system a computer would immediately boot into. It used to
be that Windows masqueraded as a MS-DOS program, that you'd boot up the
computer and see a command-line prompt, and have to type `win` before
you saw any graphical user interface whatsoever. Without a preexisting
MS-DOS installation to set up the file system and do initial hardware
configuration, you couldn't run Windows at all --- not that Windows wasn't
sophisticated enough, but it had always been run that way, and so it never
replicated that functionality in the boot process.  Similarly, Windows at
the time was constrained, just as DOS was, by its 8.3 filename convention.
It had to share a filesystem with DOS programs, as it was itself a DOS
program --- as well as an operating system in its own right.

By the time Windows had gotten to version 3, it had the ability, on
sufficiently powerful computers, to run multiple copies of MS-DOS at
the same time and an MS-DOS program in each of those copies --- and yet,
at another layer of abstraction, it was itself a program run from the
one copy of MS-DOS that your program booted. Microsoft cleaned up this
situation in Windows 95, which still used DOS internally as part of
its boot process, but went straight to graphical, Windows mode when the
computer turned on.

## Cooperative Multitasking

Windows 3 supported graphical user interfaces and running multiple
programs at the same time, and so did Mac OS System 7, both from
the early 1990's. However, multiple programs did not, and could not,
literally run at the same time --- the processor executed instructions
in a stream and that stream of instructions represented only one
program at a time.

To maintain the illusion of running multiple programs at the same
time, these systems used *cooperative multitasking*. In cooperative
multitasking a program runs for a short amount of time, and then it
is expected to *yield* control of the processor back to the
operating system.

In a graphical user interface, this usually corresponded to an event
of some sort. When the user clicked in some window, the program
that owned the window would get to run for enough time to decide
how to respond to it: what internal memory should it update,
what should it write to the hard drive, and what new things should
it display on the screen. Once it was done handling the event,
it would return to the operating system, which would then
see if the user has clicked a key in the meantime, which might
mean sending an event to another program. The program could
also, however --- maliciously or accidentally --- not return
to the operating system, in which case the computer would simply
hang and refuse to respond to more input. This is why operating
systems of that time would regularly freeze completely in the
presence of a poorly-written program.

The memory of all the programs were loaded in memory at the same time,
and there was nothing protecting one program's internal data from being
overwritten, maliciously or accidentally, by another program. Basically,
the different programs could be thought of, in a modern sense, as
collections of loadable event-handling subroutines for one graphical
interface system. They were kept separate again by convention,
by gentlemen's agreement.

For certain background tasks, like playing music, the code to
keep sending data to the speakers has to be run repeatedly, on a
timer --- so any apps that use that feature can crash the computer
at any time by simply failing to complete.

So while these operating systems were more sophisticated than MS-DOS
and its cohorts, in another sense they promised more than they could
deliver, and relied even more on the good behavior of the programs
they managed.

They allowed multiple programs to run simultaneously, but actually
required more out of the individual programs to have a harmonious
system. After all, if an MS-DOS program crashes, the computer could be
rebooted, but at least you only lost your work in that program. If a
Windows 3.1 or Mac OS System 7 program were to crash, you'd lose work
in all the other programs it was "multitasking" with.

By this point, there were stronger protections against a program
circumventing the operating system with its own drivers. It was still
generally possible, but less likely to be done. This is important, because
while in MS-DOS, it makes perfect sense for each program to define what
happens when you click the mouse, on a graphical system, the mouse has
to control a mouse pointer which moves from window to window and acts
the same whichever application is in the foreground.  When more than
one application runs at a time, more hardware becomes shared resources,
and so the operating system must take on responsibility for it,
even if this responsibility is only carried out cooperatively.

## Time-Sharing or Preemptive Multitasking

Windows wasn't Microsoft's first attempt at a more robust operating
system than MS-DOS. For a while, it tried to market a more
sophisticated version of MS-DOS, still command-line centric, but
without many of the deficits we've discussed. This operating system
was Xenix.

Xenix was Microsoft's entry into a longer, older tradition of the Unix
operating system. This tradition is mostly present today in Unix's
off-brand workalike clone, Linux. It is from the world of minicomputers,
which is what we used to call what we now call server-class computers,
from before the primary use of them was to provide centralized infrastructure
for other "client" computers.

Before any of the other operating systems we've discussed, Unix was
developed at Bell Labs for minicomputers (see the [original source
code](https://github.com/dspinellis/unix-history-repo). Don't let the name
full you --- they're named because they're the size of a refrigerator
rather than the size of a warehouse room like a mainframe. It ran on a
single computer that had multiple *dumb terminals* connected to it, which
means that there was a non-computer device that the user would sit at,
and use a command-line interface to interact, over the phone or some other
connection, with a centralized computer that was shared with other users.

In such an environment, the laxness of MS-DOS or Windows 3.1 was simply
unacceptable. While security against malicious users was not necessarily
important, depending on your user-base, there needed to be some level
of robustness against ill-behaved programs, especially as at the time,
most computer users would regularly write new programs that could easily
behave poorly, as they were still being developed.

More importantly, programs would often have to bulk-process data. On the
spectrum of "consumer interaction" to "serious work," these early minicomputers
were very much on the side of "serious work" in their common use cases. You
might leave a program running for hours as it processed a large bulk of
data. You didn't want to have to worry about letting other users' programs
get a chance to run --- at the very least, you didn't want to have to
put active effort into making it possible. It would be inconvenient.

On the hardware side, these computers' processors, like processors
on microcomputers (as personal desktop and laptop computers were once
called), processed one series of instructions at a time. Something had
to be done to give each of the users the illusion that they were the
only one running their tasks on the computer.

If a process --- meaning a currently active instance of a user running a
program --- was waiting for more data, because it had requested a read
from the operating system (which mediated all reads from files or any
terminal), it was similar to the cooperative situation: the operating
system would suspend or *block* the execution of the current process,
and schedule it again when the read had completed, perhaps in response
to a terminal user hitting the `[Enter]` key.

But there could be long gaps between when a process would enter
into a *blocked* state like this. A user could try to calculate
a million digits of Pi. On Mac OS System 7, some sort of
yield function would have to be called from time to time, to give other events
a chance to be handled, but ideally we don't want that complexity to
be passed onto the application programmer.

Instead, before letting a process run on the processor, the operating
system will first set a timer in the hardware. When the timer goes
off, it will cause a *timer interrupt*, where the processor will stop
what it's doing and run an operating system procedure instead. That
operating system procedure will suspend the currently running process,
using features of the processor to make it so that when the process is
resumed, it is almost impossible for the user --- or even for the program
--- to detect that it had ever been interrupted.

In that case, while we hope that only one user is running a complicated
task at a time, even when multiple are, their long-running tasks simply
split the processor 50/50 --- or in some other proportion deemed fair
by the system's *scheduler*.

For every purpose but speed, however, the user has the illusion that
they're the only one using the computer, although in fact many users
might be using it at the same time. Just as sharing a disk was the
primary feature of MS-DOS, splitting processor time was the primary
feature of Unix, as evidenced by its original full name, the
"Unix Time-Sharing System."

Time sharing was often, but not always, paired with *memory protection*,
the idea that a process was limited in what memory it could modify, and
isolated from other processes. This was a feature that most minicomputers
had, but that it took a longer time to mature on microcomputers. This
feature usually goes hand-in-hand with a mechanism to force programs to
interact with hardware through the operating system, which also requires
hardware support, known in the Intel universe --- appropriately --- as
*protected mode*. MS-DOS did not run in protected mode. Windows 3 could.
Windows 95 always did.

There were other time-sharing systems of that time, but Unix was
one of the most famous, partially because it has survived in
continuous evolution to this day. Its off-brand open source clone,
Linux, is the most popular OS for servers as well as part of the
Android operating system for mobile devices. One of the more popular
workstation operating systems, macOS, is nowadays also a fully licensed
brand-name Unix.

I bring up Unix to show that time-sharing features pre-date MS-DOS
and much of the microcomputer era. They were considered overkill for
microcomputers while they were still underpowered, but they existed in
other contexts. At the time, the focus was more on supporting multiple
simultaneous users --- the fact that a single user might be able to
run multiple processes at once was a minor side benefit. After all,
these systems were mostly command-line based, and it was only possible
for a user to interact with one process at a time (per terminal), so
besides background computation (which some users did really care about),
it didn't have the same immediate practical use as being able to edit
your Word document while playing music.

So why did cooperative operating systems ever exist, if Unix predates
Windows 3.1 and MacOS System 7? Well, they existed in different
domains. Preemptive multitasking was difficult to program, and was
mostly available on operating systems for minicomputers --- more powerful
systems than individuals could generally own --- or else expensive
desktop computers known as "workstations" for particular
specialized jobs.

The operating system, is, after all, about coordinating between
programs in sharing hardware resources. It makes sense that
what those hardware resources are should influence operating
system design. When it is a single terminal and no disk, you
barely need an operating system, but when it is a graphical
user interface, you need more of one, and when it is several
terminals, you have different needs. Nowadays, we expect a
lot out of simple devices, beyond what would be necessary
to get good use out of them, but in the past, the hardware
(and human/programmer) resources were not not as up to the
challenge.

Modern operating systems combine
all of these concepts, and provide graphical user interfaces
while using all the technical advantage of time-sharing and
memory protection, and more can be read about them in
the [next post](/posts/current_os/).
