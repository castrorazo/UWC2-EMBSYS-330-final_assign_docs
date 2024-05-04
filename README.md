# EMBSYS 330 Final Project
## Objective
Run through the (almost) complete project life cycle:
1. Requirements Analysis
2. Architecture / High Level Design
4. Low Level Design
5. Construction
6. Test

The learner shall chose a project that incorporates the touch screen and multiple sensors on the Discovery board and integrate them into a working project. The nature of the project is up to the student.

## Project Flow

1. Choose a project.
2. Begin Requirements Capture.
3. Start Architecture / High Level Design.
    1. Design the software with testing  in mind.
4. Identify areas requiring low level design.
5. Start low level design for the identified areas.
6. Construct the software, updating design documents as necessary.
    1. Construct the software with testing in mind. You can use test-driven development or add tests afterwards.
7. Test the software using Twister and the Ztest framework built into Zephyr.
8. Demonstrate the project in Week 10, either live or by prerecorded demo.

## Project Timing
Information on the design process shall be given throughout the first 4 weeks of the course, and on Twister / Ztest in Module 05.

There is a separate Assignment that must be handed in for the first 4 weeks to develop some level of comfort in developing Zephyr applications. It is expected that as well as these assignments, you chose your final project in week 1 and work on requirements, architecture and design in weeks 2 through 6.

Modules 06 through 08 teach Hierarchical State Machines. You may wish to refactor your design to take advantage of the power and flexibility of this design method and implement using Zephyr's State Machine Framework. With some reading ahead you should be in a position to do this after Lecture 07.

There is also an *optional* assignment in Module 09 with a nonzero risk of bricking the development board. It is highly recommended the final project is complete and demo recorded before attempting this assignment.

## Requirements
1. Demonstrate an understanding of embedded systems design through documents and/or diagrams.
    1. This must include requirements, architecture / high level design and detailed design and may include UI design, state charts, activity diagrams and other relevant documentation.
2. Demonstrate control over multiple sensors.
    1. The sensors should use their interrupts to notify the application of new data
3. Demonstrate a knowledge of multi-threaded system design.
4. MVP: Demonstrate use of the display for data output.
    1. Optionally demonstrate use of the touchscreen to create a GUI.
5. Demonstrate adequate test coverage of the code, including unit tests of modules and integration tests running on the hardware.
    1. Note that since we are on Windows, QEMU and native-sim are not available so all test shall be performed on the development board.
6. Document the operation of the application in the embsys330_final_project_readme.md file.

## Project Framework
The project framework is derived from the LVGL demo at `zephyr\samples\subsys\display\lvgl\`

The demo is places in the `app` sub-directory rather than in the folder root as before to allow for a separate `tests` directory.

Changes made to the LVGL demo:
1. Added a custom overlay and config file in the boards sub-directory to define SRAM2 and tell LVGM to use a custom section for the video memory.
1. Set the video buffer size (`CONFIG_LV_Z_VDB_SIZE`) to 14% of the screen size, instead of the default 64%, since that is the maximum size that fits in SRAM2.
1. Added a custom linker script to put the LVGL video buffer in SRAM2.
1. Added the touchscreen shield to CMakeLists.txt so it did not have to be defined on the command line.
1. `CONFIG_DEBUG_THREAD_INFO` and `CONFIG_THREAD_NAME` set in `prj.conf` to enable RTOS debug information. 
1. Added `count.c` and `count.h` to create a simple module to demonstrate testing.
1. Renamed the original demo readme to `lvgm_demo_README.rst`.

### Memory Usage
LVGM uses a lot of memory.

There is 16KB assigned for a heap for dynamic allocation of UI objects, which can be modified by changing `CONFIG_LV_Z_MEM_POOL_SIZE`.

The video buffer for rendering images before transferring them to the screen is defined as a percentage of screen size: `CONFIG_LV_Z_VDB_SIZE`. The default is 64% of the 225KB screen memory, which would be 144KB - larger than all available RAM on the STM52L475. The MCU has two SRAM banks of 96KB and 32KB respectively, and we are using the entirety of the latter to store the video buffer, (14% screen size). Note this implies that objects containing more than 10,752 pixels will not fit in the video buffer.

Documentation and simulators for the LVGL graphics library are available at: https://lvgl.io/

### Testing
Tests are store in the `tests` sub-directory and can be run with the following commands in PowerShell which will run all tests defined in `tests\testcase.yaml`:

```
cd C:\Users\<username>\zephyrproject\
.\.venv\Scripts\Activate.ps1
cd embsys330_final_project\tests
west twister --device-testing --device-serial COM3 --device-serial-baud 115200 -p disco_l475_iot1 -T .
```
Substitute `COM3` for the appropriate COM port on your setup.

# Deliverables
## Design Documentation

Documentation shall be in Markdown format in the `documents` directory of the project file. Submit documents as pull requests, and after approval merge them into `main`. Blank documents are in the `documents` directory ready to fill in, but feel free to add your own if required to complete your design.

For diagrams, feel free to use any diagram generation tool you feel comfortable with (Inkscape, Visio, UMLet, LucidChart, etc.) but submit them as PNG files in the `documents\images` directory, linked to your markdown files.

At minimum the following is expected:
1. Project Idea
2. Problem Definition - a breakdown of the project idea into a more concrete description of how the project will work. This may include what hardware to use, and user stories or other means of describing the operation.
3. Requirements document.
4. Architecture / High Level Design.
    1. This may be natural language, appropriate diagrams or both.
    2. A layer diagram is not an acceptable architecture diagram. We know you're building an application on top of Zephyr OS services, on top of the Zephyr kernel, on top of the device drivers, etc.
5. Detailed design of appropriate parts of the system.
    1. This may be natural language, appropriate diagrams or both.

## Code deliverables
1. Project code:
    1. Code shall match the design documentation
    2. Code shall be well constructed (See Code Complete for tips on this)
    3. Code shall adhere to the Zephyr Coding Style as described here: https://docs.zephyrproject.org/latest/contribute/guidelines.html#coding-style
    4. Code should be appropriately documented. i.e. document design decisions, not `x++; /* increment x */`
2. Test code:
    1. `test_counter` shows an example of testing code from the application.
    2. All module interfaces should be tested
    3. Low level hardware CAN be tested, since we are testing on the development board, but is not a hard requirement.

## Submission
1. Commit your working code to your Git repository and push to GitHub
2. In GitHub go to the green "<> Code" button, click on the down arrow and select "Dowload Zip"
    1. Name the zip file `embsys330_final_project_<lastname>_<firstname>.zip`
3. Create a GitHub pull request in your repository
4. Submit both the URL of your pull request and the Zip file to Canvas

