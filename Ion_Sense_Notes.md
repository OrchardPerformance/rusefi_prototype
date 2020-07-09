980140 - The Comparison of Mass Fraction Burned
Obtained from the Cylinder Pressure Signal and
Spark Plug Ion Signal - Very useful   
Chao F. Daniels
Champion Ignition Products

For many years at Champion, spark plug ion sensing has
been used to detect engine pre-ignition in order to select
the proper heat range spark plug for an engine. In recent
years, ion sensing again became a topic of interest due
to its increasing applications in engine diagnostic and
engine control, such as misfire and knock detection;
spark timing control; A/F ratio detection and even pressure estimation (Eriksson, et al. 1996, 1997; Satizkoff, et
al. 1996, 1997). 

 Figure 1 shows a typical ionization signal obtained through a spark plug. As has been
discussed in much of the literature, the first peak of this
signal is the result of ignition pulse. The second peak is
the flame front passing through the gap. The third peak,
which has been called postflame by many researchers,
usually correlates well with the pressure signal and can
be used to interpret the peak pressure location for spark
timing control (Eriksson, Nielsen, and Glavenius, 1997)
and gas temperature sensing around the spark gap
(Satizkoff, 1997). After the third peak, the ion signal follows the pressure signal but declines more rapidly to a
lower value. 

![](Images/980140-1.png)

![](Images/980140-2.png)

Therefore, the change in the ion signal
reflects the temperature change in the vicinity of the
spark plug. 

By now, we have found the maximum acceleration point
(q1), the maximum heat release point (q2), and the end of
combustion (Dq). Based on Figure 2, at the maximum
heat release point, the second derivative of the Wiebe
function should be zero and the third derivative of Wiebe
function should be zero at the maximum acceleration
point. Therefore:

![](Images/980140-3.png)

Combining both conditions, we are able to obtain constant a and m, and further to construct a Wiebe function
to describe the mass fraction burned versus crank angle.
For example, at 1600 rpm, 24° BTDC spark timing, the
maximum acceleration point, the maximum heat release
point, and the end of combustion point are: 28°, 42°, and
57° from the ignition point. According to equation (1) and
(2), parameter a and m are 2.24 and 2.68 respectively.
Then the MFB for this operating condition is:

![](Images/980140-4.png)

where q is the crank angle difference between the calculated point and the ignition point.

The ionization current was
established by applying a bias DC voltage 150 V across
the spark plug gap. When there is conduction between
the gap, the current can be captured through the 100 KW
resistor. The signal was then sent to the DSP system

![](Images/980140-5.png)

980166 - In-Cylinder Air/Fuel Ratio Approximation Using
Spark Gap Ionization Sensing

1999-01-0204 - Ion Current Sensing for Spark Ignition Engines

2000-01-0553 - Ignition System Integrated AC Ion Current
Sensing for Robust and Reliable
Online Engine Control

2004-01-0515 - Effects of Engine Operating Conditions on
In-Cylinder Air/Fuel Ratio Detection Using
a Production Ion Sensing Device

2006-01-0024 - Using Ion-current Sensing to Interpret Gasoline
HCCI Combustion Processes

2006-01-1345 - Analysis of SI Combustion Diagnostics Methods
Using Ion-Current Sensing Techniques

2008-01-0981 - Ionization Signal Response during Combustion Knock
and Comparison to Cylinder Pressure for SI Engines

2010-01-0567 - Characteristics of Ion Current Signals in
Compression Ignition and Spark Ignition Engines

2011-24-0142 - Misfire and Partial Burn Detection based on Ion
Current Measurement

2013-01-0354 - Ion-Sense-Based Real-Time Combustion Sensing for Closed
Loop Engine Control

2017-01-0784 - Investigation on Pre-Ignition Combustion Events and Development of
Diagnostic Solutions Based on Ion Current Signals

950004 - Ion-Gap Sense in Misfire Detection,
Knock and Engine Control

