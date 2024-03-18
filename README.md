# Hybrid Digital Piano Calibration

Please refer to *stem piano* as an example of a hybrid digital piano suitable for use of the following system.

## Definitions

#### Sensor
Device measuring distance and outputs a signal representing distance to an object. An example is the CNY-70.

#### Sensor board
An example is the Hammer Position Sensor (HPS) in *stem piano*.

#### DIS_H

Distance between the sensor and hammer or hammer shank or back of piano key (for damper).

#### DIS_C

Distance between the sensor and a surface mounted to a controllable displacement actuator.

#### SIG_S

Analog signal output from the sensor, representing the sensor distance to an object.

#### SIG_N

Numerical value of SIG_S, as output from an analog-to-digital converter (ADC).

#### PROC_P

Microprocessor on circuit board that receives values SIG_N. In *stem piano* an example of a PROC_P is the Teensy 4.1 as part of the Integrated Processing System (IPS). A PROC_P can also be an external computer.

#### AFE

All analog front-end circuitry between SIG_S and PROC_P.

## Problem Statement

1. The sensor output signal (SIG_S) may not be an exact linear (proportional) representation of the distance from the sensor to the hammer or hammer shank or back of key (DIS_H).

2. Variation in the distances between the sensor and hammer or shank or back of key (DIS_H), from key-to-key in the piano.

3. It is preferred not to require exact placement of sensors with respect to hammer or shank or back of key.

4. Variation in the analog front-end (AFE), between the sensor and the analog-to-digital converter (ADC).

5. It is preferred that an automatic system can calibrate to compensate for the sensor distortion (#1 above) and the mechanical or electrical variations (#2 and #3 and #4 above) while the piano is being played.

6. It is preferred that a simple method is available during manufacturing to create information for #5 above.

7. Automatic system is applicable to any similar measurement system.

## Solution

After each sensor board is built, connect the sensor board to the AFE and associated PROC_P then power up the system.

Place the sensor board in jig, separate from a piano action, with the sensor input facing a surface mounted to a controllable displacement actuator.

The controllable displacement actuator moves the surface linearly from a maximum distance from the sensor, to a minimum distance from the sensor. The maximum distance (surface to sensor) should be larger than the maximum distance (piano hammer, shank, or back of key to sensor) when in the piano. The minimum distance (surface to sensor) should be smaller than the minimum distance (piano hammer, shank, or back of key to sensor) when in the piano.

Measurements are taken of the distance between the surface to the sensor on the sensor board (DIS_C), and measurements are taken of the numerical signal received from ADC connected to the sensor board (SIG_N).

It may be useful to filter the measurements to reduce noise.

These pairs of measurements {DIS_C, SIG_N} are recorded at a set of N distances between the surface and the sensor on the sensor board (DIS_C). The measurement pairs are stored in a look-up table.

There are N entries in the lookup table, where N is a positive integer. Each entry consists of the distance between the surface and the sensor (DIS_C) along with the sensor board output associated with that distance (SIG_N).

The distances between the surface and the sensor board (DIS_C) can be normalized. The normalization can be, for example, 0.0 representing when the distance from the surface to the sensor (DIS_C) is at its smallest value and 1.0 representing when the distance (DIS_C) is at its maximum value. For this example, in between these values of 0.0 and 1.0 are stored as fractional values.

Normalization avoids the need to measure absolute calibration distances for DIS_C.

Repeat this process for every sensor board. Each lookup table is associated with the sensor board used for calibration.

Now build the piano and mount all of the sensor boards. The set of all lookup tables, one for each sensor, are loaded into the PROC_P. In each table, DIS_C is now called DIS_H.

When the piano is operational, the signal (SIG_N) from each sensor when at rest is recorded in PROC_P. The corresponding distance or normalized distance (DIS_H) is retrieved from the lookup table.

When the piano is operational, and each note is struck, the sensor output signal (SIG_N) at the location when the piano hammer hits the virtual string is recorded. The corresponding distance or normalized distance (DIS_H) is retrieved from the lookup table.

For examples of calculating the point at which the hammer hits the virtual string see *stem piano*.

During normal piano operation, each sensor board numerical output from the ADC (SIG_N) is converted to a distance value (DIS_H) by looking up the distance value (DIS_H) from the lookup table.

If a match for SIG_N is not found in the table, then apply interpolation when converting SIG_N to DIS_H.

The value DIS_H is used by PROC_P, for example calculating velocity or recording distances.

During normal operation, depending on the mechanical tolerances, it is possible that only a subset the entire range of the lookup table is referenced during conversion of sensor output (SIG_N) to sensor-shank distance (DIS_H). For example, because of the physical distances for one of the sensors, the subset of the table for DIS_H from 0.25 to 0.75.

The lookup table can be stored in nonvolatile memory in PROC_P. Alternatively, the lookup table can be converted to the mathematical formula during the calibration process. Then, only the mathematical formula needs to be stored in the PROC_P for operation. The mathematical formula converts sensor output (SIG_N) to distance or normalized distance (DIS_H), in a manner that matches the lookup table pair relationships.

If the sensor errors and key-to-key variations are small enough to meet the requirements on accuracy, then it is possible to only take calibration measurements for a single sensor and use the results for all keys.

The difference between the rest position and when hitting the virtual string is used to scale the velocity in order to normalize velocity values between keys, as because of physical variations each hammer or hammer shank or back of piano key will traverse a different distance to sensor.

This all can be applied for dampers or hammers or any needed measurements. It is not limited only to piano hammers, piano shanks, or the back of piano keys.

An alternative is to preform the calibration measurements while the sensors are mounted in the piano action. In this case, the controllable displacement actuator is moving a hammer shank or piano key.
