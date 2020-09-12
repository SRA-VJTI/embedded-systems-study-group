#  Embedded Systems Study Group

## Table of Contents
- [Introduction](#introduction-to-embedded-system)
- [Basic of Electronics](#basic-of-electronics)
    - [Switch debouncer](#switch-debouncer)
    - [Pull up and Pull Down Resistor](#pull-up-and-pull-down-resistor)
    - [Flipflops](#flipflops)
## Introduction to Embedded System: 

## Basic of Electronics: 


- ### Switch Debouncer:
    <p align="center">
        <img width="460" height="300" src="/assets/switch_debouncer.png">
    </p>

    - Switch Debounce waveform:
    <p align="center">
        <img width="460" height="300" src="/assets/debounce_waveform.jpeg">
    </p>

    - Hardware solution:
    <p align="center">
        <img width="540" height="260" src="/assets/hardware_bounce.gif">
    </p>

- ### Pull-Up and Pull-Down Resistor:
    - Pull-up and Pull-Down Resistors used in digital systems. 
    - In a digital system, there are three states:
    <p align="center">
        <img width="460" height="300" src="/assets/states.png">
    </p>

    -  Two obvious states are Logic High and Logic Low. But suppose if we left any pin unconnected/floating then it is said to be tristate or unknown.
    - Example of this in popular 7432 OR gate IC's datasheet:
    <p align="center">
        <img width="500" height="184" src="/assets/datasheet_7432.png">
    </p>
    
    - Range [2V - 5V]: considered as Logic HIGH.
    - Range [0 - 0.8V]: considered as Logic LOW.
    - Range [0.8 - 2V]: considered as indeterminate state.
    <p align="center">
        <img width="177" height="260" src="/assets/ttl_level.jpeg">
    </p>

    - If we try to measure simple digital input of a micro-controller without any precaution for this unknown state then input signal will look like this:
    <p align="center">
        <img width="540" height="260" src="/assets/without_pullup.gif">
    </p>
    
    - But if we connect a pull-up resistor at the input of micro-controller then input signal will look something like this:
    <p align="center">
        <img width="540" height="260" src="/assets/with_pullup.gif">
    </p>


- ### FlipFlops and Latches:
    - FlipFlops and Latches are the building blocks of most sequential circuits. FlipFlop is a device that samples it's input and changes only when a clock signal changes. On the other hand latches are sequential devices that watch it's inputs continuously and can change its outputs at any time.
    - ### Latches
    - ### SR Latch
        - There are 2 types of SR latches depending on the gates used :-
            1) SR Latch ( with NOR gates )
            <p align="center">
                <img width="540" height="260" src="/assets/SRNorLatch.PNG">
            </p>
            
            2) SR Latch ( with NAND gates )
            <p align="center">
                <img width="540" height="260" src="/assets/SRNandLatch.png">
            </p>
     - ### SR FlipFlop
        - One extra signal is used here and it is a control signal like clock signal.
        <p align="center">
            <img width="540" height="260" src="/assets/SRFlipFlop.jpg">
        </p>
    
