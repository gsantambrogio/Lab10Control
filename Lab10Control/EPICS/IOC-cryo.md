# Hosted on
[[cryo]]

# Devices
## [[ACP28-40 Scroll]]
### PVs
- ACP:switch: Turn pump ON and OFF

## AVC
Relais board controlling vacuum valves
### PVs
- Valve1: Switch ON and OFF valve between [[ACP28-40 Scroll]] and big turbo
- ValveUHV: Switch ON and OFF valve between source and UHV 

## CPA
Cryostat
### PVs
- cryo:waterIn: Temperature of cooling water input
- cryo:waterOut: Temperature of cooling water output
- cryo:oil: Oil temperature
- cryo:helium: Helium temperature
- cryo:lowPressure: Pressure Helium Low
- cryo:lowPressureAvg: Pressure Helium Low Avg
- cryo:highPressure: Pressure Helium High
- cryo:highPressureAvg: Pressure Helium High Avg
- cryo:hours: Hours of operation
- cryo:switch: Switches ON and OFF
- cryo:warnings: Warnings

## CTC Temperature controller
Stanford temperature controller. 4 temperature sensors. 2 PIDs
### PVs
- CTC:OutputEnable: _Enable all outputs_
- CTC:Temp#: _Reading of temp sensor # _
- CTC:Temp#:LowerLim: _Desired lower limit for the temperature for sensor # _
- CTC:Temp#:UpperLim: _Desired upper limit for the temperature for sensor # _
- CTC:Temp#:check: _Is temp of sensor # between upper and lower limits?_
- CTC:PID#:getsetpoint: _Gets the setpoint for the PID # _
- CTC:PID#:setpoint: _Sets the setpoint for the PID # _
- CTC:Temp#:STATUS: _Some sort of alarm_
- CTC:PID#:inputCh: _Queries input Ch for the PID # _

## MCE Flowmeter
Two flowmeters. To be described
### PVs
- flowmeter:A/B:



- MCE: Flowmeters
- TC1200: TurboPump
- TDK: Power supply used for heating
- TPG256A: Pressure Gauges 