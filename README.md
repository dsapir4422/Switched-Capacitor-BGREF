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
* **2 phases** s1 (integration) and s2 (evaluation) phases
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

We design a digital circuit to generate s1,s2 phases to create a non-overlap period of 2[ns] with 2[MHz] clock.
The non-overlap period creates a dead time region to avoid switching leakage.
Numbers of buffers (2 NOT gates) determine the delay which creates the non-overlap period.
<img width="800" alt="image" src="https://github.com/user-attachments/assets/af2fbabe-d867-4cd5-bf50-90e3eef740c3">

Transmission Gate circuit - 

<img width="400" alt="image" src="https://github.com/user-attachments/assets/85824b24-ad00-4d2f-a92c-8dd241a10cfa">

Beta-Multiplier circuit - 

<img width="1000" alt="image" src="https://github.com/user-attachments/assets/6d949460-e4ec-4590-8db8-8b5c4bbe14ad">


## Simulation results 

In order to test circuit behavior, we ran transiant response under 30[C] with the following Test Bench - 

<img width="600" alt="image" src="https://github.com/user-attachments/assets/85c32e1f-2d02-422a-a241-402398a301c2">
