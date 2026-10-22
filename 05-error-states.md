# 05 Error States

## Things That Can Go Wrong

### 1. Missing or Too-Long Customer Name
- **What triggers it:** A visitor clicks **Place Order** with an empty **Customer Name** (0 characters) or a name longer than 30 characters.
- **Exact message:** `Input Error: Please enter a customer name between 1 and 30 characters.`
- **Where it shows:** Inside the **Notification & Alert Banner Area** directly above the four-column board, and highlighted in red around the **Customer Name** input box.
- **What the system does meanwhile:** Rejects the new order submission, does not wake the machine if it is in standby, and keeps all existing orders and timers running normally.
- **How it gets back to normal:** The visitor types a valid name between 1 and 30 characters and clicks **Place Order** again (or clicks **Dismiss** on the error banner).

---

### 2. Attempting to Skip a Step (Out-of-Order Stage Transition)
- **What triggers it:** Any attempt to move an order from **Placed** directly to **Ready** or **Already Collected**, from **Making** directly to **Already Collected**, or backward to an earlier stage.
- **Exact message:** `Workflow Error: Orders must move one step at a time (Placed -> Making -> Ready -> Already Collected). Skipping a step is not allowed.`
- **Where it shows:** Inside the **Notification & Alert Banner Area** at the top of the board.
- **What the system does meanwhile:** Blocks the stage change completely and keeps the order card in its current column with its timer and data untouched.
- **How it gets back to normal:** The visitor uses the valid next-step action for that order's current column (such as clicking **Start Making** for **Placed**, **Finish & Print QR (Move to Ready)** for **Making**, or scanning the QR code for **Ready**).

---

### 3. Scanning an Invalid, Already-Used, or Not-Yet-Ready QR Code
- **What triggers it:** A customer scans a blank/unknown QR code, scans a QR code for an order still in **Placed** or **Making**, or rescans a QR code for an order already in **Already Collected**.
- **Exact message:** `Scan Error: QR code does not match any order in the Ready column. Steps cannot be skipped.`
- **Where it shows:** Inside the **Notification & Alert Banner Area** and on the **Parcel Chute** status line inside the **Automated QR Pick-Up Machine** panel.
- **What the system does meanwhile:** Keeps the parcel chute closed, does not drop any box, and leaves all order cards in their existing columns.
- **How it gets back to normal:** Automatically clears when the customer scans a valid QR code belonging to an order currently in the **Ready** column, or clicks **Dismiss**.

---

### 4. Order Taking Too Long in "Making" (10-Minute Delay)
- **What triggers it:** An order stays in the **Making** column for 10 minutes (600 seconds) or longer without the barista clicking **Finish & Print QR (Move to Ready)**.
- **Exact message:**
  - Customer banner: `Apology to [Customer Name]: Your [Coffee Order] has been in Making for 10 minutes. We are finishing it right now!`
  - Barista banner: `Barista Reminder: [Customer Name]'s [Coffee Order] has exceeded 10 minutes in Making.`
  - Card badge: `DELAYED (10+ mins) — Barista Reminder: Please prioritize this order!`
- **Where it shows:** Both apology and reminder messages appear in the **Notification & Alert Banner Area**, and the red delay badge appears directly on the delayed order card inside the **Making** column.
- **What the system does meanwhile:** Continues counting elapsed time on the order card while keeping all other orders and columns operating normally.
- **How it gets back to normal:** The barista finishes preparing the coffee and clicks **Finish & Print QR (Move to Ready)** on the delayed card, which moves the order to **Ready** and automatically removes the delay warning.

---

### 5. Board Capacity Reached (More Than 20 Active Orders)
- **What triggers it:** A visitor tries to place a 21st order while 20 active orders are already in **Placed**, **Making**, or **Ready**.
- **Exact message:** `Board Full: Maximum of 20 active orders reached. Please wait for an order to be collected.`
- **Where it shows:** Inside the **Notification & Alert Banner Area**.
- **What the system does meanwhile:** Holds the current 20 active orders safely in memory and continues advancing timers and QR pickups without accepting the 21st order.
- **How it gets back to normal:** As soon as at least 1 order in **Ready** is scanned and moves to **Already Collected** (dropping active orders to 19 or fewer), new orders can be placed immediately.

---

### 6. Machine Hardware or Dispenser Fault (Needs Reset)
- **What triggers it:** A QR printer jam, parcel chute jam, or pressing the **Simulate Fault** button.
- **Exact message:**
  - Header indicator light: `Status: Machine Needs Reset`
  - Error banner: `Machine Error: Hardware or dispenser fault detected. Automated pickup is paused. Please press Reset Machine.`
- **Where it shows:** The header indicator light turns flashing red next to **Status: Machine Needs Reset**, and the error banner spans across the top of the screen.
- **What the system does meanwhile:** Pauses 100% of automated parcel drops, disables order placement and stage transition buttons so no order is lost or mis-dispensed, and freezes active timers.
- **How it gets back to normal:** A staff member clicks the **Reset Machine** button in the header bar, which clears the fault, turns the indicator light back to solid green (**Status: Normal**), clears the board, and restores the blank standby starting state.

---

## Edge Cases

### Edge Case 1: Two People at Once
- **Scenario A — Two customers place an order at the exact same millisecond (in-person and online simultaneously):**
  - **What happens:** Both orders are processed sequentially by the intake queue without overwriting each other. If both have identical millisecond timestamps, the in-person order is placed 1st and the online order 2nd (or by arrival index), so both cards appear cleanly in the **Placed** column in deterministic order.
- **Scenario B — Two customers try to scan QR codes while the parcel chute is mid-drop:**
  - **What happens:** While the 1st parcel is taking 2 seconds to drop (`isChuteDropping = true`), a 2nd scan shows the message: `Chute Busy: Dispensing another parcel right now. Please wait 2 seconds and scan again.` in the **Notification & Alert Banner Area**. Once the 2-second drop finishes, the 2nd customer scans again and receives their parcel normally.

---

### Edge Case 2: Someone Who Never Leaves (Unclaimed Order in "Ready")
- **Scenario:** A customer places an order that reaches the **Ready** column, or stands near the screen without ever scanning their QR code to collect the parcel for 30 minutes (1800 seconds) or longer.
- **Exact message:** `Pickup Reminder: [Customer Name]'s [Coffee Order] has been waiting in Ready for 30 minutes.`
- **Where it shows:** Inside the **Notification & Alert Banner Area** and as an amber badge on that specific card in the **Ready** column.
- **What the system does meanwhile:** Keeps the unclaimed order inside its own slot in the **Ready** column without blocking the parcel chute or preventing other customers from placing, making, or scanning their own orders.
- **How it gets back to normal:** Either the customer finally scans their QR code (moving the order to **Already Collected**), or at the end of the day / during a reset the staff clicks **Reset Machine**.

---

### Edge Case 3: Nothing for an Hour (60 Minutes of Zero Business)
- **Scenario:** There are 0 active orders in **Placed**, **Making**, and **Ready**, and 60 minutes (3600 seconds) pass with 0 button clicks or new orders.
- **Exact message:**
  - Power badge changes to: `Standby — Place an order to start machine`
  - Info banner shows: `Standby Mode: No activity for 60 minutes. Screen powered down until the next order is placed.`
- **Where it shows:** In the **Machine Header Bar** and **Notification & Alert Banner Area**, while the 4 columns below clear out old **Already Collected** history so the board is completely blank beneath the 4 subheadings.
- **What the system does meanwhile:** Enters low-power standby mode with all 4 columns blank (**Placed**, **Making**, **Ready**, **Already Collected**) while continuing to listen for the next in-person or online order.
- **How it gets back to normal:** As soon as any customer places 1 new in-person or online order, the machine automatically wakes up within 1 second, changes the header badge to **Machine Active — Shop Open**, removes the standby message, and places the new order in the **Placed** column.

