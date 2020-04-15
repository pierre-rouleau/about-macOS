=============================
macOS Development Environment
=============================

:Time-stamp: <2020-04-15 07:53:39, updated by Pierre Rouleau>
:Copyright: Copyright © 2020 by Pierre Rouleau
:License: `MIT <../LICENSE>`_

.. contents::  **Table Of Contents**
.. sectnum::

-----------------------------------------------------------------------------------------


Shells
======

Bash Setup
----------


Convenience Command Aliases
~~~~~~~~~~~~~~~~~~~~~~~~~~~

I created a set of command alias that I use in various OSes: macOS, Linux and
Windows alike.  I stored them inside the ``.bashrc`` file which is source
by the ``.bash_profile`` to make them available to the bash shells.

The first part of those aliases are just simple commands abbreviations.

.. code:: bash


          # cd alias
          # --------
          alias ..='cd ..'
          alias ...='cd ../..'


          # Clear screen
          # ------------
          alias cls='clear'


          # Make directory
          # --------------
          alias md='mkdir'


          # Listing files: ls commands
          # --------------------------
          # l  : colored and show directories with explicit trailing '/'
          # la : shows all files, colored and show directories with explicit trailing '/'
          # ll : colored ls -l with Mac extensions shown
          # lla: colored ls -l also showing all .dot-files
          # lt : colored ls -ltr
          # lta: colored ls -ltra
          # lsd: display directories only
          #
          # ls options used:
          #  -F : diaply a dash for directory, * for executable, @ for links, etc...
          #  -G : colorize
          #  -O : display macOS flags
          #  -a : include all files with name that begin with a period.
          #
          alias l='ls -FG'
          alias la='ls -aFG'
          alias ll='ls -lFGO'
          alias lla='ls -lFGOa'
          alias lt='ls -lrtFGO'
          alias lta='ls -ltraFGO'
          alias lsd='ls -F | grep -i / | sort -f | sed -e "s~/~~g" | column'


          # Python shortcuts
          # ----------------
          alias p='python'
          alias p2='python2'
          alias p3='python3'


Environment Setup Strategy
~~~~~~~~~~~~~~~~~~~~~~~~~~

I want to keep my system PATH mostly unchanged and to a minimum, yet I want to
be able to setup specialized shells that support various programming
environments.  For example, an environment where I can use a specific version
of Erlang and another where Common Lisp is available, etc...  I don't want
them all available inside the same shell; that would make the PATH too long,
and eventually would slow the system down.  Of course I could use VMs or
OS-level virtualisation technologies like Docker, but I see those things as
complementary and what I make available here I can also use them in those
environments.

I want to be able to:

#. open a shell session that will already be specialized
#. change the specialization of the currently running shell
#. change important shell environment variables without having to restart the
   shell and with simple commands.

Here's my strategy:

#. Create a directory (``~/bin``) that will hold scripts with
   environment setup code.

   - This directory is on the PATH of my shell (as set by my
     ``~/.bash_profile``).

#. Write shell scripts in "~/bin" that setup the environments.
   These scripts all have a name that starts with "envfor-" and ends with a
   descriptive name for the environment.  For example:

   - ``envfor-ccl``
   - ``envfor-clisp``
   - ``envfor-sbcl``
   - ``envfor-erlang-20.2``
   - ``envfor-haskell``
   - ``envfor-rust``

#. Write small command aliases inside ``~/.bashrc`` that source the scripts
   stored in ``~/bin`` so I can just type the commands to specialize the shell
   for the environment I'm after.

   - The aliases have a name that starts with ``use-`` and have the same
     ending as the corresponding script.  The following aliases match the
     scripts listed above:

   - ``use-ccl``
   - ``use-clisp``
   - ``use-sbcl``
   - ``use-erlang-20.2``
   - ``use-haskell``
   - ``use-rust``

   - Note that my ``~/.bashrc`` file is sourced by my ``~/.bash_profile`` so these alias
     become available in the shells.

Notes about the macOS environment
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A couple of important points about macOS environment:

#. Apple does not distribute Erlang on their base macOS.

   - To use it you must install Erlang yourself.
   - This also means that Erlang is not on the system PATH.

#. Apple ships macOS with the following PATH: ``/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin``

   - The ``/usr/bin``, ``/bin``, ``/usr/sbin`` and ``/sbin`` directories are
     protected by Apples' `System Integrity Protection`_ since OS X El
     Capitan.  So you can't store anything in those directories.  Only Apple
     can as part as the OS installation.

   - ``/usr/local`` directory is empty, except for the file ``.com.apple.installer.keep``

     - Homebrew creates and stores files and symlinks inside the
       subdirectories of ``/usr/local``, with several symlinks to the
       executable files inside the ``/usr/local/bin``.  Since this directory
       is already in the default PATH, these programs become available on the
       standard shells.
     - Once a file (or symlink to a file) is stored by Homebrew in
       ``/usr/local/bin`` it becomes available on the command line or any
       process launched by it (unless it modifies the PATH environment variable).

.. _System Integrity Protection: https://en.wikipedia.org/wiki/System_Integrity_Protection



Environment for Erlang
----------------------

Installing Erlang, LFE and Elixir with Homebrew
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If all you want is being able to use Erlang_, Elixir_ or LFE_ (Lisp Flavored
Erlang), 3 of the programming languages running on the
`BEAM Virtual Machine`_, and you just want to use one version, probably the
latest stable version available, then installing the software with the
`Homebrew package manager`_ is all you need.  Homebrew is a popular package
manager for the mac, and is now also supporting Linux and has beta support for
the Windows Subsystem for Linux.  There are other package managers for macOS
like Fink and MacPorts but Homebrew is the most popular these days and works
fine.

First, if you never used Homebrew before, then  read the
instructions on how to install it on `Homebrew home page`_: it's just a curl
command to run and then you follow the instructions.

Then you can use the following instructions.

- ``brew search erlang``  searches for the *recipe* to install Erlang.
- ``brew info erlang`` provides information about the available Erlang package
  the dependencies and whether anything is currently installed on the system.
- ``brew install erlang`` installs Erlang.

When installing with Homebrew, *always* review the output printed by the
command. Look for any failures, warnings and caveats that might occur and
follow the instructions to repair them if there is any.

You can also install LFE_ and Elixir_ with the following commands:

- ``brew install lfe``
- ``brew install elixir``

Homebrew will store the files inside the ``/usr/local/Cellar`` directory and
will create symlinks for the executable files in ``/usr/local/bin`` making
them available to your shell.

The man files for lfe and elixir are available, but not for Erlang, as
described by a caveat displayed when Homebrew installs Erlang::

        ==> Caveats
        Man pages can be found in:
          /usr/local/opt/erlang/lib/erlang/man

        Access them with `erl -man`, or add this directory to MANPATH.

Adding the path to MANPATH and being able to use the man command directly is
better.  It also allows using man within Emacs, which provides extra
functionality.   Also, it's possible that we'll need other versions of Erlang
later for testing purposes.  So having a specialized shell for the version of
Erlang installed with Homebrew will help now and in the future.

The version of Erlang I just installed happens to be Erlang 22.3.2
We can see the symlink in ``/usr/local``::

    $ cd /usr/local/bin
    $ ls -l erl
    lrwxr-xr-x  1 user  admin  31 14 Apr 13:49 erl -> ../Cellar/erlang/22.3.2/bin/erl

and the man files for that version are in::

    $ cd /usr/local/opt
    $ ls -l erlang
    lrwxr-xr-x  1 user  admin  23 14 Apr 13:49 erlang -> ../Cellar/erlang/22.3.2

To ensure future upgrade of Erlang with Homebrew will not change our ability
to access Erlang 22.3 man files, we can use the real directory name or even
copy it somewhere else.  The ``~/.local/share`` is a good directory for that. For
now, I'll just use the current directory name and will create a script for
Erlang 22.3.2.

First, the script ``~/bin/envfor-erlang-22-3-2`` contains the required logic:

.. code:: bash

    #!/usr/bin/env bash
    # -----------------------------------------------------------------------------
    # Install the environment for Erlang 22.3.2
    #
    # This file *must* be sourced.
    #
    # The easiest way to use it is to execute: use-erlang
    #
    #
    # It sets up:
    # - the executable path for Erlang 22.3.2 (in fact nothing done; it's already there)
    # - the MANPATH for Erlang 22.3.2 man pages (while keeping access for others)
    # - DIR_ERLANG_DEV environment variable: flag and root of Erlang developed code
    #
    # This protects against multiple execution (via the DIR_ERLANG_DEV envvar).
    #
    # Assumes Erlang 22.3.2 installed with Homebrew:
    # - Erlang 22.3.2 executable files are accessible via symlinks in /usr/local/bin/
    # - Erlang 22.3.2 man files are located in /usr/local/Cellar/erlang/22.3.2/lib/erlang/man

    # -----------------------------------------------------------------------------
    if [ "$DIR_ERLANG_DEV" == "" ]; then
        export DIR_ERLANG_DEV="$HOME/dev/erlang"
        MANPATH=`manpath`:/usr/local/Cellar/erlang/22.3.2/lib/erlang/man
        export MANPATH
        echo "+ Erlang 22.3.2 environment set."
    else
        echo "! Erlang environment was already set for this shell: nothing done this time."
    fi

    # -----------------------------------------------------------------------------


Then, to simplify executing the script, the following alias is stored inside
the ``~/.basrc`` file:

.. code:: bash

          alias use-erlang='source envfor-erlang-22-3-2'

With these it is now possible to activate a Bash shell to get all it needs, as
is shown in the following session::

.. code:: bash

          >computer@[~]
          > man -w erl
          No manual entry for erl
          >computer@[~]
          > which erl
          /usr/local/bin/erl
          >computer@[~]
          > use-erlang
          + Erlang 22.3.2 environment set.
          >computer@[~]
          > which erl
          /usr/local/bin/erl
          >computer@[~]
          > man -w erl
          /usr/local/Cellar/erlang/22.3.2/lib/erlang/man/man1/erl.1
          >computer@[~]
          > man -w lists
          /usr/local/Cellar/erlang/22.3.2/lib/erlang/man/man3/lists.3
          >computer@[~]
          >

And then we can run the Erlang shell, using the ``code:root_dir()`` function
to display the root of the Erlang executable:

.. code:: bash

        >computer@[~]
        > erl
        Erlang/OTP 22 [erts-10.7.1] [source] [64-bit] [smp:8:8] [ds:8:8:10] [async-threads:1] [hipe] [dtrace]

        Eshell V10.7.1  (abort with ^G)
        1> code:root_dir().
        "/usr/local/Cellar/erlang/22.3.2/lib/erlang"
        2>
        2> q().
        ok
        3> >computer@[~]
        >


.. _Erlang:
.. _Erlang programming language: https://github.com/pierre-rouleau/about-erlang/blob/master/README.rst
.. _BEAM Virtual Machine:        https://en.wikipedia.org/wiki/BEAM_(Erlang_virtual_machine)
.. _Elixir:                      https://en.wikipedia.org/wiki/Elixir_(programming_language)
.. _LFE:                         https://en.wikipedia.org/wiki/LFE_(programming_language)
.. _Homebrew package manager:    https://en.wikipedia.org/wiki/Homebrew_(package_manager)
.. _Homebrew home page:          https://brew.sh



..
   -----------------------------------------------------------------------------



    The following scripts and alias allow me to create various environments for the
    `Erlang programming language`_,


-----------------------------------------------------------------------------------------
