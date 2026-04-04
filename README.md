# SO-101 Native Ubuntu ROS 2 MoveIt Bring-Up

Practical bring-up of a real SO-101 / SO-ARM101 follower-leader robot on native Ubuntu with LeRobot, ROS 2 Jazzy, and MoveIt.

---

## Project overview

This repository documents my practical engineering work with a real SO-101 / SO-ARM101 follower-leader robot pair.

The main goal of the project is not just to “run the demo”, but to understand the full integration stack step by step:

- hardware bring-up
- USB / serial device mapping
- LeRobot teleoperation
- camera setup
- dataset recording and replay
- ACT policy training and evaluation
- migration from WSL to native Ubuntu
- ROS 2 Jazzy bring-up
- MoveIt integration
- debugging real-hardware controller behavior
- identifying mismatches between calibration, URDF, ROS 2 state, and real robot motion

This repository is intended as a practical engineering log and portfolio project for robotics / mechatronics / controls / integration roles.

---

## Platform

### Hardware
- SO-101 / SO-ARM101 follower arm
- SO-101 / SO-ARM101 leader arm
- USB cameras
- Feetech serial servo bus
- Ubuntu booted from external SSD during native testing

### Software
- Ubuntu 24.04 / native Linux workflow
- ROS 2 Jazzy
- MoveIt
- LeRobot
- Python
- RViz2
- ros2_control

---

## Why this project matters

This project became much more than a basic robot setup.

It turned into a real systems-integration exercise involving:

- robot hardware initialization
- low-level serial communication debugging
- controller bring-up
- calibration consistency
- teleop validation
- real-world motion safety
- MoveIt planning on real hardware
- separation of “simulation state looks correct” vs “real robot actually behaves correctly”

A major practical lesson from this work was that **a green RViz / MoveIt status does not automatically mean the real robot is correctly calibrated or safe to execute**.

---

## Current status

### Working
- Native Ubuntu environment boots and runs correctly
- LeRobot follower-leader teleoperation works on native Ubuntu
- Cameras work in the native setup
- Dataset record / replay workflow was completed
- ACT training and online policy execution were validated previously on native Ubuntu
- ROS 2 Jazzy workspace builds successfully
- ROS 2 bring-up runs with hardware interfaces
- `joint_state_broadcaster` works
- `forward_controller` works
- `arm_trajectory_controller` can be loaded
- MoveIt launches successfully
- RViz2 + MoveIt planning scene load correctly
- MoveIt can generate and send trajectories to the real hardware controller
- Direct controller topic tests confirmed that all 6 commanded channels affect the robot

### Investigated problems
- Launch-time arm jumps into unsafe folded poses
- Real arm pose and RViz pose do not always match
- Some trajectories plan successfully but are not physically meaningful for the real configuration
- Calibration values between LeRobot and ROS 2 stack are not yet fully reconciled
- Controller command ordering / semantic mapping had to be tested manually
- Some YAML parameter combinations caused bus read timeouts during servo configuration
- MoveIt could appear operational while the real arm still had state / calibration inconsistencies

### Current engineering conclusion
The ROS 2 and MoveIt stack is now **partially working on real hardware**, but the system still needs **final calibration reconciliation and state-model alignment** before it can be treated as a robust real-hardware manipulation pipeline.

---

## Main engineering findings so far

### 1. Native Ubuntu is much better than WSL for real robot work
A major finding was that native Ubuntu gave much more reliable behavior than WSL for real-time robot operation and integration.

This affected:
- serial reliability
- camera handling
- runtime stability
- ACT online inference usability
- ROS 2 bring-up confidence

### 2. LeRobot teleop and ROS 2 teleop are not automatically equivalent
Direct LeRobot teleoperation worked well after proper calibration.

But once the ROS 2 control / teleop stack was introduced, noticeable mismatches appeared:
- mini-latency
- leader/follower mismatch
- state disagreement
- unexpected real-arm posture during bring-up

This strongly suggests that “working teleop in one stack” does not automatically mean “correct state interpretation in another stack”.

### 3. Calibration consistency is critical
One of the central issues in this project was the relationship between:
- servo EEPROM offsets
- LeRobot calibration files
- ROS 2 YAML joint config
- URDF joint axes and limits
- MoveIt model state
- actual mechanical pose of the arm

A controller may report valid joint states and still represent a real pose that is unsafe or misleading if the calibration chain is inconsistent.

### 4. Real-hardware validation must be joint-by-joint
A very important step was manually testing command channels using ROS topics and checking whether each command actually moved the expected physical joint.

This helped distinguish:
- command-order issues
- interpretation issues
- calibration issues
- joint-state modeling issues

---

## Repository goals

This repository is being built to document:

1. a real bring-up path for SO-101 / SO-ARM101 on native Ubuntu  
2. practical debugging of LeRobot + ROS 2 + MoveIt  
3. lessons learned from calibration and controller integration  
4. a portfolio-quality robotics integration project  

---

## Planned repository structure

```text
so101-native-ubuntu-ros2-moveit/
├── README.md
├── docs/
│   ├── hardware.md
│   ├── software_setup.md
│   ├── lerobot_workflow.md
│   ├── ros2_bringup.md
│   ├── moveit_debugging.md
│   ├── calibration_notes.md
│   └── lessons_learned.md
├── images/
│   ├── real_robot/
│   ├── rviz/
│   └── diagrams/
├── configs/
│   ├── ros2/
│   ├── moveit/
│   └── calibration/
└── notes/
    └── experiments.md


Practical workflow covered in this project
Stage 1 — Hardware + LeRobot bring-up
detect serial ports
identify follower and leader
validate USB mapping
bring up teleoperation
confirm real mechanical motion
Stage 2 — Cameras + data workflow
attach and validate cameras
record demonstrations
replay trajectories
prepare datasets
Stage 3 — Policy learning
ACT training
checkpoint validation
online inference on native Ubuntu
Stage 4 — ROS 2 bring-up
build workspace
configure udev aliases
bring up hardware interfaces
validate joint_state_broadcaster
validate command controllers
Stage 5 — MoveIt
launch MoveIt and RViz
load kinematics and planning pipeline
connect to ROS 2 controller
test planning
test execution on real hardware
debug state mismatch and unsafe start behavior
Safety notes

This project involves real servo-driven hardware.
Some controller / calibration states can cause sudden or unsafe motion.

Important practical lessons:

never assume startup pose is safe
never trust RViz alone
verify real robot pose before enabling execution
test each joint incrementally
keep motion amplitudes small during debugging
stop immediately if the robot starts folding into itself or loading the links mechanically
What is already valuable here from an engineering portfolio perspective

This project already demonstrates:

real robot bring-up
debugging under imperfect conditions
integration across multiple software stacks
ability to isolate faults experimentally
controller-level testing
practical understanding of calibration and motion semantics
transition from “it launches” to “it is actually physically correct”

That is much closer to real robotics engineering than a clean toy simulation project.

Next steps

The next main steps are:

reconcile LeRobot calibration with ROS 2 configuration
define a safe real robot reference pose
verify per-joint semantic mapping against physical motion
align URDF / MoveIt state with real hardware state
test repeatable MoveIt plans from safe start poses
document the final stable bring-up workflow
add photos, screenshots, controller logs, and configuration snapshots
Intended audience

This repository is useful for:

robotics students
mechatronics students
ROS 2 beginners working with real hardware
people integrating SO-101 / SO-ARM101
employers evaluating practical robotics integration work
Disclaimer

This repository documents real practical experiments on a physical robot.
Some parts of the stack are still under active debugging and should not be treated as a finished production-ready solution.

The value of the project is in the engineering process, problem isolation, and system understanding.

Author

Engineering portfolio project by a mechatronics student focused on:

robotics
controls
integration
industrial automation
practical hardware/software debugging
