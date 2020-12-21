# Extempore

Tested against `extempore` version `0.8.7`.

Download the latest Extempore from the
[project releases](https://github.com/digego/extempore/releases)
page on Github.

Assuming you've started `extempore` and have the LFE REPL running, 
as previously described, you will have been automatically connected to the 
Extempore TCP server. As such, you're ready to start living coding!

There are several ways to do this from LFE:

1. From an LFE-managed REPL supporting native Extempore syntax,
1. From an LFE-managed REPL with hybrid Extempore and LFE support, and
1. From LFE itself (limited Extempore support)

## The Extempore REPL


At the LFE prompt, start up the Extempore REPL:

``` lisp
lfe> (undertone.repl.extempore:start)
extempore> 
```

Once at the `extempore>` prompt, you will be able to enter native Extempore
expressions (Scheme and xtlang). These will be passed to the Extempore TCP
server as-is (executed asynchronously and non-blocking).

Additionally, you may call other supported functions. To see the list of
supported REPL function, type `(help)`:

``` lisp
extempore> (help)
```

``` text
The Extempore undertone REPL

Built-in functions:

(call body) -- make an explicitly blocking call to Extempore, with 'body' being
               a valid Extempore expression
(check-xt)  -- check on the status of Extempore (no response indicates a health
               problem, possibly requiring a restart of the 'extempore' binary)
(eom)       -- alias for '(term)'
(exit)      -- alias for '(quit)'
(h)         -- alias for '(help)'
(help)      -- display this information
(quit)      -- quit the Extempore REPL and return to the LFE REPL
(run file)  -- load the code in the given file and run in Extempore
(term)      -- send message-terminating character sequence to force Extempore
               end-of-message (useful when troubleshooting)
(version)   -- display all the version info for undertone

Extempore support:

All S-expressions other than the ones listed above will be treated as Extempore
Scheme / xtlang code and sent to the Extempore TCP server (compiler service) as
an asynchronous call (no result, no output printed). If you would like to block
on particular calls and see their return values, be sure to use the '(call ...)'
REPL function.
```

Most (if not all) of the official Extempore examples should work without modification
in this REPL.

## The Undertone REPL

TBD

## Native LFE

Note that only a limited number of Exempore forms are supported from within LFE
right now.

Load the appropriate Extempore files into the server and the LFE macros into the
current REPL session:

``` lisp
(xt:sys-load "examples/sharedsystem/setup.xtm")
(include-lib "undertone/include/xt-patterns.lfe")
```

Play an ascending scale using the second synthesizer that comes with Extempore:

``` lisp
(/> 'ascending-scale 4 0 (play 'syn2 '@1 80 'dur) (scale 4 8))
```

Note that the Extempore syntax for this is `:>`, a character combination not
supported in LFE symbols, so `/>` was settled upon instead. Similarly, below you
will see `//` used in LFE as an analog for the `:|` used in Extempore.

Then change the tempo:

``` lisp
(set-tempo! 72)
```

You can stop the synth in two ways -- changing 'play' form to the 'stop' form
while keeping the remaining body the same (this is useful for live coding
scenarios): 
``` lisp
(// 'ascending-scale 4 0 (play 'syn2 '@1 80 'dur) (scale 4 8))
```

or by calling the 'stop' form using just the pattern name you
defined in the 'play' form:

``` lisp
(// 'ascending-scale)
```

