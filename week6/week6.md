# FreeRTOS-esp-idf
Learning FreeRTOS in ESP-IDF

# Table Of Contents
- [FreeRTOS-esp-idf](#freertos-esp-idf)
- [Table Of Contents](#table-of-contents)
  - [What Is An RTOS](#what-is-an-rtos)
  - [Why Use An RTOS](#why-use-an-rtos)
  - [What should be considered when choosing an RTOS](#what-should-be-considered-when-choosing-an-rtos)
  - [How does the RTOS affect the development of the design](#how-does-the-rtos-affect-the-development-of-the-design)
  - [Scheduling Algorithm](#scheduling-algorithm)
    - [Preemptive Scheduling](#preemptive-scheduling)
    - [Non Preemptive Scheduling](#non-preemptive-scheduling)
  - [Difference between RTOS and GPOS](#difference-between-rtos-and-gpos)
  - [Introduction to FreeRTOS](#introduction-to-freertos)
  - [General Overview](#general-overview)
    - [Tasks](#tasks)
    - [Scheduling](#scheduling)
    - [FreeRTOS Prioritized Preemptive Scheduling with Time Slicing](#freertos-prioritized-preemptive-scheduling-with-time-slicing)
    - [Context Switching](#context-switching)
  - [What Is Stack Memory](#what-is-stack-memory)
  - [Tech Stack Size](#tech-stack-size)
  - [Controlling Stacks](#controlling-stacks)
  - [Simple Task](#simple-task)
  - [Controlling Task](#controlling-task)
  - [Basics of Queue](#basics-of-queue)
  - [Basics of Semaphores](#basics-of-semaphores)
  - [Basics of Mutex](#basics-of-mutex)
  - [Some More](#some-more)
    - [Mutex Vs Semaphore](#mutex-vs-semaphore)
    - [Benchmarks](#benchmarks)
- [FreeRTOS for ESP32](#freertos-for-esp32)
  - [Creating And Deleting Tasks](#creating-and-deleting-tasks)
    - [xTaskCreate](#xtaskcreate)
    - [vTaskDelete](#vtaskdelete)
  - [Delays](#delays)
    - [pdMS_TO_TICKS](#pdms_to_ticks)
    - [vTaskDelay](#vtaskdelay)
    - [vTaskDelayUntil](#vtaskdelayuntil)
    - [Difference between vTaskDelay and vTaskDelayUntil](#difference-between-vtaskdelay-and-vtaskdelayuntil)
  - [Suspending And Resuming Task](#suspending-and-resuming-task)
    - [vTaskSuspend](#vtasksuspend)
    - [vTaskResume](#vtaskresume)
- [FreeRTOS InterTask Communication](#freertos-intertask-communication)
  - [Queue Communication](#queue-communication)
    - [xQueueCreate](#xqueuecreate)
    - [xQueueSend](#xqueuesend)
    - [xQueueReceive](#xqueuereceive)
  - [Binary Semaphore](#binary-semaphore)
    - [xSemaphoreCreateBinary](#xsemaphorecreatebinary)
    - [xSemaphoreGive](#xsemaphoregive)
    - [xSemaphoreTake](#xsemaphoretake)
    - [EXAMPLE](#example)
  - [Mutex](#mutex)
    - [xSemaphoreCreateMutex](#xsemaphorecreatemutex)
    - [Example](#example-1)
- [Resources](#resources)
- [Assignment](#assignment)
  - [1) Implement semaphore like functionality using queues.](#1-implement-semaphore-like-functionality-using-queues)
  - [2) See If the above task can be implemented using Task Notifications.](#2-see-if-the-above-task-can-be-implemented-using-task-notifications)
  - [3) Deadlock Example](#3-deadlock-example)
    - [Task](#task)



## What Is An RTOS

* A Real Time Operating System, commonly known as an RTOS, is a software component that rapidly switches between tasks, giving the impression that multiple programs are being executed at the same time on a single processing core.

![](https://scienceprog.com/wp-content/uploads/2011/07/freertos_multitasking.gif)

* In actual fact the processing core can only execute one program at any one time, and what the RTOS is actually doing is rapidly switching between individual programming threads (or Tasks) to give the impression that multiple programs are executing simultaneously.

* An RTOS is designed to provide a predictable execution pattern and is employed when processing must conform to the time constraints of a time-bound system (i.e., processing is completed at a certain frequency or the system as a whole will fail).

* Because an RTOS is designed to respond to events quickly and perform under heavy loads, it can be slower at big tasks when compared to another OS.

## Why Use An RTOS

There are well-established techniques for writing good embedded software without the use of an RTOS. In some cases, these techniques may provide the most appropriate solution; however as the solution becomes more complex, the benefits of an RTOS become more apparent. These include:

* **Priority Based Scheduling** : The ability to separate critical processing from non-critical is a powerful tool.

* **Abstracting Timing Information** : The RTOS is responsible for timing and provides API functions. This allows for cleaner (and smaller) application code.

* **Maintainability/Extensibility** : Abstracting timing dependencies and task based design results in fewer interdependencies between modules. This makes for easier maintenance.

* **Modularity** : The task based API naturally encourages modular development as a task will typically have a clearly defined role.

* **Promotes Team Development** : The task-based system allows separate designers/teams to work independently on their parts of the project.

* **Easier Testing** : Modular task based development allows for modular task based testing.

* **Code Reuse** : Another benefit of modularity is that similar applications on similar platforms will inevitably lead to the development of a library of standard tasks.

* **Improved Efficiency**: An RTOS can be entirely event driven; no processing time is wasted polling for events that have not occurred.

* **Idle Processing** : Background or idle processing is performed in the idle task. This ensures that things such as CPU load measurement, background CRC checking etc will not affect the main processing.

## What should be considered when choosing an RTOS

* **Responsiveness** : The RTOS scheduling algorithm, interrupt latency and context switch times will significantly define the responsiveness and determinism of the system. The most important consideration is what type of response is desired – Is a hard real time response required? This means that there are precisely defined deadlines that, if not met, will cause the system to fail. Alternatively, would a non-deterministic, soft real time response be appropriate? In which case there are no guarantees as to when each task will complete.

* **Available system resources** : Micro kernels use minimum system resources and provide limited but essential task scheduling functionality. Micro kernels generally deliver a hard real time response, and are used extensively with embedded microprocessors with limited RAM/ROM capacity, but can also be appropriate for larger embedded processor systems.

Alternatively, a full featured OS like Linux or WinCE could be used. These provide a feature rich operating system environment, normally supplied with drivers, GUI’s and middleware components. Full featured OS’s are generally less responsive, require more memory and more processing power than micro kernels, and are mainly used on powerful embedded processors where system resources are plentiful.

* **Open source or professionally licensed** : There are widely used, free open source RTOS’s available, distributed under GPL or modified GPL licenses. However, these licenses may contain copy left restrictions and offer little protection. Professionally licensed RTOS products remove the copy left restrictions, offer full IP infringement indemnification and warranties. In addition, you have a single company providing support and taking responsibility for the quality of your product.

* **Quality** : What emphasis does the RTOS supplier place on quality within their organisation? Quality is more than just a coding standard. Are the correct procedures in place to guarantee the quality of future products and support? Well-managed companies that take quality seriously tend to be ISO 9001 certified.

* **Safety Certification** : Pre-certified and certifiable RTOS’s are available for applications that require certification to international design standards such as DO-178C and IEC 61508. These RTOS’s provide key safety features, and the design evidence required by certification bodies to confirm that the process used to develop the RTOS meets the relevant design standard.

* **Licensing** : It’s not only the RTOS functionality and features that you’ll need to consider, but the licensing model that will work best for your project budget and the company’s “return on investment”.

* **RTOS Vendor** : The company behind the RTOS is just as important as selecting the correct RTOS itself. Ideally you want to build a relationship with a supplier that can support not only your current product, but also your products of the future. To do this you need to select a proactive supplier with a good reputation, working with leading silicon manufacturers to ensure they can support the newest processors and tools.

Trust, quality of product, and quality of support is everything.

## How does the RTOS affect the development of the design
The choice of RTOS can greatly affect the development of the design. By selecting an appropriate RTOS the developer gains:

* A Task based design that enhances modularity, simplifies testing and encourages code reuse
* An environment that makes it easier for engineering teams to develop together
* Abstraction of timing behaviour from functional behaviour, which should result in smaller code size and more efficient use of available resources.


Peripheral support, memory usage and real-time capability are key features that govern the suitability of the RTOS. Using the wrong RTOS, particularly one that does not provide sufficient real time capability, will severely compromise the design and viability of the final product.

The RTOS needs to be of high quality and easy to use. Developing embedded projects is difficult and time consuming – the developer does not want to be struggling with RTOS related problems as well. The RTOS must be a trusted component that the developer can rely on, supported by in-depth training and good, responsive support.

## Scheduling Algorithm

* All operating systems use schedulers to allocate CPU time to tasks or threads. Because only one process can run at a given time on the CPU in case of a single core processor
* A task can be in one of these four possible states such as running, ready, blocked, suspended. Only tasks that are in ready state can be picked by a scheduler depending on the scheduling policy.
* Scheduling is the process of deciding which task should be executed at any point in time based on a predefined algorithm. The logic for the scheduling is implemented in a functional unit called the scheduler. 
* There are many scheduling algorithms that can be used for scheduling task execution on a CPU. They can be classified into two main types: preemptive scheduling algorithms and non-preemptive scheduling algorithms.

### Preemptive Scheduling

* Preemptive scheduling allows the interruption of a currently running task, so another one with more “urgent” status can be run. The interrupted task is involuntarily moved by the scheduler from running state to ready state. This dynamic switching between tasks that this algorithm employs is, in fact, a form of multitasking. It requires assigning a priority level for each task. A running task can be interrupted if a task with a higher priority enters the queue.

* As an example let’s have three tasks called Task 1, Task 2 and Task 3. Task 1 has the lowest priority and Task 3 has the highest priority. Their arrival times and execute times are listed in the table below.

| Task Name | Arrival Time(μs) | Execute Time(μs) |
| :---: | :----: | :----: |
| Task 1 | 10 | 50 |
| Task 2 | 40 | 50 |
| Task 3 | 60 | 40 |

* In above table we can see that Task 1 is the first to start executing, as it is the first one to arrive (at t = 10 μs ). Task 2 arrives at t = 40μs and since it has a higher priority, the scheduler interrupts the execution of Task 1 and puts Task 2 into running state. Task 3 which has the highest priority arrives at t = 60 μs. At this moment Task 2 is interrupted and Task 3 is put into running state. As it is the highest priority task it runs until it completes at t = 100 μs. Then Task 2 resumes its operation as the current highest priority task. Task 1 is the last to complete is operation.

![](https://open4tech.com/wp-content/uploads/2019/11/preemptive_scheduling.jpg)

* The main advantage of a preemptive scheduler is that it provides an excellent mechanism where the importance of every task may be precisely defined. On the other hand, it has the disadvantage that a high priority task may starve the CPU such that lower priority tasks can never have the chance to run.This can usually happen if there are programming errors such that the high priority task runs continuously without having to wait for any system resources and never stops

* Most RTOS uses Preemptive scheduling.

### Non Preemptive Scheduling
* In non-preemptive scheduling, the scheduler has more restricted control over the tasks. It can only start a task and then it has to wait for the task to finish or for the task to voluntarily return the control. A running task can’t be stopped by the scheduler.
* If we take the three tasks specified in the table from the previous chapter and schedule them using a non-preemptive algorithm we get the behavior shown in below figure.Once started, each task completes its operation and then the next one starts.

![](https://open4tech.com/wp-content/uploads/2019/10/non_preemptive_scheduling.jpg)



## Difference between RTOS and GPOS

* The basic difference of using a GPOS or an RTOS lies in the nature of the system – i.e whether the system is “time critical” or not! A system can be of a single purpose or multiple purpose. Example of a “time critical system” is – Automated Teller Machines (ATM). Here an ATM card user is supposed to get his money from the teller machine within 4 or 5 seconds from the moment he press the confirmation button. The card user will not wait 5 minutes at the ATM after he pressed the confirm button. So an ATM is a time critical system. Where as a personal computer (PC) is not a time critical system. The purpose of a PC is multiple. A user can run many applications at the same time. After pressing the SAVE button of a finished document, there is no particular time limit that the doc should be saved within 5 seconds. It may take several minutes (in some cases) depending upon the number of tasks and processes running in parallel.

| Characteristics | Real Time Operating System (RTOS) | General Purpose Operating System (GPOS) |
| :---: | :----: | :----: |
| Time Critical |An RTOS differs in that it typically provides a hard real time response, providing a fast, highly deterministic reaction to external events | OS’s typically provide a non-deterministic, soft real time response, where there are no guarantees as to when each task will complete, but they will try to stay responsive to the user. |
| Task Scheduling | RTOS uses pre-emptive task scheduling method which is based on priority levels. Here a high priority process gets executed over the low priority ones. | In the case of a GPOS – task scheduling is not based on  “priority” always! GPOS is programmed to handle scheduling in such a way that it manages to achieve high throughput |
| Hardware and Economical Factors | An RTOS is usually designed for a low end, stand alone device like an ATM, Vending machines, Kiosks etc. RTOS is light weight and small in size compared to a GPOS | A GPOS is made for high end, general purpose systems like a personal computer, a work station, a server system etc |
| Latency Issues | An RTOS has no such issues because all the process and threads in it has got bounded latencies – which means – a process/thread will get executed within a specified time limit | GPOS is unbounded dispatch latency, which most GPOS falls into. The more number of threads to schedule, latencies will get added up |
| Preemptible Kernel | The kernel of an RTOS is preemptible | A GPOS kernel is not preemptible |
| Examples | FreeRTOS,Embox etc.. | MacOS, Ubuntu etc.. |

* If kernel is not preemptible, then a request/call from kernel will override all other process and threads. For example:- a request from a driver or some other system service comes in, it is treated as a kernel call which will be served immediately overriding all other process and threads. In an RTOS the kernel is kept very simple and only very important service requests are kept within the kernel call. All other service requests are treated as external processes and threads. All such service requests from kernel are associated with a bounded latency in an RTOS. This ensures highly predictable and quick response from an RTOS.

## Introduction to FreeRTOS
- Kernel (Wikipedia Def): It is the "portion of the operating system code that is always resident in memory", and facilitates interactions between hardware and software components. 
- FreeRTOS is Open Source (MIT + Commercial license) RTOS kernel for embedded devices. It has been ported
  over to >35 uC platforms. 
- Good Documentation , Small Memory Footprint , Modular Libraries and support
  for 40+ Architectures , etc. are some more features of FreeRTOS.
- Since 2017 Amazon has contributed in many updates to the original kernel apart
  from AWS freeRTOS

The structure of the FreeRTOS/Source directory is shown below.
```
FreeRTOS
    |
    +-Source        The core FreeRTOS kernel files
        |
        +-include   The core FreeRTOS kernel header files
        |
        +-Portable  Processor specific code.
            |
            +-Compiler x    All the ports supported for compiler x
            +-Compiler y    All the ports supported for compiler y
            +-MemMang       The sample heap implementations
```

Features Overview :
- Tasks
- Queues, Mutexes, Semaphores
- Direct To Task Notifications
- Stream and Message Buffers
- Software Timers
- Event Groups or Flags
- Static vs Dynamic Memory
- Heap Management
- Stack Overflow Protection
- Co-Routines (Deprecated)
- Compile Time configurable
- Additional Libraries
   
## General Overview

### Tasks

Each executing program is a task (or thread) under control of the operating
system and hence if it can execute multiple tasks then it is said to be
multi-tasking. However it is not concurrent.

* A task is a sequential piece of code - it does not know when it is going to get suspended (swapped out or switched out) or resumed (swapped in or switched in) by the kernel and does not even know when this has happened. 

* Consider the example of a task being suspended immediately before executing an instruction that sums the values contained within two processor registers. While the task is suspended other tasks will execute and may modify the processor register values. Upon resumption the task will not know that the processor registers have been altered - if it used the modified values the summation would result in an incorrect value.

### Scheduling

* The scheduler is the part of the kernel responsible for deciding which task
  should be executing at any particular time. The kernel can suspend and later
  resume a task many times during the task lifetime. 

![Scheduling](https://www.freertos.org/fr-content-src/uploads/2018/07/RTExample.gif)

* The RTOS created an Idle task which will execute only when there are no other tasks able to do so. The RTOS idle task is always in a state where it is able to execute.
* When sleeping, an RTOS task will specify a time after which it requires 'waking'. When blocking, an RTOS task can specify a maximum time it wishes to wait.
* Tick - The tick count variable is incremented with strict timing accuracy
  allowing it to measure time to a resolution of chosen timer interrupt
  frequency.
* The Kernel keeps checking if its time to unblock or wake a task.
  
### FreeRTOS Prioritized Preemptive Scheduling with Time Slicing
FreeRTOS kernel supports two types of scheduling policy: 

* **Time Slicing Scheduling Policy** : This is also known as a round-robin algorithm. In this algorithm, all equal priority tasks get CPU in equal portions of CPU time. 
* **Fixed Priority Preemptive Scheduling** : This scheduling algorithm selects tasks according to their priority. In other words, a high priority task always gets the CPU before a low priority task. A low priority task gets to execute only when there is no high priority task in the ready state. 

FreeRTOS uses the combination of above two Scheduling Policy , so the scheduling policy is **FreeRTOS Prioritized Preemptive Scheduling with Time Slicing**

* In this case, a scheduler can not change the priority of scheduled tasks. But tasks can change their own priority by using FreeRTOS priority changing API
* Because this scheduling algorithm also uses Preemptive scheduling, therefore, a high priority task can immediately preempt a low priority running task. Whenever low priority task gets preempted by a high priority task, a low priority task enters the ready state and allows a high priority task to enter the running state.
* The last part of this scheduling algorithm is a time-slicing feature. In this case, all equal priority tasks get shared CPU processing time. Time slicing is achieved by using FreeRTOS tick interrupts. 



![](https://microcontrollerslab.com/wp-content/uploads/2020/06/FreeRTOS-Preemptive-scheduling-algorithm-example.jpg)

This picture shows the timing diagram about the execution sequence of high, medium and low priority tasks.



**Note** 

* The idle task is created automatically when the RTOS scheduler is started to ensure there is always at least one task that is able to run. It is created at the lowest possible priority to ensure it does not use any CPU time if there are higher priority application tasks in the ready state.
* *Periodic Tasks* : In periodic task, jobs are released at regular intervals. A periodic task is one which repeats itself after a fixed time interval
* *Continuous Task* : A continuous processing task is a task that never enters the Blocked state
* *Event Task* : The task scheduled to be invoked at occurence of particular event. 




![](https://microcontrollerslab.com/wp-content/uploads/2020/06/FreeRTOS-Preemptive-time-slicing-scheduling-algorithm-example.jpg)

* This diagram shows the execution sequence of tasks according to time-slicing policy.
### Context Switching

* As a task executes it utilizes the processor / microcontroller registers and accesses RAM and ROM just as any other program. These resources together (the processor registers, stack, etc.) comprise the task execution context.

![Img](https://www.freertos.org/fr-content-src/uploads/2018/07/ExeContext.gif)

* The error that we stumbled upon during task execution (that the summation gave
  wrong result because the registers were manipulated by some other task) needed
  some "context".
* It is essential that upon resumption a task has a context identical to that
  immediately prior to its suspension. FreeRTOS does so by saving the context of
  a task as it is suspended and restored prior to its execution.
* The process of saving the context of a task being suspended and restoring the context of a task being resumed is called context switching.

![PremptiveContext](https://www.freertos.org/fr-content-src/uploads/2018/07/TickISR.gif)
- Referring to the numbers in the diagram above:
 - At (1) the RTOS idle task is executing.
 - At (2) the RTOS tick occurs, and control transfers to the tick ISR (3).
- The RTOS tick ISR makes vControlTask ready to run, and as vControlTask has a
  higher priority than the RTOS idle task, switches the context to that of
  vControlTask.
- As the execution context is now that of vControlTask, exiting the ISR (4)
  returns control to vControlTask, which starts executing (5).
- A context switch occurring in this way is said to be Preemptive, as the
  interrupted task is preempted without suspending itself voluntarily.

* Read more about [Saving Task Context](https://www.freertos.org/implementation/a00016.html)
   
A FreeRTOS application will start up and execute just like a non-RTOS
application until vTaskStartScheduler() is called. vTaskStartScheduler() is
normally called from the application's main() function. The RTOS only controls
the execution sequencing after vTaskStartScheduler() has been called.

## What Is Stack Memory

* The stack is an area of RAM where a program stores temporary data during the execution of code blocks. Typically statically allocated, the stack is operates on a “last in, first out” basis. The life span of variables on the stack is limited to the duration of the function.
* What uses Stack Memory?
  * Local Variables of the task.
  * Function calls (function parameters + function return address)
  * Local variables of functions your task calls.
  
```c
void hello_world_task(void* p)
{
    char mem[128];
    char *amool = char* malloc(32);
    while(1) {
      foo();
    }
}
//The task above uses 128 + 4 (used to hold amool pointer , rest is heap) + <bytes used by foo> bytes of stack.
```

## Tech Stack Size
* Depends on how much memory a program is using (including its variables,
  function call depth ) . 
* For example printf uses 1024 bytes. It is recommended to put 512 + estimated
  stack space as stack size.
  
## Controlling Stacks

* When FreeRTOSrequires  RAM,  instead  of  calling  malloc(), it  calls  pvPortMalloc().   When RAM is being freed, instead of calling free(),the kernel calls vPortFree().  pvPortMalloc() has the same prototype as the standard C library malloc()function, and vPortFree() has the same prototype as the standard C library free()function.pvPortMalloc()  and  vPortFree()  are  public  functions,  so can  also  be  called  from application code.
* Refer
  [this](http://socialledge.com/sjsu/images/d/dc/CmpE146RefFiles_StackAndHeap.pdf)
  for more info on how freeRTOS will behave with stacks.
  
## Simple Task
* Below is a simple task example that prints a message once a second. Note that vTaskStartScheduler() never returns and FreeRTOS will begin servicing the tasks at this point. Also note that every task must have an infinite loop and NEVER EXIT.

```c
void hello_world_task(void* p)
{
    while(1) {
        puts("Hello World!");
        vTaskDelay(1000);
    }
}

int main()
{
    xTaskCreate(hello_world_task, (signed char*)"task_name", STACK_BYTES(2048), 0, 1, 0);
    vTaskStartScheduler();

    return -1;
}
```
* Note - This is non esp-idf freeRTOS like code

## Controlling Task
In FreeRTOS, you have precise control of when tasks will use the CPU. The rules are simple:

* Task with highest priority will run first, and never give up the CPU until it sleeps
* If 2 or more tasks with the same priority do not give up the CPU (they don't sleep), then FreeRTOS will share the CPU between them (time slice).

Here are some of the ways you can give up the CPU:

* vTaskDelay() - This simply puts the task to "sleep"; you decide how much you want to sleep.
* xQueueSend() - If the Queue you are sending to is full, this task will sleep (block).
* xQueueReceive() - If the Queue you are reading from is empty, this task will sleep (block).
* xSemaphoreTake() - Task will sleep if the semaphore is taken by somebody else.

## Basics of Queue
* A queue is a simple FIFO system with atomic reads and writes. “Atomic operations” are those that cannot be interrupted by other tasks during their execution. This ensures that another task cannot overwrite our data before it is read by the intended target.
* Note that in FreeRTOS, information is copied into a queue by value and not by reference

![](https://www.digikey.de/maker-media/88d32074-22e7-4fc3-943f-9e59b06dede9)

* A practical example of a queue may be to start another task to start doing its work while the primary task continues doing its own work independently. In the following example, we demonstrate how a terminal task can kick-off another task to begin playing an mp3 song while it operates independently to handle the next command from a terminal (user input).

```c
void terminal_task(void* p)
{
     // Assume you got a user-command to play an mp3:
     xQueueSend(song_name_queue, "song_name.mp3", 0);
  
     ...
}

void mp3_play_task(void* p)
{
    char song_name[32];
    while(1) {
        if(xQueueReceive(song_name_queue, &song_name[0], portMAX_DELAY)) {
            // Start to play the song.
        }
    }
}
```

## Basics of Semaphores
* Semaphore is simply a variable that is non-negative and shared between threads. This variable is used to solve the critical section problem and to achieve process synchronization in the multiprocessing environment. 
* Two types of Semaphores
  - Binary - Its value is either 0 or 1 and also called as `mutex lock` .
    - It Doesn't provide priority inversion mechanism (Priority inversion occurs
      when a high-priority task is forced to wait for the release of a shared
      resource owned by a lower-priority task ).
    - Better suited for helper tasks for interrupts  
    -  For example, if you have an interrupt and you don't want to do a lot of processing inside the interrupt, you can use a helper task. To accomplish this, you can perform a semaphore give operation inside the interrupt, and a dedicated task will sleep or block on xSemaphoreTake() operation.
  
    ```c
    // Somewhere in main() :
    SemaphoreHandle_t event_signal;
    vSemaphoreCreateBinary( event_signal ); // Create the semaphore
    xSemaphoreTake(event_signal, 0);        // Take semaphore after creating it.


    void System_Interrupt()
    {
      /* We have finished accessing the shared resource.Release the semaphore. */
        xSemaphoreGiveFromISR(event_signal);
    }

    void system_interrupt_task()
    {
        while(1) {
            if(xSemaphoreTake(event_signal, 9999999)) {
              /* We were able to obtain the semaphore and can now access the shared resource. */
              
                // Process the interrupt
            }
        }
    }
    ```
    - The above code shows example of a deferred interrupt processing. The idea is that you don't want to process the interrupt inside System_Interrupt() because you'd be in a critical section with system interrupts globally disabled, therefore, you can potentially lock up the system or destroy real-time processing if the interrupt processing takes too long.

    - Another way to use binary semaphore is to wake up one task from another
      task by giving the semaphore. So the semaphore will essentially act like a
      signal that may indicate: "Something happened, now go do the work in
      another task". 
    - Example - Binary semaphore is used to indicate when an I2C
      read operation is done, and the interrupt gives this semaphore. 
    - Note that
      when you create a binary semaphore in FreeRTOS, it is ready to be taken,
      so you may want to take the semaphore after you create it such that the
      task waiting on this semaphore will block until given by somebody. 

  - Counting Semaphore - Multiple Values
    - More than one user is allowed access to a resource

## Basics of Mutex
  - One of the best example of a mutex is to guard a resource or a door with a key. For instance, let's say you have an SPI BUS, and only one task should use it at a time. Mutex provides mutual exclusion with priority inversion mechanism. Mutex will only allow ONE task to get past xSemaphoreTake() operation and other tasks will be put to sleep if they reach this function at the same time. 
  
  ```c
  // In main(), initialize your Mutex:
  SemaphoreHandle_t spi_bus_lock = xSemaphoreCreateMutex();

  void task_one()
  {
      while(1) {
          if(xSemaphoreTake(spi_bus_lock, 1000)) {
              // Use Guarded Resource
  
              // Give Semaphore back:
              xSemaphoreGive(spi_bus_lock);
          }
      }
  }
  void task_two()
  {
      while(1) {
          if(xSemaphoreTake(spi_bus_lock, 1000)) {
              // Use Guarded Resource
  
              // Give Semaphore back:
              xSemaphoreGive(spi_bus_lock);
          }
      }
  }
  ```
  - In the code above, only ONE task will enter its xSemaphoreTake() branch. If both tasks execute the statement at the same time, one will get the mutex, the other task will sleep until the mutex is returned by the task that was able to obtain it in the first place. 

## Some More

### Mutex Vs Semaphore

* A mutex can be released only by the thread that had acquired it.
* A binary semaphore can be signaled by any thread (or process).
  So semaphores are more suitable for some synchronization problems.

Quoting from [here](http://niclasw.mbnet.fi/MutexSemaphore.html)

```
The Toilet Example  (c) Copyright 2005, Niclas Winquist ;)

Mutex:

Is a key to a toilet. One person can have the key - occupy the toilet - at the time. When finished, the person gives (frees) the key to the next person in the queue.

Officially: "Mutexes are typically used to serialise access to a section of  re-entrant code that cannot be executed concurrently by more than one thread. A mutex object only allows one thread into a controlled section, forcing other threads which attempt to gain access to that section to wait until the first thread has exited from that section."
Ref: Symbian Developer Library

(A mutex is really a semaphore with value 1.) (*


Semaphore:

Is the number of free identical toilet keys. Example, say we have four toilets with identical locks and keys. The semaphore count - the count of keys - is set to 4 at beginning (all four toilets are free), then the count value is decremented as people are coming in. If all toilets are full, ie. there are no free keys left, the semaphore count is 0. Now, when eq. one person leaves the toilet, semaphore is increased to 1 (one free key), and given to the next person in the queue.

Officially: "A semaphore restricts the number of simultaneous users of a shared resource up to a maximum number. Threads can request access to the resource (decrementing the semaphore), and can signal that they have finished using the resource (incrementing the semaphore)."
Ref: Symbian Developer Library


(* - Please note, that some web posts indicate, that this statement is not quite accurate 
```

### Benchmarks
According to [this](http://blob.tomerweller.com/esp32-first-steps) ring
benchmark the esp32 arduino core is 35% slower than esp-idf's FreeRTOS.


# FreeRTOS for ESP32

* Many manufacturers produce SoC with freeRTOS support as it is most *peer-reviewed* rtos. Expressif included [freeRTOS](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/system/freertos.html) in its latest version ESP – IDF.
* The Espressif Internet Development Framework (ESP-IDF) uses FreeRTOS to make better use of the two high speed processors and manage the numerous built-in peripherals.
* It is done by creating tasks,let's look at creating task,

## Creating And Deleting Tasks
* We have discussed about the FreeRTOS task, In ESP-IDF too we have FreeRTOS api to create as well as delete tasks.
* We must also know that there is always an idle task running when there are no task scheduled to prevent RTOS from crashing.

### xTaskCreate

* ```c 
    static BaseType_t xTaskCreate(TaskFunction_t  pvTaskCode, 
                                  const char      *constpcName, 
                                  const uint32_t  usStackDepth, 
                                  void            *constpvParameters, 
                                  UBaseType_t     uxPriority, 
                                  TaskHandle_t    *constpvCreatedTask)
  ```
* Description :- xTaskCreate() can only be used to create a task that has unrestricted access to the entire microcontroller memory map.

Parameters | Description
--- | --- 
**pvTaskCode** | Pointer to the task entry function (just the name of the function that implements the task)
**pcName** | A descriptive name for the task. This is mainly used to facilitate debugging, but can also be used to obtain a task handle.
**usStackDepth**|The number of words (not bytes!) to allocate for use as the task’s stack. For example, if the stack is 16-bits wide and usStackDepth is 100, then 200 bytes will be allocated for use as the task’s stack.
**pvParameters**|A value that will passed into the created task as the task’s parameter.It can be set to be NULL if there is no parameters.
**uxPriority**|The priority at which the created task will execute.This should be between 1-10 for general use.
**pxCreatedTask**|Used to pass a handle to the created task out of the xTaskCreate() function. pxCreatedTask is optional and can be set to NULL.

* **Example** :- This example will create two tasks with equal priorities. In this example It is also demonstrated how to pass paramters to the task.

```c

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"

void task1(void * params)
{
  while (true)
  {
    printf("reading temperature from %s\n", (char *) params);
    vTaskDelay(1000 / portTICK_PERIOD_MS);
  }
}

void task2(void * params) 
{
  while (true)
  {
    printf("reading humidity from %s\n", (char *) params);
    vTaskDelay(2000 / portTICK_PERIOD_MS);
  }
}

void app_main(void)
{
   const char* task1_paramter = "task 1";
   const char* task2_paramter = "task 2"; 
   xTaskCreate(&task1, "temperature reading", 2048, (void*) task1_paramter, 2, NULL);
   xTaskCreate(&task2, "humidity reading", 2048, (void*)task2_paramter, 2, NULL);
}

```
### vTaskDelete

* ```c
  void vTaskDelete( TaskHandle_t xTask )
  ```

* **Description** :- Remove a task from the RTOS kernels management. The task being deleted will be removed from all ready, blocked, suspended and event lists.

NOTE: The idle task is responsible for freeing the RTOS kernel allocated memory from tasks that have been deleted. It is therefore important that the idle task is not starved of microcontroller processing time if your application makes any calls to vTaskDelete (). Memory allocated by the task code is not automatically freed, and should be freed before the task is deleted.

Parameters | Description
--- | --- 
**xTask** | The handle of the task to be deleted. Passing NULL will cause the calling task to be deleted.

* Example :- This example is similar to the previous example , but in this we will delete one task using Task Handler , other task by simply passing NULL in vTaskDelete api.
 ```c
 #include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"

TaskHandle_t task2_handler=NULL;

void task1(void *params)
{

    for (int i = 0; i < 5; i++)
    {
        printf("reading temperature from %s\n", (char *)params);
        vTaskDelay(1000 / portTICK_PERIOD_MS);
    }
    vTaskDelete(NULL);
}

void task2(void *params)
{
    for (int i = 0; i < 5; i++)
    {
        printf("reading humidity from %s\n", (char *)params);
        vTaskDelay(2000 / portTICK_PERIOD_MS);
    }
    if(task2_handler!=NULL){
        vTaskDelete(task2_handler);
    }
    
}

void app_main(void)
{
    const char *task1_paramter = "task 1";
    const char *task2_paramter = "task 2";
    xTaskCreate(&task1, "temperature reading", 2048, (void *)task1_paramter, 2, NULL);
    xTaskCreate(&task2, "humidity reading", 2048, (void *)task2_paramter, 2, &task2_handler);
}
 
 ```
## Delays
* Delay is used to suspend execution of a task for a particular time. 
* Whenever one task enters delay state, the other tasks runs and vice-versa.

Why not use Hardware Timers for Delays?
* Although using Hardware Timers for delays is much more efficient than software based delays, ultimately the availability of such Timers depends on your Hardware.
* Moreover in a complex or large implementation you might not have the luxury of using Hardware Timers(either due to cost, space or internal hardware constraints) and hence would have to resort to using Software based delays.

### pdMS_TO_TICKS
```c
TickType_t pdMS_TO_TICKS(uint32_t millis)

```
* **Description** :- The pdMS_TO_TICKS() API converts your millisecond requirement to FreeRTOS Ticks.

Parameters | Description
--- | --- 
**millis** | The time in milliseconds that is to be converted into freeRTOS ticks

* The FreeRTOS Kernel uses Ticks to schedule and keep track of the various tasks in a microcontroller up to 1KHZ.
* Example :- If your FreeRTOS is configured to use a frequency of 100HZ, your tick rate would be 10ms. If the task needs to be delayed by 500ms, the TickType_t would be 50 (500/10).
* To avoid unnecessary math and computation on the user’s end the MACRO pdMS_TO_TICKS is very useful.

### vTaskDelay
```c
void vTaskDelay(TickType_t xTicksToDelay)

```
* **Description** :- This function sends that particular task into the blocked state for a set amount of Ticks.

Parameters | Description
--- | --- 
**xTicksToDelay** | No. of ticks to be delayed 

### vTaskDelayUntil

```c

void vTaskDelayUntil(TickType_t *pxPreviousWakeTime, TickType_t xTimeIncrement)

```

* **Description** :- Places the task that calls this function into the Blocked state until that absolute time is reached.This function is very useful for Periodic Tasks where a constant execution frequency is of the utmost importance.

Parameters | Description
--- | --- 
**pxPreviousWakeTime** | Pointer to a variable that holds the time at which the task was last unblocked. The variable must be initialised with the current time prior to its first use.Following this the variable is automatically updated within vTaskDelayUntil().
**xTimeIncrement** | The cycle time period. The task will be unblocked at time (pxPreviousWakeTime + xTimeIncrement). Calling vTaskDelayUntil with the same xTimeIncrement parameter value will cause the task to execute with a fixed interval period.

### Difference between vTaskDelay and vTaskDelayUntil

vTaskDelay | vTaskDelayUntil
--- | ---
In vTaskDelay you say how long after calling vTaskDelay you want to be woken | In vTaskDelayUntil you say the time at which you want to be woken
The parameter in vTaskDelay is the delay period in number of ticks from now | The parameter in vTaskDelayUntil is the absolute time in ticks at which you want to be woken calculated as an increment from the time you were last woken.
vTaskDelay is relative to the function itself | vTaskDelayUntil is absolute in terms of the ticks set by scheduler and FreeRTOS Kernel.

Example 1:- 
```c

#include <stdio.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"

TaskHandle_t taskHandle1=NULL;
TaskHandle_t taskHandle2=NULL;

void myTask1(void *p)
{
    while(1)
    {
        printf("hello world\n");
        vTaskDelay(pdMS_TO_TICKS(1000));
    }
}

void myTask2(void *p)
{
    TickType_t mylastunblock=xTaskGetTickCount();
    while(1)
    {
        vTaskDelay(pdMS_TO_TICKS(1000));
        printf("SRA is Great\n");
        vTaskDelayUntil(&mylastunblock,pdMS_TO_TICKS(5000));
    }
}

void app_main()
{
    xTaskCreate(myTask1,"Task 1",2048,NULL,5,taskHandle1);
    xTaskCreate(myTask2,"Task 2",2048,NULL,5,taskHandle2);
}

```

* If we run the Example 1, In myTask2 , there will be a delay of 1 sec, then "SRA is Great" will be printed, and since there was already a delay of 1 sec since the start of the task , the vTaskDelayUntil will just delay for additional 4 sec instead of 5 secs. Therefore vTaskDelayUntil ensured that since the start of the task there is no more as well as no less delay than 5 secs.

Example 2 :- 
```c
#include <stdio.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"

TaskHandle_t taskHandle1=NULL;
TaskHandle_t taskHandle2=NULL;

void myTask1(void *p)
{
    while(1)
    {
        printf("hello world\n");
        vTaskDelay(pdMS_TO_TICKS(1000));
    }
}

void myTask2(void *p)
{
    TickType_t mylastunblock=xTaskGetTickCount();
    while(1)
    {
        vTaskDelay(pdMS_TO_TICKS(1000));
        printf("SRA is Great\n");
        
        // instead of using vTaskDelayUntil we used vTaskDelay
        vTaskDelay(pdMS_TO_TICKS(5000));
    }
}

void app_main()
{
    xTaskCreate(myTask1,"Task 1",2048,NULL,5,taskHandle1);
    xTaskCreate(myTask2,"Task 2",2048,NULL,5,taskHandle2);
}
```

* If we run the Example 2, In myTask2 , there will be a delay of 1 sec, then "SRA is Great" will be printed, and then there will be an additional delay of 5 sec, taking the total delay in the task to 6 secs.

## Suspending And Resuming Task

* We have seen how to create the task as well as delete it. We have also seen how to delay a Task.
* But we may need to stop the execution of any task until specified to resume the execution , that's where suspension of Task comes in.
* ESP-IDF also provides with the FreeRTOS api to suspend as well as resume the suspended task.

### vTaskSuspend
```c
void vTaskSuspend( TaskHandle_t xTaskToSuspend )
```
* **Description** :- Suspend any task. When suspended a task will never get any microcontroller processing time, no matter what its priority.
* Calls to vTaskSuspend are not accumulative - i.e. calling vTaskSuspend () twice on the same task still only requires one call to vTaskResume () to ready the suspended task.

Parameters | Description
--- | --- 
**xTaskToSuspend** | Handle to the task being suspended. Passing a NULL handle will cause the calling task to be suspended.

### vTaskResume
```c
void vTaskResume( TaskHandle_t xTaskToResume )

```
* **Description** :- Resumes a suspended task.

Parameters | Description
--- | --- 
**xTicksToResume** | Handle to the task being readied

Example :- In this example task2 is suspending task1 after a 5 sec delay ,after the delay of another 5 sec task1 is resumed.

```c
#include <stdio.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"

TaskHandle_t taskhandle1=NULL;
TaskHandle_t taskhandle2=NULL;

void myTask1(void *p){
    int count =0;
    while(1){
        printf("Hello World: %d\n",count++);
        vTaskDelay(1000/portTICK_PERIOD_MS);
    }

}

void myTask2(void *p){
    while(1){
        vTaskDelay(5000/portTICK_PERIOD_MS);
        vTaskSuspend(taskhandle1);
        vTaskDelay(5000/portTICK_PERIOD_MS);
        vTaskResume(taskhandle1);
    }
}

void app_main(){
    xTaskCreate(myTask1,"Task 1",2048,NULL,5,&taskhandle1);
    xTaskCreate(myTask2,"Task 2",2048,NULL,5,&taskhandle2);
}

```
# FreeRTOS InterTask Communication
* Till Now we have seen examples where task are independent of each other. But what if we need to implement tasks which are dependent on each other and they need to communicate with each other.
* The term inter-task communication comprises all mechanisms serving to exchange information among tasks
* FreeRTOS also provides api for task to communicate amongst each other.
 
## Queue Communication

### xQueueCreate
```c
QueueHandle_t xQueueCreate( UBaseType_t uxQueueLength,UBaseType_t uxItemSize );
```

* **Description** Creates a new queue and returns a handle by which the queue can be referenced.
* Each queue requires RAM that is used to hold the queue state, and to hold the items that are contained in the queue (the queue storage area). 
* If a queue is created using xQueueCreate() then the required RAM is automatically allocated from the FreeRTOS heap. If a queue is created using xQueueCreateStatic() then the RAM is provided by the application writer, which results in a greater number of parameters, but allows the RAM to be statically allocated at compile time

Parameters|Description
--- | ---
**uxQueueLength**|The maximum number of items the queue can hold at any one time.
**uxItemSize**|The size, in bytes, required to hold each item in the queue.Items are queued by copy, not by reference, so this is the number of bytes that will be copied for each queued item. Each item in the queue must be the same size.

* **RETURN:-** If the queue is created successfully then a handle to the created queue is returned. If the memory required to create the queue could not be allocated then NULL is returned.

### xQueueSend
```c
BaseType_t xQueueSend(QueueHandle_t xQueue,const void * pvItemToQueue,TickType_t xTicksToWait)
```
* **Description** :- Post an item on a queue. The item is queued by copy, not by reference. 
* This function must not be called from an interrupt service routine. See xQueueSendFromISR() for an alternative which may be used in an ISR.

Parameters|Description
--- | ---
**xQueue**|The handle to the queue on which the item is to be posted.
**pvItemToQueue**|A pointer to the item that is to be placed on the queue. The size of the items the queue will hold was defined when the queue was created, so this many bytes will be copied from pvItemToQueue into the queue storage area.
**xTicksToWait**|The maximum amount of time the task should block waiting for space to become available on the queue, should it already be full. The call will return immediately if the queue is full and xTicksToWait is set to 0. The time is defined in tick periods so the constant portTICK_PERIOD_MS should be used to convert to real time if this is required.

* **RETURN:-** pdTRUE if the item was successfully posted, otherwise errQUEUE_FULL

### xQueueReceive
```c
BaseType_t xQueueReceive(QueueHandle_t xQueue,void *pvBuffer,TickType_t xTicksToWait)
```

* **Description** :- Receive an item from a queue. The item is received by copy so a buffer of adequate size must be provided.
* The number of bytes copied into the buffer was defined when the queue was created.
* This function must not be used in an interrupt service routine. See xQueueReceiveFromISR for an alternative that can.

Parameters|Description
--- | ---
**xQueue**|The handle to the queue on which the item is to be received.
**pvBuffer**|Pointer to the buffer into which the received item will be copied.
**xTicksToWait**|The maximum amount of time the task should block waiting for an item to receive should the queue be empty at the time of the call. Setting xTicksToWait to 0 will cause the function to return immediately if the queue is empty. The time is defined in tick periods so the constant portTICK_PERIOD_MS should be used to convert to real time if this is required.

* **RETURN:-** pdTRUE if an item was successfully received from the queue, otherwise pdFALSE.

* Example

```c
#include <stdio.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "freertos/semphr.h"

QueueHandle_t queue;

void sendingTheCount(void *p){
    int count=0 ;
    
    while(1){
        count++;
        printf("Received message \n");
        long ok=xQueueSend(queue,&count,1000/portTICK_PERIOD_MS);
        if(ok){
            printf("added message to queue\n");
        }
        else{
            printf("failed to add message to the queue\n");
        }
        vTaskDelay(5000/portTICK_PERIOD_MS);
    }
}

void Task1(void *params){
    while(true){
        int rxInt;
        if(xQueueReceive(queue,&rxInt,5000/portTICK_PERIOD_MS)){
            printf("doing something with count %d\n\n",rxInt);
        }
        
    }
}

void app_main(void){
    queue=xQueueCreate(3,sizeof(int));
    xTaskCreate(&sendingTheCount,"get HTTP",2048,NULL,2,NULL);
    xTaskCreate(&Task1,"Task 1",2048,NULL,1,NULL);
}
```

* In main(), we create the Queue before creating tasks, otherwise sending to un-initialized Queue will crash the system.
* In sendingTheCount(), we add the count to the queue
* In Task1(), we are reading from the queue and printing the count.
* If the priority of the receiving queue(Task1) is higher, FreeRTOS will switch tasks the moment xQueueSend() happens, and the next line inside sendingTheCount will not execute since CPU will be switched over to Task1.

## Binary Semaphore

### xSemaphoreCreateBinary

```c
SemaphoreHandle_t xSemaphoreCreateBinary( void );
```

* **Description** :- Creates a binary semaphore, and returns a handle by which the semaphore can be referenced.
* The semaphore is created in the 'empty' state, meaning the semaphore must first be given using the xSemaphoreGive() API function before it can subsequently be taken (obtained) using the xSemaphoreTake() function.

* **RETURN:-** ```NULL``` If semaphore could not be created because there was insufficient FreeRTOS heap, else returns the handle by which the semaphore can be referenced.

### xSemaphoreGive
```c
xSemaphoreGive( SemaphoreHandle_t xSemaphore );
```
* **Description** :- Macro to release a semaphore. The semaphore must have previously been created with a call to xSemaphoreCreateBinary(), xSemaphoreCreateMutex() or xSemaphoreCreateCounting().

Parameters|Description
--- | ---
**xSemaphore** | A handle to the semaphore being released. This is the handle returned when the semaphore was created.

* **RETURN** :- pdTRUE if the semaphore was released. pdFALSE if an error occurred.

### xSemaphoreTake
```c
xSemaphoreTake( SemaphoreHandle_t xSemaphore,
                 TickType_t xTicksToWait );
```
* **Description** :- Macro to obtain a semaphore. The semaphore must have previously been created with a call to xSemaphoreCreateBinary(), xSemaphoreCreateMutex() or xSemaphoreCreateCounting().

Parameters|Description
--- | ---
**xSemaphore** | A handle to the semaphore being taken - obtained when the semaphore was created.
**xTicksToWait** | The time in ticks to wait for the semaphore to become available. The macro portTICK_PERIOD_MS can be used to convert this to a real time. A block time of zero can be used to poll the semaphore.

* **RETURN** :- pdTRUE if the semaphore was obtained. pdFALSE if xTicksToWait expired without the semaphore becoming available.

### EXAMPLE 
```c
#include <stdio.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "freertos/semphr.h"
#include "esp_log.h"

SemaphoreHandle_t bi_sema = NULL;
static int shared_int = 0;

void led_blink_1()
{
    const char task[] = "led blink 1";
    while (1)
    {

        if (bi_sema != NULL)
        {
            // See if we can obtain the semaphore.  If the semaphore is not
            // available wait 10 ticks to see if it becomes free.
            if (xSemaphoreTake(bi_sema, 10/portTICK_PERIOD_MS) == pdTRUE)
            {
                // We were able to obtain the semaphore and can now access the
                // shared resource.
                shared_int += 1;
                // ...
                ESP_LOGI(task,"Semaphore Taken Succesfully | Shared Res - %d", shared_int);
                // We have finished accessing the shared resource.  Release the
                // semaphore.
                xSemaphoreGive(bi_sema);
            }
            else
            {
                // We could not obtain the semaphore and can therefore not
                // access the shared resource safely.
                ESP_LOGW(task, "Failed in taking Semaphore");
            }
        }
        vTaskDelay(1000 / portTICK_PERIOD_MS);
    }
}

void led_blink_2()
{
    const char task[] = "led blink 2";
    while (1)
    {
        if (bi_sema != NULL)
        {
            // See if we can obtain the semaphore.  If the semaphore is not
            // available wait 10 ticks to see if it becomes free.
            if (xSemaphoreTake(bi_sema, 10/portTICK_PERIOD_MS) == pdTRUE)
            {
                // We were able to obtain the semaphore and can now access the
                // shared resource.
                shared_int -= 1;
                // ...
                ESP_LOGI(task,"Semaphore Taken Succesfully | Shared Res - %d", shared_int);
                // We have finished accessing the shared resource.  Release the
                // semaphore.
                xSemaphoreGive(bi_sema);
            }
            else
            {
                // We could not obtain the semaphore and can therefore not
                // access the shared resource safely.
                ESP_LOGW(task, "Failed in taking Semaphore");
            }
        }
        vTaskDelay(1000 / portTICK_PERIOD_MS);
    }
}

void app_main()
{
    bi_sema = xSemaphoreCreateBinary();
    // xSemaphoreCreateBinary() Creates a new binary semaphore instance, and
    // returns a handle by which the new semaphore can be referenced.
    
    xSemaphoreGive(bi_sema);
    // The semaphore must be given before it can be taken if calls are made
    // using xSemaphoreCreateBinary()

    xTaskCreate(&led_blink_1, "Led Blink 1", 4096, NULL, 0, NULL);
    xTaskCreate(&led_blink_2, "Led Blink 2", 4096, NULL, 1, NULL);
}
```

## Mutex
### xSemaphoreCreateMutex
```c
SemaphoreHandle_t xSemaphoreCreateMutex( void )
```
* **Description** :- Creates a mutex, and returns a handle by which the created mutex can be referenced

* **RETURN :-** If the mutex type semaphore was created successfully then a handle to the created mutex is returned. If the mutex was not created because the memory required to hold the mutex could not be allocated then NULL is returned.

### Example 

```c
#include <stdio.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "freertos/semphr.h"
#include "esp_log.h"

SemaphoreHandle_t mutex_1  = NULL;

static int shared_int = 0;

void led_blink_1(void *paramter)
{
    const char task[] = "led blink 1";
    while (1)
    {

        if (mutex_1 != NULL)
        {
            // See if we can obtain the semaphore.  If the semaphore is not
            // available wait 10 ticks to see if it becomes free.
            if (xSemaphoreTake(mutex_1, 1000/portTICK_PERIOD_MS) == pdTRUE)
            {
                // We were able to obtain the semaphore and can now access the
                // shared resource.
                shared_int += 1;
                // ...
                ESP_LOGI(task,"Semaphore Taken Succesfully | Shared Res - %d", shared_int);

                vTaskDelay(1500/portTICK_PERIOD_MS);
                // We have finished accessing the shared resource.  Release the
                // semaphore.
                xSemaphoreGive(mutex_1);
            }
            else
            {
                // We could not obtain the semaphore and can therefore not
                // access the shared resource safely.
                ESP_LOGW(task, "Failed in taking Semaphore");
            }
        }
        vTaskDelay(1000 / portTICK_PERIOD_MS);
    }
}


void led_blink_2(void *paramter)
{
    const char task[] = "led blink 2";
    while (1)
    {
        if (mutex_1 != NULL)
        {
            // See if we can obtain the semaphore.  If the semaphore is not
            // available wait 10 ticks to see if it becomes free.
            if (xSemaphoreTake(mutex_1, 1000/portTICK_PERIOD_MS) == pdTRUE)
            {
                // We were able to obtain the semaphore and can now access the
                // shared resource.
                shared_int -= 1;
                // ...
                ESP_LOGI(task,"Semaphore Taken Succesfully | Shared Res - %d", shared_int);
                // We have finished accessing the shared resource.  Release the
                // semaphore.
                xSemaphoreGive(mutex_1);
            }
            else
            {
                // We could not obtain the semaphore and can therefore not
                // access the shared resource safely.
                ESP_LOGW(task, "Failed in taking Semaphore");
            }
        }
        vTaskDelay(1000 / portTICK_PERIOD_MS);
    }
}

void app_main()
{
    mutex_1 = xSemaphoreCreateMutex();

    // Semaphore cannot be used before a call to xSemaphoreCreateMutex().
    // This is a macro so pass the variable in directly.

    xTaskCreate(&led_blink_1, "Led Blink 1", 4096, NULL, 0, NULL);
    xTaskCreate(&led_blink_2, "Led Blink 2", 4096, NULL, 1, NULL);
}
```


# Resources
* [FreeRTOS Documentation](https://www.freertos.org/Documentation/RTOS_book.html)
* [ESP IDF FreeRTOS Documentation](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/system/freertos.html)
* [FreeRTOS Features specific to ESP IDF](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/system/freertos_additions.html)

# Assignment 
## 1) Implement semaphore like functionality using queues.    
    * If task A is blocked then task B can only process if task A is freed. vice versa.
(Explore [FreeRTOS Queue API](https://www.freertos.org/a00018.html))

    * When boot button is pressed all tasks should get suspended if they were running and resumed if they were suspended. if suspended the tasks then you must also delete the queue. 

## 2) See If the above task can be implemented using [Task Notifications](https://www.freertos.org/RTOS-task-notifications.html).

## 3) Deadlock Example
* FreeRTOS does not provide any solution to solve the problem of deadlock. 
* It can be only solved while designing real-time embedded systems. 
* We must design tasks such that the deadlock does not occur. 
* One other possibility is to not use the indefinite waiting time for the tasks to acquire the mutex. 
* Instead use a minimum possible blocked time for the task that will be waiting to take mutex. 
* If a task is not able to take mutex within that time, it should release other resources also. 
* In small real-time embedded systems, deadlock is not a big problem. 
* Because an application designer can easily trace deadlock while designing an application and can remove it before deploying an application in the market. 

### Task

* Find the solution to deadlock
  * By your method
  * Try using mutex instead of semaphore
  * Try changing the wait times
* Refer this for additional info on [deadlock](https://www.quora.com/What-is-the-difference-between-binary-semaphore-and-mutex-in-the-context-of-RTOS-Can-the-priority-inversion-be-avoided-using-semaphore)

```c
#include <stdio.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "freertos/semphr.h"
#include "esp_log.h"

SemaphoreHandle_t bi_sema_1 = NULL;
SemaphoreHandle_t bi_sema_2 = NULL;
static int shared_int = 0;

void led_blink_1()
{
    const char task[] = "led blink 1";
    while (1)
    {

        if (bi_sema_1 != NULL)
        {
            // See if we can obtain the semaphore.  If the semaphore is not
            // available wait 10 ticks to see if it becomes free.
            if (xSemaphoreTake(bi_sema_1, portMAX_DELAY) == pdTRUE)
            {
                // We were able to obtain the semaphore and can now access the
                // shared resource.
                shared_int += 1;
                // ...
                ESP_LOGI(task, "Semaphore 1 Taken Succesfully | Shared Res - %d", shared_int);
                // We have finished accessing the shared resource.  Release the
                // semaphore.
                if (xSemaphoreTake(bi_sema_2, portMAX_DELAY) == pdTRUE)
                {
                    // We were able to obtain the semaphore and can now access the
                    // shared resource.
                    shared_int -= 1;
                    // ...
                    ESP_LOGI(task, "Semaphore 2 Taken Succesfully | Shared Res - %d", shared_int);
                }
                else
                {
                    // We could not obtain the semaphore and can therefore not
                    // access the shared resource safely.
                    ESP_LOGW(task, "Failed in taking Semaphore 2");
                }
                xSemaphoreGive(bi_sema_1);
                xSemaphoreGive(bi_sema_2);
            }
            else
            {
                // We could not obtain the semaphore and can therefore not
                // access the shared resource safely.
                ESP_LOGW(task, "Failed in taking Semaphore 1");
            }
        }
        vTaskDelay(1000 / portTICK_PERIOD_MS);
    }
}

void led_blink_2()
{
    const char task[] = "led blink 2";
    while (1)
    {
        if (bi_sema_2 != NULL)
        {
            // See if we can obtain the semaphore.  If the semaphore is not
            // available wait 10 ticks to see if it becomes free.
            if (xSemaphoreTake(bi_sema_2, portMAX_DELAY) == pdTRUE)
            {
                // We were able to obtain the semaphore and can now access the
                // shared resource.
                shared_int -= 1;
                // ...
                ESP_LOGI(task, "Semaphore 2 Taken Succesfully | Shared Res - %d", shared_int);
                // We have finished accessing the shared resource.  Release the
                // semaphore.
                if (xSemaphoreTake(bi_sema_1, portMAX_DELAY) == pdTRUE)
                {
                    // We were able to obtain the semaphore and can now access the
                    // shared resource.
                    shared_int -= 1;
                    // ...
                    ESP_LOGI(task, "Semaphore 1 Taken Succesfully | Shared Res - %d", shared_int);
                }
                else
                {
                    // We could not obtain the semaphore and can therefore not
                    // access the shared resource safely.
                    ESP_LOGW(task, "Failed in taking Semaphore 1");
                }
                xSemaphoreGive(bi_sema_2);
                xSemaphoreGive(bi_sema_1);
            }
            else
            {
                // We could not obtain the semaphore and can therefore not
                // access the shared resource safely.
                ESP_LOGW(task, "Failed in taking Semaphore 2");
            }
        }
        vTaskDelay(1000 / portTICK_PERIOD_MS);
    }
}

void app_main()
{
    bi_sema_1 = xSemaphoreCreateBinary();
    bi_sema_2 = xSemaphoreCreateBinary();
    // xSemaphoreCreateBinary() Creates a new binary semaphore instance, and
    // returns a handle by which the new semaphore can be referenced.

    xSemaphoreGive(bi_sema_1);
    xSemaphoreGive(bi_sema_2);
    // The semaphore must be given before it can be taken if calls are made
    // using xSemaphoreCreateBinary()

    xTaskCreate(&led_blink_1, "Led Blink 1", 4096, NULL, 0, NULL);
    xTaskCreate(&led_blink_2, "Led Blink 2", 4096, NULL, 1, NULL);
}
```
  

