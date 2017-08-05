# Vagrancy

## Copyright and Licensing

### Copyright Notice

The copyright for the software, documentation, and associated files are
held by the author.

    Copyright © 2015, 2016, 2017 Todd A. Jacobs
    All rights reserved.

The AUTHORS file is also included in the source tree.

### Software License

![GPLv3 Logo][1]

The software is licensed under the [GPLv3][2]. The LICENSE file is
included in the source tree.

### README License

![Creative Commons BY-NC-SA 4.0 Logo][3]

This README is licensed under the [Creative Commons
Attribution-NonCommercial-ShareAlike 4.0 International License][4].

## Purpose

Handle common [Vagrant][7] tasks from the top level of a code base. This
is particularly useful when the Vagrant directory containing the
Vagrantfile is tucked away inside a subdirectory or submodule within the
source tree, and avoids the need to manage [VAGRANT\_CWD][8] and
relative paths for any Vagrant-related provisioning or configuration
files.

## Use Case

The general use case is managing a Vagrant-managed virtual machine from
the top level of your source tree when the actual Vagrantfile and other
related filed are nested elsewhere within the code base. For example,
given a project located at  *~/src/foo* with a Vagrantfile and other
assets located in *~/src/foo/vagrant*, you would normally have to be
inside the *vagrant* subdirectory in order to run Vagrant commands. This
can be problematic if you're trying to mount your source tree, or even
just difficult to remember to keep moving in and out of the right
directories as your work.

The Vagrancy convenience script takes care of all that, so that you can
spend more time coding and less time managing your directory stack and
Vagrant itself. However, it's not actually magic. It relies on some
sensible autodiscovery, and basically replicates things you could do
yourself with environment variables, aliases, and pushd/popd if someone
hadn't already automated it for you using Expect.

## Dependencies
If you want to use the included Expect script to manage Vagrant from the
top-level directory of your source tree, you may need some prerequisites
to run compatible versions of [Tcl][5] and [Expect][6]. Then again, you
may already have them; if you don't the script should complain
appropriately.

1. [Tcl 8.6](https://www.tcl.tk/software/tcltk/8.6.html)

    On macOS, use the ActiveTCL cask installable through Homebrew, as
    the bottled tcl-tk package is keg-only. Using `brew cask install
    caskroom/cask/tcl` will place tclsh8.6 properly into your standard
    *PATH*. Earlier versions might work with tweaking and the *try*
    library package, but are not currently supported.

1. [Expect 5.4](https://www.tcl.tk/software/tcltk/8.6.html)

    On macOS Sierra, the default system binary installed at
    */usr/local/expect* is version 5.45 and works just fine.

1. The *cmdline* package (v1.5 or higher) from the [Standard Tcl
   Library](https://www.tcl.tk/software/tcllib/). You probably already
   have this installed if you have a reasonably recent version of Tcl.

The *cmdline* library will work with earlier versions of Tcl, but the
code may also require the *try* library; this may not be included in the
core of earlier Tcl interpreters or the associated [tcllib][9]. Your
mileage (and level of Tcl-fu) may vary.

## Installation in Source Tree

Install the Vagrancy repository as a submodule below your Vagrant
environment. For example, if you've already using installed a Vagrant
submodule like [CentOS 7 LAPP Stack for QA][10] into a *vagrant*
directory at the top of your project, then installation takes just three
or four commands.

    cd vagrant

    git submodule add https://github.com/CodeGnome/vagrancy.git \
        vagrancy_module

    # link the script into the top of the source tree
    ln -s "${PWD##*/}/vagrancy_module/vagrancy.exp" ../vagrancy

    # link the script into the Vagrant directory too; it will complain
    # non-fatally if your Vagrant submodule already contains a link
    ln -s vagrancy_module/vagrancy.exp vagrancy

*NB: If the submodule is nested more deeply than one directory below the
top level, then add a few more parent directories to your link target,
e.g. `../../vagrancy`.*

Now, whether you're in the the top level of your source tree or in the
Vagrant directory, you can invoke `./vagrancy` and it will generally do
the right startup things automagically. Passing the `-help` flag will
bring up a complete set of command-line options, including flags to
suspend or halt Vagrant when not in use.

## Installing in PATH

If you're feeling more adventurious, you can also install the script in
your *PATH*, provided that you always pass a command-line argument
telling Vagrancy where your project's Vagrant directory is. You can even
use an alias or shell function to make this easier.

Consider the following example, which can be run from wherever you've
cloned the Vagrancy project:

    ln vagrancy.exp ~/bin/
    alias vagrancy="$HOME/bin/vagrancy.exp -v /path/to/vagrant/dir"
    echo `alias vagrancy` >> ~/.bashrc

## Usage

From the top level of your source tree, you can perform most common
Vagrant activities by invoking the *vagrancy* script. For example,
assuming Vagrant was installed in the expected *vagrant* directory:

`./vagrancy`
: 1. start Vagrant (if not running)
: 1. provision Vagrant (if not already provisioned)
: 1. SSH into Vagrant

`./vagrancy -s`
: suspend Vagrant

`./vagrancy -v "vagrant_environments/centos/centos7-lapp-qa"`
: change the path to the Vagrant submodule if the script can't find it

`./vagrancy -help`
: show help/usage

## Caveats

Please note that Vagrancy is primarily focused on  *starting* Vagrant
from within your source tree. Commands like `vagrant destroy` and
`vagrant box update` are deliberately not supported at this time.
However, you can those things manually when necessary. Vagrancy
automates some things for you, but you still have the full power of
Vagrant (and the underlying virtual machine platform such as VirtualBox)
to draw on if you need it.

## To-Do
Automate the installation instead of relying on this finely honed and
easy to read help file. Because automation!

[1]: http://www.gnu.org/graphics/gplv3-88x31.png
[2]: http://www.gnu.org/copyleft/gpl.html
[3]: https://licensebuttons.net/l/by-nc-sa/4.0/88x31.png
[4]: https://creativecommons.org/licenses/by-nc-sa/4.0/
[5]: https://www.tcl.tk/
[6]: https://www.nist.gov/services-resources/software/expect
[7]: https://www.vagrantup.com/
[8]: https://www.vagrantup.com/docs/other/environmental-variables.html#vagrant_cwd
[9]: https://www.tcl.tk/software/tcllib/
[10]: https://github.com/CodeGnome/centos7-lapp-qa
