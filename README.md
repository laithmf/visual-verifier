[README.md](https://github.com/user-attachments/files/26172830/README.md)
# 🤖 Visual Verifier

> A FANUC robotics project using iRVision object detection to autonomously draw and verify a 2D house.

**Course:** Robotics Systems — Wayne State University  
**Professor:** Dr. Smoak  
**Team:** Laith Fakhruldin · Nahid Ahmed · Spencer Martinez  
**Date:** November 2023

---

## 📌 Project Overview

Visual Verifier is a teach pendant (TP) program developed for the **FANUC LR Mate 200iD4S** robot. It uses the **iRVision** camera system to detect physical objects placed in the robot's workspace and respond by drawing portions of a 2D house. Once drawing is complete, the robot checks its own work — marking a **✓ checkmark** if a complete house was drawn, or an **✗** if not.

This project demonstrates the intersection of machine vision, object recognition, and precise robotic motion in an automated quality-verification workflow.

---

## 🎯 How It Works

| Objects Detected | Action Taken |
|---|---|
| Object 1 (base) | Robot draws a **square** (house exterior) |
| Object 2 (roof) | Robot draws a **triangle** (roof) |
| Object 3 (door) | Robot draws the **door** |
| All 3 present | Complete house drawn → **✓ Checkmark** |
| Incomplete house | **✗ drawn** beneath the picture |

The robot scans for each object sequentially using `VISION GET_PASSFAIL` commands with `IF` statements and label-based jumps throughout the TP program.

---

## 🎬 Demo Videos

| Video | Description | Link |
|---|---|---|
| Demo 1 | All three objects present — complete house drawn | *(add YouTube link after upload)* |
| Demo 2 | Only one object present — partial drawing | *(add YouTube link after upload)* |
| Demo 3 | *(add description)* | *(add YouTube link after upload)* |

---

## 🛠️ Hardware & Software

- **Robot:** FANUC LR Mate 200iD4S
- **Vision System:** iRVision (built-in FANUC camera)
- **Programming Environment:** FANUC Teach Pendant (TP language)
- **Object Teaching:** iRVision training mask + emphasis area

---

## 📋 Program Logic (High-Level)

```
START
│
├─ Open/close gripper (insert marker)
│
├─ Move to vision capture point
│
├─ VISION GET_PASSFAIL → Search for Object 1
│   ├─ FOUND    → Draw square (house base) → jump to next check
│   └─ NOT FOUND → Search for Object 2
│       ├─ FOUND    → Draw triangle (roof) → jump to next check
│       └─ NOT FOUND → Search for Object 3 → draw door
│
├─ Move to vision capture point
│
├─ VISION GET_PASSFAIL → Search for complete house
│   ├─ FOUND    → Draw ✓ checkmark
│   └─ NOT FOUND → Draw ✗
│
END
```

---

## 🖥️ TP Program Summary

The robot is programmed using FANUC's Teach Pendant (TP) language. The program uses **iRVision profiles** and **conditional jumps** to navigate between drawing sections based on what the camera detects.

```
START
  │
  ▼
Grip marker → Move to camera point (P[1])
  │
  ▼
Scan for Object 1 ──── FOUND → Draw Square (house base)
  │
  ▼
Scan for Object 2 ──── FOUND → Draw Triangle (roof)
  │
  ▼
Scan for Object 3 ──── FOUND → Draw Door
  │
  ▼
Scan for complete house
  ├── FOUND     → Draw ✓ Checkmark
  └── NOT FOUND → Draw ✗ X Mark
  │
  ▼
Return home → END
```

Each vision scan stores a **pass (1) or fail (2)** result in a register, and `IF` statements direct the robot to the correct drawing routine. See [`CODE_EXPLAINED.md`](CODE_EXPLAINED.md) for a full line-by-line breakdown.

---

## 📂 Repository Structure

```
visual-verifier/
│
├── README.md                  ← You are here
├── CODE_EXPLAINED.md          ← Full line-by-line code breakdown
├── report/
│   └── Visual_Verifier.docx  ← Full final report
├── code/
│   └── Visual_Verifier_code.txt  ← Raw FANUC TP program
└── videos/
    ├── demo_all_objects.mp4
    ├── demo_two_objects.mp4
    └── demo_one_object.mp4
```

---

## 📚 References

- Helfrich, T. (2022, May 31). *Why Robotics And Artificial Intelligence Are The Future Of Mankind.* Forbes. https://www.forbes.com/sites/forbestechcouncil/2022/05/31/why-robotics-and-artificial-intelligence-are-the-future-of-mankind/

- Yasar, K., & Lutkevich, B. (2023, August 31). *Machine vision.* TechTarget Enterprise AI. https://www.techtarget.com/searchenterpriseai/definition/machine-vision-computer-vision

---

## 👥 Team

| Name | Role |
|---|---|
| Laith Fakhruldin | Team Member |
| Nahid Ahmed | Team Member |
| Spencer Martinez | Team Member |

---

*Wayne State University · Robotics Systems · Fall 2023*
