---
title: "Saving Time on Simulations: My OMNeT++ & Simu5G Position Generator"
date: 2025-08-22T10:33:07+01:00
description: "A custom-built Streamlit tool designed to automate UE positioning for OMNeT++ and Simu5G network simulations, saving valuable research time."
series:
  - "Network Simulation Tools"
author: "Kouros Zanbouri"
summary: "Manually configuring coordinates for numerous User Equipments (UEs) in OMNeT++ and Simu5G simulations is a tedious task. This post details the creation of a Python-based web tool using Streamlit that automates this process. The application allows for interactive, visual placement of UEs in static or mobile scenarios, generating the necessary .ini configuration code instantly to accelerate the simulation setup workflow."
tags: ["Python", "Streamlit", "OMNeT++", "Simu5G", "5G", "Network Simulation", "Automation"]
disableComments: true
---
As anyone who works with network simulators knows, a significant amount of time is spent on setup and configuration before you can even get to the interesting part—running the simulation itself. Recently, while working on a project with **Simu5G**, the 5G network simulator built on OMNeT++, I hit a familiar, tedious roadblock: positioning a large number of User Equipments (UEs).

## The Problem: A Sea of Coordinates

My goal was to simulate various scenarios with dozens, sometimes hundreds, of UEs interacting with a central gNodeB (the 5G base station). I needed them placed in specific patterns—sometimes static, sometimes mobile, often within a certain distance ring. The only way to do this out of the box was to manually calculate and write the `initialX` and `initialY` coordinates for every single UE in the `omnetpp.ini` file.

Doing this for 10 UEs is annoying. Doing it for 100 is a mind-numbing waste of time. I knew there had to be a better way. I wanted to focus on analyzing simulation results, not on being a human coordinate-entry machine. That's why I decided to build a simple tool to automate the entire process.

## The Solution: A Streamlit Web App 

I created the **OMNeT++ UE Position Generator**, a simple web application using Python's **Streamlit** library. My goal was to build something visual, interactive, and fast that could generate the exact `.ini` configuration I needed with just a few clicks.



Here’s what it does:

* **Interactive Controls**: A clean sidebar allows you to set all the key parameters: the gNB's coordinates, the total number of UEs, and the placement rules.
* **Flexible Placement**: You can choose between a **Static** deployment, where UEs are placed in a ring and don't move, or a **Mobility** deployment.
* **Mobility Models**: For mobile scenarios, you can select from common models like **Linear Mobility** (where UEs move in a straight line) or **Random Waypoint** (where they move randomly within a defined area).
* **Instant Visualization**: The app generates a 2D plot of the deployment, so you can see exactly where your gNB and UEs are. For linear mobility, it even draws arrows to show the UEs' initial direction of travel!
* **Code Generation**: The most important part! It produces a clean block of `.ini` configuration text that you can directly copy and paste into your OMNeT++ project.

## How It Works

The app is surprisingly simple under the hood. It uses `numpy` to handle the math, `matplotlib` to generate the plots, and of course, `streamlit` magically turns the whole Python script into an interactive web app.

### The Geometry of Placement

The core of the placement logic is based on simple geometry. To place a UE, the script doesn't think in terms of `X` and `Y` at first. Instead, it uses **polar coordinates**:
1.  It picks a random **distance** from the gNB (within the min/max you set).
2.  It picks a random **angle** (from 0 to 360 degrees).

With a distance and an angle, it then uses trigonometry (`cosine` and `sine`) to convert these polar coordinates into the final Cartesian (`X`, `Y`) coordinates for the `.ini` file.

The relationship between two points is defined by the Euclidean distance formula:

$D = \sqrt{(X_{UE} - X_{gNB})^2 + (Y_{UE} - Y_{gNB})^2}$

Where:
* $D$ is the distance between the UE and the gNB.
* $(X_{UE}, Y_{UE})$ are the coordinates of the User Equipment.
* $(X_{gNB}, Y_{gNB})$ are the coordinates of the gNodeB.

My tool simply works this logic in reverse to guarantee UEs are placed exactly where they need to be.

## Future Plans & Other Mobility Models

The tool currently supports three basic but very common scenarios. However, the INET framework for OMNeT++ is incredibly powerful and offers many more sophisticated mobility models that could be implemented. I may add these in the future, but I also wanted to share some references for anyone interested in exploring them further.

Some interesting models include:

* **GaussMarkovMobility**: A model where the speed and direction of a node change over time based on a Gauss-Markov stochastic process. It's great for creating more realistic, less predictable movement patterns.
* **MassMobility**: Simulates a scenario where a large group of nodes moves together towards a common destination, like a crowd moving at a concert.
* **CircleMobility**: A simple model where nodes move in a circular pattern around a central point.
* **TurtleMobility**: Allows you to define movement using a LOGO-style script with commands like "forward," "turn," etc.

You can find detailed documentation on these and many others in the official **[INET Framework Mobility Documentation](https://inet.omnetpp.org/docs/users-guide/ch-mobility.html)**.

This project started as a personal tool to solve a frustrating problem, but I hope it can be useful to other researchers in the OMNeT++ and Simu5G community. You can find the complete source code on GitHub at **[KourosZanbouri/OMNET-UE-Generator](https://github.com/KourosZanbouri/OMNET-UE-Generator)**. Feel free to check it out, use it in your own research, and contribute if you have ideas for improvement!