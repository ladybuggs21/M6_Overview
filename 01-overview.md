# 01 Overview

All ten questions about the Coffee Order Board are answered below, with explicit numbers for every rule, limit, and timing threshold.

1. **What is this system and what is its main purpose?**
   It is a 1-screen automated Coffee Order Board and QR parcel dispenser that tracks up to 20 concurrent coffee orders across 4 sequential columns—**Placed**, **Making**, **Ready**, and **Already Collected**—and dispenses completed drinks in 2 seconds via a scanned QR code without allowing any order to skip 1 single step.

2. **Who uses the system and how many roles interact with it?**
   There are 2 visitor roles sharing 1 interface with 0 accounts or logins required: **Customers** (who place 1 order at a time and scan 1 QR code to collect their parcel) and **Baristas** (who advance orders 1 stage at a time and can press 1 reset button if a fault occurs).

3. **When does the machine turn on and when does it turn off?**
   The machine wakes from standby in less than 1 second as soon as at least 1 in-person or online order is placed, stays active throughout open business hours, and automatically powers down into standby mode after 60 minutes (1 hour) of 0 new actions or active orders.

4. **How many stages must an order pass through, and can any stage be skipped?**
   Every order must pass through exactly 4 stages in strict order—Stage 1 (**Placed**), Stage 2 (**Making**), Stage 3 (**Ready**), and Stage 4 (**Already Collected**)—moving forward by exactly 1 step per transition, with 0 skipped steps and 0 backward moves permitted.

5. **How are orders identified and sorted on the board?**
   The board uses 0 ticket numbers; instead, each order card displays 3 data points—the customer's name (between 1 and 30 characters), 1 selected coffee drink, and the placement timestamp (formatted in 5 characters as HH:MM)—sorted chronologically from 1st (oldest timestamp at the top) to last (newest timestamp at the bottom).

6. **How long can an order stay in the "Making" stage before a delay alert happens?**
   Each order in Stage 2 (**Making**) is timed once every 1 second; if preparation reaches 10 minutes (600 seconds), the system immediately triggers 2 alerts at the same time: 1 apology notification banner for the customer and 1 red warning badge on the order card for the barista.

7. **How does the automated QR-code label printing and pick-up work?**
   When the barista moves an order from Stage 2 (**Making**) to Stage 3 (**Ready**), the machine generates 1 unique 8-character QR code, prints 1 box label, and displays 1 matching QR pass for the customer; scanning that 1 QR code checks the item, drops 1 parcel into the pickup bin in 2 seconds, and moves the card to Stage 4 (**Already Collected**).

8. **What is the maximum capacity of the board, and how fast does the screen update?**
   The board holds up to 20 active orders across Stages 1 to 3 and up to 50 completed orders in Stage 4 (**Already Collected**) per day, updating the screen within 100 milliseconds of any button press or QR scan.

9. **What happens in edge cases like two people acting at once, someone who never leaves, or nothing happening for an hour?**
   If 2 visitors submit orders within 1 millisecond of each other, both are queued 1 by 1 in arrival order; if 1 customer never leaves or fails to pick up a **Ready** order within 30 minutes, 1 unclaimed reminder alert appears while keeping the other 19 slots moving; and if 0 actions occur for 60 minutes (1 hour), the screen dims into standby mode.

10. **What happens if something goes wrong with the machine, and how is it fixed?**
    If 1 or more hardware or state faults occur (such as a label printer jam or parcel drop jam), 1 status indicator light switches from green to flashing red, 100% of automated parcel drops pause, and pressing the 1 **Reset Machine** button clears all faults and resets the board to 0 orders in 1 second.

