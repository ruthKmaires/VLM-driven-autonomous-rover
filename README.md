# VLM-Driven Autonomous Rover with Real-Time Decision Making

An autonomous rover that uses a Vision-Language Model (VLM) to interpret live camera input and make real-time navigation decisions — detecting obstacles and issuing motor commands without hardcoded rule-based logic.

## Overview

This project combines a VLM-based perception/decision pipeline with embedded motor control to build a rover capable of basic autonomous obstacle avoidance. The VLM analyzes camera frames and returns a structured navigation command, which is executed on an ESP32-based motor controller.

## Architecture
[ESP32-CAM] → captures frame
↓
[VLM Server] → POST /analyze → returns JSON command
↓
[Integration layer (Python)] → translates command
↓
[ESP32-WROOM-32] → drives motors via L298N
## Team
This repository primary focus
- **Integration & data** — GitHub repo, dataset handling, connecting VLM output to motor control

## Tech Stack

- **VLM:** LLaVA-1.5-7B (or similar Colab-compatible model)
- **Compute:** Google Colab (free-tier GPU) for inference; team laptops (CPU-only) for development
- **Datasets:** BDD100K, nuScenes-mini (for baseline inference testing)
- **Microcontrollers:** ESP32-CAM (image capture), ESP32-WROOM-32 (motor control)
- **Motor driver:** L298N dual H-bridge
- **Server:** Flask-based VLM inference server

## VLM Server API

**Endpoint:** `POST /analyze`
**Input:** `multipart/form-data`, image file under key `"image"`
**Response:**
```json
{
  "prompt": "Find the obstacle",
  "answer": "Tv remote.",
  "description": "Two cats are sleeping on a pink color cloth.",
  "obstacle_detected": true,
  "obstacle_position": "center",
  "command": "stop",
  "inference_seconds": 13.6
}
Command values:
Command --Meaning
forward --Path clear, proceed
stop    --obstacle directly ahead
turn_right --Obstacle on left → steer right
turn_left --Obstacle on right → steer left
speed low --Obstacle position unclear, slow down
Hardware Setup
L298N Pin|ESP32 GPIO|Purpose
IN1|26|Left motor direction 1
IN2|27|Left motor direction 2
ENA|25|Left motor speed (PWM)
IN3|14|Right motor direction 1
IN4|12|Right motor direction 2
ENB|33|Right motor speed (PWM)
Motors are powered via a separate battery supply through the L298N (not from the ESP32); ESP32 and L298N share a common ground.
