#  Embedded Systems Study Group

## Table of Contents
- [Introduction](#introduction-to-embedded-system)
- [Basic of Electronics](#basic-of-electronics)
    - [Pull up and Pull Down Resistor](#pull-up-and-pull-down-resistor)
    - [Flipflops](#flipflops)
## Introduction to Embedded System: 

## Basic of Electronics: 

- ### Pull up and Pull Down Resistor:
    - Pull up and Pull Down Resistors are used in digital systems. 
    - In digital states there are three states:
    <p align="center">
        <img width="460" height="300" src="/assets/states.png">
    </p>

    -  As you can see two obvious states are Logic High and Logic Low. But suppose if we left any pin unconnected/floating then it is said to be tristate or unknown.
    - Example of this in popular 7432's(OR gate IC) datasheet:
    <p align="center">
        <img width="500" height="184" src="/assets/datasheet_7432.png">
    </p>
    
    - Range [2V - 5V]: considered as Logic HIGH.
    - Range [0 - 0.8V]: considered as Logic LOW.
    - Range [0.8 - 2V]: considered as indeterminate state.
    <p align="center">
        <img width="118" height="175" src="/assets/ttl_level.jpeg">
    </p>

    - If we try to measure simple digital input of a micro-controller wihtout any precaution for this unknown state then input signal will look like this:
    <p align="center">
        <img width="460" height="300" src="/assets/without_pullup.gif">
    </p>
    
    - But if we connect a pull up resistor at input of micro-controller then input signal will look like this:
    <p align="center">
        <img width="460" height="300" src="/assets/with_pullup.gif">
    </p>


- ### Flipflops: