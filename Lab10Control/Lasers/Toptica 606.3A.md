# Description
Toptica laser used to drive the [[X (v=0) -- A (v=0)]] transition. 
The laser 1212.6 nm is first tapered amplified and the frequency doubled. 
Used only as a main MOT laser
# Model
DLC TA-SHG Pro


# Specifications
- Wavelength: 606.34
- Tuning range: 4 nm
- SHG output power: 1 W (in 2026 we actually had 1.5 W output power)

# Locking
The fundamental of the laser at 1212.6 nm is beaten against a portion of the comb light, separated with a grating. The beatnote is detected using the [[Photodiode PDX10B]], powered by [[PowerSupply Funk PWS-05B-T]]. The signal is first amplified using [[Amplifier ZFL-500LN+]], then a coupler by minicircuit extracts a portion of the signal for diagnostics; the main part of the signal is filtered with a low pass 60 MHz filter, and the further amplified with a [[Amplifier ZFL-500LN+]]

Reference Local oscillators - LO1 (Siglent SDG1062X 60MHz Function)

PID: Toptica Falc