# solar_rhythm

The sound and data processing of *The 25th Cycle* and *Solar Flares*, in the
browser.

*The 25th Cycle* (installation, since 2021) and *Solar Flares* (live performance,
since 2019) are two audiovisual works driven by the solar cycle. In both, a
Eurorack system plays the daily sunspot record, from 1818 to today: the drone, the
percussion, the timbres and the form all come from the data.

This repository contains a readable, executable translation of the sound and data
processing the two works share: **`solar_rhythm_v001.html`** (version
`SOLAR-v069`), a browser page that drives the same Eurorack system, available in
**English and French** (FR / EN button, or `?lang=en` / `?lang=fr` in the
address). It is not the works' Max/MSP program: the patches of the installation,
which also recombine the cycle phase by phase and drive the image, are not
included.
Repository: [github.com/fdiba/solar_rhythm](https://github.com/fdiba/solar_rhythm).

## How it works

- **A drone in every phase.** The kick (WMD Crater) excites a long reverb with
  held notes, at irregular intervals drawn from the days: two to four bars after a
  spotless day, depending on how long the run of spotless days is, two down to one
  bar after a day with spots; each note lasts longer after a long spotless run (up
  to 4 seconds). Its pitch follows the 27-day mean, placed on a scale. A Make Noise
  **Strega** adds a layer of matter that grows with solar activity: its activation,
  filter, repeats and tone colour follow the data.
- **Percussion that changes with the cycle.** Each voice has a test on the daily
  sunspot number:

  | Voice | Module | Days that pass the test |
  |---|---|---|
  | kick | WMD Crater | local peaks of activity, above the 27-day mean |
  | snare | WMD Kraken, played by the Strega from phase 3 | rises from one day to the next |
  | Strega hits | Make Noise Strega | bursts above the 27-day mean |
  | calm swells | Make Noise Strega (slow activation swells) | days ending a run of 14 or more spotless days, in state 1 only |

  Every phrase of 8 bars, the page reads the 32 days the phrase will cover: the
  share of days that pass a voice's test sets its number of hits, capped by the
  state of the cycle, and the hits fill the positions of the voice's own loop
  (Crater 16 steps, Kraken 16 then 12, Strega 18, calm swells 32), so that the voices drift
  against one another. Each phase has its own percussive character: near minimum,
  the Kraken alone, its hits repeated by the Strega, widely spaced drone notes, some of which bloom into a long reverb after a few spotless days, and slow Strega swells after two spotless weeks; then the Strega takes over
  the Crater's drone notes (the Crater is muted), its tone colour sweeping back and forth, with the dry Kraken; then the same with a few Strega hits of its own, the Crater heard only in the reverb; then dry, dull Crater
  knocks; at maximum, all voices with bright, short timbres, the Strega delay time drawing an arch over the phase and its agitation generator running faster on stronger days. On bars 4 and 8 of
  each phrase, each voice mutates: its least important hit moves to the position
  pointed to by the days of that bar. Caps and timbres per phase can be edited in
  the page.
- **The form follows the cycle.** The 13-month smoothed series is cut into five
  states. The reverb carries the drone throughout, slightly shorter as the sun
  rises; the hits are sent to it sparingly, except through **dub throws** (the
  send opens for one step on the last snare of each phrase, on fills, and on the
  Strega hits of bars 4 and 8). Optionally, in the deepest part of the minimum,
  the reverb freezes.
- **The timbres follow the data.** The Kraken `SHELL` and `SNARES` take one value
  per phase, `SHELL` departing slightly from it on every hit; the Desmodus Versio
  `TONE` follows the difference between the 27-day mean and the 13-month smoothed
  series. All are sent as 14-bit MIDI CC.
- **One clock.** A single 24 ppqn look-ahead scheduler drives the MIDI clock, the
  steps, the melody line, the Strega, the timbres and the form.

## Requirements

- A browser with **Web MIDI**: Chrome or Edge. The page works offline, opened
  directly from disk (`file://`); the data are embedded.
- An **Expert Sleepers FH-2** (MIDI to CV) with three FHX-8CV expanders. Without
  the rack, the page still runs: the *Audio monitor* button plays a simple sound
  for each voice.

## Patching

| FH-2 output | Function | To |
|---|---|---|
| 1 | gate, channel 3, note 1 | WMD Crater `TRIG` (kick and drone) |
| 2 | gate, channel 3, note 2 | WMD Kraken `HEAD` (snare) |
| 3 | `direct control`, CC 3 channel 4 | WMD Kraken `SHELL` (set on every snare hit) |
| 4 | `direct control`, CC 4 channel 4 | Intellijel Quad VCA `CV 2`: level of the Strega before the Ruina |
| 5 | gate, channel 3, note 5 | Strega `Begin and End In` (agitation running, phase 5) |
| 7 | MIDI/CV converter, channel 7 | WMD Crater `1V/oct` and, through a multiple, Strega `Tonic 1V/Oct` |
| 8 | LFO, Base 96 × 3 | WMD Kraken `VELOCITY` |

Expander outputs 9 to 24 drive the mix and effects (XAOC Hrad and Praga, Desmodus
Versio): gates on channel 3 and `direct control`
CVs on channel 4, note or CC = output number (output 21: CC 25). Output 32 drives the Strega `Absorb CV In` (CC 12, 0-10 V); output 22 its `Time CV In` and output 10 its agitation `Speed CV In` (0-5 V); the Strega `Agitation Function Out` is patched into its `Time Modulation Input`. Outputs 30 and 31 drive the Make Noise QPAS that filters the
copy of the Kraken sent to the Strega (`FREQ1 CV`, CC 30; `Level CV`, CC 31). Output 25 drives the Ruina `Blend` CV input (CC 9, 0-5 V, dry in
phase 1, wetter as the Sun rises). Outputs 26 to 29 of the third FHX-8CV drive the
Strega `Activation`, `Decay`, `Filter` and `Tones` CV inputs (0-5 V, `Tones` at 0-10 V, CC 26 to 29,
14-bit). The full list is in the page, in the *Patching* and *Test bench*
sections. All gates and clocks are set to 0-5 V.

Audio: the Strega `Modular Level Output` goes through an Intellijel Quad VCA into a
Noise Engineering Ruina Versio, whose outputs feed Praga channels 1 and 2 (panned
hard left and right); a copy of the Kraken output, low-pass filtered by a Make Noise
QPAS, feeds the Strega `External Substance In`, so that the Strega repeats the snare.
A passive multiple copies the Kraken output into the Hrad `aux ins 3`; a Joranalogue
Link 2 copies the Crater output into a Doepfer A-121-2 high-pass filter, then into
`aux ins 4` (pre-fader sends: the drone level in the reverb does not depend on the
dry level).

Praga channels whose volume is set by the page (1 to 4): mode button **green**
(unipolar) and `vol` knob **fully clockwise**, so that the control voltage alone sets
the level (0 V silent, about 8 V at 0 dB). With the knob lower, or in bipolar mode
(red), a channel set to 0 by the page still sounds.

## Data

Total daily sunspot number, version 2.0, 1818 to 2026.

**Source: WDC-SILSO, Royal Observatory of Belgium, Brussels,
DOI: [10.24414/qnza-ac80](https://doi.org/10.24414/qnza-ac80).**
Licensed under [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/):
non-commercial use, attribution required.

The page embeds the data file of 30 September 2026. To update, download
`SN_d_tot_V2.0.csv` from [sidc.be/SILSO/datafiles](https://www.sidc.be/SILSO/datafiles)
and drop it onto the page: it is checked, used and remembered by the browser.

Solar cycles are cut at the official SILSO minima
([sidc.be/SILSO/cyclesminmax](https://www.sidc.be/SILSO/cyclesminmax)), on the
first day of the minimum month.

## How to cite

Use the **Cite this repository** button (top right of the GitHub page): it reads
[`CITATION.cff`](CITATION.cff) and gives the reference in APA and BibTeX.

> Di Bartolo, F. (2026). *solar_rhythm: the sound and data processing of The 25th
> Cycle and Solar Flares, in the browser* (version SOLAR-v069) [Computer software]. https://github.com/fdiba/solar_rhythm

When using the sunspot data, cite SILSO as well (see *Data* above).

## License

**[CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/)**, full
text in [`LICENSE`](LICENSE). You may use, share and adapt this work under three
conditions:

- **Attribution**: credit the author and link to this repository and the license,
  and indicate any changes;
- **NonCommercial**: no commercial use;
- **ShareAlike**: adaptations are shared under the same license.

For any commercial use, contact the author. The embedded sunspot data keep their
own license, CC BY-NC 4.0 (see *Data*).

## Author

Florent Di Bartolo, Université Gustave Eiffel, LISAA.
ORCID: [0000-0002-4910-6936](https://orcid.org/0000-0002-4910-6936)
