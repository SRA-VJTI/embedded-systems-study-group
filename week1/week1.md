#  Embedded Systems Study Group

- [Basic of Electronics](#basic-of-electronics)
    - [Switch debouncer](#switch-debouncer)
    - [Pull up and Pull Down Resistor](#pull-up-and-pull-down-resistor)
    - [Latches and Flipflops](#flipflops-and-latches)

## Basic of Electronics

- ### Switch Debouncer:
    <p align="center">
        <img width="460" height="300" src="../assets/week1/switch_debouncer.png">
    </p>

    - Switch Debounce waveform:
    <p align="center">
        <img width="460" height="300" src="../assets/week1/debounce_waveform.jpeg">
    </p>

    - Hardware solution:
    <p align="center">
        <img width="540" height="260" src="../assets/week1/hardware_bounce.gif">
    </p>

- ### Pull-Up and Pull-Down Resistor:
    - Pull-up and Pull-Down Resistors used in digital systems. 
    - In a digital system, there are three states:
    <p align="center">
        <img width="460" height="300" src="../assets/week1/states.png">
    </p>

    -  Two obvious states are Logic High and Logic Low. But suppose if we left any pin unconnected/floating then it is said to be tristate or unknown.
    - Example of this in popular 7432 OR gate IC's datasheet:
    <p align="center">
        <img width="500" height="184" src="../assets/week1/datasheet_7432.png">
    </p>
    
    - Range [2V - 5V]: considered as Logic HIGH.
    - Range [0 - 0.8V]: considered as Logic LOW.
    - Range [0.8 - 2V]: considered as indeterminate state.
    <p align="center">
        <img width="177" height="260" src="../assets/week1/ttl_level.jpeg">
    </p>

    - If we try to measure simple digital input of a micro-controller without any precaution for this unknown state then input signal will look like this:
    <p align="center">
        <img width="540" height="260" src="../assets/week1/without_pullup.gif">
    </p>
    
    - But if we connect a pull-up resistor at the input of micro-controller then input signal will look something like this:
    <p align="center">
        <img width="540" height="260" src="../assets/week1/with_pullup.gif">
    </p>


- ### FlipFlops and Latches:
    - FlipFlops and Latches are the building blocks of most sequential circuits. FlipFlop is a device that samples it's input and changes only when a clock signal changes. On the other hand latches are sequential devices that watch it's inputs continuously and can change its outputs at any time.
    - ### Latches
        - SR Latch
            - There are 2 types of SR latches depending on the  gates used :-
            
                1) SR Latch ( with NOR gates )
                <p align="center">
                    <img width="540" height="260" src="../assets/week1/SRNorLatch.PNG">
                </p>

                2) SR Latch ( with NAND gates )
                <p align="center">
                    <img width="540" height="260" src="../assets/week1/SRNandLatch.png">
                </p>
     
     - ### FlipFlops
        - SR FlipFlop
            - One extra signal is used here and it is a control     signal like the clock signal.
            <p align="center">
                <img width="540" height="260" src="../assets/week1/SRFlipFlop.jpg">
            </p>
        - D FlipFlop
            - What if we go into the invalid state by mistake???    The data will be lost and we do not want that.  
            We can see that if we make S and R complements of each other then the invalid state is removed. That is a problem as we can't access the S=0 and R=0 state i.e the memory state. What can you do?  
            Actually this is not a problem as you have clock signal. If you make the clock signal 0 then you automaticaly go into memory state.

            <p align="center">
                <img width="540" height="260" src="../assets/week1/DFlipFlop.gif">
            </p>
    
### Assignment
   - [Assignment 1](../assets/week1/embedded_assignment_1.pdf)
