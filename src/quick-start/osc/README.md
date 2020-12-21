# OSC

## The Erlang Server

Tested against `erlsci/osc` `2.0` and `2.1`.

``` lisp
lfe> (set c (undertone.osc.client:connect "localhost" 2357))
; ok#(client #Pid<0.276.0> #Pid<0.277.0>)
lfe> (undertone.osc.client:echo c)
; ok
```

If you view the output of the running Erlang OSC server, you should see some
debug output logged:

```
=INFO REPORT==== 27-Nov-2020::16:14:05.986773 ===
Received message: []
```

You can also check passed arguements:

``` lisp
lfe> (undertone.osc.client:echo c '(a list of args))
ok
```

```
=INFO REPORT==== 27-Nov-2020::16:35:28.652299 ===
Received message: [a,list,'of',args]
```

## SuperCollider [&#x219F;](#table-of-contents)

Tested against SuperCollider `3.11.2`.

### Connecting

Start up the SuperCollider GUI / IDE, then in the editor enter the following:

```
s.options.maxLogins = 8;
s.reboot;
Server.default.options.asOptionsString
```

The last line will help you confirm the current settings, showing what they
would be if you'd started `scsynth` manually. In particular, you want to confirm
that you see `-l 8`.

Then, in a terminal at the LFE REPL:

``` lisp
lfe> (set c (sc.client:connect "localhost" 57110))
;#(client #Pid<0.344.0> #Pid<0.345.0>)
```

Once the client is set up, do a quick check to see that you are connected to the
right server / version and that there are synthesizer definitions loaded:

``` lisp
lfe> (sc.client:version c)
;(#(version "3.11.2") #(branch "HEAD") #(commit-id "9a34118e"))

lfe> (sc.client:status c)
;(#(unit-generators 0)
; #(synths 0)
; #(gruops 9)
; #(loaded-synth-definitions 106)
; #(cpu-average-usage 0.030904095619916916)
; #(cpu-peak-usage 0.2695612609386444)
; #(nominal-sample-rate 4.41e4)
; #(actual-sample-rate 44099.98856935304))
```

### Playing Sounds

First create a handful of instances of the default synth and then stop them,
until we're ready:

``` lisp
lfe> (set synth-ids '(1000 1001 1002 1003 1004))
;"ϨϩϪϫϬ"
lfe> (set (list s0 s1 s2 s3 s4) (list-comp
       ((<- id synth-ids))
       (sc.client:create-synth c id)))
;"ϨϩϪϫϬ"
lfe> (list-comp ((<- id synth-ids)) (sc.client:stop-node c id))
;(ok ok ok ok ok)
```

Instead of a boring C chord, let's take some notes from the C scale's Locrian
mode using `B`, `E`, `F`, `A`, and `B` (a combination _rarely_ heard!):

``` lisp
lfe> (include-lib "include/notes.lfe")
;|-- loaded include: notes --|
lfe> (sc.client:set-node c s0 `("freq" ,(B3) out 0))
;ok
lfe> (sc.client:set-node c s1 `("freq" ,(E3) out 1))
;ok
lfe> (sc.client:set-node c s2 `("freq" ,(F3) out 0))
;ok
lfe> (sc.client:set-node c s3 `("freq" ,(A3) out 0))
;ok
lfe> (sc.client:set-node c s4 `("freq" ,(B4) out 1))
;ok
```

Now play them all:

``` lisp
lfe> (list-comp ((<- id synth-ids)) (sc.client:start-node c id))
;(ok ok ok ok ok)
```

For the MIDI-minded, in addition to the `notes.lfe` include, there is a
`midi-notes.lfe` file with functions defined for getting notes by MIDI number.

And then, when you're done listening to that beautiful dissonance:

``` lisp
lfe> (list-comp ((<- id synth-ids)) (sc.client:stop-node c id))
;(ok ok ok ok ok)
```

## Ardour [&#x219F;](#table-of-contents)

Tested with versions 5 and 6 of Ardour.

``` lisp
lfe> (set c (ardour.client:connect "localhost" 3819))
;#(client #Pid<0.281.0> #Pid<0.282.0>)
lfe> (ardour.client:strip-list c)
;(#(name "SynthMaster One")
; #(strip-number 1)
; #(type "MIDI track")
; #(inputs 0)
; #(outputs 2)
; #(muted? 0)
; #(soloed? 0)
; #(record-enabled? 0))
```
