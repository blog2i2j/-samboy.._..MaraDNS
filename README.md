# What is MaraDNS

MaraDNS is a free open-source computer program written by Sam Trenholme.

MaraDNS implements the Domain Name System (DNS), an essential internet
service. MaraDNS is open source software: This means that anyone is
free to download, use, and modify the program free of charge, as per
its license. 

People like MaraDNS because it’s small, lightweight, easy to set up,
and remarkably secure. It’s also cross platform — the program runs
both in Windows and in UNIX clones.

MaraDNS has a web page and blog at https://maradns.samiam.org.
MaraDNS’s Git tree is hosted at 
[GitHub](https://github.com/samboy/MaraDNS),
[GitLab](https://gitlab.com/maradns/maradns), 
[Bitbucket](https://bitbucket.org/maradns/maradns/),
[Codeberg](https://codeberg.org/samboy/MaraDNS),
and 
[SourceHut](https://git.sr.ht/~samiam/MaraDNS)
(Please use 
[GitHub discussions](https://github.com/samboy/MaraDNS/discussions) 
for bug reports).  

# ABOUT

MaraDNS is a small and lightweight cross-platform open-source DNS
server. The server is remarkably easy to configure for someone
comfortable editing text configuration files. MaraDNS is released under
a BSD license.

I initially wrote MaraDNS while I was a college student and a travelling
English teacher during the first 2000s decade.

Since the COVID-19 pandemic, I have been actively adding new features
to MaraDNS, most notably the new `coLunacyDNS` service which uses Lua
to customize DNS replies.

Versions of some MaraDNS programs compiled as Windows binaries
(without needing Cygwin or another POSIX emulation library) are
in the folder `maradns-win32/`

# Building MaraDNS

To build MaraDNS, one needs a POSIX system with:

* A POSIX compatible shell
* A POSIX compatible implementation of `make`, which has the POSIX202X
  feature of allowing a make target to have a `/` in it.
* Other standard POSIX utilities (`awk`, etc.)
* A current C compiler which can support, via `stdint`, 8-bit, 16-bit, 
  32-bit, and 64-bit sized integers.
* A POSIX C library with both POSIX and Berkeley socket support.

(To build some of the documentation, the non-standard but widely
 available `unix2dos` text conversion utility is used.)

Then, do this:

```
./configure
CC=cc # Change this to gcc/clang/tcc as desired
export CC
make
```

Note that most implementations of `make` will set `$CC` to `cc` if it’s
not already set, but the [POSIX spec](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/make.html)
says that the default value should be `c99`, a compiler which will not
compile MaraDNS.  A POSIX program like MaraDNS will not compile with a
strictly ISO compliant C compiler (the POSIX system calls will fail to
compile).  However, all of the C programs here compile with `gcc`,
`clang`, and `tcc`, and should compile with any reasonable POSIX C
compiler.

All of these are very standard tools which are included with the vast
majority of Linux and BSD distributions; packages usually have names like:

* `clang` (which uses `llvm`) for the C compiler
* `libc-dev` for the development C standard library, which will have sockets
* `make` for make; if one does not wish to use GNU make (the Linux standard),
  other make implementations exist, e.g. https://github.com/samboy/maramake
* POSIX compatible implementations of `sh`, `awk`, and other utilities are
  also almost always included as part of a Linux base install.

MaraDNS successfully compiles with `gcc`, `clang`, and `tcc`; it 
successfully builds with GNU make, bmake, pdpmake (as long as non-POSIX
and/or POSIX202X extensions are enabled and `CC` has the value `cc`), and 
maramake.  The version of `make` used to compile MaraDNS needs to have 
the command name `make`.  MaraDNS compiles and runs with both Busybox 
versions of the core POSIX utilities and GNU coreutils.

## Supported OSes

MaraDNS is built and runs on Ubuntu 22.04 as of late 2022.  

I currently use Ubuntu 22.04 to develop MaraDNS, both Ubuntu 22.04 and
Alpine Linux 3.14 to test that MaraDNS builds and passes all automated
regressions, and a Windows XP virtual machine to make the Windows
binaries.

### Please use systemd

While MaraDNS does have scripts for starting up MaraDNS at system boot
time on `sysvinit` systems, these scripts are no longer fully supported,
since most major Linux distributions have moved on to `systemd`, which
is a lot better than the older `sysvinit` process for starting up the
MaraDNS related services.

While `make install` is only guaranteed to get MaraDNS to start up at
system boot time on Ubuntu 22.04, it *should* do the right thing in Rocky
Linux, Debian, RedHat, CentOS, Fedora, and other Linux distributions
which use `systemd`.

There *is* some scaffolding for getting MaraDNS to run without `systemd`.
There is a helper, `duende`, to make MaraDNS a daemon on systems with
classic *NIX style init.  While, with `systemd`, it’s no longer needed
to use this Duende helper, I will keep Duende around for `sysvinit` and
some level of `OpenRC` compatibility.  There are also shell scripts for
starting MaraDNS on `sysvinit` systems which may or may not work with
other init systems.

`systemd` handles the daemonization of the MaraDNS services the way
`duende` does (did?) on `sysvinit` and similar systems.  The `systemd` way
is much simpler at the development level, since systemd now handles a lot
of complexity the old init systems forced networking services to handle.

## Important note for Windows users

Users of Microsoft Windows are better off downloading a prebuilt Windows
binary: http://maradns.samiam.org/download.html (or, look in the
folder `maradns-win32` here) 
Be sure to download the file with the .zip extension.

Only Deadwood and coLunacyDNS binaries are provided.  

Deadwood has passed Y2038 tests in Windows 10.

# What is DNS

The internet uses numbers, not names, to find computers. DNS is the
internet’s directory service: It takes a name, like “www.maradns.org”,
and converts that name in to an “IP” number that your computer can use
to connect to www.maradns.org.

DNS is one of these things many take for granted that is essential to
using today’s internet. Without DNS, the internet breaks. It is
critical that a DNS server keeps the internet working in a secure and
stable manner.

# MaraDNS' History

MaraDNS was started in 2001 in response to concerns that there were
only two freely available DNS servers (BIND and DjbDNS) at the time.
MaraDNS 1.0 was released in mid-2002, MaraDNS 1.2 was released in late
2005, MaraDNS 2.0 was released in the fall of 2010, and MaraDNS had
a version number jump up to 3.3 in 2019 in order to have the same version 
number as Deadwood.

MaraDNS 1.0 used a recursive DNS server that was implemented rather
quickly and had difficult-to-maintain code. This code was completely
rewritten for the MaraDNS 2.0 release, which now uses a separate
recursive DNS server.

# Overview

MaraDNS 3.5 consists of three primary components: A UDP-only authoritative
DNS server for hosting domains, a UDP recursive DNS server called
`Deadwood` for finding domains on the internet, and a Lua-powered DNS
server called `coLunacyDNS`. MaraDNS’ recursive DNS server `Deadwood`
shares no code with MaraDNS’ authoritative DNS server.

`coLunacyDNS` is a Lua-based name server which uses a combination of C
(for the heavy lifting of binding to DNS sockets, processing DNS requests,
and handling pending replies from upstream DNS servers) and Lua (for
deciding how to respond to a given query) to have both performance
and flexibility.

In more detail: MaraDNS has one daemon, the authoritative daemon
(called `maradns`), that provides information to recursive DNS servers
on the internet, and another daemon, the recursive daemon (called
`Deadwood`), that gets DNS information from the internet for web
browsers and other internet clients.

A simplified way to look at it: `MaraDNS` puts your web page on the
Internet; `Deadwood` looks for web pages on the Internet.

Since MaraDNS’ authoritative daemon does not support TCP, MaraDNS
includes a separate DNS-over-TCP server called `zoneserver` that
supports both standard DNS-over-TCP and DNS zone transfers.

Neither MaraDNS nor the UNIX version of Deadwood have support for
daemonization; this is handled by a separate program included with
MaraDNS called `Duende`. Deadwood's Windows port, on the other hand,
includes support for running as a Windows service.

MaraDNS also includes a simple DNS querying tool called `askmara` and
a number of other miscellaneous tools: Lua 5.1 scripts for processing
MaraDNS' documentation (since MaraDNS comes with a fork of Lua 5.1, these
scripts do not have an external dependency), some Unicode conversion
utilities, scripts for building and installing MaraDNS, automated SQA
tests, etc.

`MaraDNS` is a native UNIX program which can run in Windows via cygwin.
Both `Deadwood`, MaraDNS' recursive resolver, and `coLunacyDNS`, a DNS
server configured with a Lua 5.1 script, are cross-platform applications
with full Windows ports.

MaraDNS 2.0 has full (albeit not fully tested) IPv6 support.

# Internals

MaraDNS 3.5’s authoritative server uses code going all the way back 
to 2001. The core DNS-over-UDP server has a number of components,
including two different zone file parsers, a mararc parser, a secure
random number generator, and so on.

MaraDNS is written entirely in C. No objective C nor C++ classes are
used in MaraDNS’ code.

MaraDNS 2.0’s “Deadwood” recursive server was started in 2007 and has
far cleaner code. Its random number generator, for example, uses a
smaller, simpler, and more secure cryptographic algorithm; its
configuration file parser uses a finite state machine interpreter; its
handling of multiple simultaneous pending connections is done using
select() and a state machine instead of with threads.

# Other DNS servers

The landscape of open-source DNS servers has changed greatly since 2001
when MaraDNS was started. There are now a number of different DNS
servers still actively developed and maintained: BIND, Power DNS,
NSD/Unbound, as well as MaraDNS. DjbDNS is no longer being updated and
the unofficial forks have limited support; notably it took nearly five
months for someone to come up with a patch for CVE-2012-1191.

MaraDNS’ strength is that it’s a remarkably small, lightweight, easy to
configure, and mostly cross-platform DNS server. Deadwood is a tiny DNS
server with full recursion support, perfect for embedded systems.

MaraDNS’ weakness is that it does not have some features other DNS
servers have. For example, while Deadwood has the strongest spoof
protection available without cryptography, it does not have support for
DNSSEC.

As another example, MaraDNS does not have full zone transfer support;
while MaraDNS can both serve zones and receive external zone files from
other DNS servers, MaraDNS needs to be restarted to update its database
of DNS records.

# MaraDNS’ future

MaraDNS is a mature application.  Being open source code, the amount
of time I have to devote to MaraDNS is highly variable.  Right now,
I am concentrating my efforts to revamp MaraDNS so that it can 
continue to compile and run for as long as possible, minimizing the
number of external dependencies so that outside changes are unlikely
to break MaraDNS.

For MaraDNS to break, either the C language would have to change to
break programs that compile with few to no warnings here in the 2020s,
or the POSIX standard would have to change to the point that POSIX
compliant scripts which run here in the 2020s no longer run.  Both
of these are very unlikely to happen.

## Y2038 statement

MaraDNS is fully Y2038 compliant on systems with a 64-bit time_t.  Here
in the 2020s, even 32-bit Linux distributions, such as Alpine Linux,
have a 64-bit time_t.

Deadwood, in addition, for its Windows 32-bit binary, uses Windows
filetime to generate internal timestamps; filetime stamps will not run
over until the year 30827 or so.  

coLunacyDNS, likewise, uses Windows filetime for timestamps with its
Win32 binary.

On *NIX systems with a 32-bit `time_t`, some features which depend on 
OS-level time and date libraries are disabled.  MaraDNS has support for
showing a human readable timestamp with the `timestamp_type` parameter;
this parameter is disabled on systems with a 32-bit `time_t` since the
underlying libraries MaraDNS uses will probably fail at the Y2038 cutoff.
Likewise, MaraDNS has support for generating a human-readable SOA
serial number with the `synth_soa_serial` parameter, but this feature
is disabled if `time_t` is 32-bit.  In both cases, the feature in
question is, by default, disabled in MaraDNS, so only users who have
explicitly enabled these features will see any change in behavior.

MaraDNS has the ability to generate a synthetic SOA serial number if
a zone file does not have a SOA record.  The SOA serial is based on the
timestamp for the zone file.  If `time_t` is 32-bit, MaraDNS assumes that
the `stat` call will return a negative timestamp after the Y2038 cutoff,
and will adjust timestamps from before 2001 (the year MaraDNS was first
developed) to be after the Y2038 cutoff.  If there are systems out there
where a `stat` call for a file’s modification time fail after the Y2038
cutoff, one can avoid Y2038 issues by having a SOA record with a serial
number in zone files.  The Windows port of MaraDNS, as of 3.5.0028,
uses Windows Y2038 compliant system calls instead of POSIX calls to get
zone file timestamps.

Both Deadwood and coLunacyDNS make some effort to generate accurate
timestamps on *NIX systems with a 32-bit time_t until later than
2106; this code assumes that 32-bit systems will have the time
stamp “wrap around” after 2038 but still have the 32-bit time be 
updated.

## Cyber Resilience Act statement

MaraDNS is a project developed on a strictly volunteer, non-commercial
basis.  It has been developed outside the course of a commercial
activity, developed entirely in the Americas (i.e. *outside of Europe*)
and therefore is not subject to the restrictions or conditions of the 
proposed EU Cyber Resilience Act.  Someone selling a product that 
uses any component of MaraDNS may be subject to this act and may
need to handle any and all necessary compliance.

MaraDNS, as always, is provided free of charge but with **no warranty**.

# Updates

## 2022 Updates

MaraDNS was updated in 2022 to have its automated tests run in an Ubuntu
22.04 Docker container instead of an Ubuntu 20.04 Docker container.
The tests have also been updated to be more portable, running in both
Alpine Linux (Busybox-based Linux distro) and Ubuntu 22.04.

I also fixed a minor security issue, which also affected other DNS servers,
where a clever attacker with access to the recurisve DNS server could had
kept records in the cache longer than desired.

`min_ttl` now correctly sets a minimum TTL for direct answers to queries.
I have backported the `min_ttl` parameter to the older legacy 3.4 version
of MaraDNS.

While using Deadwood as a fully recursive server is not guaranteed to
be fully supported, I have fixed a long standing bug with how Deadwood
handled certain CNAME chains, and have added tests to make sure this
bug stays fixed.

MaraDNS no longer uses non-POSIX scripting languages not included 
with MaraDNS:

* MaraDNS’s documentation system, EJ, has been updated to use Lua 5.1
  (included with MaraDNS with the name `lunacy` in `coLunacyDNS/lunacy`)
  instead of Perl scripts.
* The old `bind2csv2.py` tool has been removed, so that MaraDNS no
  longer needs Python to run any of its components.

## 2021 Updates

Deadwood has a new parameter: `source_ip4`.  This optional parameter
is used to specify the source IP when sending queries upstream.  The
majority of users should be able to leave this untouched; this is for
cases when Deadwood is multi-homed and we need to specify which IP
to use when querying root or upstream DNS servers.

One line change to zoneserver.c to make it work better with systemd.

Synthetic IP generator example added to `coLunacyDNS`

## 2020 Updates

I have updated things so that the Git version of MaraDNS is the 
authoritative “One source of truth” for MaraDNS’s source code.
The Git code is, every time a new MaraDNS release is made,
converted in to tarballs (with full Git history) which can be
downloaded at [Sourceforge](https://sourceforge.net/projects/maradns/)
and [MaraDNS’s web page](https://maradns.samiam.org/download.html).

I have added block list support to Deadwood, to allow a large list
of host names to be blocked.

I have created a new service: `coLunacyDNS`, a simple Lua-based DNS server
which can return IPv4 (`A`) and IPv6 (`AAAA`) DNS records.  It has the
ability to query other DNS servers, and customize the answer given to
the client based on the contents of a Lua script.  All programs have IPv6
support in Linux as well as *NIX clones, and the Windows 32-bit binary of
`coLunacyDNS` has IPv6 support.

