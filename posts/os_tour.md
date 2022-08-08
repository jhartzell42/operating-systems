+++
title = "Components of a Modern Operating System"
date = "2019-07-11"
slug = "os_tour"
author = "Jimmy Hartzell"
tags = ["computers"]
showFullContent = false
draft = false
+++

In [previous](/posts/operating_system/) [posts](/posts/current_os/),
we discussed historic operating systems and where various OS features come
from, but we only gave a brief overview of how they worked.

Now that we have a modern operating system's full complement of features,
we can look at what components need to exist in a modern operating system
to get those features. As discussed with MS-DOS, an operating system,
even today, is partially code, and partially conventions, like file
formats or rules of good behavior -- the difference being, that modern
operating systems have more ability to enforce some of these
conventions.


These conventions are still important. Linux is considered a version of Unix
by the original authors of Unix --- even though for legal and trademark reasons
it is not --- not because it has any code in common (it doesn't), but because
it follows the conventions of Unix.

So on our tour we'll discuss both more concrete software components
that are a body of code, and also conventions that hold the operating
system together at various levels.

## The Kernel

One big problem with the MS-DOS model is that a program could circumvent
its interfaces. It could directly access hardware if it wanted
to, without regard to the OS's file system code, setting the file system
conventions in stone. A program could install your own procedures to run when
hardware events happened, its own *interrupt handlers*, and the system
wouldn't stop you.

This wasn't really a limitation of MS-DOS per se, but of the 8086,
the processor MS-DOS was designed for. If code is running on an
8086, it can execute any of an 8086's instructions, no matter what.
A more modern processor -- including Intel's later processors and
therefore most of the processors MS-DOS ran on in practice --
has a distinction between *user* mode and a *supervisor mode*, which
will only allow hardware access to take place while the
processor is in the supervisor mode (also known as *kernel mode*).

Application code, regular program code, will all run in user mode.
A lot of operating system code can as well: How much code should
be actually run in kernel mode as opposed to user mode is a
complicated design decision. Certain instructions in the processor
are only allowed in kernel mode, including those that control what
memory is *mapped*, or currently accessible, those that install
interrupt handlers, and those that control which pieces of hardware
the processor is currently permitted to send data to.

In MS-DOS, all code was functionally in kernel mode -- or more precisely,
in a legacy mode of the Intel processor that emulated a time when the
distinction didn't exist, and all instructions were always allowed. A
separate mode, referenced above, put the processor into a different legacy
mode where it also acted like an 8086, but invoked special procedures
whenever the program executed a privileged instruction, basically allowing
MS-DOS to run inside a sandbox inside a larger operating system (I've
used both Windows and Linux as the larger operating system in this model).

Unlike MS-DOS, a modern operating system will have controls on what is
allowed to run in kernel mode, and everything else must run instead in
user mode. The body of code that is intended to run in kernel mode is
known as *the kernel*, or *kernel code*. If someone asks you what an
operating system kernel is, this is the answer --- the set of code that
runs in kernel mode. It might be stored in multiple files, it might be
all in one file, and it might be divided into internal components with
different names, but that is what the kernel is.

So, if only the kernel can access hardware directly, and most code isn't
allowed to be in the kernel, then how does a normal application access
the hardware? Well, instead of accessing it directly, the application
must ask the operating system to do the thing on its behalf. Just as
the operating system can install procedures as interrupt handlers,
for the processor to trigger in case of hardware events, it can
install system call handlers, procedures that run in kernel mode
but can be invoked in user mode. These procedures will be designed
to make sure that the user program in question is accessing the
hardware in an acceptable way, and only perform the operation
if it is allowed --- possibly, there will be no reasonable
way for the program to even request an impermissable hardware
operation.

This is a key distinction between MS-DOS and even older Mac operating
systems: whereas all operating systems provide abstractions, those with
an OS kernel can provide _mandatory_ abstractions. This means that,
if you want to support new features, you can change what the system
calls do, and all programs will automatically adapt to it. If your
file system is suddenly stored over the network, programs won't get
tripped up trying to access the hard drive directly. The operating
system can insert itself at the level of the system call interface
and redirect your request to the network instead --- if the
system call interface is well-designed.

## The Application Binary Interface

So let's say you have a Windows program, and you want to run it on Linux.
Or you have a Linux program, and you want to run it on macOS, which
are both Unixes and have a better chance of being compatible. It won't
work --- certainly not "out of the box."

Why? Well, one reason is mentioned above. Different operating systems
provide different ways of organizing the functionality of the computer
into system calls. They provide different abstractions, which are nowadays
mandatory.

For example, on Windows, different drives use different letters, and
volumes shared over the network are also assigned letters, e.g. the famous
`C:` drive, or `A:` for floppies, or `X:` maybe for a shared drive. On
Unixes, different volumes --- Unix doesn't use the word "drive" as often
--- are assigned different *mount points* within the system.  One volume
might be `/`, and another at `/home`, and another `/mnt/network`, and
it would provide the illusion of one unified hierarchical filesystem.
Imagine if you had --- as a simplified example --- a system call to
assign a drive letter to a network share. This would make sense with
the Windows abstraction, but what would it even mean on Linux?

Another reason has to do with how programs are stored on the drive.
Programs are not just a list of instructions for the processor.
They usually have to be loaded at a particular address. Memory
must be mapped for them to store their variables --- and how much
memory varies program by program. They have to load libraries
of other procedures, which may be stored separately through
*dynamic linking* in a *shared library* (Unix terminology, `.so`)
or a *dynamically loadable library* (Windows terminology, `.dll`),
which is also going to be mapped at a certain address in
memory according to arcane rules.

Different operating systems have different *binary file formats*, or
formats for storing programs (which are often called *binaries* when stored
on disk, although everything a disk stores is in binary).  Linux has
ELF (Executable and Linkable Format, which can use DWARF to store its debugging
information), Windows has PE (standing for portable executable, which falsely
implies it runs on more systems besides just Windows). Different Unix
varieties have different binary file formats --- it's something that
evolves over time. Some operating systems --- many operating systems +++
have different binary formats supported, for backwards-compatibility,
or for simulating other operating system, or even for different types
of programs or programs written in different program languages.

The combination of the set of available system calls, the available
libraries on the system, and the format of the binaries, constitute
the main blocker to compatibility between operating systems, the
*ABI* or *application binary interface*, an acronym or phrase that is
intended to sum up everything that needs to match for *binary
compatibility*, the ability to run binaries (compiled programs
as they are usually stored for running) from one system on
another.

## The Application Programming Interface(s)

There are other kinds of compatibility. Even though you can't take the
Windows version of a program and run it on macOS, we see plenty of programs
that have versions available, right on their website, for both Windows and
macOS. Similarly, most phone apps are available in both the iPhone and Android
stores.

In some cases, that's because there's two applications, written by
different teams, that solve the same problem (and have the same branding)
or interact with the same servers (which run on Linux and where all the
complex stuff happens anyway). But in others, it is substantially the
same program that is run on both systems.

In many cases, though, that's because the versions were written sharing
a lot of the same *source code*, with a layer of software interfacing
between that and the specific operating systems in question. This
might be because there were different teams (or people) who maintained
compatibility layers proprietary to that company (this is what many
traditional software vendors do and have done in the past). Nowadays,
it is more likely because there was a programming language that has
implementations available on both platforms, and versions of the same
library functions available for each (which is what Java was originally
famous for and what Python does today).

This is fairly common for relatively new programming languages, where
the program language was written after the operating system was already
around, and where part of the point of the programming langauge is to
support multiple operating systems for your programs. For programming in
an operating systems "native language," so to speak -- for programming
in C on Linux or Objective-C on macOS, it's a bit harder: An Objective-C
macOS program is unlikely to be particularly portable to anything (except
maybe iOS).

There are some exceptions to this. A program written for Linux can usually
be made to run on macOS, because of their common Unix heritage. Even
though Linux and macOS have different ABIs or application _binary_
interfaces, they have very similar *APIs*, which stands for application
_programming_ interface (NB: This term means something different in a
modern, web programming context). This means that, although they are not
very binary compatible, they are *source compatible*, or close to it,
which is to say, that there are few changes to the source code you would
have to make to a Linux C-based program to make it work on macOS. It
might be invoking different system calls with different identification
numbers when you write the code to open and read a file, but that code
looks exactly identical on both platforms, possibly something like this:

```
    // Simultaneously both Linux and macOS C code
    int file_descriptor = open(filename, O_RDONLY);
    ssize_t res = read(file_descriptor, buffer, sizeof buffer);
```

As you might have picked up, this applies to only a subset of the
functionality. Any GUI-related code would not enjoy this level of
portability --- macOS and Linux have very different GUIs. More likely
this is code intended to be primary run on servers (and perhaps run
on a Mac for testing), or code used by programmers (like `git` and
other development tools designed to be run from the command line)
or by scientists or other researchers (like the non-GUI components
of Matlab and R or even Python).

The baseline API that all Unix-like operating systems have in common is
called POSIX. Operating systems are certified as brand-name Unixes based
on a bigger API specification, with more functions and more requirements,
called X/Open --- which is to say that Unix is defined not by where the
code originated nor by its ABI, but rather by its C programming API. To
be clear, an operating system based on Linux could probably pass X/Open
and become legally a Unix, but nobody has decided to spend the time and
money to try and make this certification happen. It is the fact that
it is as close as it is that leads many of the original developers of
Unix to consider Linux "a Unix," as it is this API that ties the Unix
family together.

The Unix/Linux API is so important that Microsoft needed to add it to
Windows and that macOS's native use of it is considered a selling point,
especially for developers. This is because a lot of server software
and programmer tools assumes this Unix API (as well as, for example,
Unix filesystem conventions), or else it assumes Linux which has few
enough peculiar features to make much of a difference. Most users are
isolated from this, but anyone who has to write software to run on servers
(which is most programmers) or use programmer tools (which is
all programmers) is very keenly aware of this.

This Unix API is a core API provided by the operating system itself,
the official, default way for applications to be written, but the other
programming interfaces discussed above are also APIs. That is to say, Java
comes with its own API that it brings to every operating system it runs
on, leading to it its once-famous "write once, run everywhere" slogan.

The most important API for application compatibility today is something
irrelevant to most of this discussion though, and relatively new
to operating system history. Most applications that run on your
computer today run in Javascript in the very controlled environment
of a web browser. Part of what a web browser does is provide a stable,
cross-platform (that is, multi-operating system) API for the portion of
a web application that runs on each local computer. This interface is
so important that many modern apps for phone and desktop are internally
implemented as running inside a web browser, or something that resembles
a web browser in more or fewer ways.

## The System Library/Libraries

We spoke in the last section about the POSIX or Unix APIs. There are
a lot of functions that a Unix-like operating system is expected to
provide functionality for, in a lot of domains. Some, like opening or
reading files, more or less have to be implemented as system calls,
at least the most basic versions of them. Others, like calculating a
square root, are simply procedures that run in user mode. Still others,
like printing a number to the console, have to involve some system calls
(to output text to the screen) but also some computation appropriate
for user mode (to convert the number into a string of digits).

To provide these functions, Unix-like systems will provide their
own version of the C standard library. On most Unix systems, this is
maintained by the same organization that maintains the kernel, with Linux
as the major exception. The set of POSIX APIs that a Unix will maintain is
implemented through the standard library --- some of them system calls,
some of them implemented in user mode, and the programmer doesn't have
to care which.

In fact, between versions of the same operating system, and certainly
between different operating systems, what used to be a system call might
become a wrapper around a new, more advanced system call interface, where
basically the library is providing compatibility with other versions
of the same operating system. This is especially important in Unix, as
there's a lot of calls descended from different branches of the family
tree with slightly different *semantics*, or subtleties of meaning,
all of which are used by modern programmers, who can use whichever is
more convenient to them or simply preferred.

The library enables source compatibility and API compatibility, even
in situations where the kernel itself is much more particular about
its system calls. The question is, where does the ABI compatibility
layer go? On Linux, the kernel itself is responsible that its updates
don't break working programs, and its founding and lead developer Linus
Torvalds is adament and dictatorial --- sometimes abusively so --- about
this rule.  If you want a system call to behave differently, what you
do in these situations is actually make a new system call that behaves
the new way, and leave the old system call available at the old number
in case a program wants to use it.

However, all modern operating systems support dynamic linking.
This means that the libraries and the main program binary are stored
in separate files, and the main program binary specifies the _names_
of the functions it calls, rather than using numbers. If all programs
use dynamic linking, and only call system calls through the library,
you can update the library to use a different system call interface,
and change the kernel along with it. This is what macOS requires +++
while it is technically possible on macOS to bypass the library to call a
system call, the attitude is, that if you do that, you should not expect
your program to work as expected. The operating system will still ensure
it won't break other programs, but will not guarantee your program to
behave the same from version to version.

These are two vastly different approaches to maintaining ABI
compatibility. In making the standard library part of the ABI, macOS
doesn't allow *static linking*, where all code in a process comes from a
single file and a copy of the libraries are placed into the main binary
when you compile it. It's not only not recommended --- by default, it
will not even run statically compiled binaries. If you want to have an
alternative version of the C library, you can't.  If you're writing in
another programming language that doesn't work like C, you still have
to go through the C library to talk to the operating system, which isn't
written necessarily with other programming languages in mind.

But, the kernel developers have the ability to control their system call
interface better. If they want to add a new system call, they can make
their old way of doing it call the new system call, and keep the kernel
cleaner. This is important because all code in the kernel constitutes a
greater level of vulnerability --- if a kernel accesses unmapped memory,
it's generally a *kernel panic* (the Blue Screen of Death on Windows),
but a user process will just crash with a *segmentation fault*. Or worse,
if you exploit a vulnerability in the kernel and manage to manipulate
it into doing something for you it wasn't supposed to, it can literally
do everything on your computer. This as opposed to a regular program,
which still can only do things the kernel permits it to.

Linux, on the other hand, has more flexibility. You can have statically
linked files, your own C library, or libraries specialized for other
programming languages. You can avoid all the baggage that comes with
its implementation of the C library functions that have nothing to do
with system calls.

Honestly, my preference would be somewhere in between. I'd have a smaller
library than `libc` --- maybe `libsystem` --- that every program would
be automatically dynamically linked to.  This would be for things that
are usually implemented as system calls, or that were system calls in
previous versions of the operating system. These would be things that any
programming language might reasonably want to use.  The more C-specific
stuff would be relegated to its own, more general library. `libsystem`
would be as simple as possible.

Libraries that form part of the main API and that are provided with
any installation of the operating system definitely constitute
part of the operating system. Libraries that come bundled with
specific application or that exist to do certain program
tasks are not part of the operating system. Which count
as core operating system functionality is up to the operating
system vendor, but all operating systems come with at
least some libraries, to abstract their austere system
call interfaces into something that you can actually
program.

## The Shell (Command Line)

All modern (non-mobile) operating systems come with a command line
interface, whether on the computer or on the server. When you type
commands into the command line interface, it isn't the kernel itself that
reads the line you typed and decides how to proceed. Instead, a separate
process does that. This process is key to the core job of an operating
system --- letting you run multiple programs and share resources between
them --- and therefore counts as part of the operating system, but is
also not part of the kernel.

The concept of having the shell be a user process like any other was
actually one of the early innovations of Unix over other contemporary
operating systems. Before that, the kernel would often be responsible
for this. By removing it from the kernel, Unix allows different users
to use different shells, with different syntax for advanced features
like scripting or running commands conditionally on the results
of other commands. Even Windows has two shells now, traditional
`cmd` and its newer "object oriented" PowerShell.

All shells can run any terminal-oriented program, and usually can also
be used as a starting point to launch graphical programs when the system
supports it, i.e., when it's a desktop OS and not a server OS.

## The Shell (GUI)

Not all modern operating systems have GUIs. Remember that many computers
are servers (or embedded devices) where you don't actually sit at a
monitor and keyboard --- where they likely don't even have a monitor and
keyboard. But for those that do, the concept of shell can be generalized
to the program from which you run other programs.

On macOS this is called Finder, and it dates back to the early pre-Unix
Macintoshes. On Windows this is called Windows Explorer. On Linux,
and other Unixes that share Linux's user interface philosophy, there
are multiple *desktop environments* available, each of which handles
program launching differently, and each of which usually comes bundled
with a *window manager* that draws decorations around your windows and
allows you to minimize, maximize, tile or overlap them. This leads to
a rich diversity of Linux systems in their appearance and casual
use.

It is usually these graphical shells, these desktop environments,
that form your mental image of what an "operating system" is. But that
can be misleading. Linux can have one of many different graphical user
interfaces --- or none at all --- and most of what makes Linux Linux
will be the same.

So what about Linux, Android, and ChromeOS? Are they the same operating
system then, because they all share the same kernel? Linux and Android
differ at a deeper level than a shell. An Android program can't be run
on a normal Linux distribution without some layer to accommodate the
additional libraries, and vice versa. The different desktop environments
on Linux all tend to be compatible with X, a unified protocol for
UI interactions, and the many command line shells all run the same
set of command line utilities, but Android display is not done through X.

In the case of ChromeOS, the situation is different. The shell in ChromeOS
is basically the Google Chrome browser, which is the same thing that
on other platforms acts as a single program in a larger context. So
many programs nowadays are run through the medium of the browser that
it's become more than a single program in practice --- many people only
open the browser on their computer and use that for all or almost all of
their computer-oriented tasks: one tab open to GMail for their e-mail;
one tab open to Twitter; one to Spotify, to play the background music;
another to Slack to talk with their colleagues; and finally yet another
to Google Docs to do the actual productive work of writing whatever it
is they're writing. Is Chrome a shell in practice on these other operating
systems? Is it just an annoyance for some users that there is the taskbar
to switch between multiple programs, in addition to the tab bar to switch
between multiple websites? Google certainly thinks this is true for some
users, and it is for them that the Chromebook is intended.
