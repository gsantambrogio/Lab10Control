# SMD3 Stepper Motor Driver

## Description
SMD3 Stepper Motor Drive, single-axis bipolar stepper motor drive.

## Type
Stepper motor driver

## Model
SMD3 Stepper Motor Drive

## Controlled device
- [[Stepper Motor D57.1 UHV]]

## Connected to
- Motor: [[Stepper Motor D57.1 UHV]]
- Control system:
- Power supply:

## EPICS
PV prefix: `StpM:`

Important PVs:
- Temperature: `StpM:Temp`
- Position: `StpM:Pos`
- Move absolute: `StpM:MoveAbs`
- Motor current: `StpM:Current`
- Start velocity: `StpM:VStart`
- Stop velocity: `StpM:VStop`
- Maximum velocity: `StpM:VMax`
- Maximum position: `StpM:Max`
- Minimum position: `StpM:Min`
- Scan: `StpM:Scan`
- Stop: `StpM:Stop`
- Should step: `StpM:ShouldIStep`
- Do step: `StpM:DoStep`
- Status: `StpM:STATUS`
- Moving: `StpM:Moving`
- Status flags: `StpM:SFlags`
- Serial/error register: `StpM:SER`

## Control
The driver controls the [[Stepper Motor D57.1 UHV]].

## Documentation
- Product page: https://arunmicro.com/products/smd3-stepper-motor-drive/
- Local datasheet: [[Documentation/Datasheet_SMD3.pdf]]
- Online datasheet: https://arunmicro.com/documentation/Datasheet_SMD3.pdf
- Local manual: [[Documentation/Manual_SMD3.pdf]]
- Online manual: https://arunmicro.com/documentation/Manual_SMD3.pdf
- Software page: https://arunmicro.com/documents/software/

## Maintenance
- Installation date:
- Last check:

## Notes
