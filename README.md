# Holochain

 ![Code Status](https://img.shields.io/badge/Code-Pre--Alpha-orange.svg) [![Travis](https://img.shields.io/travis/metacurrency/holochain.svg)](https://travis-ci.org/metacurrency/holochain) [![Go Report Card](https://goreportcard.com/badge/github.com/metacurrency/holochain)](https://goreportcard.com/report/github.com/metacurrency/holochain) [![Gitter](https://badges.gitter.im/metacurrency/holochain.svg)](https://gitter.im/metacurrency/holochain?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=body_badge) [![In Progress](https://badge.waffle.io/metacurrency/holochain.svg?label=in%20progress&title=In%20Progress)](http://waffle.io/metacurrency/holochain)

**Holographic storage for distributed applications.** A holochain is a monotonic distributed hash table (DHT) where every node enforces validation rules on data before publishing that data against the signed chains where the data originated.

In other words, a holochain functions very much **like a blockchain without bottlenecks** when it comes to enforcing validation rules, but is designed to  be fully distributed with each node only needing to hold a small portion of the data instead of everything needing a full copy of a global ledger. This makes it feasible to run blockchain-like applications on devices as lightweight as mobile phones.

**[Code Status:](https://github.com/metacurrency/holochain/milestones?direction=asc&sort=due_date&state=all)** Active development for **proof-of-concept stage**. Pre-alpha. Not for production use. We still expect to destructively restructure data chains at this time.
<br/>

| Holochain Links: | [FAQ](https://github.com/metacurrency/holochain/wiki/FAQ) | [Developer Wiki](https://github.com/metacurrency/holochain/wiki) | [White Paper](http://ceptr.org/projects/holochain) | [GoDocs](https://godoc.org/github.com/metacurrency/holochain) |
|---|---|---|---|---|

**Table of Contents**
<!-- TOC START min:2 max:4 link:true update:true -->
  - [Installation](#installation)
  - [Usage](#usage)
    - [Starting a Holochain](#starting-a-holochain)
    - [1. Initializing Holochain Service for the First Time](#1-initializing-holochain-service-for-the-first-time)
    - [2. Getting Application DNA](#2-getting-application-dna)
    - [3. Generate New Chain](#3-generate-new-chain)
    - [4. Testing your Application](#4-testing-your-application)
    - [5. Launching the Holochain Server](#5-launching-the-holochain-server)
      - [Other Useful Commands](#other-useful-commands)
      - [File Locations](#file-locations)
  - [Architecture Overview and Documentation](#architecture-overview-and-documentation)
  - [Development --](#development---)
    - [Dependencies](#dependencies)
    - [Tests](#tests)
    - [Contributor Guidelines](#contributor-guidelines)
      - [Tech](#tech)
      - [Social --](#social---)
  - [License](#license)
  - [Acknowledgements](#acknowledgements)

<!-- TOC END -->

## Installation

### Windows Installation Prep
1. **Install [Windows git](https://git-scm.com/downloads).** Be sure to select the appropriate options so that git is accessible from the Windows command line.
1. **Install [GnuWin32 make](http://gnuwin32.sourceforge.net/packages/make.htm).** Add C:\Program Files (x86)\GnuWin32\bin to your PATHS directory. (Make sure C:\go\bin is in your PATHS directory already, too).  **ALTERNATIVE: Install [MinGW](http://mingw.org/wiki/Getting_Started).**  This is optional and will give you a GDB debugging environment for your Go work if you use the LiteIDE for Go.
1. **Add the Paths to access git and make.** Click start and type "Environment variables" until you see the Control Panel option. Select the "Edit system environment variables" option and the window will open.  Click the "Environment Variables" button.  Under "System Variables," click "New..." and put GOPATH as the name, and the path to your Go workspace (usually "c:\Users\<username>\go").  
1. **Ensure that GOROOT is set** (usually to "C:\Go"). For more information, see [How to Write Go Code](https://golang.org/doc/code.html#GOPATH)
1. **Add bin directories to PATH.** Double-click PATH under System Variables, and click New in the window that pops up. Add the path to go's bin directory as the value (usually C:\Users\<username>\go\bin). This will allow you to run compiled executables from anywhere in the Windows command line.  Now click New again and add the path to your GnuWin32 (or MinGW) make bin directory (usually C:\Program Files (x86)\GnuWin32\bin).
1. **Ensure a functioning Command Prompt.** Start a Command Prompt and type ```go version```.  You should see the version of go presented.  Type ```git version``` and you should see the version of git presented. Type ```make -v``` and you should see the version of make presented.  If any of these fail, close your Command Prompt and check your System Environment Variables above.  Then try starting the Command Prompt again, and try again.  Type ```set PATH``` and ```set GO``` to examine the values from the Command Prompt.

### Holochains Installation   
    
1. Make sure you have a working environment set up for the Go language version 1.7 or later. [See the installation instructions for Go](http://golang.org/doc/install.html).
2. Follow their instructions on the above doc page for **exporting** your **$GOPATH** and adding your go/bin directory to your search **PATH** for programs. Almost all installation problems reported, stem from skipping one of these path related steps.
3. Install the [gx package manager](https://github.com/whyrusleeping/gx):
```
$ go get -u github.com/whyrusleeping/gx
```
4. Then you can install the holochain command line interface with:
```
$ go get -d github.com/metacurrency/holochain
$ cd $GOPATH/src/github.com/metacurrency/holochain
$ make
```

Make sure your `PATH` includes the `$GOPATH/bin` directory so the program it builds can be easily called:
```
$ export PATH=$PATH:$GOPATH/bin
```

## Usage
Since holochain is essentially a data integrity engine intended to be used by distributed applications, you will normally only do some basic setup and maintenance through the command line.

Once you've gotten everything working as described above you can execute some basic holochain commands from the command line like this: ``` hc help ```

And you can get help on specific sub commands with ```hc <cmd> help```. For example: ```hc gen help```

### Starting a Holochain
You've installed and built a distributed data engine, but you don't have a data application running in it yet. Here's the basic flow involved in getting a chain running:

1. ```hc init```
2. ```hc clone```
3. ```hc gen chain```
4. ```hc test```
4. ```hc serve```

Details of each of these steps are below...

### 1. Initializing Holochain Service for the First Time
The first time the holochain service is run, you need to create your default public/private keys, set up config files and directories, and set a default identity token for your participation on chains. As a general user, you should only need to do this once, but as a developer, you will need to do this if you remove your ```.holochain``` directory during testing and such.

Here's a full example of the initialization command, just substitute your own email address.

    hc init 'pebbles@flintstone.com'

### 2. Getting Application DNA
You can use a pre-existing holochain application configuration by replacing SOURCE with path for loading existing application files. You can source from files anywhere such as from a git repo you've cloned, from a live chain you're already running in your ```.holochain``` directory, or one of the examples included in the holochain repository.

    hc clone <SOURCE_PATH> <NAME_FOR_NEW_HOLOCHAIN>

For example: ```hc clone ./examples/sample sample```

Before you launch your chain, this is the chance for you to customize the application settings like the NAME, and the UUID

### 3. Generate New Chain

After you have cloned and/or completed development for your chain, you need to generate the genesis entries which start your new chain. The first entry is the DNA which is the hash of all the application code. This confirms every person's chain starts with the the same code/DNA. The second block registers your keys so you have an address, identity, and signing keys for communicating on the chain.

    hc gen chain <HOLOCHAIN_NAME>

### 4. Testing your Application
We have designed holochains to function around test-driven development, so each developer should have tests to confirm that you've built a functioning chain. Run the tests with;

    hc test <HOLOCHAIN_NAME>

If the tests fail, then you know your application DNA is broken and you should not proceed thinking that your system is going to work. If you're a developer, you should be running this command as you make changes to your holochain DNA files to leverage test-driven development. And obviously, please do not send out applications that don't pass their own tests.

### 5. Launching the Holochain Server
Holochains service function requests via local web sockets. This let's interface developers have a lot of freedom to build html / javascript files and drop them in that chain's UI directory. You launch the service to listen on the socket on localhost with:

    hc serve <HOLOCHAIN_NAME> [<PORT>]

In a web browser you can go to ```localhost:3141``` (or whatever PORT you served it under) to access UI files and send and receive JSON with exposed application functions

#### Other Useful Commands
 * ```hc status``` to view all the chains on your system and their status
 * ```hc dump <HOLOCHAIN_NAME>``` to can inspect the contents of your local chain

#### File Locations
By default `hc` stores all holochain data and configuration files to the `~/.holochain` directory.  You can override this with the -path flag or by setting the `HOLOPATH` environment variable, e.g.:

    hc -path ~/mychains init '<my@other.identity>'
    HOLOPATH=~/mychains hc

You can use the form: ```hc -path=/your/path/here``` but you must use the absolute path, as shell substitutions will not happen

## Architecture Overview and Documentation
Start in the [Holochain Wiki](https://github.com/metacurrency/holochain/wiki), and hopefully it will keep growing with good development resources.

You can also find the [auto-generated Reference API for Holochain on GoDocs](https://godoc.org/github.com/metacurrency/holochain)

## Development -- [![In Progress](https://badge.waffle.io/metacurrency/holochain.svg?label=in%20progress&title=In%20Progress)](http://waffle.io/metacurrency/holochain)
We accept Pull Requests and welcome your participation.
 * [Milestones](https://github.com/metacurrency/holochain/milestones?direction=asc&sort=due_date&state=all) and progress on Roadmap
 * Kanban on [Waffle](https://waffle.io/metacurrency/holochain) of GitHub issues
 * Or [chat with us on gitter](https://gitter.im/metacurrency/holochain)

[![Throughput Graph](http://graphs.waffle.io/metacurrency/holochain/throughput.svg)](https://waffle.io/metacurrency/holochain/metrics)

### Dependencies

This project depends on various parts of [libp2p](https://github.com/libp2p/go-libp2p), which uses the [gx](https://github.com/whyrusleeping/gx) package manager.  This means that installation doesn't follow the normal "go get" process but instead also requires a make step.  Thus, to install the code and dependencies run:

    go get github.com/metacurrency/holochain/
    make deps

If you already installed the hc command line interface the dependencies will have been installed, and this step is unnecessary.

Note that `make` and `make deps` have a side-effect of re-writing some of the imports in various files.  This is how `gx` handles dependencies on specific versions of go imports.  But this means that when you are ready to make commits to your repo, you must undo these re-writes so they don't get committed to the repo.  You can do this with:

    make publish

After you have made your commit and are ready to continue working, you can redo those rewrites without re-running the full dependency install with:

    make work

### Tests

To compile and run all the tests:

    cd $GOPATH/github.com/metacurrency/holochain
    make test

Or if you have already done the initial `make` or `make deps` step, you can simply use `go test` as usual.

### Contributor Guidelines

#### Tech
* We use **test driven development**. Adding a new function or feature, should mean you've added the tests that make sure it works.
* Set your editor to automatically use [gofmt](https://blog.golang.org/go-fmt-your-code) on save so there's no wasted discussion on proper indentation of brace style!
* [Contact us](https://gitter.im/metacurrency/holochain) to set up a **pair coding session** with one of our developers to learn the lay of the land
* **join our dev documentation calls** twice weekly on Tuesdays and Fridays.

#### Social -- [![Twitter Follow](https://img.shields.io/twitter/follow/holochain.svg?style=social&label=Follow)](https://twitter.com/holochain)
<!-- * Protocols for Inclusion. -->
We are committed to foster a vibrant thriving community, including growing a culture that breaks cycles of marginalization and dominance behavior. In support of this, some open source communities adopt [Codes of Conduct](http://contributor-covenant.org/version/1/3/0/).  We are still working on our social protocols, and empower each team to describe its own <i>Protocols for Inclusion</i>.  Until our teams have published their guidelines, please use the link above as a general guideline.

## License
[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](http://www.gnu.org/licenses/gpl-3.0)  Copyright (C) 2017, The MetaCurrency Project (Eric Harris-Braun, Arthur Brock, et. al.)

This program is free software: you can redistribute it and/or modify it under the terms of the license provided in the LICENSE file (GPLv3).  This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

**Note:** We are considering other 'looser' licensing options (like MIT license) but at this stage are using GPL while we're getting the matter sorted out.

## Acknowledgements
* **MetaCurrency & Ceptr**: Holochains are a sub-project of [Ceptr](http://ceptr.org) which is a semantic, distributed computing platform under development by the [MetaCurrency Project](http://metacurrency.org).
&nbsp;
* **Ian Grigg**: Some of our initial plans for this architecture were inspired in 2006 by [his paper about Triple Entry Accounting](http://iang.org/papers/triple_entry.html) and his work on [Ricardian Contracts](http://iang.org/papers/ricardian_contract.html).
&nbsp;
* **Juan Benet**: For all his work on IPFS and being a generally cool guy. Various functions like multihash, multiaddress, and such come from IPFS as well as the libP2P library which helped get peered node communications up and running.
&nbsp;
* **Crypto Pioneers** And of course the people who paved the road before us by writing good crypto libraries and *preaching the blockchain gospel*. Back in 2008, nobody understood what we were talking about when we started sharing our designs. The main reason people want it now, is because blockchains have opened their eyes to the power of decentralized architectures.
