# MA6216 real-robot deployment

The hardware demonstration uses a UR5e, a Robotiq 2F-85, and fixed and wrist RGB-D cameras. The fixed view provides a CAD-based FoundationPose pickup estimate. Before descent the wrist view refines XY while holding the grasp orientation. The hardware controller then runs guarded Cartesian moves and checks grasp, release, and placement with vision. The simulated workshop's pre-contact 6D PBVS is a separate controller.

| File | Role |
| --- | --- |
| `pick_parts.py` | Multi-part tray transfer and two-layer tabletop stacking |
| `pick_place_once.py` | Single-block planning and supervised execution |
| `wrist_align.py`, `wrist_tray.py`, `wrist_block.py` | Pickup alignment, tray measurement, and placement checks |
| `ur_motion.py`, `hardware_interfaces.py`, `rs_stream.py` | Motion, gripper, and camera interfaces |
| `foundationpose_real_check.py`, `fp_batch.py` | RGB-D preparation, 6D inference, and base-frame reporting |
| `verify_calibration.py`, `probe_hardware.py` | Calibration checks and device diagnostics |

## Workcell setup

1. Use a Linux hardware environment with `requirements-hardware-lock.txt`. Use the independent CUDA FoundationPose environment described in [the project setup](../docs/SETUP.md) for inference. Set `MA6216_FP_PYTHON` to that environment's Python and `MA6216_FOUNDATIONPOSE_DIR` if the upstream checkout is outside the usual path. The separate offline requirements file describes the preparation environment.
2. Copy `hardware.example.json` to `hardware.local.json`. Enter the local addresses and the serial numbers for the `front` and `wrist` cameras. The example carries no live settings.
3. Supply independently checked fixed-camera-to-base and wrist-camera-to-TCP transforms as 4×4 matrices under `T_base_camera_third` and `T_tcp_camera_wrist` in `calibration.local.json`, or point `MA6216_HARDWARE_CALIBRATION` to the local file. Check the TCP, camera profiles, workcell geometry, colour thresholds, and motion limits against the physical setup.
4. Full execution also requires the lab's local `visual_gap_rsi.py` helper and its measured `ur_yellow_finger_gap_v2.json`. The helper is loaded before the multi-part run opens devices. The release includes the grasp verification logic that consumes it. The helper and calibration, raw camera data, logs, and videos are maintained separately.

The entry points expose help without connecting to devices.

```sh
python real_deployment/pick_parts.py --help
python real_deployment/pick_parts.py all --help
python real_deployment/pick_place_once.py --help
```

The `perceive` subcommand uses cameras and inference without robot motion. The multi-part `all` subcommand runs tray transfer by default and supports `--pallet` and `--pallet-at` for tabletop stacking. The archived demonstration used a calibrated workcell and an operator at the E-stop. Validate a new workcell before issuing motion commands. The demo establishes feasibility, and the published code was checked offline.
