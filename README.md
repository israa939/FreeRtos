# Embedded System Project with FreeRTOS and STM32

This project demonstrates the use of FreeRTOS with STM32 to manage multiple tasks, semaphores, mutexes, queues, and I2C communication for controlling an LCD and handling analog-to-digital conversion (ADC) for a potentiometer. The system includes tasks for toggling LEDs, performing ADC conversions, updating the LCD display, and handling user interactions via an external button (PA0).

## Overview

This project includes six tasks that work together to perform the following functions:

1. **Task 1**: Toggles a blue LED to indicate that the project has started.
2. **Task 2**: Toggles a green LED and uses an external interrupt (EXTI) triggered by a button (PA0).
3. **Task 3**: Handles ADC conversion for a potentiometer value.
4. **Task 4**: Controls the LEDs based on the potentiometer value.
5. **Task 5**: Displays the potentiometer value on an LCD.
6. **Task 6**: Handles the emergency condition by showing "Urgence" on the LCD when the PA2 button is pressed.

### Key Components

- **FreeRTOS**: Used for task scheduling, synchronization (semaphores, mutexes), and inter-task communication (queues).
- **I2C Protocol**: Used to communicate with the LCD to display data.
- **ADC Conversion**: The potentiometer's analog value is converted to a digital value for display and further processing.

---

## Task Overview and Communication

### 1. **Blue LED Task (Task 1)**  
- **Function**: Toggles a blue LED to indicate that the project has started.  
- **Synchronization**: This task runs first to ensure that the system is initialized before proceeding to other tasks.

### 2. **Green LED Task (Task 2)**  
- **Function**: Toggles a green LED and listens for an external interrupt triggered by the PA0 button press (EXTI).  
- **Synchronization**: This task communicates with the **Blue LED Task** through a **binary semaphore**. The semaphore is released by the blue LED task after it toggles the blue LED, signaling that the system is ready for the green LED task to run.  
- **External Interrupt**: An interrupt service routine (ISR) is used to trigger the semaphore release, allowing the green LED task to toggle the LED on a button press.

### 3. **ADC Conversion Task (Task 3)**  
- **Function**: Performs ADC conversion on the potentiometer to read its value.  
- **Synchronization**: This task waits for the green LED task to release a **binary semaphore** before starting the ADC conversion. This ensures that the conversion process happens after the green LED has toggled and the system is ready for the next step.  
- **Communication**: After the ADC conversion, the task sends the potentiometer value to two other tasks using **queues**.

### 4. **LCD Display Task (Task 4)**  
- **Function**: Displays the potentiometer value on an LCD using I2C communication.  
- **Communication**: The ADC conversion task sends the potentiometer value through a **queue** to this task, which then updates the LCD.  
- **Synchronization**: I2C communication with the LCD requires a **mutex** to ensure that no other task accesses the LCD at the same time, preventing data corruption when updating the display.

### 5. **LED Control Task (Task 5)**  
- **Function**: Controls the LEDs based on the potentiometer value.  
- **Communication**: The ADC task also communicates the potentiometer value through a second **queue** to this task. The task uses the value to control the behavior of the LEDs, adjusting them based on the input from the potentiometer.

### 6. **Emergency Button Task (Task 6)**  
- **Function**: Displays "Urgence" on the LCD when the PA2 button is pressed.  
- **Synchronization**: This task uses a **mutex** to protect the LCD resource. When the PA2 button is pressed, this task takes control of the LCD and updates it with "Urgence." The mutex prevents other tasks (like the LCD display or LED control tasks) from accessing the LCD during this critical operation.

---

## Inter-Task Communication and Synchronization

- **Binary Semaphores**: Used between the blue LED task and the green LED task, as well as between the green LED task and the ADC conversion task. The binary semaphores ensure that each task runs only after the previous task has completed its work.
- **Queues**: Used to pass data (the potentiometer value) between the ADC conversion task, LCD task, and LED control task.
- **Mutex**: Used to protect the LCD resource from being accessed by multiple tasks simultaneously, ensuring that the emergency button task can safely update the display without interference.

---

## Hardware Requirements

- **STM32 Microcontroller**: For running the FreeRTOS tasks and handling hardware interfaces.
- **I2C LCD Display**: To display the potentiometer value and emergency message.
- **Potentiometer**: For analog input that is read by the ADC.
- **LEDs**: To indicate the status of the system.
- **PA0 Button**: For triggering the green LED task via an external interrupt (EXTI).
- **PA2 Button**: For triggering the emergency condition (displaying "Urgence").

---

## How To Run the Project

### Prerequisites

- STM32 development board (e.g., STM32F4 or STM32L4 series).
- **STM32CubeMX**: For peripheral configuration (I2C, ADC, GPIO, etc.).
- **STM32CubeIDE**: To write, compile, and flash the firmware.
- **FreeRTOS**: Integrated with STM32CubeIDE for task scheduling and management.
- **I2C LCD Display**: For output.
- **Potentiometer**: For ADC input.
- **External Button PA0**: For triggering tasks using EXTI interrupts.
- **External Button PA2**: For emergency conditions.

### Steps to Run

1. **Clone the repository**:
    ```bash
    git clone https://github.com/your-username/FreeRTOS-STM32-LCD-Potentiometer.git
    cd FreeRTOS-STM32-LCD-Potentiometer
    ```

2. **Open the project in STM32CubeIDE**:
    - Open STM32CubeIDE and import the project.

3. **Configure the peripherals**:
    - Use STM32CubeMX to configure the peripherals:
        - Set up **I2C** for LCD communication.
        - Configure **ADC** for the potentiometer input.
        - Enable **EXTI** for the PA0 and PA2 buttons.
        - Configure **GPIO** for the LEDs.

4. **Write the code**:
    - Implement the task logic as per the description in the README.
    - Use **binary semaphores**, **queues**, and **mutexes** to synchronize the tasks.

5. **Build the project**:
    - Click on **Build** in STM32CubeIDE to compile the project.

6. **Flash the firmware**:
    - Connect your STM32 board via ST-Link or other programming tools.
    - Click **Run** or **Debug** in STM32CubeIDE to flash the firmware to your device.

7. **Test the system**:
    - Upon powering the device, the **Blue LED Task** will toggle to show that the system has started.
    - Press **PA0** to trigger the green LED task via EXTI interrupt.
    - The ADC will convert the potentiometer value and display it on the LCD.
    - If **PA2** is pressed, "Urgence" will be shown on the LCD.

---

## Conclusion

This project demonstrates how FreeRTOS can be used to manage multiple tasks, synchronize them using binary semaphores and mutexes, and communicate between tasks using queues. It highlights the importance of task coordination, resource management, and inter-task communication in real-time embedded systems.

---

Let me know if you need further adjustments!
