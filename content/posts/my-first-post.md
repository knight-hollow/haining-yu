+++ 
draft = false
date = 2025-09-22T20:53:34+02:00
title = "Affordance, Gestalt Laws and Dark design pattern"
description = ""
slug = ""
authors = ["Haining"]
tags = ["HCI", "Affordance", "Gestalt Laws", "Dark design pattern", "Lecture 1 - 3"]
categories = []
externalLink = ""
series = []
+++

### Affordance
Good design:  

![Volume Button](/haining-yu/images/p1.jpg "Volume Button")

Description: The volume button on the side of the phone.  
Why it's good: The raised, elongated shape suggests a "press," and its placement aligns with the natural resting point of the finger when holding the phone.  

Bad design:  
![Switchs](/haining-yu/images/p2.jpg "Switchs")  
Description: A row of identical flat-pressure switches each controls a different light source. Users cannot determine which light a switch controls by looking at its state, forcing them to rely on memorization or trial and error.

Why it's bad: There's no clear "mapping" between the switch state (on/off) and the object it controls.

Improvement suggestion: Label the switch panel with text or a simple icon, or integrate a small LED indicator on the switch to indicate that the circuit is energized (even if the light isn't on).

### Gestalt Laws
![Web Cookie Banner](/haining-yu/images/p3.png "Web Cookie Banner")   
Description: Web Cookie Banner (Proximity Principle & Common Area Principle)

Why it's bad: The website's cookie consent pop-up window has a large and prominent "Agree to All" button, while the "Reject All" or "Manage Settings" options are placed far away in the form of light-colored text links, or even hidden in a secondary menu. This abuses the principle of proximity - users will think that the conspicuous large button is the primary action and ignore those seemingly irrelevant text links.

Improvement suggestion: Adhering to the principles of common area and proximity, the three buttons of "Agree to All", "Reject All", and "Manage Settings" are placed side by side in the same area with the same visual weight (size, color), clearly telling users that this is a set of options that require selection.

![Shelf Labels](/haining-yu/images/p6.png "Shelf Labels")   
Description: Supermarket Shelf Labels (Continuity Principle & Proximity Principle)

Why it’s bad: The shelf labels are not aligned in a straight line and are scattered at different heights, sometimes closer to the products of the next row. This violates the principle of continuity and proximity — customers may mistakenly associate a price tag with the wrong product because their visual flow is interrupted and the label appears closer to another item.

Improvement suggestion: Arrange all shelf labels in a continuous, straight line directly under the corresponding products. Keep equal spacing between labels and ensure consistent alignment, so that customers can clearly perceive which price belongs to which product.

### Dark design pattern
![Shake-to-Open Ad](/haining-yu/images/p4.png "Shake-to-Open Ad")  
Description: Shake-to-Open Ad. When you open an app, this ad will appear. Once the user moves the phone slightly, it will automatically jump to the app in the ad.

Why it's bad: It hijacks the device’s motion sensor to interpret unintended minor movements as intentional “shaking,” triggering forced redirects to ads. This violates user intent, removes control, and relies on deception and forced action.

Improvement suggestion: Include an option in app settings: “Enable shake-to-interact,” turned OFF by default.

![Dorm Application Web](/haining-yu/images/p5.png "Dorm Application Web")  
Description: Non-Cancellable Dorm Application. When a user wants to cancel a previous dormitory application, but finds that there is no corresponding cancellation channel, he must send an email to the dormitory administrator to cancel.

Why it's bad: This is a "Roach Motel" dark pattern: users can easily apply for a dormitory but are trapped with no option to cancel or withdraw, even when plans change. It removes user autonomy and creates a forced commitment.

Improvement suggestion: Add a "Withdraw Application" button in the user's profile or application status page.

