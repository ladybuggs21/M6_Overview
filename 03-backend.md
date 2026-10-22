# 03 Backend

Nobody logs in.

---

## What Runs Out of Sight, in Order

1. **Initial State Load (Step 1):**
   When the page opens, the system reads any saved board state from browser storage, or initializes the default blank standby state if none exists, and starts a background clock loop that ticks once every 1 second (1000 milliseconds).
2. **Order Intake & Machine Wake-Up (Step 2):**
   When a visitor submits an order, the system trims the customer name, checks that the name is between 1 and 30 characters, checks that the active order count (across Stages 1 to 3) is below 20, switches the machine power state to active if it was in standby, stamps the exact placement time, assigns Stage 1 (**Placed**), and appends the order to the chronological queue.
3. **Sequential Stage Guard (Step 3):**
   Whenever a stage transition is requested, the state validator checks two rules before changing anything: first, the machine fault state must be false; second, the requested destination stage must be exactly 1 step higher than the order's current stage (Stage 1 to Stage 2, Stage 2 to Stage 3, or Stage 3 to Stage 4). Any request that skips a step is rejected immediately.
4. **QR Code Generation & Label Dispatch (Step 4):**
   When an order moves from Stage 2 (**Making**) to Stage 3 (**Ready**), the system stops that order's preparation timer, generates a unique 8-character uppercase alphanumeric QR code (prefixed with `QR-`), attaches it to the order record as the printed box label, and registers the matching code in the active scanner lookup table.
5. **1-Second Background Timer & Delay Checker (Step 5):**
   Every 1 second, the background loop increments the elapsed preparation time for every order in Stage 2 (**Making**), increments the waiting time for every order in Stage 3 (**Ready**), and increments the shop idle counter if 0 active orders exist.
   - If an order in Stage 2 reaches 600 seconds (10 minutes) and has not yet sent a delay alert, the system dispatches both the customer apology message and the barista reminder message.
   - If an order in Stage 3 reaches 1800 seconds (30 minutes) without being scanned, the system dispatches the unclaimed order alert.
   - If 0 orders exist in Stages 1 to 3 and 3600 seconds (60 minutes) pass with 0 actions, the system switches the machine back to standby mode.
6. **QR Scan Verification & Parcel Drop Sequence (Step 6):**
   When a QR code is scanned at the pickup station, the system looks up the code, verifies that a matching order exists and is currently in Stage 3 (**Ready**), checks that the item matches the order record, triggers a 2-second parcel drop lock so only 1 parcel drops at a time, marks the order as Stage 4 (**Already Collected**), records the collection timestamp, and removes the QR code from active scanning.
7. **Fault Lockout & Full Reset Handler (Step 7):**
   If a machine fault occurs, the system sets the fault flag to true, halts all automated parcel drops and stage changes, and illuminates the reset indicator light. When **Reset Machine** is pressed, the system clears all orders, resets all timers and flags to their initial starting values, and returns the screen to standby.

---

## Each Message: Who Sends It and Who Receives It

| # | Message Name | Who Sends It | Who Receives It | Exact Message / Payload Sent |
|---|---|---|---|---|
| 1 | **New Order Request** | Order Intake Panel | Order Queue Manager | Customer Name, Coffee Order, Order Channel, and current timestamp |
| 2 | **Wake Machine Signal** | Order Queue Manager | Machine Power Controller | Command to switch machine power from **Standby** to **Active** |
| 3 | **Stage Advance Request** | Barista Card Control | Sequential State Guard | Order ID and target stage (Stage 2 **Making** or Stage 3 **Ready**) |
| 4 | **Print Box QR Signal** | Sequential State Guard | QR Label Printer & Customer Pass View | **Box Label QR: [8-character QR Code] — Ready for Scan** |
| 5 | **10-Minute Customer Apology** | Background Timer Service | Customer Notification Banner | **Apology to [Customer Name]: Your [Coffee Order] has been in Making for 10 minutes. We are finishing it right now!** |
| 6 | **10-Minute Barista Reminder** | Background Timer Service | Barista Card & Alert Banner | **Barista Reminder: [Customer Name]'s [Coffee Order] has exceeded 10 minutes in Making.** |
| 7 | **30-Minute Unclaimed Alert** | Background Timer Service | Notification Banner | **Pickup Reminder: [Customer Name]'s [Coffee Order] has been waiting in Ready for 30 minutes.** |
| 8 | **QR Scan Pickup Request** | Customer QR Scanner Station | Pickup Verification Engine | Scanned 8-character QR code string |
| 9 | **Dispense Parcel Command** | Pickup Verification Engine | Parcel Drop Chute & Board View | **Parcel Chute: Verified [Coffee Order] for [Customer Name] — Dropping parcel now!** |
| 10 | **Order Collected Update** | Pickup Verification Engine | Order Board (**Already Collected** Column) | **Complete / Picked Up at [HH:MM]** |
| 11 | **Machine Fault Broadcast** | Fault Monitor | Header Indicator Light & Alert Banner | **Machine Error: Hardware or dispenser fault detected. Automated pickup is paused. Please press Reset Machine.** |
| 12 | **Master Reset Command** | Reset Machine Button | All Backend Stores & UI Panels | Command to wipe all orders, clear faults, and restore blank **Standby** state |

---

## Each Thing It Remembers

| Name of Value | What Kind of Value It Is | What It Starts As | Kept After the Page Closes? |
|---|---|---|---|
| `machinePowerState` | Text (`"standby"` or `"active"`) | `"standby"` | Yes — kept in browser `localStorage` until **Reset Machine** is clicked |
| `machineNeedsReset` | Boolean (`true` or `false`) | `false` | Yes — kept in browser `localStorage` until **Reset Machine** is clicked |
| `idleSeconds` | Whole Number (seconds since last activity when no active orders exist) | `0` | No — resets to `0` when the page closes |
| `isChuteDropping` | Boolean (`true` or `false` during the 2-second parcel drop) | `false` | No — resets to `false` when the page closes |
| `orders` | List of Order Objects | Empty list (`[]`, 0 items) | Yes — kept in browser `localStorage` so an accidental refresh does not lose orders |
| `order.id` | Text (unique identifier per order) | None (created when order is placed) | Yes — inside `orders` in `localStorage` |
| `order.customerName` | Text (1 to 30 characters) | None (set from input when placed) | Yes — inside `orders` in `localStorage` |
| `order.coffeeItem` | Text (name of drink) | None (set from selection when placed) | Yes — inside `orders` in `localStorage` |
| `order.channel` | Text (`"In-Person"` or `"Online"`) | `"In-Person"` | Yes — inside `orders` in `localStorage` |
| `order.stage` | Whole Number (`1` = Placed, `2` = Making, `3` = Ready, `4` = Already Collected) | `1` | Yes — inside `orders` in `localStorage` |
| `order.placedAt` | Text (5-character timestamp `HH:MM` plus millisecond sort key) | Exact time order was placed | Yes — inside `orders` in `localStorage` |
| `order.makingSeconds` | Whole Number (elapsed seconds spent in Stage 2 **Making**) | `0` | Yes — inside `orders` in `localStorage` |
| `order.delayAlertSent` | Boolean (`true` or `false`) | `false` | Yes — inside `orders` in `localStorage` |
| `order.readySeconds` | Whole Number (elapsed seconds spent waiting in Stage 3 **Ready**) | `0` | Yes — inside `orders` in `localStorage` |
| `order.unclaimedAlertSent` | Boolean (`true` or `false`) | `false` | Yes — inside `orders` in `localStorage` |
| `order.qrCode` | Text (8-character code, e.g. `QR-4829A`) | Empty text (`""`) until Stage 3 | Yes — inside `orders` in `localStorage` |
| `order.collectedAt` | Text (5-character timestamp `HH:MM`) | Empty text (`""`) until Stage 4 | Yes — inside `orders` in `localStorage` |
| `activeAlerts` | List of Banner Alert Objects | Empty list (`[]`, 0 items) | No — cleared when the page closes |

