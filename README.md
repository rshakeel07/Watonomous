# LM1117 Adjustable Regulator PCB

**Name:** Rayan Shakeel
**Email:** [r2shakee@uwaterloo.ca](mailto:r2shakee@uwaterloo.ca)
**Date:** September 25, 2026
**Revision:** 2

## Notes

Just wanted to say, thanks for letting me submit this a little bit late. Altium was giving me some really bad trouble the past couple weeks, but I had been keen on joining this even before the semester started (tysm) 😭🙏.

Finished on KiCad, but will resolve before hopefully joining 🤞.

Also, for the BOM CSV file, it is a bit strange and different on KiCad, which is why I made my own here as well.

* **Regulator:** LM1117MP-ADJ/NOPB was used. This was because the adjustable version made it so that we could choose 3.3V specifically.
* **Package:** Used SOT-223 for the thermal and hand-soldering balancing.
* Deviated from the datasheet's 100uF capacitor value in favor of something less, however with a better ESR for what we are looking for while still having a good margin to work with.
* Unsure about if a VIA needs to be used with every single capacitor ground, and how that works, along with if the math for T_J was done correctly since it was my first time learning it.
* Also, for Gerber and NC, there is no 4.4. It is only 4.5 and 4.6 in KiCad.

---

# BOM

| Part          | MPN              | Digikey Part          | Package / Case |       Unit Price | Datasheet                                                                                                                                          | Lifecycle     |
| ------------- | ---------------- | --------------------- | -------------- | ---------------: | -------------------------------------------------------------------------------------------------------------------------------------------------- | ------------- |
| Regulator     | Already Given    | Already Given         | Already Given  |    Already Given | Already Given                                                                                                                                      | Already Given |
| C1 / C2       | TCTP1C106M8R     | TCTP1C106M8R          | 0805           |             1.17 | [Datasheet](https://datasheets.kyocera-avx.com/tct-series.pdf)                                                                                     | Active        |
| C3            | TLJR476M006R3200 | TLJR476M006R3200      | 0805           |             1.02 | [Datasheet](https://datasheets.kyocera-avx.com/TLJ.pdf)                                                                                            | Active        |
| R1 / R3 (121) | RMCF0805FT121R   | RMCF0805FT121R-ND     | 0805           |             0.11 | [Datasheet](https://www.seielect.com/catalog/SEI-RMCF_RMCP.pdf)                                                                                    | Active        |
| R2 (200)      | RNCP0805FTD200R  | RNCP0805FTD200R       | 0805           |             0.11 | [Datasheet](https://www.seielect.com/catalog/SEI-RNCP.pdf)                                                                                         | Active        |
| D1            | XL-2012SURC      | 5962-XL-2012SURCTR-ND | 0805           | 0.01125 (for 3k) | [Datasheet](https://mm.digikey.com/Volume0/opasdata/d220001/medias/docus/8903/XL-2012SURC.pdf)                                                     | Active        |
| J1 / J2       | M20-9990246      | M20-9990246           | 2.54mm x 2     |             0.10 | [Datasheet](https://content.harwin.com/m/7d31e750480889aa/original/C001-C001XX-Component-Specification-M20-Series-2-54mm-pitch-PCB-connectors.pdf) | Active        |

---

# 4. Justification

## Capacitors

### C1

* 10uF, as mentioned on the datasheet.
* Tantalum polarized capacitor.
* Voltage rating: 16V.
* Net voltage: ~5V.
* Derating ratio is approximately 3.
* This is because of the ESR window for the tantalum and makes it so that it does not experience extreme heat.
* The reason for the 3x instead of 2x is just for stability and is because of any sort of noise or extra voltage that may occur or be placed on accidentally.

### C3

* Recommended from the datasheet that if using C_adj, the uF value exceeds at least 22, which is why I chose 47 for the extra buffer.
* Voltage rating is 6.3V, which is good against the 3.3V that it experiences, giving it a 2x derating ratio.

### C2

* C_adj was said to be lower than 11uF in the datasheet, so we picked 10uF.
* Voltage across the node is around 2.05V (3.3V - 1.25V reference voltage).
* The reason for the overkill 16V is just so that we did not have to choose multiple types of capacitors since we could just get copies of the same one. This would be easier for the long run and mean we would only have to buy one thing.

## R1

* Default value on datasheet.

## R2

* Doing the math with the formula given:

\(V_{out} = 1.25(1 + R_2/R_1)\)

* Working backwards to find 3.3V output gives 198Ω.
* The closest available value is 200Ω.

## R3

* Similar reason for the value of C2, since it means that the LED will work.
* Using:

\(R_3 = \frac{V_{out} - V_f}{I_{LED}}\)

* This gave us 121Ω after assuming I_LED as 10mA, since the maximum is 20mA and we want some buffer, sticking to the 2x trend as before.

## D1

* Red + 0805.
* V_f is 2.1V.

## J1 / J2

* Standard 2.54mm headers that would connect to most things.
* Chosen specifically because they can handle the current and voltage ratings we are giving them.
* Vertical configuration.

---

# 5.1 Voltage Tolerance

* R1 and R2 have 1% tolerance.
* V_ref = 1.238 - 1.262V from the datasheet.
* The approach was choosing the combination of V_ref, R1, and R2 that makes R2/R1 either the highest or lowest.

### Minimum

* V_REF = 1.238V
* R2 = 198Ω
* R1 = 122.21Ω
* Using the equation from before gives:

**V_out = 3.24V**

### Maximum

* V_REF = 1.262V
* R2 = 202Ω
* R1 = 119.79Ω
* Using the equation from before gives:

**V_out = 3.39V**

Therefore:

**Absolute worst case = 3.24 - 3.39V**

This accounts for resistor tolerance and reference voltage.

If ADJ is floating, then the entire thing would not be controlled and would not be regulated, since the equation we have been using would become invalid.

---

# 5.2 Thermal

\(P = (V_{in} - V_{out}) \times Load\)

At 500mA and 5V / 3.24V respectively, we get:

**P = 0.88W**

### Efficiency

\(Efficiency = \frac{V_{out}}{V_{in}}\)

Using the nominal output:

**Efficiency = 66%**

### Thermal Resistance

Theta_JA drops as copper increases.

We chose this because when finding the temperatures at 45°C and 25°C, we got Theta_JA values of:

* **133.24°C/W**
* **142.05°C/W**

Using:

\(T_J = T_A + P \times \Theta_{JA}\)

where T_J is never allowed over 150°C, and T_A is given to us. P and Theta_JA are given to us via the chart and calculations.



# 5.3 Capacitor ESR

* Base minimum is 10uF and ESR should be between 0.3 and 22Ω.
* Since I am using C_adj, the minimum goes up to 22uF.
* Cannot use ceramics since ESR is too low and they are not polarized.
* Found something with 3.2Ω, which is in the range.



# 7.4 Trace Width

* The nets that carry the load current are 5V, 3V3, and GND, which carry higher amps.
* The LED branch has much less current than we calculated earlier.
* Using the table, we get around 10-12 mil as the minimum, but that seems to be the **bare minimum**.
* The actual choice was more similar to the minimum that we were supposed to give (0.25mm).
* I decided to increase it to **0.7mm** for the sake of low risk.
* We are not worried about route congestion and this still meets the requirements for board size.



# 7.5 Thermal Copper Area

* Based off previous calculations and working with **0.066 in²** for the thermal area.
* I made a block that was a bit bigger, around **74mm²**, which is around **30mm² greater** than what was required.
* This was done just to ease out the margins a bit since it seemed a bit tighter.
