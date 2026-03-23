# Visual Verifier — FANUC TP Program Explained

This document breaks down the FANUC Teach Pendant (TP) program line by line, explaining what each section does and why.

---

## 🔧 How the Program is Structured

The program is divided into **labeled sections (LBL)** that the robot jumps between depending on what the camera detects. Think of each label as a chapter — the robot reads the result of a vision scan and jumps to the right chapter accordingly.

| Label | Purpose |
|---|---|
| LBL[1] | Start — scan for Object 1 |
| LBL[2] | Draw square (house base) |
| LBL[3] | Scan for Object 2 |
| LBL[4] | Draw triangle (roof) |
| LBL[5] | Scan for Object 3 |
| LBL[6] | Draw door |
| LBL[7] | Scan for complete house |
| LBL[8] | Draw checkmark ✓ |
| LBL[9] | Draw X mark ✗ |
| LBL[10] | Return to home position |

---

## 📋 Line-by-Line Breakdown

---

### 🤖 Gripper Setup (Lines 1–3)
```
1:  RO[3: GRIPPER OPEN] = ON
2:  RO[4: GRIPPER CLOSE] = ON
3:L @P[1] 100mm/sec FINE
```
- **Lines 1–2:** The robot opens and closes its gripper to grip the marker used for drawing
- **Line 3:** The robot moves to **P[1]** — the home/camera capture point where it can see all objects. `FINE` means it stops precisely at that exact position before doing anything else

---

### 🔍 LBL[1] — Search for Object 1 (Lines 4–8)
```
4:  LBL[1]
5:  VISION RUN_FIND 'LNS_OBJECT1'
6:  VISION GET_PASSFAIL 'LNS_OBJECT1' R[20]
7:  IF R[20]=1, JMP LBL[2]
8:  IF R[20]=2, JMP LBL[3]
```
- **Line 5:** Runs the vision program named `LNS_OBJECT1` — the camera scans the workspace for Object 1
- **Line 6:** Retrieves the pass/fail result and stores it in register **R[20]**
  - `R[20] = 1` → Object was **found** ✅
  - `R[20] = 2` → Object was **not found** ❌
- **Line 7:** If found → jump to LBL[2] to draw the square
- **Line 8:** If not found → jump to LBL[3] to search for Object 2 instead

---

### ✏️ LBL[2] — Draw Square / House Base (Lines 9–17)
```
9:  LBL[2]
10: L P[2] 100mm/sec FINE
11: L P[3] 100mm/sec FINE
12: L P[4] 100mm/sec FINE
13: L P[5] 100mm/sec FINE
14: L P[6] 100mm/sec FINE
15: L P[3] 100mm/sec FINE
16: L P[2] 100mm/sec FINE
17: JMP LBL[3]
```
- **Lines 10–16:** The robot moves through **7 taught points** (P[2] through P[6] and back) in a sequence that traces out a **square** on the drawing surface — this forms the base/exterior of the house
- Each `L` means a **linear move** — the robot travels in a straight line between points, which is essential for drawing clean, precise shapes
- **Line 17:** Once the square is drawn, jump to LBL[3] to check for the next object

---

### 🔍 LBL[3] — Search for Object 2 (Lines 18–23)
```
18: LBL[3]
19: L @P[1] 100mm/sec FINE
20: VISION RUN_FIND 'LNS_OBJECT2'
21: VISION GET_PASSFAIL 'LNS_OBJECT2' R[21]
22: IF R[21]=1, JMP LBL[4]
23: IF R[21]=2, JMP LBL[5]
```
- **Line 19:** Robot returns to P[1] (camera capture point) to get a clear view
- **Lines 20–21:** Runs the `LNS_OBJECT2` vision program and stores the result in **R[21]**
- **Line 22:** If Object 2 found → jump to LBL[4] to draw the triangle (roof)
- **Line 23:** If not found → jump to LBL[5] to search for Object 3

---

### ✏️ LBL[4] — Draw Triangle / Roof (Lines 24–31)
```
24: LBL[4]
25: L P[2] 100mm/sec FINE
26: L P[3] 100mm/sec FINE
27: L P[12] 100mm/sec FINE
28: L P[4] 100mm/sec FINE
29: L P[3] 100mm/sec FINE
30: L P[2] 100mm/sec FINE
31: JMP LBL[5]
```
- **Lines 25–30:** The robot moves through **6 taught points** that trace a **triangle** shape — the roof of the house
- Notice **P[12]** is the peak point of the triangle (the tip of the roof)
- **Line 31:** Once the roof is drawn, jump to LBL[5] to check for Object 3

---

### 🔍 LBL[5] — Search for Object 3 (Lines 32–37)
```
32: LBL[5]
33: L @P[1] 100mm/sec FINE
34: VISION RUN_FIND 'LNS_OBJECT3'
35: VISION GET_PASSFAIL 'LNS_OBJECT3' R[22]
36: IF R[22]=1, JMP LBL[6]
37: IF R[22]=2, JMP LBL[7]
```
- **Line 33:** Returns to P[1] camera capture point
- **Lines 34–35:** Runs `LNS_OBJECT3` vision program, result stored in **R[22]**
- **Line 36:** If Object 3 found → jump to LBL[6] to draw the door
- **Line 37:** If not found → jump to LBL[7] to verify the house as-is

---

### ✏️ LBL[6] — Draw Door (Lines 38–44)
```
38: LBL[6]
39: L P[17] 100mm/sec FINE
40: L P[18] 100mm/sec FINE
41: L P[19] 100mm/sec FINE
42: L P[20] 100mm/sec FINE
43: L P[21] 100mm/sec FINE
44: L P[22] 100mm/sec FINE
```
- **Lines 39–44:** The robot moves through **6 taught points** tracing the **door** shape at the base of the house
- This section has no `JMP` — it naturally falls through to LBL[7] below

---

### 🔍 LBL[7] — Scan for Complete House (Lines 45–50)
```
45: LBL[7]
46: L P[23] 100mm/sec FINE
47: VISION RUN_FIND 'LNS_HOUSE'
48: VISION GET_PASSFAIL 'LNS_HOUSE' R[23]
49: IF R[23]=1, JMP LBL[8]
50: IF R[23]=2, JMP LBL[9]
```
- **Line 46:** Moves to P[23] — a position where the camera can view the **entire drawing**
- **Lines 47–48:** Runs `LNS_HOUSE` — a separate vision profile taught to recognize a complete 2D house. Result stored in **R[23]**
- **Line 49:** If house recognized → jump to LBL[8] to draw checkmark ✓
- **Line 50:** If house not recognized → jump to LBL[9] to draw X ✗

---

### ✅ LBL[8] — Draw Checkmark (Lines 51–57)
```
51: LBL[8]
52: L P[24] 100mm/sec FINE
53: L P[25] 100mm/sec FINE
54: L P[26] 100mm/sec FINE
55: L P[27] 100mm/sec FINE
56: L P[28] 100mm/sec FINE
57: JMP LBL[10]
```
- **Lines 52–56:** The robot moves through **5 taught points** tracing a **checkmark ✓** below the completed house drawing
- **Line 57:** Jumps to LBL[10] to end the program

---

### ❌ LBL[9] — Draw X Mark (Lines 58–65)
```
58: LBL[9]
59: L P[7] 100mm/sec FINE
60: L P[9] 100mm/sec FINE
61: L P[10] 100mm/sec FINE
62: L P[11] 100mm/sec FINE
63: L P[13] 100mm/sec FINE
64: L P[8] 100mm/sec FINE
65: L P[14] 100mm/sec FINE
```
- **Lines 59–65:** The robot moves through **7 taught points** tracing an **X ✗** below the incomplete drawing
- The X requires more points than the checkmark because it has two crossing diagonal strokes

---

### 🏠 LBL[10] — Return Home (Lines 66–67)
```
66: LBL[10]
67: L @P[1] 100mm/sec FINE
[End]
```
- **Line 67:** Robot returns to **P[1]** — the home/camera position — and the program ends cleanly

---

## 🔑 Key Concepts Used

| Concept | Explanation |
|---|---|
| `VISION RUN_FIND` | Tells the camera to actively search for a taught object profile |
| `VISION GET_PASSFAIL` | Retrieves whether the object was found (1) or not (2) and stores in a register |
| `R[n]` Registers | Variables that store vision results — each object has its own register (R[20], R[21], R[22], R[23]) |
| `IF ... JMP LBL` | Conditional logic — the robot makes decisions based on what the camera sees |
| `L` (Linear Move) | Robot moves in a straight line — critical for accurate drawing |
| `FINE` | Robot stops precisely at the point before continuing — no overshooting |
| `@P[1]` | The `@` symbol means the robot uses the camera-adjusted position of P[1] |
| `LBL` / `JMP` | Labels and jumps — how the program navigates between sections |

---

## 🔄 Full Program Flow Diagram

```
START
  │
  ▼
Grip marker → Move to P[1]
  │
  ▼
Scan for Object 1 (LNS_OBJECT1)
  ├─ FOUND (R[20]=1)  → Draw Square  ──────────────────┐
  └─ NOT FOUND (R[20]=2)                               │
       │                                               │
       ▼                                               ▼
  Scan for Object 2 (LNS_OBJECT2)          Scan for Object 2 (LNS_OBJECT2)
  ├─ FOUND (R[21]=1)  → Draw Triangle ─────────────────┤
  └─ NOT FOUND (R[21]=2)                               │
       │                                               │
       ▼                                               ▼
  Scan for Object 3 (LNS_OBJECT3)          Scan for Object 3 (LNS_OBJECT3)
  ├─ FOUND (R[22]=1)  → Draw Door ──────────────────── ┤
  └─ NOT FOUND (R[22]=2)                               │
       │                                               │
       └───────────────────────────────────────────────┘
                          │
                          ▼
              Scan for Complete House (LNS_HOUSE)
              ├─ FOUND (R[23]=1)    → Draw ✓ Checkmark
              └─ NOT FOUND (R[23]=2) → Draw ✗ X Mark
                          │
                          ▼
                   Return to P[1]
                        END
```
