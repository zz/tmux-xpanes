XPANES 1 "FEB 2019" "User Commands" ""
=======================================

NAME
----

xpanes, tmux-xpanes - Ultimate terminal divider powered by tmux

SYNOPSIS
--------

### Normal mode

`xpanes` [`OPTIONS`] *argument* ...

### Pipe mode

command ... | `xpanes` [`OPTIONS`] [<*utility*> ...]

DESCRIPTION
-----------

`xpanes` and `tmux-xpanes` (alias of `xpanes`) commands have following features.

* Split tmux window into multiple panes
  * Build command lines & execute them on the panes
* Runnable from outside of tmux session
* Runnable from inside of tmux session
* Record operation log
* Flexible layout arrangement for panes
  * Select layout presets
  * Set columns or rows as you like
* Display pane title on each pane
* Generate command lines from standard input (Pipe mode)


OPTIONS
-------

`-h`, `--help`
  Show this screen.

`-V`, `--version`
  Show version.

`-c` <*utility*>
  Specify <*utility*> which is executed as a command in each panes. If <*utility*> is omitted, `echo(1)` is used.

`-d`, `--desync`
  Make synchronize-panes option off on new window.

`-e`
  Execute given arguments as is.

`-I` <*repstr*>
  Replacing one or more occurrences of <*repstr*> in <*utility*> given by -c option. Default value of <*repstr*> is {}.

`-C` *NUM*, `--cols=`*NUM*
  Number of columns of window layout.

`-R` *NUM*, `--rows=`*NUM*
  Number of rows of window layout.

`-l` <*layout*>
  Specify a layout for a window. Recognized layout arguments are:
    `t`    tiled (default)
    `eh`   even-horizontal
    `ev`   even-vertical
    `mh`   main-horizontal
    `mv`   main-vertical

`-s`
  Speedy mode: Run command without creating a interactive shell.

`-ss`
  Speedy mode AND close the pane automatically at the same time as the process ends.

`-n` <*number*>
  Set the maximum number of arguments taken for each pane of <*utility*>.

`-S` <*socket-path*>
  Specify a full alternative path to the server socket.

`-t`
  Display each argument on the each pane's border.

`-x`
  Create extra panes on the current active window.

`--log`[`=`<*directory*>]
  Enable logging and store log files to ~/.cache/xpanes/logs or <*directory*>.

`--log-format=`<*FORMAT*>
  File name of log files follows <*FORMAT*>.

`--ssh`
  Same as `-t -s -c 'ssh -o StrictHostKeyChecking=no {}'`.

`--stay`
  Do not switch to new window.

`--bulk-cols=`*NUM1*[,*NUM2* ...]
  Number of columns on multiple rows (i.e, "2,2,2" represents 2 cols x 3 rows).

`--debug`
  Print debug message.

### *FORMAT*
Default value is "[:ARG:].log.%Y-%m-%d_%H-%M-%S".
  Interpreted sequences are:
    `[:PID:]`   Process id of the tmux session. (e.g, 41531)
    `[:ARG:]`   Argument name

In addition, sequences same as `date(1)` command are available.
  For example:
    `%Y`   year  (e.g, 1960)
    `%m`   month (e.g, 01)
    `%d`   date  (e.g, 31)
    And etc.
Other sequences are available. Please refer to `date(1)` manual.

SHELL VARIABLES
-------

`TMUX_XPANES_EXEC`
  Preferentially used as a internal `tmux` command.
  Default value is `tmux`.

`TMUX_XPANES_LOG_DIRECTORY`
  Path to which log files generated by `--log` option are stored.
  Default value is `$HOME/.cache/xpanes/logs`.

`TMUX_XPANES_LOG_FORMAT`
  *FORMAT* of the log file name generated by `--log` option.
  Default value is `[:ARG:].log.%Y-%m-%d_%H-%M-%S`.

`TMUX_XPANES_PANE_BORDER_FORMAT`
  Defines the format of pane title used in `-t` option.
  Default value is `#[bg=green,fg=black] #T #[default]`.
  See *FORMATS* section in tmux(1) for further details.

`TMUX_XPANES_PANE_BORDER_STATUS`
  Defines location of the pane's title.
  See *FORMATS* section in tmux(1) for further details.
  Default value is `bottom`.

MODES
------

### [Normal mode1] Outside of tmux session.

When the tmux is not open and `xpanes` is executed on the normal terminal, the `xpanes`'s behavior is as follows:

* It newly creates a tmux session and new window on the session.
* In addition, it separates the window into multiple panes.
* Finally, the session will be attached.

### [Normal mode2] Inside of tmux session.

When the tmux is already open and `xpanes` is executed on the existing tmux session, the command's behavior is as follows:

* It newly creates a window **on the existing active session**.
* In addition, it separates the window into multiple panes.
* Finally, the window will be active.


### [Pipe mode] Inside of tmux session & Accepting standard input.

When `xpanes` accepts standard input (i.e, `xpanes` follows another command and pipe `|`) under **Normal mode2** , `xpanes`'s behavior is going to be the special one called "Pipe mode".

Pipe mode has two features.

1. `xpanes`'s argument will be the common command line which will be used within all panes (this is same as the `-c` option's argument in Normal mode).
1. Each line provided by standard input is corresponding to the each pane's command line (this is corresponding to normal argument of `xpanes` in Normal mode).

EXAMPLES
-------

#### Simple example

`xpanes` 1 2 3 4

    +-------------------------------+-------------------------------+
    │$ echo 1                       │$ echo 2                       │
    │1                              │2                              │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    +-------------------------------+-------------------------------+
    │$ echo 3                       │$ echo 4                       │
    │3                              │4                              │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    +-------------------------------+-------------------------------+

### `-c` option and `-I` option

`xpanes` -I@ -c 'seq @' 1 2 3 4

    +-------------------------------+-------------------------------+
    |$ seq 1                        │$ seq 2                        |
    │1                              │1                              │
    │                               │2                              │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    +-------------------------------+-------------------------------+
    |$ seq 3                        │$ seq 4                        |
    │1                              │1                              │
    │2                              │2                              │
    │3                              │3                              │
    │                               │4                              │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    +-------------------------------+-------------------------------+

### Ping multiple hosts

`xpanes` -c "ping {}" 192.168.1.{5..8}

    +-------------------------------+-------------------------------+
    |$ ping 192.168.1.5             │$ ping 192.168.1.6             |
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    +-------------------------------+-------------------------------+
    |$ ping 192.168.1.7             │$ ping 192.168.1.8             |
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    +-------------------------------+-------------------------------+

#### Run commands without opening a login shell

`xpanes` -s -c "seq {}" 2 3 4 5

    +-------------------------------+-------------------------------+
    │1                              │1                              │
    │2                              │2                              │
    │Pane is dead: Press [Enter] to │3                              │
    │exit...                        │Pane is dead: Press [Enter] to │
    │                               │exit...                        │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    +-------------------------------+-------------------------------+
    │1                              │1                              │
    │2                              │2                              │
    │3                              │3                              │
    │4                              │4                              │
    │Pane is dead: Press [Enter] to │5                              │
    │exit...                        │Pane is dead: Press [Enter] to │
    │                               │exit...                        │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    +-------------------------------+-------------------------------+

### Display host always

`xpanes` -t -c "ping {}" 192.168.1.{5..8}

    +-------------------------------+-------------------------------+
    │$ ping 192.168.1.5             │$ ping 192.168.1.6             │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    +--192.168.1.5------------------+--192.168.1.6------------------+
    │$ ping 192.168.1.7             │$ ping 192.168.1.8             │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    +--192.168.1.7------------------+--192.168.1.8------------------+

#### Use SSH without key checking

`xpanes` --ssh myuser1@host1 myuser2@host2

    +-----------------------------------------------+------------------------------------------------+
    │$ ssh -o StrictHostKeyChecking=no myuser@host1 │ $ ssh -o StrictHostKeyChecking=no myuser@host2 │
    │                                               │                                                │
    │                                               │                                                │
    │                                               │                                                │
    │                                               │                                                │
    │                                               │                                                │
    │                                               │                                                │
    │                                               │                                                │
    │                                               │                                                │
    │                                               │                                                │
    │                                               │                                                │
    │                                               │                                                │
    │                                               │                                                │
    │                                               │                                                │
    +-----------------------------------------------+------------------------------------------------+

#### Create new panes on existing window

    +-------------------------------+-------------------------------+
    │$                              │$                              │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    +-------------------------------+-------------------------------+
    │ $ xpanes -x 4 5 6                                             │
    │                                                               │
    │                                                               │
    │                                                               │
    │                                                               │
    │                                                               │
    │                                                               │
    │                                                               │
    +-------------------------------+-------------------------------+

    +-------------------------------+-------------------------------+
    │$                              │$                              │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    │                               │                               │
    +-------------------------------+-------------------------------+
    │$ xpanes -x 4 5 6              │$ echo 4                       │
    │$                              │4                              │
    │                               │$                              │
    │                               │                               │
    │                               │                               │
    +-------------------------------+-------------------------------+
    │$ echo 5                       │$ echo 6                       │
    │5                              │6                              │
    │$                              │$                              │
    │                               │                               │
    │                               │                               │
    +-------------------------------+-------------------------------+

#### Execute different commands on the different panes

`xpanes` -e "top" "vmstat 1" "watch -n 1 free"

    +-------------------------------+------------------------------+
    │$ top                          │$ vmstat 1                    │
    │                               │                              │
    │                               │                              │
    │                               │                              │
    │                               │                              │
    │                               │                              │
    │                               │                              │
    +-------------------------------+------------------------------+
    |$ watch -n 1 free                                             |
    │                                                              │
    │                                                              │
    │                                                              │
    │                                                              │
    │                                                              │
    │                                                              │
    +--------------------------------------------------------------+

#### Change layout of panes (using presets)

`xpanes` -l ev -c "{}" "top" "vmstat 1" "watch -n 1 df"

    +-------------------------------------------------------------+
    |$ top                                                        |
    |                                                             |
    |                                                             |
    |                                                             |
    |                                                             |
    +-------------------------------------------------------------+
    |$ vmstat 1                                                   |
    |                                                             |
    |                                                             |
    |                                                             |
    |                                                             |
    +-------------------------------------------------------------+
    |$ watch -n 1 df                                              |
    |                                                             |
    |                                                             |
    |                                                             |
    |                                                             |
    +-------------------------------------------------------------+

### Change layout of panes (Fixed number of columns)

`xpanes` -C 2 AAA BBB CCC DDD EEE FFF GGG HHH III

    +------------------------------+------------------------------+
    │$ echo AAA                    │$ echo BBB                    │
    │                              │                              │
    │                              │                              │
    +------------------------------+------------------------------+
    │$ echo CCC                    │$ echo DDD                    │
    │                              │                              │
    │                              │                              │
    +------------------------------+------------------------------+
    │$ echo EEE                    │$ echo FFF                    │
    │                              │                              │
    │                              │                              │
    +------------------------------+------------------------------+
    │$ echo GGG                    │$ echo HHH                    │
    │                              │                              │
    │                              │                              │
    +------------------------------+------------------------------+

#### Pipe mode

`seq` 3 | `xpanes`

    +------------------------------+------------------------------+
    |$ echo 1                      │$ echo 2                      |
    |1                             │2                             |
    |                              │                              |
    |                              │                              |
    |                              │                              |
    |                              │                              |
    |                              │                              |
    |                              │                              |
    +------------------------------+------------------------------+
    |$ echo 3                                                     |
    |3                                                            |
    |                                                             |
    |                                                             |
    |                                                             |
    |                                                             |
    |                                                             |
    |                                                             |
    +------------------------------+------------------------------+

#### Pipe mode with an argument

`seq` 4 | `xpanes` seq

    +-------------------------------+------------------------------+
    │$ seq 1                        │$ seq 2                       |
    │                               │                              │
    │                               │                              │
    │                               │                              │
    │                               │                              │
    │                               │                              │
    │                               │                              │
    │                               │                              │
    +-------------------------------+------------------------------+
    │$ seq 3                        │$ seq 4                       |
    │                               │                              │
    │                               │                              │
    │                               │                              │
    │                               │                              │
    │                               │                              │
    │                               │                              │
    │                               │                              │
    +-------------------------------+------------------------------+

SEE ALSO
--------

tmux(1)


AUTHOR AND COPYRIGHT
------

Copyright (c) 2019 Yamada, Yasuhiro <greengregson@gmail.com> Released under the MIT License.
https://github.com/greymd/tmux-xpanes


ACKNOWLEDGEMENTS
------

Thanks to Yamada, Yuka for her awesome [logo](https://github.com/greymd/tmux-xpanes/wiki/Image-Library).


