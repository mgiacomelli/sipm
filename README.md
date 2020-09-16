# Introduction

These are the design files for the SIPM amplifier described in M. G. Giacomelli, "Evaluation of silicon photomultipliers for multiphoton and laser scanning microscopy," J. Biomed. Opt. 24, 1 (2019) available from https://www.spiedigitallibrary.org/journals/journal-of-biomedical-optics/volume-24/issue-10/106503/Evaluation-of-silicon-photomultipliers-for-multiphoton-and-laser-scanning-microscopy/10.1117/1.JBO.24.10.106503.full?SSO=1.  

The design is a relatively low cost, single photon sensitive fluorescense detector using the S14420 silicon photomultiplier from Hamamatsu, which is a TO-5 mounted detector with high QE from 500-700nm.  Compared to a PMT the S14420 has higher effective quantum efficiency (especially above 600nm) but higher dark counts.  

![Image of SIPM](https://github.com/mgiacomelli/sipm/blob/master/Improved%20Design/sipm-render.jpg)

# Files

The 'Old Gerbers' folder contains the exact gerber files used to manufacture the boards in the manuscript.  The Improved Design folder has KiCAD and gerber files for an updated version of that design that has a few improvements.  In addition, since the design files are present, you can make changes to the design as needed using the open source EDA software KiCAD.  If you only want to make a board, you can upload either set of Gerber files directly to JLCPCB, SEEED, etc.  

# Setting up KiCAD

This project was designed using the current development (5.99) branch of KiCAD, so you must be running that or newer.  Windows builds are available here: 
https://kicad-downloads.s3.cern.ch/index.html?prefix=windows/nightly/

# Ordering boards

Go to https://jlcpcb.com/ or https://www.seeedstudio.com/fusion_pcb.html or the service of your choice, upload the Gerber files, choose 2 layer, and pay.  At present prices, 10 boards will cost about 15-20 dollars shipped to most of the world.  

# Bill of materials

An interactive BOM tool is available here to make placing and ordering compnents easy:  http://htmlpreview.github.io/?https://github.com/mgiacomelli/sipm/blob/master/Improved%20Design/KiCAD/bom/ibom.html

In addition, you will need the matching power cable.  The part is DF13-5S-1.25C, and unless you have the matching crimp tool, you will need 5x H4BBT-10112-R8 pre-crimped leads.  I recommend getting multiple colors as mixing up the wires will destroy your board.  

# Assembly

The easiest way to assemble these boards is using solder paste and a reflow oven.  The inexpensive T-962 clones on Amazon will work fine for simple boards such as this.  Alternatively, they can also be manually soldered.  The design uses a mix of 0402 and larger parts, which can be challenging to solder with the wrong tools.  A ~0.6-1.2mm diameter chissel type soldering tip, high quality no clean flux such as amtech nc-559-v2-tf, 40/60 lead solder, and good tweezer such as Hakko CHP 5-SA or equivilent are strongly recommended if reflow is not used. These are inexpensive and readily available on Amazon. 

# Circuit design

Both the old and improved designs are a 1500 V/A transimpedance amplifier paired with a pole zero compensation (PZC) circuit.  Both PZCs have the same frequency cut on.  The old design uses a capacitive PZC.  While more obvious, this design is problematic because the capacitance of the PZC loads the amplifier.  The original design put an isolation resistor before the capacitance, but this design halves the gain and is less stable.  The improved design implements the same filter but using an inductor.  As a result the resistor and reactive element are flipped, meaning that the opamp is not directly connected to the reactive element and so the isolation resistor is not needed. Therefore gain is doubled.  Further since the PZC resistance is larger, stability is improved.  

# PZC

The values of L1, R5 and R6 define the PZC cut on and amplitude.  These values were near optimal for a previous board iteration, however, the PZC is very sensitive to the exact values so you may find that to hit very high bandwidth (40-50 MHz) or very flat pass band that the values must be adjusted.  Essentially the PZC is adding a gain that compensates for the frequency roll off caused by the RC time constant associated with the individual SIPM cell recharge through the quenching resistors, so adjusting L1 changes at what frequency the added gain kicks in, while changing the values of the resistors lets you change the strength of that gain.  By setting the values carefully, you can cancel out the recharge time of the individual APDs and greatly extend the bandwidth of the detector, often by a factor of 10 or more.  

A more intuitive explination of the PZC method is described in this article:
A. Gola, C. Piemonte, and A. Tarolli, "Analog circuit for timing measurements with large area sipms coupled to lyso crystals," IEEE Trans. Nucl. Sci. 60, 1296–1302 (2013).

# Low pass filter

It is important to add an external low pass filter, either in your ADC or inline with it to reject out of band noise.  If you do not have a suitable lowpass on your ADC, the minicircuits BLP-25 is a reasonable choice with relatively little group delay in the pass band:  https://www.minicircuits.com/WebStore/dashboard.html?model=BLP-25%2B

The bandwidth of a laser scanner is 0.312 times the scan velocity (in meters per second) divided by the spot size (also in meters).  It is a good idea to reduce the bandwidth of the detector as needed with a smaller low pass filter if you do not require 25 MHz at your scan speed.  This will reduce electronic noise.    

# Gain

Total transimpedance gain (as configured, you can adjust it) is 1500 ohms.  There is an additional factor of 2 gain in the OPA842 stage, but this merely compensates for the attenuation caused by the 50 Ohm output impedance.  In addition, the PZC attenautes by a factor of R6/R5 (about 8:1).  Thus peak ampltidue per photon at +5Vbr should be about 4.5mV.  

# Mounting

The entire board is 1 inch across and will mount in an SM1 lens tube:  https://www.thorlabs.com/newgrouppage9.cfm?objectgroup_id=3307
