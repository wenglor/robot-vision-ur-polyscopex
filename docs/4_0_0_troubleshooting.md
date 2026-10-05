# 4. Troubleshooting

## Communication errors

- Verify the values of `WG_CAM_IP` and `WG_CAM_PORT` in `Program → Before Start → User Config`.
- Ensure the robot server on the Machine Vision Device is active: navigate to the device website → `Jobs` → Processing Instance → `Robot Server`.
- Check network connectivity and firewall rules between the robot controller and the Machine Vision Device.
- If the socket cannot be opened at all, `wenglor_helpers.connect_with_vision_device` shows *"No connection to vision device. Program stopped."* and halts — this points to a network- or address-level issue rather than a protocol error.

## Insufficient calibration accuracy

You can improve calibration accuracy by using more than five calibration poses. Add additional calibration movements — each followed by a call to `wenglor_api.add_calibration_pose` — in `Program → wenglor_api → run_calibration`, after the existing `WG_CALIB_POSE_5` step and before `calibration:calculate[...]` is sent.

!!! note

    See the [wenglor Robot Server overview](https://wenglor.github.io/robot-vision-generic-string/5_1_0_basics_with_robot_server/) in the wenglor robot vision manual for calibration-plate selection, pose-variation recommendations, and how to interpret the reprojection error returned by `calibration:calculate`.

## Error codes returned by the device

If the robot server returns a negative error code (`-5001` … `-5010`), `wenglor_helpers.check_camera_reply` detects it (reply starts with `-`) and `wenglor_helpers.set_return_error` maps it to a readable popup message before the program halts. For the meaning of each code, see the [Generic Robot Vision API → Error codes](https://wenglor.github.io/robot-vision-generic-string/5_6_0_generic_robot_vision_api/#error-codes) in the wenglor robot vision manual.

## Program exits unexpectedly

| Cause | Detail | Message shown |
| --- | --- | --- |
| `g_detection_pose` not set | The example routines compare it against `WIG_EMPTY_POSE` and halt if it still matches. | — |
| Calibration poses not set | `WG_CALIB_POSE_1` … `WG_CALIB_POSE_5` must not equal `WIG_EMPTY_POSE`; `wenglor_helpers.are_calibration_poses_set` checks each of them before `run_calibration` proceeds. | — |
| No reply from the camera, or an empty socket read | `wenglor_helpers.check_camera_reply` detects the missing reply. Check the device state via `state[WG_USE_CASE];`. | *"No reply from camera. Exiting program."* |
| Unknown use case | `WG_USE_CASE` must be exactly `camera_on_robot` or `camera_not_on_robot`; any other value halts `run_calibration`. | *"Unknown use case. Program was aborted."* |
