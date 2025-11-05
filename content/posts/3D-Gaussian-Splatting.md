+++ 
draft = false
date = 2025-11-06T00:07:25+02:00
title = "3D Gaussian Splatting Reconstruction"
description = ""
slug = ""
authors = ["Haining"]
tags = ["3D Reconstruction", "Gaussian Splatting", "Brush", "Supersplat", "Unity"]
categories = []
externalLink = ""
series = []
+++
## Introduction to Gaussian Splatting

**3D Gaussian Splatting** is a new point-based representation for 3D reconstruction.  
Instead of using meshes or voxels, it models a scene as millions of small 3D Gaussian primitives (splats),  
each defined by its position, color, opacity, and covariance (shape/orientation).

**Advantages:**
- Real-time rendering performance  
- Smooth and continuous surfaces  
- No explicit mesh topology required  
- Compatible with NeRF-style optimization  

Essentially, the 3D world is represented as a *field of Gaussian points* that blend together during rendering, producing a photo-realistic model.


## Generating Point Clouds with the Brush Web Demo

You can easily create and train Gaussian splats using the online **Brush Demo**:

> [https://arthurbrussee.github.io/brush-demo/](https://arthurbrussee.github.io/brush-demo/)

This web demo allows you to load multi-view images or datasets, train a Gaussian Splat model, and export the resulting `.ply` point cloud file.


### Step 1. Load Data

In the top-left panel:

Load Data → File / Directory / URL

You can load:
- A directory of multi-view images (e.g., from COLMAP or a camera rig)
- A dataset with camera poses

Once loaded, the dataset will appear in the bottom preview panel.

### Step 2. Set Training Parameters

On the right-hand side, open the **Settings** panel and configure your parameters.

<div style="text-align: center;">  
<img src="/haining-yu/images/brush-setting.png" alt="Brush Training Settings" width="600">
</div> 

Recommended configuration:

| Parameter | Example | Description |
|------------|----------|--------------|
| **Max Splats** | 500k – 1M | Maximum number of points |
| **Learning Rate** | 1e-3 | Training learning rate |
| **Growth** | Enabled | Dynamically adds new splats during training |
| **Losses** | Default | Keep default settings |
| **Spherical Harmonics Degree** | 3 | Controls lighting representation |
| **Subsample Points** | Enabled | Improves performance |
| **Evaluate Every** | 1000 steps | Saves checkpoints during training |

### Step 3. Start Training

Click **Start** to begin training.  
You can monitor:
- Current iteration and loss  
- Number of active splats  
- GPU memory usage  

As training progresses, the reconstructed scene will appear in the right preview window.


### Step 4. Export the Point Cloud

Once training is complete: The system will export a `.ply` file containing all Gaussian points with their color and spatial parameters.
<div style="text-align: center;">  
<img src="/haining-yu/images/export.png" alt="export" width="600">
</div> 

## 3. Clean and Edit the Point Cloud in Supersplat

Use **Supersplat** ([https://superspl.at/editor/](https://superspl.at/editor/))  
to clean and optimize your point cloud before rendering in Unity.

### Workflow:

1. Open **Supersplat**
2. Go to `File → Import` and select your `my_scene_gaussians.ply`
3. Remove unnecessary regions (e.g., background, sky, noise)
4. Adjust point size, color, or brightness
5. Export the cleaned file

## 4. Rendering in Unity with Gaussian Splatting

Use the official project by Aras-p:

> 🔗 [https://github.com/aras-p/UnityGaussianSplatting](https://github.com/aras-p/UnityGaussianSplatting)

### Steps:

#### (1) Clone the repository
```bash
git clone https://github.com/aras-p/UnityGaussianSplatting.git
```
#### (2) Open in Unity Hub
- Click Add Project
- Select: UnityGaussianSplatting/projects/GaussianExample-URP
- Recommended version: Unity 6000.0.23f1

#### (3) Create Gaussian Asset
In the Unity menu:   
Tools → Gaussian Splats → Create GaussianSplatAsset

Choose the .ply file you exported from Supersplat (my_scene_cleaned.ply)
and click Create Asset.

#### (4) Attach Renderer
1. Create an empty GameObject → name it MyPointCloud
2. Click Add Component → GaussianSplatRenderer
3. Drag the generated .asset into the Data Asset field

<div style="text-align: center;">  
<img src="/haining-yu/images/scene.png" alt="3D output" width="600">
</div> 

**It's Time to Creat Something Cool!**