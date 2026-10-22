# 02 Frontend

## Every Part the Visitor Sees (Each on Its Own)

### 1. Machine Header Bar & Status Light
- **What it shows (exact words):**
  - Main title: **Coffee Order Board**
  - Power badge when idle/off: **Standby — Place an order to start machine**
  - Power badge when active: **Machine Active — Shop Open**
  - Fault indicator light when normal: **Status: Normal** (next to a solid green circle)
  - Fault indicator light when broken: **Status: Machine Needs Reset** (next to a flashing red circle)
  - Reset button: **Reset Machine**
  - Simulate fault button (for testing): **Simulate Fault**
  - Idle timer button (for testing): **Fast-Forward 60 Mins Idle**
- **What the visitor can do with it:**
  - View whether the machine is in standby, active, or needing a reset.
  - Click **Reset Machine** to clear any fault and return the entire screen to its initial blank state.
  - Click **Simulate Fault** to trigger the machine fault state, or **Fast-Forward 60 Mins Idle** to trigger the 1-hour no-business standby state.

---

### 2. Order Intake Panel (Customer & Counter Input)
- **What it shows (exact words):**
  - Section heading: **Place an Order**
  - Text field label: **Customer Name** with placeholder text **Enter your name (1-30 chars)**
  - Dropdown label: **Coffee Order** with options: **Espresso**, **Latte**, **Cappuccino**, **Americano**, **Oat Flat White**, **Iced Mocha**
  - Channel selector label: **Order Channel** with two radio choices: **In-Person** and **Online**
  - Submit button: **Place Order**
- **What the visitor can do with it:**
  - Type a customer name into the **Customer Name** box.
  - Select 1 coffee drink from the **Coffee Order** list.
  - Choose either **In-Person** or **Online**.
  - Click **Place Order** to wake the machine (if in standby) and add the order to the **Placed** column.

---

### 3. Four-Column Order Board
- **What it shows (exact words):**
  - Four side-by-side columns with the exact subheadings:
    1. **Placed**
    2. **Making**
    3. **Ready**
    4. **Already Collected**
  - Beneath each subheading, the column area is completely blank when no orders are in that stage (no placeholder text and no ticket numbers).
- **What the visitor can do with it:**
  - Read the live status of every order from left to right.
  - Locate their order by matching their **Customer Name**, **Coffee Order**, and **Placed at** timestamp in chronological order from top (earliest) to bottom (latest).

---

### 4. Order Card (Inside the Columns)
- **What it shows (exact words):**
  - Customer and drink header: **[Customer Name] — [Coffee Order]**
  - Channel tag: **In-Person** or **Online**
  - Placement time line: **Placed at [HH:MM]**
  - When in **Making**:
    - Elapsed timer line: **Making time: [MM:SS]**
    - When delayed (10 minutes or more): **DELAYED (10+ mins) — Barista Reminder: Please prioritize this order!**
    - Action button: **Finish & Print QR (Move to Ready)**
    - Test timer button: **+10 Mins Making Time**
  - When in **Placed**:
    - Action button: **Start Making**
  - When in **Ready**:
    - Printed box label badge: **Box Label QR: [8-character QR Code] — Ready for Scan**
  - When in **Already Collected**:
    - Completion badge: **Complete / Picked Up at [HH:MM]**
- **What the visitor can do with it:**
  - In **Placed**, the barista clicks **Start Making** to move the card 1 step forward to **Making**.
  - In **Making**, the barista clicks **Finish & Print QR (Move to Ready)** to complete the drink, print the parcel QR label, and move the card 1 step forward to **Ready**; or clicks **+10 Mins Making Time** to add 10 minutes to the preparation timer.
  - In **Ready** and **Already Collected**, visitors cannot click any stage-skip button on the card; moving from **Ready** to **Already Collected** happens only through the QR Pick-Up Scanner.

---

### 5. Automated QR Pick-Up Scanner Station
- **What it shows (exact words):**
  - Section heading: **Automated QR Pick-Up Machine**
  - Instruction text: **Scan your app QR code to verify items and drop your coffee parcel.**
  - Customer pass selector label: **Customer App QR Passes** (shows clickable pass buttons for active orders in the format **Scan QR: [8-character QR Code] ([Customer Name] - [Coffee Order])** plus a manual input box with placeholder **Enter QR code** and button **Scan QR Code**)
  - Dispenser chute status line when idle: **Parcel Chute: Waiting for QR scan**
  - Dispenser chute status line when dropping: **Parcel Chute: Verified [Coffee Order] for [Customer Name] — Dropping parcel now!**
- **What the visitor can do with it:**
  - Click a customer's **Scan QR** button or enter a QR code and click **Scan QR Code** to scan at the pickup station.

---

### 6. Notification & Alert Banner Area
- **What it shows (exact words):**
  - Hidden when there are no alerts.
  - Customer 10-minute delay message: **Apology to [Customer Name]: Your [Coffee Order] has been in Making for 10 minutes. We are finishing it right now!**
  - Barista 10-minute reminder message: **Barista Reminder: [Customer Name]'s [Coffee Order] has exceeded 10 minutes in Making.**
  - Unclaimed 30-minute ready alert: **Pickup Reminder: [Customer Name]'s [Coffee Order] has been waiting in Ready for 30 minutes.**
  - Error message banner: Displays exact error text when an invalid action is attempted (detailed in Section 05).
- **What the visitor can do with it:**
  - Read live status apologies, barista reminders, and error messages, or click **Dismiss** on informational banners.

---

## The Screen Before Anyone Does Anything

Before anyone places an order or clicks any button:
1. The **Machine Header Bar** displays **Coffee Order Board**, the power badge reads **Standby — Place an order to start machine**, and the status light shows a green circle with **Status: Normal**.
2. The **Four-Column Order Board** displays the 4 subheadings—**Placed**, **Making**, **Ready**, and **Already Collected**—and the space beneath all 4 subheadings is completely blank, with 0 cards and 0 numbers.
3. The **Order Intake Panel** shows an empty **Customer Name** box, **Espresso** selected by default in **Coffee Order**, **In-Person** selected in **Order Channel**, and the **Place Order** button enabled.
4. The **Automated QR Pick-Up Machine** panel shows **Parcel Chute: Waiting for QR scan** and no active QR passes.
5. The **Notification & Alert Banner Area** is completely empty.

---

## What Changes After Each Action

1. **Visitor types a name, chooses a coffee and channel, and clicks "Place Order":**
   - If the power badge read **Standby — Place an order to start machine**, it immediately changes to **Machine Active — Shop Open**.
   - The **Customer Name** input box clears back to empty.
   - A new order card appears at the bottom of the **Placed** column showing **[Customer Name] — [Coffee Order]**, the channel tag (**In-Person** or **Online**), **Placed at [HH:MM]**, and the **Start Making** button.

2. **Barista clicks "Start Making" on a card in the "Placed" column:**
   - The card disappears from the **Placed** column and appears in the **Making** column.
   - The **Start Making** button is replaced by the live timer **Making time: 00:00**, the button **Finish & Print QR (Move to Ready)**, and the test button **+10 Mins Making Time**.

3. **Order stays in "Making" for 10 minutes (or visitor clicks "+10 Mins Making Time"):**
   - The card's timer updates to **Making time: 10:00** (or higher) and displays the red badge **DELAYED (10+ mins) — Barista Reminder: Please prioritize this order!**.
   - Two banners appear in the **Notification & Alert Banner Area**:
     - **Apology to [Customer Name]: Your [Coffee Order] has been in Making for 10 minutes. We are finishing it right now!**
     - **Barista Reminder: [Customer Name]'s [Coffee Order] has exceeded 10 minutes in Making.**

4. **Barista clicks "Finish & Print QR (Move to Ready)" on a card in the "Making" column:**
   - The card disappears from the **Making** column and appears in the **Ready** column.
   - If a 10-minute delay alert was showing for that order, it clears.
   - The card now displays **Box Label QR: [8-character QR Code] — Ready for Scan**.
   - A matching button **Scan QR: [8-character QR Code] ([Customer Name] - [Coffee Order])** appears in the **Automated QR Pick-Up Machine** section.

5. **Customer scans a matching QR code in the "Automated QR Pick-Up Machine" section:**
   - The dispenser chute status line changes to **Parcel Chute: Verified [Coffee Order] for [Customer Name] — Dropping parcel now!** for 2 seconds, then returns to **Parcel Chute: Waiting for QR scan**.
   - The order card moves from the **Ready** column to the **Already Collected** column.
   - The QR scan button for that order is removed from the active scanner list.
   - On the card in **Already Collected**, the QR label badge is replaced by **Complete / Picked Up at [HH:MM]**.

6. **Visitor clicks "Simulate Fault" (or a hardware fault triggers):**
   - The header status light changes from green (**Status: Normal**) to flashing red (**Status: Machine Needs Reset**).
   - An error banner appears reading **Machine Error: Hardware or dispenser fault detected. Automated pickup is paused. Please press Reset Machine.**
   - All stage transition buttons and QR scan buttons are disabled until the machine is reset.

7. **Visitor clicks "Reset Machine":**
   - The flashing red light returns to solid green (**Status: Normal**).
   - The power badge returns to **Standby — Place an order to start machine**.
   - All error banners, notifications, QR passes, and order cards across all 4 columns are cleared, returning the board to the exact blank starting screen.

