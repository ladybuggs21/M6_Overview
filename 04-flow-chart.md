# 04 Flow Chart

Every step of the Coffee Order Board is mapped below using a **Start State**, **Action Boxes** (`[Box]`), and **Question Diamonds** (`<Diamond>`). Every single diamond contains an explicit number, has both a **Yes** and a **No** path out of it, and every path has a way back to the **Start State**.

---

## Visual Flow Map (Boxes and Diamonds)

**START STATE:**
**( START: Machine in Standby — 4 Columns Blank, 0 Active Orders, 0 Faults )**
↓
- **<Diamond 1: Has at least 1 machine fault occurred (Fault Count >= 1)?>**
  - **Yes** → **[Box A: Turn Status Light to Flashing Red ("Status: Machine Needs Reset") and pause 100% of parcel drops]** → **<Diamond 2: Did a visitor press the 1 "Reset Machine" button?>**
    - **Yes** → **[Box B: Clear all faults, wipe all orders to 0, and reset timers to 0]** → Return to **START STATE**
    - **No** → **[Box C: Keep board locked and show fault banner]** → Loop back to **<Diamond 2>**
  - **No** → Go to **<Diamond 3>**

- **<Diamond 3: Was at least 1 new order submitted via the Order Intake Panel?>**
  - **Yes** → **<Diamond 4: Is the Customer Name length between 1 and 30 characters?>**
    - **No** → **[Box D: Show error banner "Input Error: Please enter a customer name between 1 and 30 characters."]** → Go to **<Diamond 11>**
    - **Yes** → **<Diamond 5: Are there fewer than 20 active orders currently in Stages 1 to 3?>**
      - **No** → **[Box E: Show error banner "Board Full: Maximum of 20 active orders reached. Please wait for an order to be collected."]** → Go to **<Diamond 11>**
      - **Yes** → **[Box F: Wake machine to Active (if in Standby), stamp HH:MM placement time, and add card to Stage 1 (Placed) in chronological order]** → Go to **<Diamond 6>**
  - **No** → Go to **<Diamond 6>**

- **<Diamond 6: Did the barista click "Start Making" on 1 order currently in Stage 1 (Placed)?>**
  - **Yes** → **[Box G: Move order from Stage 1 (Placed) to Stage 2 (Making) and start its 1-second preparation timer at 00:00]** → Go to **<Diamond 7>**
  - **No** → Go to **<Diamond 7>**

- **<Diamond 7: Has any order stayed in Stage 2 (Making) for 10 minutes (600 seconds) or more without a delay alert?>**
  - **Yes** → **[Box H: Send 1 customer apology banner and show 1 red barista reminder badge on the delayed order card]** → Go to **<Diamond 8>**
  - **No** → Go to **<Diamond 8>**

- **<Diamond 8: Did the barista click "Finish & Print QR" on 1 order currently in Stage 2 (Making)?>**
  - **Yes** → **[Box I: Stop making timer, generate 1 unique 8-character QR code, print box label, and move order from Stage 2 (Making) to Stage 3 (Ready)]** → Go to **<Diamond 9>**
  - **No** → Go to **<Diamond 9>**

- **<Diamond 9: Did a customer scan 1 QR code at the Automated QR Pick-Up Machine?>**
  - **Yes** → **<Diamond 10: Does the scanned QR code match 1 order currently in Stage 3 (Ready)?>**
    - **No** → **[Box J: Reject scan and show error banner "Scan Error: QR code does not match any order in the Ready column. Steps cannot be skipped."]** → Go to **<Diamond 11>**
    - **Yes** → **[Box K: Verify items, drop 1 matching coffee parcel in 2 seconds, stamp HH:MM collection time, and move order from Stage 3 (Ready) to Stage 4 (Already Collected)]** → Go to **<Diamond 11>**
  - **No** → Go to **<Diamond 11>**

- **<Diamond 11: Has any order waited in Stage 3 (Ready) for 30 minutes (1800 seconds) or more unclaimed?>**
  - **Yes** → **[Box L: Show 1 pickup reminder banner for the unclaimed Ready order while keeping the board open]** → Go to **<Diamond 12>**
  - **No** → Go to **<Diamond 12>**

- **<Diamond 12: Have 60 minutes (3600 seconds) passed with 0 active orders and 0 visitor actions?>**
  - **Yes** → **[Box M: Clear completed day history and power down screen into Standby mode]** → Return to **START STATE**
  - **No** → **[Box N: Advance clock by 1 second and keep board active]** → Loop back to **<Diamond 1>** (which connects back to **START STATE** via reset or 60-minute standby)

---

## Summary Checklist of All 12 Question Diamonds

| Diamond # | Question Text (with Number) | Yes Path | No Path |
|---|---|---|---|
| **Diamond 1** | Has at least **1** machine fault occurred (Fault Count >= **1**)? | Go to **Box A** (Lock drops & turn light red) | Go to **Diamond 3** (Check for new order) |
| **Diamond 2** | Did a visitor press the **1** "Reset Machine" button? | Go to **Box B** → **START STATE** | Go to **Box C** → Loop to **Diamond 2** |
| **Diamond 3** | Was at least **1** new order submitted via the Order Intake Panel? | Go to **Diamond 4** (Check name length) | Go to **Diamond 6** (Check Start Making click) |
| **Diamond 4** | Is the Customer Name length between **1** and **30** characters? | Go to **Diamond 5** (Check 20-order limit) | Go to **Box D** (Show input error) → **Diamond 11** |
| **Diamond 5** | Are there fewer than **20** active orders currently in Stages **1** to **3**? | Go to **Box F** (Add to Stage 1 Placed) | Go to **Box E** (Show board full error) → **Diamond 11** |
| **Diamond 6** | Did the barista click "Start Making" on **1** order currently in Stage **1** (Placed)? | Go to **Box G** (Move to Stage 2 Making) | Go to **Diamond 7** (Check 10-minute timer) |
| **Diamond 7** | Has any order stayed in Stage **2** (Making) for **10** minutes (**600** seconds) or more? | Go to **Box H** (Send 2 delay alerts) | Go to **Diamond 8** (Check Finish & Print QR) |
| **Diamond 8** | Did the barista click "Finish & Print QR" on **1** order currently in Stage **2** (Making)? | Go to **Box I** (Print QR & move to Stage 3 Ready) | Go to **Diamond 9** (Check QR scan) |
| **Diamond 9** | Did a customer scan **1** QR code at the Automated QR Pick-Up Machine? | Go to **Diamond 10** (Verify Stage 3 match) | Go to **Diamond 11** (Check 30-minute unclaimed) |
| **Diamond 10** | Does the scanned QR code match **1** order currently in Stage **3** (Ready)? | Go to **Box K** (Drop parcel & move to Stage 4) | Go to **Box J** (Show scan error) → **Diamond 11** |
| **Diamond 11** | Has any order waited in Stage **3** (Ready) for **30** minutes (**1800** seconds) or more? | Go to **Box L** (Show 30-min pickup reminder) | Go to **Diamond 12** (Check 60-minute idle) |
| **Diamond 12** | Have **60** minutes (**3600** seconds) passed with **0** active orders and **0** visitor actions? | Go to **Box M** → **START STATE** | Go to **Box N** → **Diamond 1** |

