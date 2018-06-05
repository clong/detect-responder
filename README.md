# Detect Responder (LLMNR, NBT-NS, MDNS poisoner) with osquery

<img src="img/sample.gif">

## Overview
[![Build Status](https://circleci.com/gh/clong/detect-responder.svg?&style=shield)](https://circleci.com/gh/clong/detect-responder/tree/master)

This repo contains a python-based extension for osquery to detect active instances of Responder or any NBT-NS and LLMNR spoofers/poisoners on the network.

This extension was developed using osquery's Python bindings from https://github.com/osquery/osquery-python/

This extension was written with native Python modules to reduce the need for installing third-party Python libraries on hosts. Although it would have been cleaner and easier to use a library like Scapy, it would require installing it on every host where the extension was used.

Although many similar tools exist, most of them exist as independent scripts. This extension can take advantage of an existing osquery deployment and can provide network coverage everywhere that you have an osquery agent installed.

Note: This extension has not been tested on production networks and exists only as a proof-of-concept.

## How it works
The extension operates by sending 4 network requests:
1. A llmnr query for "wpad" to a multicast address
2. A llmnr query for a randomized 16 character name to a multicast address
3. A NBT-NS query for "WPAD" to a broadcast address
4. A NBT-NS query for a randomized 16 character name to a broadcast address

## Installation
To begin, the osquery-python package must be installed on the system. The easiest way to install it is via `$ sudo pip install osquery`.

Create a folder  called `/var/osquery/extensions` (MacOS) or `/etc/osquery/extensions` (Linux), `chmod 755` it, and copy detect_responder.ext to that directory. The extension and directory should have the following permissions:
```
$ ls -al /var/osquery/extensions
total 3336
drwxr-xr-x   4 root  wheel      128 Jun  3 14:37 .
drwxr-xr-x  15 root  wheel      480 Apr 17 10:25 ..
-rwxr-xr-x   1 root  wheel     5594 Jun  3 14:33 detect_responder.ext
```
  To configure the extension to load when osquery starts, do one of the following:
* Create a file called `extensions.load` in `/var/osquery` (MacOS) or `/etc/osquery` (Linux) and populate the file with the full path to `detect_responder.ext`
* Edit your flags file and add the following flag: `--extension /path/to/detect_responder.ext`

## Usage
Once you have verified that the extension has loaded correctly, you should be able to run `SELECT * FROM detect_responder;`. To test it, run Responder on a different host on the same network.

To test using osqueryi, run:
`sudo osqueryi --nodisable_extensions --extension /var/osquery/extensions/detect_responder.ext --verbose`

## Limitations
* This extension currently only picks the first broadcast address returned by the query. It will support multiple interfaces in the future.
* This extension can be easily fingerprinted and detected. There are no plans to modify it to be harder to profile.

## Acknowledgements
* https://blog.netspi.com/identifying-rogue-nbns-spoofers/
* https://github.com/violentlydave/Poaching-Hunting-in-an-Uncooperative-Environment/
* https://github.com/SpiderLabs/Responder
