# Description
Precilaser used for SFG together [[Precilaser 1050]]

# Model
Precilaser FECL-SF-1560nm

# Specifications
- Output power: 25 mW
- Wavelength: 1560.48 nm
- Bandwidth: 1.01 MHz

# Locking
This laser is locked to the comb using the 1560 nm output of the comb called RP1. This output is the one used otherwise for locking the comb to the fibre link. The beam for the locking is sampled by a fibre-beam splitter: 25% for the locking and 75% for SFG with [[Precilaser 1050]].

The beatnote is detected using the [[Photodiode PDX10B]], powered by [[PowerSupply Funk PWS-05B-T]]. The signal is first amplified using [[Amplifier ZFL-500LN+]], then a coupler by minicircuit extracts a portion of the signal for diagnostics; the main part of the signal is filtered with a low pass 60 MHz filter, and the further amplified with a [[Amplifier ZFL-500LN+]]

The **PID** is done by the RedPitaya1. 

**Beatnote** with the comb on the [[Photodiode PDX10B]], powered by [[PowerSupply Funk PWS-05B-T]]. Signal is amplified by [[Amplifier ZFL-500LN+]]. Local oscillator 