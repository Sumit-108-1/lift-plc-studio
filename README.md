# Lift Studio: PLC Elevator Simulator

A browser-based simulator for a three-floor automatic elevator (lift) controlled by a 16-network ladder logic program. It was built as the mini-project from my PLC programming internship at Wonder Automation Training Division, Mohali, using KincoBuilder (IEC 61131-3 ladder logic).

**Live demo:** https://YOUR-USERNAME.github.io/lift-plc-studio/

<!-- Add a screenshot: save it as screenshot.png in this repo, then uncomment the next line -->
<!-- ![Simulator screenshot](screenshot.png) -->

## What it does

- Runs the real ladder program on a simulated PLC scan cycle, network by network.
- Shows a live lift: cabin movement, door, floor indicator lamps, position sensors and call buttons.
- Ladder logic tab draws the same networks the simulator executes, with contacts highlighted when they pass power.
- I/O map tab lists every input, output and internal bit with its live state.
- Operator panel for door obstruction, emergency stop, trip counter reset and simulation speed.

## How the program works

| Stage | Networks | What happens |
|---|---|---|
| Call registration | 0-2 | Each floor button latches its own memory bit (%M0.0-%M0.2) until the trip-complete pulse clears it |
| Direction decision | 3-4 | Compares the current floor sensor with the latched calls to raise Move-Up (%M0.5) or Move-Down (%M0.6) |
| Motor drive | 5-7 | Motor outputs are interlocked against each other, the door-open status and the emergency stop |
| Arrival | 8 | Raises the arrived flag (%M1.0) when a floor sensor is active and the cabin has stopped |
| Door cycle | 9-11 | Opens the door, holds it on timer T20 (5 s), then closes it unless the obstruction sensor is active |
| Indicators and counter | 12-15 | Floor lamps follow the position sensors; counter C0 counts completed trips |

Addresses: `%I` physical inputs, `%Q` physical outputs, `%M` internal memory bits, `T20` on-delay timer, `C0` up-counter.

## Known limitation

Networks 3-4 only hold the direction command while the departure floor's sensor is on. Real limit sensors release as the cabin leaves a floor, so on real hardware the cabin would stop between floors. The simulator has a "Hold last-floor sensor" switch (on by default) that mimics toggling the sensor by hand in KincoBuilder. Turn it off to see the stall. A seal-in on %M0.5 and %M0.6 would fix this properly.

The program also serves one call at a time: when a trip completes, all pending call latches are cleared.

## Run locally

No build step or dependencies. Open `index.html` in any modern browser.
