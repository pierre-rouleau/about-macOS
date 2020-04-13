=============================
macOS Development Environment
=============================

:Time-stamp: <2020-04-13 16:56:35, updated by Pierre Rouleau>
:Copyright: Copyright © 2020 by Pierre Rouleau
:License: `MIT <../LICENSE>`_

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

Environment for Erlang
~~~~~~~~~~~~~~~~~~~~~~
