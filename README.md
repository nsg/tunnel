# Tunnel

[![Snap Status](https://build.snapcraft.io/badge/nsg/tunnel.svg)](https://build.snapcraft.io/user/nsg/tunnel)

This project contains the sources of the snap `tunnel`, it's used to bridge
network segments together over the Internet.

This snap relies on the software project [tinc](http://tinc-vpn.org) that
does the actual work to mesh network segments together. Tinc has several
advanced features that is out of scope for this snap. If you disagree or
like to have something added, please open an issue.

## A little warning

This snap is based on a pre-release of Tinc 1.1. I'm using 1.1 because it
contains several improvements, like for example the invite future.
**Do not** use this in production. I'm using this my self successfully to
bridge both servers and workstations together.

The tunnel may also be restarted when snapd updates this snap, or when you
change any settings. This is expected behavior, if you can't handle rare
short disconnects please do not use this snap.

## Install

I use the build services at [build.snapcraft.io](https://build.snapcraft.io/user/nsg/tunnel)
to build this snap. Master of this repository is published to the edge
channel. Try it with `snap install --edge tunnel`.

The get the latest stable version, type `snap install tunnel`.

## Why tunnel and not just tinc?

There is a little setup you need to do like create a tinc.conf, tinc-up
and down scripts and the exchange the host keys. Configure bridges and
possible setup routing to get started.

Tinc 1.1 solves a lot of that for some use cases with the "tinc join"
feature but I feel that there still is a few pieces missing so I made
this snap.

If you need to customize the setup outside what this snap provides, I
recommend that you take your time to install tinc yourself.

## Usage

Do a `snap install tunnel` on all your systems. At least one need to
have a public exposed port. Run `tunnel` to get started.

### Connect two hosts

Let's assume that you have a server, with the hostname "server" and
a laptop with the hostname "laptop".

#### Install and setup permissions

Install it on both systems:

```
server> sudo snap install tunnel
laptop> sudo snap install tunnel
```

You now need to setup the permissions, the easiest way is just to
run this (feel free to run `tunnel.setup` and inspect the commands):

```
server> tunnel.setup | sudo bash
laptop> tunnel.setup | sudo bash
```

All done, if you need to change the port on the server, run
`tunnel config`.

#### Invite and join

Invite the laptop from the server.

```
server> sudo tunnel invite laptop
Invitation generated for laptop, this is your URL:
Trying to discover externally visible hostname...
123.45.678.9:655/gLnKky5hkJxCab_p7WR85pfYujGdqYiuWlsL9ZZ0

On the other system, type this:
sudo tunnel join 123.45.678.9:655/gLnKky5hkJxCab_p7WR85pfYujGdqYiuWlsL9ZZ0

```

Now run the suggested command on the laptop:

```
laptop> sudo tunnel join 123.45.678.9:655/gLnKky5hkJxCab_p7WR85pfYujGdqYiuWlsL9ZZ0
```

All done, `tunnel0` on the server and laptop are now connected.

#### Finally

Note that at this point you need to configure both the server and your client to
route/forward traffic over the tunnel. There is a few options for that in tunnel,
see "tunnel config".
