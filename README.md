# Stencil (Max for Live)

Probabilistic MIDI sequence generator inspired by Music Thing
Modular's Turing Machine — a shift register that emits notes from a
controlled-randomness loop.

This repository distributes the **Max for Live** build of Stencil.
Download the latest `Stencil.amxd` from
[Releases](../../releases) and drop it on a MIDI track in Live.

## Install

1. Download `Stencil.amxd` from the latest [release](../../releases).
2. Drag it onto a MIDI track in Ableton Live, **before** an
   instrument. Stencil is a generator, so it typically does not need
   a MIDI source upstream.
3. Press play. Adjust **lock** and **density** to taste.

## Requirements

- Ableton Live 12 or later, with Max for Live. (Live 11 technically
  has node.script but Cycling74 advises against it for production
  use; Live 12 ships Max 9.1.4 with Node 20, which is the supported
  floor.)

## What it does

On each host step, Stencil reads its **shift register** as an
integer, maps the value into a user-set MIDI note range, emits the
note with probability `density`, then shifts the register one
position — the bit cycling out is flipped with probability
`1 - lock` before being reinserted at the head. `lock = 1.0` is a
perfect loop; `lock = 0.0` is pure noise; values around `0.95` give
the classic slowly-evolving-pattern sweet spot.

Parameters:

- **length** — register width sets loop length
- **lock** — stability of the loop. `1.0` = perfect loop; `0.0` =
  pure noise; `0.95` is the classic slowly-evolving-pattern sweet
  spot
- **density** — per-step note-emission probability; controls hole
  pattern in the rhythm without shifting timing
- **lo / hi** — MIDI note range the register output is mapped into
- **mode** — `note` (pitch from register), `gate` (fixed pitch,
  on/off only), `velocity` (pitch + register-driven velocity)
- **triggerMode** — `auto` (transport-driven), `gate` (advance only
  while a key is held), `seed` (incoming `noteOn` / `noteOff` writes
  the head bit; the player becomes the bit source)
- **subdivision** — step rate relative to host tempo
- **outputVelocity / outputGate** — fixed velocity and gate length
  for emitted notes
- **inputChannel / outputChannel** — MIDI channel routing
- **seed** — RNG seed; output is reproducible from `(seed, params)`
  bit-for-bit

Stencil emits unquantized chromatic notes. Chain a
[Pointsman](https://github.com/im9/pointsman-m4l) device downstream
for scale-locked output — `Stencil → Pointsman → instrument` is the
canonical chain.

## Naming

Named after Herbert Stencil, the protagonist of Thomas Pynchon's
*V.* — a man assembling a pattern from random clues, "always
becoming." The device does something similar audibly: a circulating
bit register, nudged by probability, congeals into a melodic figure
and drifts away again as `lock` is loosened.

## License

[MIT](LICENSE). Free distribution under the `im9` label.
