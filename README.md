# Meson: Brainfuck
A proof that Meson is actually Turing-Complete.

## Background
While surveying various modern build systems for C and C++, I stumbled upon Meson (https://mesonbuild.com/).
After reading the docs, watching a few talks from Jussi Pakkanen, and experimenting on a few existing projects, I found myself pleasently suprised by Meson.

A lot of what has made Meson work well has been the highly principled approach Jussi has taken:

- Learn from previous systems: CMake, Scons, etc
- Minimize complexity by adding only the minimal feature set required
- Design a DSL for build specification, but discourage any feature creep towards becoming a general-purpose langauge

However, in many of the talks, there are claims that "Meson is not Turing-Complete and will never be Turing-Complete".

After reading through documentation and seeing if-stmts, foreach-loops, arrays, strings, etc the claim seemed very implausible.
Further, user-defined functions were rejected in the past because apparently they'd suddenly make Meson turing-complete. Huh?

So, this is a proof that shows Meson actually already is turing-complete.

## Turing Complete
Mathematically speaking, no modern lanaguage is turning-complete because memory size on any machine has a fixed upper-bound.
So, when programmers say "turing-complete" they usually mean "it would be turing-complete if it also had infinite memory". This is a
dubious definition, but most programmers seem to agree that any language that can implement a brainfuck interpretor is turing-complete.

## Brainfuck
An esoteric language that seems to be nearly every programmer's favorite esolang toy, with countless implementations (https://en.wikipedia.org/wiki/Brainfuck)

## Discussion

I don't think this proof invalidates any of the excellent work that has been put into Meson.  If anything, this shows that build
configuration is a hard enough problem that requires a turing-complete language. Most features have gone into Meson because somebody
actually needed them. And for this reason, Meson serves as an estimate of the required complexity.

The distinction of turing-completeness is actually not very interesting (e.g. see https://en.wikipedia.org/wiki/Turing_tarpit)

I would argue that Meson's goal is not to avoid turing-complete-ness. It's goal is to be an excellent build-description langaugae
and go no further. It's goal is to be a terrible general-purpose langauge.
