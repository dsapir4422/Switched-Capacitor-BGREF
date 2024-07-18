# Switched-Capacitor-BGREF
In This project I will show the design of Switched Capacitor (SC) Bandgap reference voltage (BGREF) in 65[nm] technology.
The design is based on the following article - **“A Miniaturized 0.003 mm2 PNP-Based Thermal Sensor for Dense CPU Thermal Monitoring”** by Ori Bass, Joesph Shor.

This project was part of a final design in Analog Integrated Circuits course at Bar-Ilan University.

## Design specification
* VDD = 1.8[V]
* TC < 10 PPM
* Vref = 1[V]
* Ibias = 10[uA]
* Pdiss < 200uW
* dT (Temperature range) = -10:110[C]
* Clock = 2[MHz]

## Design Explained in details
Circuit contains the followings sub-circuits - 
* Diodes to create VBE and dVBE
* Switch cap adder
* Cascode Common Source (CS) with bias network (“Telescopic” amplifier)
* Non-overlap digital circuit for s1,s2 phases
* Beta multiplier (“constant Gm”) for stable bias current

**Main circuit functionality**

Main circuit schematic - 

<img width="500" alt="image" src="https://github.com/user-attachments/assets/27cd90d7-a264-48f2-b141-b1136c862797">

The different circuit elememts are - 
* **Sensing elements** - PNP BJT diode connected for VBE (vbe1) and another PNP BJT for creating dVBE on N2 node
* **Switch cap adder** - for adding VBE and dVBE with C1,C2 factor 
* **2 phases** -  s1 (integration) and s2 (evaluation)
* **CS amplifier** for producing Vref voltage
* C2/C1 ratio determine PTAT/CTAT for temperature independent Vref
* C3 value for Vref voltage scaling

**Integration phase (s1)**

In the integration phase C1,C2 are being charged with VBE (n1,n2 nodes).
CS amplifier is auto-zero to cancel out DC offset and set the bias point.
Vref = Vtrip, which is the bias point of the amplifier.
Charge conservation - $Q_{s1} = C_3(V_{trip}-0) + C_2(V_{BE}-V_{trip}) + C_1(V_{BE}-V_{trip})$

Circuit switch position is as following - 

<img width="500" alt="image" src="https://github.com/user-attachments/assets/e7e917e7-9e28-4155-ab21-2cd6d172ea6e">

**Evaluation phase (s2)**

In the evaluation stage C1 is grounded and C2 is charged with VBE from 2nd diode.
Now C2 is charged with VBE from 1st diode and with VBE from 2nd diode, leading to dVBE on C2.
N3 node sum VBE (CTAT) and dVBE (PTAT) voltages to generate Vref from amplifier output.
Charge conservation - $Q_{s2} = C_3(V_{trip}-V_{ref}) + C_2(V_{BE_8}-V_{trip}) + C_1(0-V_{trip})$

If we equate the sum of charges (Qs1=Qs2) - 

<img width="400" alt="image" src="https://github.com/user-attachments/assets/c2f72d1b-27d5-4445-a4f1-f0c5577e2ec6">


Circuit switch position is as following - 

<img width="500" alt="image" src="https://github.com/user-attachments/assets/e3938482-68aa-4945-9fca-b21f0022e114">


## Design schematic
Main circuit schematic - 
<img width="1083" alt="image" src="https://github.com/user-attachments/assets/20afca08-19a0-492f-aeb7-501de8e2e68a">

CS amplifier ("Telescopic") - 

Bias network consists of low-voltage cascode current mirror for PMOS devices and a basic current mirror for NMOS device
![image](https://github.com/user-attachments/assets/0960324f-e203-4ff7-b0ba-6dfa8d120ab0)

Non-overlapping circuit  - 

Digital circuit to generate s1,s2 phases to create a non-overlap period of 2[ns] with 2[MHz] clock.
The non-overlap period creates a dead time region to avoid switching leakage.
Numbers of buffers (2 NOT gates) determine the delay which creates the non-overlap period.

<img width="800" alt="image" src="https://github.com/user-attachments/assets/af2fbabe-d867-4cd5-bf50-90e3eef740c3">

Transmission Gate circuit - 

<img width="400" alt="image" src="https://github.com/user-attachments/assets/85824b24-ad00-4d2f-a92c-8dd241a10cfa">

Beta-Multiplier circuit - 

<img width="1000" alt="image" src="https://github.com/user-attachments/assets/6d949460-e4ec-4590-8db8-8b5c4bbe14ad">


## Main Circuit Simulation results 
In order to test circuit behavior, we ran transiant simulation under 30[C] with the following Test Bench - 

<img width="600" alt="image" src="https://github.com/user-attachments/assets/85c32e1f-2d02-422a-a241-402398a301c2">


Capacitor values (According to article) - $C_1 = 41.2[fF], C_2 = 410[fF], C_3 = 61.8[fF]$.

Sensing elements (VBE's and dVBE) signals on the left, clock phases and node voltages on the right - 

<img width="800" alt="image" src="https://github.com/user-attachments/assets/a5f46d9d-e654-45b1-b2da-0f1ba7be3471">

Calculating Vref by theory (from C's and N1,N2 voltages) - $V_{ref} = (C_2 * PTAT + C_1 * CTAT)/C_3 = 0.898[V]$
While the measured Vref on CS amplifier output (Vref node) - $V_{ref} = 0.911[V]$ which is offset by 13[mV]

<img width="800" alt="image" src="https://github.com/user-attachments/assets/ac7d0186-086e-44fb-b85f-03e8761ee987">

Next, we simulate Vref vs. temperatures by sampling voltages at the end of s2 phase (evaluation) - 

<img width="800" alt="image" src="https://github.com/user-attachments/assets/f6003b6f-eda2-4131-ac44-0f02abf47355">

with delta Vref pk2pk of 0.3[mV], we can calculate $TC = dV_{ref}/(V_{avg}*dT) = 2.6[PPM]$ on TT corner!

Monte-Carlo simulation for mismatch show 13[mV] offset - 
<img width="666" alt="image" src="https://github.com/user-attachments/assets/4cd56ced-a6b7-4067-8260-d414e215adea">


## Sub-circuits Simulation results 
Common-Source single ended Gain - $A_v \approx g_{m1}[(g_{m2}r_{o2}r_{o1})||(g_{m3}r_{o3}r_{o4})$ = 63dB, was simulated using STB - 
<img width="800" alt="image" src="https://github.com/user-attachments/assets/9e7b4e2c-acad-45b4-b33f-d08ff390e7ab">

Non-overlap clock with 2[ns] delay - 

<img width="800" alt="image" src="https://github.com/user-attachments/assets/b5399230-a376-4e4a-82b1-8496322a712a">

Beta-multiplier start-up - 

<img width="800" alt="image" src="https://github.com/user-attachments/assets/82ac9bb8-3247-476e-b3f9-da6f66b26c6e">





