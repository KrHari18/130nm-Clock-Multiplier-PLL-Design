# 130nm-Clock-Multiplier-PLL-Design

An on-chip clock mulitplier PLL designed using open source tools and sky130nm pdk

Introduction
A clock multiplier Phase-Locked Loop (PLL) is a negative feedback control system that generates a high-frequency output clock whose phase is related to the low frequency input clock. The need for PLLs is widespread as they find application in analog, digital, RF and communication systems.
The low-frequency clock can be generated using a crystal with frequencies up to 200 MHz. Generating a high-frequency clock with high spectral purity is not possible with a crystal and this is where PLLs come into play.

A simple PLL consists of a Phase and Frequency Detector (PFD), Charge Pump (CP), Loop Filter (LF), Voltage Controlled Oscillator (VCO) and a frequency divider in the feeback path

This PLL design multiplies clock frequency by 8 times.

Significance of a PLL

The processors used in our smartphones have various modules inside serving different purposes. These modules may include CPU, GPU, RAM, WiFi module, Audio module, and so on.
The interesting thing is that each of these modules may not run at a particular clock frequency. For example, the CPU would need a high clock frequency for performing calculations faster, while the Display module may not.
This is where a Phase-Locked Loop circuit comes into the picture. As shown in Figure above, a PLL is installed at the clock entry point of each above module to produce the desired frequency at which the module would operate.

Specifications
Pre layout simulations
Parameter	Description	min	val	max	Unit	Conditions
VDD	Digital Supply	-	1.8	-	V	T = 27C
FCLKREF	Reference	5	9	12.5	MHz	T = 27C
FCLKOUT	Output Clock	40.98	71.42	100	MHz	PLL Mode, T = 27C
Vctrl	Control Voltage	0.77	0.817	0.844	MHz	PLL Mode, T = 27C
DC	Duty Cycle	60.24	61.26	61.38	%	T = 27C
TSET	Settling Time	~12	~11.5	~9	us	T = 27C
Post layout simulations
Parameter	Description	min	val	max	Unit	Conditions
VDD	Digital Supply	-	1.8	-	V	T = 27C
FCLKREF	Reference	5	9	12.5	MHz	T = 27C
FCLKOUT	Output Clock	40.76	73.26	100	MHz	PLL Mode, T = 27C
Vctrl	Control Voltage	0.732	0.780	0.789	MHz	PLL Mode, T = 27C
DC	Duty Cycle	56.82	58.82	60.27	%	T = 27C
TSET	Settling Time	~8.6	~5.1	~6.4	us	T = 27C
Block Diagram
block diagram

EDA tools used
xschem
ngspice
magic vlsi
Pre layout simulations
1. Phase frequency detector (PFD)
A phase frequency detector or a phase comparator outputs an UP or a DOWN signal depending upon the timed phase difference between the input signal and reference signal. If the input signal leads the reference signal an UP signal is produced whereas if the input lags the reference signal a down signal is produced.

Circuit of PFD used

Screenshot 2024-03-19 221328
input signal - f_clk_in
reference signal - f_vco
There is a 1ns difference between the time period of f_vco and f_clk_in. This is done to emulate phase & frequency error.
Expected output

PDF
Simulated output

considering constant phase difference between input signal and reference signal
pfd const phase difference




2. Charge Pump and Loop filter
Charge Pump
These UP and DOWN pulses are fed into the input of the charge pump. The charge pump is a combination of switches connected to the power supply. This system acts as a current source/sink which injects/draws current into/from the loop filter, based on the duty cycle of UP/DOWN signal.

Circuit of Charge Pump used

Screenshot 2024-03-20 004414
In the above schematic transistors M4 and M3 act as current sources while transistors M18 and M10 are current mirrors. The schematic shown above contains the loop filter. One can see it at the Vctrl output pin.
Loop filter
This charge pump current is fed into the low pass filter. The low pass filter is a capacitor in series with a resistor. The entire system is connected in shunt with the control loop. The duration of pulses from the charge pump decides the amount of charge injected in the capacitor.

Circuit of loop filter used


This charge on the plates of the capacitor generates a voltage, which is then used as the control signal (Vctrl) to the VCO.
Design considerations of Loop filter

The loop filter consists of a resistor in series with a capacitor. This combination is in parallel with another capacitor as shown in figure above Capacitor C3 adds an integrator path and introduces a pole. The resistor adds a proportional path and introduces a zero. Thus, the system is stable with the addition of these two paths. Capacitor C5 is used to suppress the Vctrl ripple. However, the addition of an additional capacitor comprises on the stability of the PLL. This is because C5 adds another pole. So, to mitigate this issue the value of C5 is chosen to be very small than C3 (~C5 = 0.2*C3)
Expected output (CP+LF)

image
Simulated output (CP + LF)

Charge pump output for UP signal

cp_up
Charge pump output for DOWN signal

cp_down

Voltage controlled oscillator
The control voltage is fed to a differential ring oscillator VCO. The VCO produces an output which is oscillating in nature. The frequency of these oscillations is a function of control voltage.

Schematic of VCO

vco_full

Simulated output of VCO

vco_o

We can see that the frequency of oscillations increases with increase in control voltage input to VCO.

Frequency Divider
The frequency divider used in this circuit divides the input frequency by 8 times.
Simulated output

fd

Full PLL Simulation
Integrated schematic

pll
Pre layout Simulation results
Generates 8x Multiplied Clock

Pre-Layout:

Frequency Obtained for 5Mhz input:     40.98MHz
Frequency Obtained for 12.5Mhz input:     100MHz

Duty Cycle obtained:     60.24% at 40MHz and 61.38% at 100MHz

Lock-in starts at ~12us for 5MHz input and ~9us for 12.5Mhz input

40Mhz output

5mhz, 40 98mhz, 12us

100Mhz output

12 5mhz, 100mhz, 9us

Close up for 100Mhz

close up for 100mhz op
Red signal constantly overlaps blue signal indicating locked state.

Pre-layout summary

Input Frequency	Output Frequency
5MHz	40.98MHz
9MHz	71.42MHz
10MHz	80.97MHz
12.5MHz	100MHz
Post layout circuits
a. Phase Frequency Detector

Fig: Layout of Phase Frequency Detector (PFD)
b. Voltage controlled oscillator

Fig: Layout of Voltage controlled oscillator (VCO)
c. Charge pump & loop filter

Fig. - Combined layout of charge pump and loop filter (large boxes are the capacitors of the loop filter)
Charge pump close up

d. Frequency divider

Fig: Layout of Frequency divider by 8 circuit
The frequency divider by 8 is made from cascading three frequency divider by 2 circuits. The fd/2 ckt layout is shown below


d. Integrated PLL layout
pll


circuit area = 0.2641 mm2

PLL Layout closeup
pll close up

Post layout simulations
Parameter	Description	min	val	max	Unit	Conditions
VDD	Digital Supply	-	1.8	-	V	T = 27C
FCLKREF	Reference	5	9	12.5	MHz	T = 27C
FCLKOUT	Output Clock	40.76	73.26	100	MHz	PLL Mode, T = 27C
Vctrl	Control Voltage	0.732	0.780	0.789	MHz	PLL Mode, T = 27C
DC	Duty Cycle	56.82	58.82	60.27	%	T = 27C
TSET	Settling Time	~8.6	~5.1	~6.4	us	T = 27C
1. 5 Mhz input
5mhz

Fig - PLL simulation for 5Mhz input frequency

Red - input signal
Blue (on red signal) - feedback signal
Yellow - up signal
Green - down signal
Pink - control voltage
Brown - output signal


5mhz io

Fig - input - output for 5 Mhz operation


5mhz close up

Fig - settled output for 5 Mhz operation


2. 9 Mhz input
9mhz 

Fig - PLL simulation for 9Mhz input frequency


9mhz io

Fig - input-output for 9mhz operation


9mhz close up

Fig - settled output for 9mhz operation


2. 12.5 Mhz input
12 5mhz 

Fig - PLL simulation for 12.5Mhz input frequency


12 5mhz io

Fig - input-output for 12.5mhz operation


9mhz close up

Fig - settled output for 12.5 Mhz operation


To use this repository
Clone the github repository in your ubuntu system
Go to the mag folder in the postlayout directory and open terminal in the folder
Type ‘ngspice pll_tb.spice’
Paste the following command after simulation is complete
 plot V(f_clk_in)+8 V(x1.pfd_lay_0/f_vco)+8 V(x1.cp_0/up)+6 V(x1.cp_0/down)+4 V(x1.cp_0/vctrl)+2 V(f_clk_out)
This will show the plots mentioned in the post layout section of this github repository

To see the layout

Type in folder terminal window

 magic pll.mag 
The layout will be hollow when the magic tool is opened for the first time. To see the layout clearly. Open the magic terminal (opens with the magic tool) and type

 % select top cell
 % expand
NOTE : all the above steps are valid for a system which has ngspice and magic (sky130nm integrated) installed. The same can be installed from here

References
[1] Paras Gidd, avsdpll_3v3 github repository

[2] Sun, Qingbo et al. “On-chip Phase Locked Loop (PLL) design for clock multiplier in CMOS Monolithic Active Pixel Sensors (MAPS).” (2009).

[3] Vincent Von Kaenel, “A 320 MHz, 1.5 mW @ 1.35 V CMOS PLL for Microprocessor Clock Generation” (1996)

[4] J. Dhurga Devi “Jitter Reduced Self Biased PLLs—A Systematic Simulation Study” [5] Jitter and Phase Noise in Ring Oscillators, Ali Hajimiri, Sotirios Limotyrakis, and Thomas H. Lee

[6] Mo Zhang, A Programmable Frequency Divider Having a Wide Division Ratio, and Close-to-50% Output Duty-Cycle

[7] George Tom Varghese, MS thesis on “Phase Locked Loop Design as a Frequency Multiplier” NIT Rourkela (2009)

[8] Yang Liu, “Phase Noise in CMOS Phase-Locked Loop Circuits” (2011)

[9] Rushabh Mehta, Design and implementation of a phase locked loop for high-speed serial links

[10] Shruti Suman, An Improved Performance Ring VCO: Analysis and Design (2018)

[11] Scott Buchanan, Phase Locked Loop Integrated Circuit (2015)

Acknowledgements
I thank Mr. Kunal Ghosh, co-founder VSD, for helping me through out the project, I would like to thank my college professors Dr. Chandradeep Singh and Dr. Kunal Singh for encouraging me initiate this project and helping me with conceptual doubts.
I thank Stephan Scrippers, David Mitchell Bailey, Luis Henrique Rodovalho, Tim Edwards and Lucas Daudt Franck from the sky130 Slack channel for helping me with the doubts in xschem and skywater pdk.
Rajdeep Mazumder, who's VLSI Project help video helped me install the open source tools
I would also thank Paras Gidd, who's PLL repository was a reference for this project.
Contact Information
Himanshu Singh (Author), BTech EE - himanshuksingh107@gmail.com
Kunal Ghosh, Co-founder, VSD Corp. Pvt. Ltd. - kunalghosh@gmail.com
