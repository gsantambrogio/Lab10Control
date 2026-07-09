# Description
Toptica laser used to drive the [[X (v=1) -- A (v=0)]] transition. 
The laser 1257.2 nm is first tapered amplified and the frequency doubled. 
Used as a repumper for slowing

# Model
DLC TA-SHG Pro

# Specifications
- Wavelength: 628.55
- Tuning range: 2 nm
- SHG output power: 1 W (in 2026 we actually had 1.5 W output power)

# Locking
Locked with the Vescent 1. Local oscillator WFG-B. 

# Modulation
Modulated with one EOM (EOM1 PM-CaF_25) for the MOT and RF Driver (old Qubig driver EOM-ADU). This produces sidebands at 24 MHz. 


Modulated with another EOM for slowing: EOM2 (PM_CaF_5) and RF genarated by WFG-?? (RIGOL DG1022). Amplified by two 2-W minicircuit amplifiers in chain. 

This is to broad the spectrum instead of chirping. 
