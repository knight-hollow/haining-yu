+++ 
draft = false
date = 2025-10-22T14:07:25+02:00
title = "How to Use ML-Agents in Unity"
description = ""
slug = ""
authors = ["Haining"]
tags = ["ML-Agents","Unity","Reforce-Learning"]
categories = []
externalLink = ""
series = []
+++

## Resources
- [ML-Agents Installation Guide](https://unity-technologies.github.io/ml-agents/Installation/)
- [ML-Agents GitHub Repository](https://github.com/Unity-Technologies/ml-agents)

## Requirements
### Microsoft Visual C++ Redistributable  
Install the latest supported version:  
[https://learn.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist](https://learn.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist)

### CUDA Toolkit  
> The official PyTorch page recommends **CUDA 12.1**,  
> but **CUDA 11.8** has been tested and confirmed to work properly.

```bash
pip install torch~=2.2.1 --index-url https://download.pytorch.org/whl/cu121
```

### Install cuDNN
Download the latest cuDNN compatible with your CUDA version (e.g., 11.8).  
Install cuDNN and manually copy the following files into the CUDA Toolkit folders:
1. Copy all DLL files
```text
From:  <cuDNN path>\bin\*.dll
To:    C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.8\bin\
```
2. Copy all header files
```text
From:  <cuDNN path>\include\*.h
To:    C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.8\include\
```
3. Copy all library files
```text
From:  <cuDNN path>\lib\x64\*.lib
To:    C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.8\lib\x64\
```

## Verify CUDA Installation
Run the following command to confirm CUDA installation:
```bash
nvcc --version
```

Expected output example:
```
Cuda compilation tools, release 11.8, V11.8.89
Build cuda_11.8.r11.8/compiler.31833905_0
```

## Create a Python Virtual Environment (Anaconda)
```bash
conda create -n mlagents python=3.10.12
conda activate mlagents
```
If you encounter any solver issues:
- Retry after conda updates its metadata.
- Continue even if it shows libmambapy warning (it will still activate successfully).

## Install PyTorch (with CUDA 11.8)
```bash
pip install torch==2.2.1+cu118 torchvision==0.17.1+cu118 torchaudio==2.2.1+cu118 --index-url https://download.pytorch.org/whl/cu118
```
## Install NumPy (compatible version)
```bash
conda install numpy==1.23.5
```
## Verify PyTorch + cuDNN Availability
Open Python and test:
```python
import torch
print("CUDA available:", torch.cuda.is_available())
print("cuDNN available:", torch.backends.cudnn.is_available())
print("cuDNN version:", torch.backends.cudnn.version())
```
Expected output:
```
CUDA available: True
cuDNN available: True
cuDNN version: 8700
```

## Clone ML-Agents from GitHub
```bash
cd Documents
cd Unity_ML_agent
git clone https://github.com/Unity-Technologies/ml-agents.git
```
## Install ML-Agents Locally
Change directory into the cloned folder:
```bash
cd ml-agents
```
Install both core packages:
```base
python -m pip install ./ml-agents-envs
python -m pip install ./ml-agents
```

## Verify the Installation
Run:
```bash
mlagents-learn --help
```
If installed correctly, you will see a help message showing available arguments:
```
usage: mlagents-learn [-h] [--env ENV_PATH] [--resume] [--deterministic]
                      [--force] [--run-id RUN_ID]
...
```

## Building the Unity Environment
### Create the Scene
| Object | Role | Description |
|:--------|:------|:-------------|
| **Plane** | Ground | Resize to (5, 1, 5) |
| **Sphere** | Agent | Add Rigidbody component |
| **Cube** | Target | The goal the agent should reach |
| **Empty Object (RollerBall)** | Manager | Parent object for organization |

<div style="text-align: center;">  
<img src="/haining-yu/images/test.png" alt="Unitytest" width="400">
</div> 

### Add Components to the Agent (Sphere)
- **Rigidbody** → Enable gravity  
- **Decision Requester**  
- **Behavior Parameters**  
- **Custom script** → `RollerAgent.cs`

### Configure Behavior Parameters

| Parameter | Setting | Description |
|:-----------|:---------|:-------------|
| **Behavior Name** | `RollerBall` | Must match the YAML config file |
| **Vector Observation → Space Size** | 8 | 3 (Target) + 3 (Agent) + 2 (Velocity) |
| **Actions → Continuous Actions** | 2 | Move on X and Z axes |
| **Behavior Type** | `Default` for training / `Heuristic` for manual testing |

### Configure Decision Requester

| Setting | Value | Description |
|:----------|:--------|:-------------|
| **Decision Period** | 10 | Agent acts every 10 frames |
| **Request Decision on Start** | ✅ Enabled | Ensures agent starts immediately |

### Create and Attach Scripts

Create a new C# script named **`RollerAgent.cs`** under `Assets/Scripts/`.

```csharp
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class RollerAgent : Agent
{
    Rigidbody rBody;
    public Transform Target;
    public float forceMultiplier = 10;

    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }

    public override void OnEpisodeBegin()
    {
        if (transform.localPosition.y < 0)
        {
            rBody.angularVelocity = Vector3.zero;
            rBody.velocity = Vector3.zero;
            transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        Target.localPosition = new Vector3(Random.value * 8 - 4, 0.5f, Random.value * 8 - 4);
    }

    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(Target.localPosition);
        sensor.AddObservation(transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }

    public override void OnActionReceived(ActionBuffers actions)
    {
        float actionX = actions.ContinuousActions[0];
        float actionZ = actions.ContinuousActions[1];
        rBody.AddForce(new Vector3(actionX, 0, actionZ) * forceMultiplier);

        float distanceToTarget = Vector3.Distance(transform.localPosition, Target.localPosition);

        if (distanceToTarget < 1.42f)
        {
            SetReward(1.0f);
            EndEpisode();
        }
        else if (transform.localPosition.y < 0)
        {
            EndEpisode();
        }
    }

    public override void Heuristic(in ActionBuffers actionsOut)
    {
        var continuousActionsOut = actionsOut.ContinuousActions;
        continuousActionsOut[0] = Input.GetAxis("Horizontal");
        continuousActionsOut[1] = Input.GetAxis("Vertical");
    }
}
```
### Test the Scene
1.	Set Behavior Type → Heuristic Only in the Behavior Parameters.
2.	Press Play ▶️ in Unity.
3.	Use arrow keys (or WASD) to move the ball toward the cube.
4.	Confirm Rigidbody physics and agent movement work correctly.

### Prepare for Training

Once behavior logic is confirmed, switch the Behavior Type back to Default.  
This enables connection with the Python ML-Agents training process. 

## Training Configuration and Execution
### Create the Training Configuration File
Inside your cloned **ml-agents** folder, navigate to:
```
ml-agents/config/ppo/
```
Create a new YAML file named:
```
rollerball_config.yaml
```
Add the following hyperparameters:
```yaml
behaviors:
  RollerBall:
    trainer_type: ppo
    hyperparameters:
      batch_size: 10
      buffer_size: 100
      learning_rate: 3.0e-4
      beta: 5.0e-4
      epsilon: 0.2
      lambd: 0.99
      num_epoch: 3
      learning_rate_schedule: linear
      beta_schedule: constant
      epsilon_schedule: linear
    network_settings:
      normalize: false
      hidden_units: 128
      num_layers: 2
    reward_signals:
      extrinsic:
        gamma: 0.99
        strength: 1.0
    max_steps: 1000000
    time_horizon: 64
    summary_freq: 10000
```
### Start Training
Before pressing Play in Unity, open your Anaconda terminal and run:
```bash
mlagents-learn config/ppo/rollerball_config.yaml --run-id=RollerBall --force
```
Then, go back to Unity and click ▶️ Play to start training.
- --run-id=RollerBall → gives a unique name to the training session
- --force → overwrites any previous runs with the same ID
### Console Output Example
When training starts successfully, you should see logs similar to:
```
Version information:
  ml-agents: 1.2.0.dev0
  ml-agents-envs: 1.2.0.dev0
  Communicator API: 1.5.0
  PyTorch: 2.2.1+cu118

[INFO] Listening on port 5004. Start training by pressing Play in the Unity Editor.
[INFO] Connected to Unity environment with package version 4.0.0
[INFO] Connected new brain: RollerBall
[INFO] Exported results/RollerBall/RollerBall-100000.onnx
Copied results/RollerBall/RollerBall-100000.onnx to results/RollerBall/RollerBall.onnx
```
### Exported Model
After training completes, ML-Agents automatically exports a trained model file:
```
results/RollerBall/RollerBall.onnx
```
You can import this .onnx file into Unity for inference.

## Using the Trained Model in Unity
### Load the ONNX Model
1.	In Unity, open the Behavior Parameters component of the Agent.
2.	Under Model, click the field and select your trained .onnx file:
```
Assets/ML-Agents/Results/RollerBall/RollerBall.onnx
```
3.	Set Behavior Type to Inference Only.
### Test the Trained Agent
1.	Press Play ▶️ in Unity.
2.	Observe how the agent (Sphere) automatically navigates toward the cube (Target).
3.	The movement becomes smooth and efficient after successful training.
