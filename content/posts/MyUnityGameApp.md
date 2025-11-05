+++ 
draft = false
date = 2025-11-1T13:00:25+02:00
title = "Unity Action Game — Development and iOS Export Summary"
description = ""
slug = ""
authors = ["Haining"]
tags = ["Unity"，"GameDevelopment", "ActionGame", "ThirdPerson", "MobileGame","iOS"]
categories = []
externalLink = ""
series = []
+++

## Project Overview
###  Game Description
A third-person action-adventure game featuring:
- Player movement, rolling, melee attacks
- Enemy AI (melee + ranged)
- Health bar & coin UI
- Pause / Victory / Failure menus
- Gate-unlock level system

<div style="text-align: center;">  
<img src="/haining-yu/images/little-adventure.png" alt="UI Hierarchy" width="600">
</div> 

### Core Scripts

| Module | Script | Purpose |
|---------|---------|----------|
| Player Control | `Player.cs` | Movement, attack, roll, hurt, death, UI updates |
| Player VFX | `PlayerVFXManage.cs` | Footsteps, attack slash effects, healing VFX |
| Enemy Base AI | `EnemyBase.cs`, `Enemy1.cs`, `Enemy2.cs` | Common enemy logic, melee & ranged behaviors |
| Damage System | `DamageCaster.cs` | Handles collision-based damage |
| Health Items | `Medical.cs` | Heal the player on pickup |
| Gates | `Gate.cs` | Open / close animation control |
| Game Manager | `GameManage.cs` | Pause, win/lose states, UI updates |
| Enemy Spawner | `Spawner.cs` | Wave spawn control and door unlock logic |
| Bullet | `Bullet.cs` | Projectile movement and collision |
| Enemy VFX | `enemyVFXmanage.cs` | Enemy footstep and attack particles |
| Main Menu | `MainMenuController.cs` | Handles Start / Quit buttons |

## Health Bar & UI System

### Mechanism
Health bar (`Slider`) value is updated through:
```csharp
_healthslider.value = Player.Instance.hp / Player.Instance.maxHp;
```
Whenever the player is hurt, healed, or dies,
GameManage.UpdateHealth() refreshes the UI.

### UI Hierarchy
<div style="text-align: center;">  
<img src="/haining-yu/images/canvas.png" alt="UI Hierarchy" width="600">
</div> 

## Mobile Input Adaptation (for iOS)
### Mobile Input Bridge
MobileInputAdapter.cs
```csharp
public class MobileInputAdapter : MonoBehaviour {
    public static MobileInputAdapter I;
    public Vector2 move;
    public bool attackPressed, rollPressed;
    void Awake() => I = this;
    public void OnJoystick(Vector2 v) => move = v;
    public void OnAttackDown() => attackPressed = true;
    public void OnRollDown() => rollPressed = true;
    void LateUpdate() { attackPressed = false; rollPressed = false; }
}
```
Attach this to a persistent object named MobileInput.

### On-Screen Controls

Under **Canvas** create:

| UI Element | Description | Event Binding |
|-------------|--------------|----------------|
| `JoystickBG` + `JoystickHandle` | Virtual stick with `SimpleJoystick.cs` | `OnValueChanged → MobileInputAdapter.OnJoystick(Vector2)` |
| `Button_Attack` | Attack button | `OnClick → MobileInputAdapter.OnAttackDown()` |
| `Button_Roll` | Roll button | `OnClick → MobileInputAdapter.OnRollDown()` |

### Updated Input in Player.cs
```csharp
if UNITY_IOS || UNITY_ANDROID
var a = MobileInputAdapter.I;
horizontalInput = a.move.x;
verticalInput   = a.move.y;
if (a.attackPressed) _anim.SetTrigger("Attack");
if (a.rollPressed && canRoll) {
    canRoll = false; isRolling = true;
    _anim.SetTrigger("Roll");
}
else
// Keyboard controls (for PC)
horizontalInput = Input.GetAxisRaw("Horizontal");
verticalInput   = Input.GetAxisRaw("Vertical");
if (Input.GetMouseButtonDown(0)) _anim.SetTrigger("Attack");
if (Input.GetKeyDown(KeyCode.Space) && canRoll) {
    canRoll = false; isRolling = true;
    _anim.SetTrigger("Roll");
}
endif
```

## iOS Build & Deployment Process

### Environment Setup

| Requirement | Description |
|--------------|-------------|
| **macOS + Xcode** | Mandatory for iOS builds |
| **Apple ID** | Free ID for testing / Paid for App Store |
| **Unity iOS Build Support** | Add via Unity Hub → Modules |
| **iPhone / iPad + USB Cable** | For deployment and testing |


### Build Settings

1. Open Unity → **File → Build Settings**  
2. Select **iOS** → Click **Switch Platform**  
3. Click **Player Settings**

| Section | Option | Recommended Value |
|----------|---------|-------------------|
| **Identification** | Bundle Identifier | `com.haining.knightadventure` |
|  | Company Name | HainingStudio |
|  | Product Name | KnightAdventure |
| **Configuration** | Scripting Backend | IL2CPP |
|  | Architecture | ARM64 |
|  | Target SDK | Device SDK |
| **Resolution & Presentation** | Orientation | Landscape Left |
| **Graphics** | Graphics API | Metal (default) |
| **Other Settings** | Multithreaded Rendering | Enabled |


### Build Process

1. In **Build Settings**, click **Build**  
2. Choose an export folder (e.g., `/Desktop/iOSBuild/`)  
3. Wait until Unity generates `Unity-iPhone.xcodeproj`  
4. Open the generated project in Xcode


### Xcode Configuration

1. Open `Unity-iPhone.xcodeproj` in **Xcode**  
2. Select the project → **General** tab  

| Setting | Value |
|----------|--------|
| **Team** | Your Apple ID / Development Team |
| **Signing Certificate** | Automatically Manage Signing |
| **Deployment Target** | iOS 16.0 or higher |
| **Architecture** | ARM64 |

3. Connect your iPhone via USB  
4. Select your device from Xcode’s top bar  
5. Click ▶️ **Run** to build and install the app

### First-Launch Verification

If the app shows:  
> “Unable to verify ‘Apple Development…’ App”

Follow these steps:

1. Ensure the iPhone is connected to the Internet  
2. Go to **Settings → General → VPN & Device Management**  
3. Tap your Apple ID → **Trust This Developer**  
4. Reopen the app on your device  

**Now Enjoy My Game - Little Adventure**
<video width="720" controls>
  <source src="/haining-yu/images/adventure.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>