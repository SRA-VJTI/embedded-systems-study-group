#  Embedded Systems Study Group

- [Basic of Electronics](#basics-of-electronics)
    - [Basic Electronic Components](#basic-electronic-components)
- [PCB Designing](#pcb-designing)
    - [Importance of PCBs](#importance-of-pcbs)
    - [But why to design PCBs?](#but-why-to-design-pcbs)
    - [Types of PCBs](#types-of-pcbs)
    - [Types of components PCB is designed for](#types-of-components-pcb-is-designed-for)
    - [Basic Softwares Used](#basic-softwares-used)
    - [Steps to design a PCB](#steps-to-design-a-pcb)    
- [Measurement Instruments](#measurement-instruments)
    - [Digital Multimeter (DMM)](#digital-multimeter)
    - [Oscilloscope](#oscilloscope)

# Basics of Electronics
Electronics is the science and technology concerned with regulating flow of electrical current. Put simply, it lets you move electrons around. The electronics revolution and the computer revolution go hand in hand. While this may seem like not that big of a deal, the regulation of electrons has enabled some of the most important innovations of the last century including computers, televisions, rocketships, electric cars, rocketships, video games, smartphones.

### Resistance
Before having hands on circuits, a very important consideration to keep in mind is that electricity in a circuit must be used. There needs to be something wired between positive and ground that adds resistance to the flow of electricity and uses it up. If positive voltage is connected directly to ground and does not first pass through something that adds resistance, like a motor, this will result in a short circuit. This means that the positive voltage is connected directly to ground.
Likewise, if electricity passes through a component (or group of components) that does not add enough resistance to the circuit, a short will likewise occur.
'Shorts are bad because they will result in your battery and/or circuit overheating, breaking, catching on fire, and/or exploding.
It is very important to prevent short circuits by making sure that the positive voltage is never wired directly to ground.'

That said, always keep in mind that 
>Electricity always follows the path of least resistance to ground.

Also note that a switch does not add any resistance to a circuit and simply adding a switch between power and ground will create a short circuit.

# Basic Electronic Components

## Resistor
![Resistors](https://content.instructables.com/ORIG/FGN/I05P/GWDY31I9/FGNI05PGWDY31I9.jpg?auto=webp&frame=1&width=1024&height=1024&fit=bounds&md=68aa629b2cf89e3a430a5373997ce8f7)

### What is it?

As the name implies, resistors add resistance to the circuit and reduces the flow of electrical current.

### How is it measured?
The electrical resistance of a resistor is measured in ohms. Resistors are rated by the value of their resistance and the electrical power given in watts, (W) that they can safely dissipate based mainly upon their size (For most low-voltage DC circuits, 1/4 watt resistors should be suitable.)

### Identification factor
Most resistors have colored stripes on the outside and this code will tell you its value of resistance. 

![Resistor](https://4.bp.blogspot.com/-LTrONlroLGs/XN6ebCCDOJI/AAAAAAAABT8/hnFundobI8kymwnH-_ZHjF4Al-4pwqxWACLcBGAs/s1600/resistor-color-code-chart.jpg)

>B B ROY of Great Britain has a Very Good Wife who wears Gold & Silver

## Capacitors
![Capacitors](https://content.instructables.com/ORIG/F00/SK40/GWDXZHOH/F00SK40GWDXZHOH.jpg?auto=webp&frame=1&width=933&height=1024&fit=bounds&md=8e91bb0c003cfedc29815e8008efb66a)

### What is it?
A capacitor is a component that stores electricity and then discharges it into the circuit when there is a drop in electricity. In simple terms, it works as a small rechargeable battery that stores electricity. However, unlike a battery, it can charge and discharge in the split of a second. You can think of it as a water storage tank that releases water when there is a drought to ensure a steady stream.

### Types
The most commonly encountered types of capacitors are ceramic disc capacitors that look like tiny M&Ms with two wires sticking out of them and electrolytic capacitors that look more like small cylindrical tubes with two wires coming out the bottom (or sometimes each end

**Ceramic Disc Capacitors** 
are non-polarized, meaning that electricity can pass through them no matter how they are inserted in the circuit. They are typically marked with a number code which needs to be decoded. Instructions for reading ceramic capacitors can be found here. This type of capacitor is typically represented in a schematic as two parallel lines.

**Electrolytic Capacitors** are typically polarized. This means that one leg needs to be connected to the ground side of the circuit and the other leg must be connected to power. If it is connected backwards, it won't work correctly. Electrolytic capacitors have the value written on them, typically represented in uF. They also mark the leg which connects to ground with a minus symbol (-). This capacitor is represented in a schematic as a side-by-side straight and curved line. The straight line represents the end which connects to power and the curve connected to ground.

### How is it measured?
Capacitors are measured in Farads. The values that you will typically encounter in most capacitors are measured in picofarad (pF), nanofarad (nF), and microfarad (uF). 

## Diodes
![Diode](https://content.instructables.com/ORIG/FL0/YF9A/GWDYTKHE/FL0YF9AGWDYTKHE.jpg?auto=webp&frame=1&width=933&height=1024&fit=bounds&md=e53469e3b59fccdafdbf79e8e2464269)

### What is it?
Diodes are components which are polarized. They only allow electrical current to pass through them in one direction. This is useful in that it can be placed in a circuit to prevent electricity from flowing in the wrong direction. 

Another thing to keep in mind is that it requires energy to pass through a diode and this results in a drop of voltage. This is typically a loss of about 0.7V. This is important to keep in mind for later when we talk about a special form of diodes called LEDs.

### Identification factor

The ring found on one end of the diode indicates the side of the diode which connects to ground. This is the cathode. It then follows that the other side connects to power. This side is the anode.

The part number of the diode is typically written on it, and you can find out its various electrical properties by looking up its datasheet.

## Transistors
![Transistor](https://content.instructables.com/ORIG/FHI/R0V9/GWDYFV1R/FHIR0V9GWDYFV1R.jpg?auto=webp&frame=1&width=1024&height=1024&fit=bounds&md=c8a266eb71ab666854fc0b4011b4562a)

### What is it?

A transistor takes in a small electrical current at its base pin and amplifies it such that a much larger current can pass between its collector and emitter pins. The amount of current that passes between these two pins is proportional to the voltage being applied at the base pin.

### Types

There are two basic types of transistors, which are **NPN** and **PNP**. These transistors have opposite polarity between collector and emitter. For a very comprehensive intro to transistors check out this page.

**NPN transistors** allow electricity to pass from the collector pin to the emitter pin. They are represented in a schematic with a line for a base, a diagonal line connecting to the base, and a diagonal arrow pointing away from the base.

**PNP transistors** allow electricity to pass from the emitter pin to the collector pin. They are represented in a schematic with a line for a base, a diagonal line connecting to the base, and a diagonal arrow pointing towards the base.

### Identification factor

Transistors have their part number printed on them and you can look up their datasheets online to learn about their pin layouts and their specific properties. Be sure to take note of the transistor's voltage and current rating as well.

## Integrated Circuits
![Integrated Ciruits](https://content.instructables.com/ORIG/FZV/9Y1E/GWDXZHP6/FZV9Y1EGWDXZHP6.jpg?auto=webp&frame=1&width=1024&height=1024&fit=bounds&md=20d732e88773a226ccdd6142632eb2d4) 

### What is it?

An integrated circuit is an entire specialized circuit that has been 
miniaturized and fit onto one small chip with each leg of the chip connecting to a point within the circuit. These miniaturized circuits typically consist of components such as transistors, resistors, and diodes.
For instance, the internal schematic for a 555 timer chip has over 40 components in it.

Each IC has a datasheet, one can learn all about that IC just by looking at it.On the datasheet you will learn the functionality of each pin. It should also state the voltage and current ratings of both the chip itself and each individual pin. Integrated circuits come in a variety of different shapes and sizes. 

### Identification

The round notch on one edge of the IC chip indicates the top of the chip. The pin to the top left of the chip is considered pin 1. From pin 1, you read sequentially down the side until you reach the bottom (i.e. pin 1, pin 2, pin 3..). Once at the bottom, you move across to the opposite side of the chip and then start reading the numbers up until you reach the top again.
Keep in mind that some smaller chips have a small dot next to pin 1 instead of a notch at the top of the chip.

## Potentiometers
![Potentiometer](https://content.instructables.com/ORIG/FW0/PEDF/GWDYTKTV/FW0PEDFGWDYTKTV.jpg?auto=webp&frame=1&width=1024&height=1024&fit=bounds&md=cee571cae812dc881571fdf5c74023cd)

### What is it?
Potentiometers are variable resistors. In plain English, they have some sort of knob or slider that you turn or push to change resistance in a circuit. If you have ever used a volume knob on a stereo or a sliding light dimmer, then you have used a potentiometer.

Potentiometers have three legs as to create a voltage divider, which is basically two resistors in series. When two resistors are put in series, the point between them is a voltage that is a value somewhere between the source value and ground.

For instance, if you have two 10K resistors in series between power (5V) and ground (0V), the point where these two resistors meet will be half the power supply (2.5V) because both of the resistors have identical values. Assuming this middle point is actually the center pin of a potentiometer, as you turn the knob, the voltage on the middle pin will actually increase towards 5V or decrease toward 0V (depending which direction that you turn it). This is useful for adjusting the intensity of an electrical signal within a circuit (hence its use as a volume knob).
 
### How is it measured?
Potentiometers are measured in ohms like resistors
 
### Identification factor

They are marked with an "A" or a "B, " which indicated the type of response curve it has.
Potentiometers marked with a "B" have a linear response curve. This means that as you turn the knob, the resistance increases evenly (10, 20, 30, 40, 50, etc.). The potentiometers marked with an "A" have a logarithmic response curve. This means that as you turn the knob, the numbers increase logarithmically (1, 10, 100, 10,000 etc.)

***
# PCB Designing

## Importance of PCBs

Eminent PCB manufacturers such as PCB Power make PCBs from thin insulating boards with a coating of metal on one or both sides. PCBs hold almost all the electronic components necessary for a device to function. Fabricators etch the metal with acid to create electrical pathways among various components that assemblers mount on the board. Using a PCB brings many advantages to the electronic industry:

1. Compact Design
2. Ease of Testing and Repair
3. Lower Assembly Errors
4. Avoiding Short Circuits
5. Low Noise and Interference
6. Improved Repeatability and Reliability

## But why to design PCBs?

Early radio receivers built using valves had a metal chassis for mounting the valves, dials and switches.

Since the total number of components were very few, and all were with leads, it was easy to solder them while they hung from terminal boards and interconnect them with wires. However, this procedure became a nightmare when circuit complexity grew and the number of components increased. Using Printed Circuit Boards (PCBs) is a solution to this problem.



## Types of PCBs

Printed circuit boards (PCBs) are the heart of all electronic products, but not all are created equal.
Below is a list of the common types of printed circuit boards.

### 1. Single Layer PCB
Single layer printed circuit boards are among some of the simplest to design and manufacture.  These boards have a single layer of conductive material (such as copper) on only one side of a non-conductive substrate.

### 2. Double Layer PCB
Double layer PCBs have one conductive layer on top of a non-conductive substrate and another conductive layer on the reverse side (top and bottom layers).  The two conductive sides can be connected using plated holes in the substrate that connect to pads on both sides of the circuit board; these are called vias.

### 3. Multi-Layer PCB
This term refers to a circuit board that has three or more conductive layers.  The conductive layers are at the top and bottom, as well as at least one conductive layer sandwiched between non-conductive substrate
the most commonly used have lower layer counts such as 4, 6, or 8 layer boards.

### 4. High Density Interconnect (HDI) PCB
HDI PCBs take advantage of precision based manufacturing technology to pack as much functionality into a small space.  This is done by using many conductive layers, laser drilled microvias, fine lines and tolerances, and advanced laminate materials.  HDI PCBs can accommodate the complex routing of high pin count chips and other high-tech miniaturized components.

## Types of components PCB is designed for

### 1. Dual In-line Package (DIP):
![DIP](https://www.raypcb.com/wp-content/uploads/2021/05/DIP-soldering-PCB.jpg
)

A dual in-line kit is a digital part kit with a pyramid shape and the electrical connecting pins in two parallel rows. The product is installed or placed in a socket on a circuit board.


### 2. Surface Mounted Devices (SMD):
![SMD](https://www.ourpcb.com/wp-content/uploads/2019/03/1-1.jpg)

Texture mounting technology (SMT) is a means by which electronic circuits are installed or positioned adsorbed on the surface of circuit boards such as PCBs. This is called the surface mounting system an electrical device SMD Service.

## Basic Softwares Used


![KiCad](https://www.kicad.org/img/kicad_logo_small.png)

**KiCad EDA**

KiCad is a free and open-source electronics design automation (EDA) suite. It features schematic capture, integrated circuit simulation, printed circuit board (PCB) layout, 3D rendering, and plotting/data export to numerous formats. KiCad also includes a high-quality component library featuring thousands of symbols, footprints, and 3D models.

#### Schematic Capture

![Schematic Capture](https://www.kicad.org/img/frontpage/kicad_eeschema.png
)

**KiCad's Schematic Editor** supports everything from the most basic schematic to a complex hierarchical design with hundreds of sheets. Create your own custom symbols or use some of the thousands found in the official KiCad library. Verify your design with integrated SPICE simulator and electrical rules checker.

#### PCB Layout

![PCB Layout](https://www.kicad.org/img/frontpage/kicad_pcbnew.png) 

**KiCad's PCB Editor** is approachable enough to make your first PCB design easy, and powerful enough for complex modern designs. A powerful interactive router and improved visualization and selection tools make layout tasks easier than ever.

#### 3D Viewer

![3D Viewer](https://www.kicad.org/img/frontpage/kicad_3dviewer.png)

KiCad's 3D Viewer allows easy inspection of your PCB to check mechanical fit and to preview your finished product. A built-in raytracer with customizable lighting can create realistic images to show off your work.

## Steps to design a PCB

The process flow for designing a PCB shows four major steps:

### 1. Requirements Gathering

This is the input stage and the designer collects information about the intended functioning of the circuit. 

### 2. Schematic Design

![SChematic Drawing](https://makerobot.files.wordpress.com/2013/01/sss3.png?w=584)

In the next stage, the designer draws the circuit and creates an orderly schematic that represents the logical functioning of the circuit. 

The software module handling the schematic drawing assigns individual part numbers to each component and interprets the interconnections to and from their pins as part of a net. The designer must assign a valid footprint to each part in the schematic.

After completion of the schematic design, the software module offers two outputs—
#### 1. A Bill of Materials (BOM) and 

The BOM is a complete list of all the components the designer has used to build the schematic. The organization uses this BOM to source the components.

#### 2. Net List.
  
The net list is an important document that the designer uses to layout the components for routing. The net list ensures that the designer faithfully transfers all interconnections in the schematic to the layout before starting on the routing.

### 3. Component Placement

![Component Placement](https://i0.wp.com/www.autodesk.com/products/eagle/blog/wp-content/uploads/2017/03/component-placement-final.png?resize=891%2C690&ssl=1
) 

The designer must carefully select the correct footprint, as a component may be available in several forms such as through-hole and surface mount types.
The designer must place each component such that it can function properly either within the specified boundary of the PCB or within a minimum area.

### 4. Routing and Output Generation

![Routing](https://cdn-blog.adafruit.com/uploads/2016/11/F8B5NG4IVA54AHJ.LARGE_.jpg)


In the final stage, the designer invokes the routing module of the CAD package. This module usually has two parts—the auto-router and the manual router. The auto-router uses algorithms to create traces while routing them through the components. If the designer wants, they can use the manual router to manually create the traces, rather than allowing the computer to create them automatically.

---

For a complex design, the designer may have to shuttle between placement and routing until they achieve a satisfactory compromise. On completion, they generate the necessary standard outputs, which they forward for fabricating Primarily, fabricators require Gerber data, drill information, a netlist, and specific fabrication instructions to start manufacturing a board.

---

# Measurement Instruments

##  Digital Multimeter
(DMM)

### Manual:

![DMM](https://i.pinimg.com/564x/d2/2c/b1/d22cb12b04a0c0db8385d69f5c514da5.jpg)

In manual DMM you have to set the range of readings manually.

Method:

#### DC voltage:

Insert **BLACK** probe in common/negative/ground terminal of DMM and **RED** terminal in positive terminal.

Set the knob on appropriate **DC voltage range**

Measure the voltage without touching your hands to probe.

#### AC voltage /DC current:

Follow the same steps mentioned above

#### Resistance:

Estimate the value of resistance using color code

Insert **BLACK** probe in common/negative/ground terminal of DMM and **RED** terminal in positive terminal.

Set the knob on appropriate **Resistance range**

Measure the voltage without touching your hands to probe.

#### 10A/20A mode:

Some DMMs can have a dedicated mode to measure higher DC current. In this mode, the fuse of DMM is bypassed. Thus the DMM can damage if high current is flowing through it for a longer duration (Read the specifications mentioned on DMM).

To measure in this mode, Insert **BLACK** probe in common/negative/ground terminal of DMM and **RED** terminal in dedicated terminal.

#### Continuity/ Diode test/ LED:

Insert **BLACK** probe in common/negative/ground terminal of DMM and **RED** terminal in positive terminal.

Set the knob on **Continuity mode**

**Continuity**: Listen for the buzzer sound.

**Diode test**: The display will show the forward bias resistance.

**LED**: LED will turn on and display will show forward bias resistance.

#### Transistor test:

Set the knob on transistor test/ hFE mode.

Insert the transistor in appropriate transistor tester terminals.

Display will show the current gain (beta value/hFE parameter) of the transistor.

#### Precautions:

- It is always advised to set the range one step higher than estimated reading. For example if the estimated reading is 18V then first measure the voltage using the 200V option and then if the reading is below 20V, you can switch to the 20V range.

- Extra care should be taken while measuring higher voltage or current.

- While taking readings, don’t touch your hands to DMM probes. It may cause hazards or errors in readings.

### Automatic:

![DMM](https://www.gotronik.pl/images/ut136c-plus-multimetr1.jpg)

In these multimeters, the user doesn’t need to set the range.

To measure, insert DMM probes in appropriate terminals, set the knob on the quantity which you want to measure. Measure the quantity, wait till the readings stabilize.

## Oscilloscope:

Horizontal controls used to adjust time axis


Vertical controls used to adjust amplitude of signal

![Oscilloscope](https://www.electronicshub.org/wp-content/uploads/2021/12/Oscilloscope-Front-Panel.png)
  
Oscilloscope is a powerful tool used to visualize signals, waveforms as a function of time. It can be used to analyze frequency, amplitude, rise time, time interval and distortions of signals.

#### Functions:

- Cursors

- Protocol decoding

- Mathematical functions like addition/subtraction

- Measure (automatically measure time period, frequency, amplitude)

- Auto (auto adjust time scale and voltage scale)


***

