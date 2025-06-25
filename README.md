## SG3525A_Based_PWM_Generator
The SG3525A series of pulse width modulator integrated circuits are designed to offer improved performance and lowered external parts count when
used in designing all types of switching power supplies. The on-chip + 5.1 V reference is trimmed to ±
1 % and the input common-mode range of the error
amplifier includes the reference voltage eliminating
external resistors. A sync input to the oscillator allows multiple units to be slaved or a single unit to be
synchronized to an external system clock. A single
resistor between the CT and the discharge terminals
provide a wide range of dead time ad- justment.
These devices also feature built-in soft-start circuitry
with only an external timing capacitor required. A
shutdown terminal controls both the soft-start circuity and the output stages, providing instantaneous
turn off through the PWM latch with pulsed shutdown, as well as soft-start recycle with longer shutdown commands. These functions are also controlled by an undervoltage lockout which keeps the outputs off and the soft-start capacitor discharged for
sub-normal input voltages. This lockout circuitry includes approximately 500 mV of hysteresis for jitterfree operation. Another feature of these PWM circuits is a latch following the comparator. Once a
PWM pulses has been terminated for any reason,
the outputs will remain off for the duration of the period. The latch is reset with each clock pulse. The
output stages are totem-pole designs capable of
sourcing or sinking in excess of 200 mA. The
SG3525A output stage features NOR logic, giving a
LOW output for an OFF state. <br><br>
![image](https://github.com/user-attachments/assets/ca440edb-4205-4c56-b5fb-5c73451ad0c2)<br>



## Working of SG3525A IC<br>
The IC generates a pwm signal by comparing the control voltage and sawtooth carrier wave using the PWM comparator<br>
![image](https://github.com/user-attachments/assets/bf1d12d5-673e-409a-8f2a-77fa59605697)<br><br>

<ol>
  <li>The making of the Sawtooth/Ramp(Carrier Wave) - </li>
  <ol>
    <li>The rise of the sawtooth is made by the linear portion of the charging capacitor voltage graph.<br>
  CT is charged via Vref(5.1 V) with RT in series till Vcap = Vref/2 and during this period the Vcap varies linearly with its slope given by RT. 
      The higher the RT, the lower the slope. This gives the rising part of the sawtooth.<br>
   <ul> 
  <li>Ramp Slope = Vref/(2*Rt*Ct*ln2)</li>  
  
  <li>Rise Time = Rt*Ct*ln2</li>        
  Note: ln2 is approximately 0.7<br>
  </ul>
  </li>
  <li>The falling part of the sawtooth is made by the discharging of the capacitor.<br>
  CT is discharged through RD once Vcap = Vref/2 and then the voltage across CT gives the falling part of sawtooth. 
    The value of RD determines the deadtime or time of fall. Higher the value of RD, higher the deadtime. 
    The time of discharge can be assumed to our convenience like 5*τ but the datasheet recommends to use the  value of  3*τ.
    <br>
  
  Fall time = 3*τ = 3*Rd*Ct<br>
  Hence after combining the a, b we get the sawtooth waveform with
  </li>
  
  ![image](https://github.com/user-attachments/assets/8d474c24-0837-4fbe-a6dc-8ddd3de765ea) <br>
  The oscillator range is from 100hz to 500Khz hence the acceptable values of Ct,Rt and Rd are:
  <ul>
    <li>CT = 0.001 to 0.2 µF</li>
  <li>RT = 2 K to 150 KΩ</li>
  <li>RD = 0 to 500 Ω</li>
  </ul>
  
  </ol>


<li>Making of Reference or Control voltage - </li>
Reference is given to the non inverting terminal of the IC and this value must be between 0 - Vref(5.1),<br>
This reference will then be compared with Carrier to get a PWM signal. Changing value of reference will alter the duty cycle of the PWM at output, higher the reference higher the duty cycle of output PWM.<br>
Note: The SG3525A is capable of producing a maximum of 50% duty cycle at the output and this is because frequency at output is F/2 . This will be explained in detail ahead <br>

![image](https://github.com/user-attachments/assets/b3db83c0-db84-45cd-984b-6ae048da14dd) <br>

<li>PWM Comparator - </li>
As seen from the diagram above the PWM takes input from CT and Error Amp. When compensation is connected to inverting input, the error amp acts like a buffer and the net input to PWM comparator is thus the Non inverting input and CT.
PWM Comparator just compares the value of the two signals. <br>If Reference(non inverting input) is higher than the Carrier (CT), it outputs low and high if vice versa. <br>

<li>SR Latch - </li>
The use of latch was not properly understood but according to the datasheet, it helped in instantaneous shutting down and latching of PWM output in event of pulsed signal at the shutdown pin. <br>

<li>D Flip Flop - </li>
The D flip flop is the reason the frequency of the outputs is halved. A D Flip Flop triggers at the positive edge of the clock. From the diagram above, the flip flop is receiving oscillator as the clock pulse which has a frequency of F. <br>
Now the output Q and Q’ change only at positive pulses of the oscillator hence, frequency of Q and Q’ is F/2.<br>

<li>Output NOR - </li>
The output NOR gate implements the logic given below<br>
<ul>
  <li>Output A = (Q(D flip flop output) + UnderVoltageLockout + Shutdown + Latch output)’</li><br>
  <li>Output B = (Q'(D flip flop output) + UnderVoltageLockout + Shutdown + Latch output)’</li><br>
</ul>
Hence from this we can analyse that the outputs A and B are complementary to each other and their frequency is F/2.<br>

The timing diagram for Output A has been prepared<br>
![Untitled](https://github.com/user-attachments/assets/9a1b8858-0a3c-441a-ab76-83fcdf0b2eb2)


## Designing Custom PWM Generator
After understanding the working and simulations of SG3525A, it was decided to design a customizable control card with variable resistors to control values of RT, RD and also to control the Vc and VNI via potential dividers. 
Additional toggle switches were added to turn on the circuit as well as to operate it in open loop/closed loop configuration utilizing feedback. 
Currently, the feedback was ignored in these iterations but it is a scope in future iterations.  The following specifications were decided:<br>
![Screenshot 2025-06-25 162324](https://github.com/user-attachments/assets/902c3bec-cd55-4e67-8bf6-1e96a3b924c5) <br>
The following model was simulated on LTSpiceIV, and component selections are explained in detail below:<br>
![Screenshot 2024-06-22 020232](https://github.com/user-attachments/assets/45ca92c2-b0cd-4f9c-a569-8c1f643333bf)
 <br>
<ol>
  <li>Input Voltage (Vin/Vcc): <br>
  The design adheres to the recommended operating range of the SG3525A by utilizing a supply voltage (Vin/Vcc) within the specified limits of 8 V to 35 V. In this case, a value of 12 V was chosen for Vin/Vcc.</li>

  <li>Output Voltage (Vc): <br>
    A voltage divider circuit was employed to generate a reference voltage (Vc) from Vcc. The selected value of 6V for Vc falls within the acceptable range of 4.5 V to 35V as outlined in the datasheet.</li>
  <li>Internal Reference Voltage (Vref): <br>
    The datasheet value of 5.1 V was adopted for the internal reference voltage (Vref) of the SG3525A</li>

  <li>Current Limiting Resistor (R): <br>
    To restrict the current through the reference voltage path to a maximum of 15 mA, a series resistor (R) with a value of 330 Ω was calculated based on the datasheet specifications and the chosen Vref value.</li>

  <li>Control Voltage Adjustment: <br>
    A 1 kΩ potentiometer was connected in series with the reference voltage path to enable variation of the voltage at the non-inverting terminal of the comparator. 
    This adjustment, in turn, affects the pulse width of the output signal.</li>

  <li>Timing Capacitor (CT): <br>
    A standard value of 4.7 nF was chosen for the timing capacitor (CT) of the oscillator circuit. This capacitance falls within the recommended range of 1 nF to 200 nF as specified in the datasheet.</li>

  <li>Dead Time Control (RD): <br>
    A 500 Ω potentiometer was incorporated across the dead time control resistor (RD) to provide adjustability for the dead time parameter.</li>

  <li>Frequency Control (RT): <br>
    A series resistor of 2 kΩ was connected in series with a 10 kΩ potentiometer across the timing resistor (RT) of the oscillator. This configuration allows for variation of RT from 2 kΩ to 12 kΩ, which encompasses the acceptable range of 2 kΩ to 150 kΩ as indicated in the datasheet. 
    By adjusting the potentiometer, the oscillator frequency can be tuned within a range of approximately 24.7 kHz to 132 kHz.</li>
  <li>Decoupling Capacitors: <br>
    To minimize the impact of AC components on the circuit's operation, 0.1µF decoupling capacitors were employed at various key pins of the SG3525A.</li>
  <li>Comparator Configuration: <br>
    The datasheet recommendation of shorting the compensating and inverting pins of the comparator was implemented in this design.</li>
</ol>
<br>

![image](https://github.com/user-attachments/assets/4a92d610-53b2-48fd-9838-c574c6e7e470)
<br>

## Schematic diagram and PCB Design
A schematic of the proposed circuit was made using KiCAD and then converted to a single layer PCB with manually routed traces and through hole components.
<br>
![Screenshot 2025-06-25 164223](https://github.com/user-attachments/assets/6e824592-11eb-43ee-8fdc-f9d311cfaaee)
<br>
![image](https://github.com/user-attachments/assets/af956947-fb43-441d-8bbc-02b8d09dc137)
<br>
![image](https://github.com/user-attachments/assets/473848d3-1b7e-4b41-9233-419dd2baeca0)
<br>
Finally a practical board was also made on a perfboard using availaible components and the outputs A & B were used to drive the 2 legs of a H-Bridge Inverter consisting of 4 IRFZ44N mosfets<br>
![image](https://github.com/user-attachments/assets/af2d9579-aafc-4f97-99e0-da67d22c63ad)









