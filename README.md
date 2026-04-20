# My Policy Node

This directory contains a minimal `MyPolicy` that executes a pure Vision-Language-Action (VLA) control loop using SmolVLA and Real-Time Control (RTC) action queueing.

## Code Structure

- **`MyPolicy.py`**: The sole orchestrator policy. It sets up parameters, loads the `SmolVLA` model, handles GPU inference threading, real-time Action Queue processing, and coordinates the entire execution loop.

## Functionality Available

The behavior policy provides the following core capabilities:

- **Vision-Language-Action (VLA) Control:** Integrates with `SmolVLA` for visual trajectory generation, utilizing threaded, GPU-accelerated inference.
- **Real-Time Control (RTC):** Seamlessly merges overlapping trajectory chunks using a latency-aware action queue.

## How to set up

1. Add dependency to your project's pixi.toml, in `~/ws_aic/src/aic/pixi.toml`:

    ```toml
    [dependencies]
    ros-kilted-aic-my-policy-node = { path = "my_policy_node" }
    ```

2. Install it using `pixi`:

    ``` bash
    cd ~/ws_aic/src/aic
    git clone https://github.com/gsotirchos/my_policy_node.git
    pixi reinstall ros-kilted-aic-my-policy-node
    ```

> [!IMPORTANT]
> Changes to packages within a Pixi environment are not tracked automatically. To apply updates, you must rerun `pixi reinstall ros-kilted-aic-my-policy-node`.

## How to use

Run the standard `aic_model` command, specifying the unified `MyPolicy` class. You can configure the behavior dynamically by passing ROS arguments via `-p <argument>:=<value>`.

To run the default configuration (Pure SmolVLA):

```bash
pixi run ros2 run aic_model aic_model --ros-args -p use_sim_time:=true -p policy:=my_policy_node.MyPolicy
```

### Available Configuration Arguments

- **`checkpoint_path`**: Absolute path to the SmolVLA model weights (default: `my_policy_node/my_policy_node/model/`).
- **`vla_chunk_size_threshold`** (`float`, default: `0.9`): Buffer fullness ratio that triggers inference yielding.
