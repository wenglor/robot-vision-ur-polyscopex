# Example Universal Robots Polyscope X program for the generic vision interface

**Example program version:** 1.0.0

This repository demonstrates how to use the Generic Vision Interface with wenglor vision devices on a UR Polyscope X controller. The included [Generic wenglor interface.urpx](sources/Generic%20wenglor%20interface.urpx) file acts as a working sample program that you can adapt and customize for your application.

> NOTE
>
> This repository focuses exclusively on Polyscope X-specific topics. For general robot vision information, please refer to the [wenglor robot vision manual](https://wenglor.github.io/robot-vision-generic-string/).

📖 **Full documentation** is available in the [online manual](https://wenglor.github.io/robot-vision-ur-polyscopex/)

---

## Table of Contents

- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Running the Sample Program](#running-the-sample-program)
- [Configuration](#configuration)
  - [Connection](#connection)
  - [uniVision jobs](#univision-jobs)
  - [Use case and calibration plate](#use-case-and-calibration-plate)
  - [Poses](#poses)
- [Troubleshooting](#troubleshooting)
  - [Communication errors](#communication-errors)
  - [Insufficient calibration accuracy](#insufficient-calibration-accuracy)
- [Support & Feedback](#support--feedback)

---

## Prerequisites

> Tested with Polyscope X SW 10.7.0

- Polyscope X compatible ControlBox (version ≥ CB5.6).
- e-Series or UR Series robot arm.
- Basic knowledge of URScript.
- A [B60](https://www.wenglor.com/B60) (firmware ≥ 1.4) or [Machine Vision Controller (MVC)](https://www.wenglor.com/MachineVisionController) (firmware ≥ 1.1).
- A [uniVision](https://www.wenglor.com/uniVision3) job for calibration and object detection.
- Robot and Machine Vision Device in the same network.

---

## Installation

1. Get `Generic wenglor interface.urpx` from the [`sources`](sources) directory of this repository.
2. Copy the file to a USB stick or a location reachable from the Polyscope X file browser.
3. On the UR Teach Pendant, open the Polyscope X **Program** interface, import the file, and confirm. Polyscope X loads the program together with its `wenglor_examples`, `wenglor_api`, and `wenglor_helpers` modules.
4. Enter the password of the UR robot when prompted (by default `ursafe`).
5. Follow the [Configuration](#configuration) steps below.

---

## Running the Sample Program

1. Navigate to the program tab and call either `single_detection` or `multi_detection` from the `wenglor_examples` module as the Main Program entry point.
2. Select the TCP used for calibration.
3. Execute the program and monitor the messages displayed on the teach pendant.

---

## Configuration

All parameters and poses are located in the **Installation** node tree under **Before Start → User Config**, in the `Program` tab.

### Connection

- `WG_CAM_IP` — IP address of the Machine Vision Device (default `192.168.100.1`).
- `WG_CAM_PORT` — TCP port of the robot vision server (default `32006`).
- `WIG_COMMAND_TIMEOUT` — Socket read timeout in seconds (default `20`).

### uniVision jobs

- `WG_CALIB_JOB` — Job used for calibration (default `calibration.u3p`).
- `WG_FIND_OBJECTS_JOB` — Job used by `single_detection` and `multi_detection` (default `find_objects.u3p`).
- `WG_FIND_TARGET_JOB` — Job used by `update_reference_frame` (default `find_target.u3p`).

### Use case and calibration plate

- `WG_USE_CASE` — Either `camera_on_robot` or `camera_not_on_robot` (default `camera_on_robot`).
- `WG_CALIBRATION_TARGET` — Defines the calibration plate (default `zvzj001`). Select `zvzj001` for ZVZJ005, `zvzj002` for ZVZJ006.

### Poses

- `g_detection_pose` — Pose the robot moves to for object detection and validation. Teach this pose via **Set Expression** → **Edit Waypoint**.
- `WG_CALIB_POSE_1` … `WG_CALIB_POSE_5` — The five calibration poses; teach each one.

See the [User Configuration](https://wenglor.github.io/robot-vision-ur-polyscopex/2_0_0_user_configuration/) page for the full variable list, including the validation offset and the `update_reference_frame` workflow (`WG_MACHINE_POSES_TAUGHT`, `G_POSE_IN_MACHINE`).

---

## Troubleshooting

### Communication errors

- Verify the values of `WG_CAM_IP` and `WG_CAM_PORT` in `Program → Before Start → User Config`.
- Ensure the robot server on the Machine Vision Device is active: navigate to the device website → `Jobs` → Processing Instance → `Robot Server`.
- Check network connectivity and firewall rules between the robot controller and the Machine Vision Device.

### Insufficient calibration accuracy

You can improve calibration accuracy by using more than five calibration poses. Add additional calibration movements — each followed by a call to `wenglor_api.add_calibration_pose` — in `Program → wenglor_api → run_calibration`, after the existing `WG_CALIB_POSE_5` step and before `calibration:calculate[...]` is sent.

See the [Troubleshooting](https://wenglor.github.io/robot-vision-ur-polyscopex/4_0_0_troubleshooting/) page for more, including error codes returned by the device and reasons the program may exit unexpectedly.

---

## Support & Feedback

- **Bugs:** Please open a new Issue in the [GitHub Issues section](../../issues) if needed.
- **Feature Requests & Ideas:** Discuss suggestions in the Discussions → Ideas category under [GitHub Discussions](../../discussions).
