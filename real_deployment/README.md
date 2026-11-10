# MA6216 real-robot quick start

The UR5e uses a fixed RGB-D camera and CAD-based FoundationPose for pickup. The wrist RGB-D camera refines XY before descent while holding the grasp orientation. The hardware runner checks the grasp, release, and placement.

## 1. Set up the workcell

- Use Linux with a UR5e, Robotiq 2F-85, and fixed and wrist RealSense cameras. In the hardware Python environment, run `python -m pip install -r real_deployment/requirements-hardware-lock.txt`.
- Set up a separate CUDA FoundationPose environment using [the project setup guide](../docs/SETUP.md). Include SciPy and trimesh in that environment. Set `MA6216_FP_PYTHON` to its Python executable and `MA6216_FOUNDATIONPOSE_DIR` to the FoundationPose checkout.
- Copy `hardware.example.json` to `hardware.local.json` in this folder and enter the device details. Save the measured 4×4 transforms as `T_base_camera_third` and `T_tcp_camera_wrist` in `calibration.local.json`, or set `MA6216_HARDWARE_CALIBRATION` to that file.

## 2. Check perception

Run from the repository root with the tray and parts in view. This uses the cameras and model without moving the robot.

```sh
python real_deployment/pick_parts.py perceive --run artifacts/hardware_perception
```

## 3. Run a supervised demo

Install the lab-local `visual_gap_rsi.py` helper and its measured `ur_yellow_finger_gap_v2.json` beside the runner. Confirm the TCP, table height, and motion bounds for your workcell before commanding motion. Run with an operator at the E-stop.

```sh
python real_deployment/pick_parts.py all --task artifacts/hardware_tray --confirm RUN
```

For tabletop stacking, add `--pallet --pallet-at X Y YAW_DEG` with your measured robot-base coordinates in metres and yaw in degrees. Choose a fresh output directory for each run.

The published snapshot contains the project runner and hardware interfaces. The lab helper, site calibration, and device settings are local dependencies.
