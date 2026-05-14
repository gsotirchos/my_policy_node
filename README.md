# My Policy Node

This directory contains a minimal `MyPolicy` that executes a pure Vision-Language-Action (VLA) control loop using SmolVLA and Real-Time Control (RTC) action queueing.

**`MyPolicy.py`** is the sole orchestrator policy. It sets up parameters, loads the `SmolVLA` model, handles GPU inference threading, real-time Action Queue processing, and coordinates the entire execution loop. The behavior policy provides the following core capabilities:

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

Run the standard `aic_model` command, specifying the `MyPolicy` class. You can configure the behavior dynamically by passing ROS arguments via `-p <argument>:=<value>`.

To run the default configuration (Pure SmolVLA):

```bash
pixi run ros2 run aic_model aic_model --ros-args -p use_sim_time:=true -p policy:=my_policy_node.MyPolicy
```

### Available Configuration Arguments

- **`checkpoint_path`**: Absolute path to the SmolVLA model weights (default: `my_policy_node/my_policy_node/model/`).
- **`vla_chunk_size_threshold`** (`float`, default: `0.9`): Buffer fullness ratio that triggers inference yielding.

## How to prepare the submission container

1. Clone the `submission-env` branch of the `aic` repository, which contains this one as a _submodule_:

``` bash
mkdir -p ~/ws_aic/src/ && cd ~/ws_aic/src/
git clone --recurse-submodules -j8 -b luca/rtc https://github.com/gsotirchos/aic.git
```

2. Clear all `pixi` and *importantly* `docker` build cache:

``` bash
rm -rf ./.pixi
rm -rf ~/.cache/rattler
docker builder prune --filter type=exec.cachemount
```

3. Fix the pixi version with:

``` bash
pixi self-update --version 0.67.2
```

4. Install the workspace:

``` bash
pixi install
```

5. Build the image and verify it locally:

``` bash
docker compose -f docker/docker-compose.yaml build model
docker compose -f docker/docker-compose.yaml up
```
