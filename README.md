# The story  
After many years of using a soldering iron with a lab power supply, I could finally purchase a 'cheap' soldering station. (SMA 051)  
![alt text](https://github.com/nghfp9wa7bzq/soldering-calibration/blob/main/soldering_station.jpg "Soldering station")  

At the time (2 years ago) it was around 50 USD/EUR.  
Now it costs around 70...  
As you can see, it is a pretty simple device.  
It has a 3 digit 7-segment display to show the temperature in degrees C (0-400),  
a switch to be able to display either the set or the measured temperature,  
and LEDs to show the switch position (you can obviously see that without LEDs)  
and an LED to show when the heater is on.  
A potentiometer is used to set the temperature.  

It just had one 'simple' problem.  
I had to turn the pot. all the way up for it to melt solder...  

### How it works  
I have drawn a partial schematic:  
![alt text](https://github.com/nghfp9wa7bzq/soldering-calibration/blob/main/schematic.jpg "Schematic")  
  
One half of A3 (LM358) amplifies the thermocouple voltage (later: TCV) by 51-81 times based on VR5.  
The other half compares this voltage with the voltage set by the main pot., VR4 and trimmers VR2 and VR3,  
and turns on TR4 (BT136), a triac, which switches the heater in the iron.  
The display is driven by A1 (ICL7107).  
A1 has a reference voltage between A1/35 REF LO (connected to GND) and A1/36 REF HI (set with VR1)  
and an input voltage between A1/30 IN LO (set with VR6) and A1/31 IN HI (connected to the switch, i.e.  
either to the amplified thermocouple voltage or the pot. voltage).  
As per datasheet, if the input voltage is equal to the reference voltage,  
the display shows 999 or 1000.  
A2, a CD4069 hex inverter is used to generate a negative power supply for A1.
(See ICL7107 datasheet.)  

### Measuring the thermocouple  
As the general idea is to correlate the temperature or rather the TCV with the display,  
I had to know the voltages at different temperatures.  
Unfortunately I don't have a good temperature measuring device, so this calibration is not really accurate,  
however the calibration steps are the same either way.  
The amplified TCV can be measured between A1/1 and GND (A1/4).  
I have immersed the iron in ice and boiling water and measured the voltage.  
There are multiple measurements with 5-10 minute intervals to allow the iron to reach thermal equilibrium.  
Note that ice from the freezer is around -18 degrees C and not zero, which we want.  
![alt text](https://github.com/nghfp9wa7bzq/soldering-calibration/blob/main/measurements.jpg "Measurements")  

Column F (calc. TC mV) is the average measured voltage divided by the average amplification (67).  
This is a rough estimate of the TCV, without amplification.  
The second part is a table with chosen values and a graph made from it.  
As you can see, the temperature and the voltage has a NON-linear relationship.  
This is a problem.  
Either a measurement problem on my part or a design problem.  
As far as I understand, the circuit does not compensate for this non-linearity.  
You would use a microcontroller with a look-up table in this case.  
(Or some opamp compensation, but I'm not sure what exactly.)  
Seeing this, I have concluded that without a proper thermometer,  
the best I can do is to set up an arbitrary linear relationship with the trimmers,  
so that the soldering station becomes usable at least.  
  
### Calculation  
![alt text](https://github.com/nghfp9wa7bzq/soldering-calibration/blob/main/calculation.jpg "Calculation")  
  
I have set the reference voltage to 3.3V.  
A1/36 REF HI (set with VR1)  
This means 1 on the display is 3.3 mV between A1/30 IN LO and A1/31 IN HI.  
183 degrees C is 603.9 mV.  
D49 and D50 are the difference values, divided by 100.  
`D49=(C49-B49)/100`  
`E49=B49+$F$47*D49`  
`E50=B50+$F$47*D50+E49`  
The question is, where do I set VR5, so that I get 603.9 mV at 183 degrees C.  
I calculated that to be ~18%.  
I have measured the TCV (at A1/1) at both ends of VR5 (at room temperature) and set it to 18%.  
I have changed R2 from 2k to 1k, to be able to set a higher max. voltage / temperature.  
Min. voltage is set to 89 mV, which is around 20 degrees C,  
Max. voltage is set to 1650 mV, which is 500 degrees C.  

### Result  
It seems the soldering station is now working fine,  
solder melts at ~160 and I can solder at around 200.  :)  



