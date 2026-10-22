# 06 Tech Spec

## Three-File Architecture (HTML, CSS, and JavaScript)

The entire application is built in exactly 3 files in the root workspace folder:

1. **`index.html` (Structure & Exact Copy):**
   Contains the semantic page layout for the **Machine Header Bar** (with the green/red reset status light and **Reset Machine** button), the **Order Intake Panel** (Customer Name input, Coffee Order dropdown, In-Person/Online radio buttons, and **Place Order** button), the **Notification & Alert Banner Area**, the **Four-Column Order Board** (**Placed**, **Making**, **Ready**, **Already Collected**), and the **Automated QR Pick-Up Machine** station.
2. **`styles.css` (Visual Layout & State Styling):**
   Contains the 4-column responsive CSS Grid layout, high-contrast typography for distant viewing in a coffee shop, standby dimmed styling vs. active awake styling, card badges, the 10-minute delayed red warning highlight, the 2-second parcel chute drop animation, and the flashing red indicator light animation when the machine needs a reset.
3. **`app.js` (State Machine, Timers, QR Engine & Storage):**
   Contains the order intake validator, chronological sorting engine, strict 4-stage sequential state guard (`1 -> 2 -> 3 -> 4`), 1-second background interval loop for the 10-minute making delay and 60-minute idle standby transitions, 8-character QR code generator and scanner verifier, fault lockout handler, and browser storage synchronization.

---

## Framework and Libraries

- **Framework:** `none`
  - **Reason:** A single-screen state machine with 4 columns and a 1-second timer runs with zero build steps, instant startup, and deterministic DOM updates using plain HTML, CSS, and JavaScript.
- **External Libraries:** `none`
  - **Reason:** Native browser APIs (`localStorage`, `setInterval`, `Date`, and standard DOM events) handle timestamps, chronological sorting, QR code token generation/verification, and state persistence without any external network dependencies or bundle overhead.

---

## What Is Saved and Where

- **Where data is saved:**
  - Persistent board state is saved in the browser's `localStorage` under the single key `coffee_order_board_state_v1`.
  - Temporary transient values (`idleSeconds`, `isChuteDropping`, and `activeAlerts`) are kept in JavaScript memory (`app.js`) while the page is open.
- **What is saved in `localStorage`:**
  - `machinePowerState` (`"standby"` or `"active"`)
  - `machineNeedsReset` (`true` or `false`)
  - `orders` (the list of all current orders, including `id`, `customerName`, `coffeeItem`, `channel`, `stage`, `placedAt`, `makingSeconds`, `delayAlertSent`, `readySeconds`, `unclaimedAlertSent`, `qrCode`, and `collectedAt`)
- **When saved data is cleared:**
  - Clicking **Reset Machine** or reaching 60 minutes (3600 seconds) of zero-business standby clears `coffee_order_board_state_v1` back to an empty order list and returns the machine to its blank starting state.

---

## Do List and Do Not List

### Do List
- **Do** display the exact 4 column subheadings—**Placed**, **Making**, **Ready**, and **Already Collected**—at all times.
- **Do** keep the space beneath all 4 subheadings completely blank when 0 orders exist.
- **Do** wake the machine automatically from **Standby** to **Active** as soon as 1 in-person or online order is placed.
- **Do** display the **Customer Name**, **Coffee Order**, and **Placed at [HH:MM]** timestamp on every order card without using ticket numbers.
- **Do** sort orders chronologically from earliest placed at the top to latest placed at the bottom.
- **Do** enforce the strict 1-step-at-a-time workflow (`Placed` → `Making` → `Ready` → `Already Collected`).
- **Do** track elapsed time in **Making** and trigger both the customer apology banner and barista reminder badge at exactly 10 minutes (600 seconds).
- **Do** generate a unique 8-character QR code when an order moves from **Making** to **Ready**, and move the order from **Ready** to **Already Collected** only when that matching QR code is scanned.
- **Do** turn on the flashing red **Status: Machine Needs Reset** light and pause all parcel drops when a fault occurs, and clear everything back to blank standby when **Reset Machine** is clicked.

### Do Not List
- **Do not** add login screens, passwords, user accounts, or role sign-in forms (nobody logs in).
- **Do not** display ticket numbers or order numbers on the cards.
- **Do not** allow any order to skip a step (such as moving from **Placed** to **Ready**, or scanning a QR code while an order is still in **Placed** or **Making**).
- **Do not** allow any order to move backward to an earlier column.
- **Do not** dispense a parcel or allow stage transitions while the **Status: Machine Needs Reset** fault light is active.
- **Do not** use external JavaScript frameworks, CSS frameworks, or third-party CDN libraries.

