# Lift Heavy Metals (LHM)

A lightweight, single-file offline-first Progressive Web App designed specifically for iOS Safari (Add to Home Screen) to track resistance training, manage complex routine templates, and analyze historical performance.

---

## 1. System & Deployment Architecture
- **Host & Environment:** Single `index.html` static web application hosted on Cloudflare Pages via GitHub repository with a custom domain on GoDaddy DNS.
- **Client & Platform:** Optimized for mobile iOS Safari viewport and iOS standalone PWA container.
- **Offline Storage Engine:** High-capacity client-side local database (IndexedDB recommended over standard 5MB `localStorage` to avoid iOS Safari data evictions and handle exercise image attachments).
- **Zero Lock-In:** Native CSV export engine ensuring all logged workouts and exercise records can be exported and backed up at any time.

---

## 2. Core Data Models

### 2.1 Exercise Library Model
- **Name:** Unique title of the movement.
- **Category:** Categorized into Chest, Back, Legs, Arms, Shoulders, Neck, Stretch, or Misc.
- **Media:** Optional base64/blob image attachment.
- **Description:** Permanent technical notes regarding form, setup, or execution.

### 2.2 ExInfo (Set & Exercise Parameters)
- **Weight:** Load used per set.
- **Reps:** Completed repetitions per set.
- **Movement Type:** Isolation vs. Compound / Non-isolated.
- **Failure:** Boolean flag indicating set reached muscular failure.
- **Instance Notes:** Freeform notes field specific to that exercise instance, editable at any point.

### 2.3 Data Group & Inheritance Engine
When an active workout starts, prior set parameters (ExInfo) are pre-populated based on configured data groups:
- **Global Group:** ExInfo inherits from the most recent historical instance of that exercise across all routines.
- **Routine Group:** ExInfo inherits strictly from the last instance of that exercise executed *within the same routine* (e.g., Leg Press in "Legs 1" only pulls previous values from "Legs 1", ignoring "Legs 2").
- **Custom Groups:** User-defined groups allowing arbitrary routines to share an isolated inheritance pool.

---

## 3. UI Navigation & Tab Structure
Fixed five-tab bottom navigation bar:
1. **Active Routine**
2. **Create Routines**
3. **Historical Log**
4. **Exercise Library**
5. **Settings**

---

## 4. Tab Specifications

### Tab 1: Active Routine
- **Default State:** Prompts user to start a session if none is active (choice between an empty custom routine or a saved predefined routine).
- **Execution Interface:**
  - Displays list of exercises in the session with dynamic set rows.
  - Inputs for each set: Weight, Reps, Movement Type (Isolationism), Failure (y/n), and editable Exercise Notes.
  - Auto-fills initial fields from the active Data Group hierarchy.
- **On-the-Fly Adjustments:**
  - Pinned `+` button at bottom of exercise list to append exercises from the Library mid-workout.
  - Controls to adjust set counts or delete exercises from the active session.
- **Template Synchronization Prompt:**
  - If the user modifies sets or exercise composition during an active session, a prompt asks: *"Permanently update routine template?"*
  - **Yes:** Updates the master routine template to reflect the changes.
  - **No:** Applies changes strictly to the current active session without affecting future instances.

### Tab 2: Create Routines
- **Overview:** Displays all existing routine templates with a top-pinned "Create New Routine" button.
- **Builder Interface:**
  - Fields for Routine Name and Description/Notes.
  - Exercise picker linked to the Exercise Library.
  - Exercise parameters: planned set count and superset pairings.
- **Group Management:** UI action to create new custom Data Groups and assign routines to them.

### Tab 3: Historical Log & Analytics
- **Chronological List:** Vertical feed of completed workout cards, with an option to filter by category.
- **Workout Card Metadata:** Routine Name, Date, Timestamp, Duration.
- **Status Badges:**
  - **Modified Badge (Warning Icon):** Triggers if the routine was modified on-the-fly and the user declined to update the master template.
  - **Out-of-Date Badge:** Triggers if a logged routine was unmodified at creation but the master routine template was subsequently edited at a later date.
- **Detail & Edit View:** Tapping a card opens a read-only view of all exercises, sets, weights, and notes. A top-bar "Edit" button unlocks all fields (date, duration, sets, reps, weight) for retroactive modification.
- **Analytics / Graphing Modal:**
  - Dedicated action button opening a full graphing interface.
  - **X-Axis:** Date range selector.
  - **Y-Axis:** Dynamic metric selector (e.g., Routine usage frequency, exercise-specific weight progression over time, rep progression over time).
  - Designed for future expansion into multi-variable correlations (e.g., tracking weight progression alongside isolation status changes).

### Tab 4: Exercise Library
- **Categorization:** Accordion/dropdown lists grouped by category (Chest, Back, Legs, Arms, Shoulders, Neck, Stretch, Misc.), sorted alphabetically by category, then alphabetically by exercise inside each category.
- **Sorting Toggle:** Option to flatten the list and sort all exercises strictly A–Z.
- **Creation Flow:** Pinned floating `+` action button at bottom-right opens a modal to define name, category, description, and optional photo.

### Tab 5: Settings & Preferences
- **Accessibility Toggles:** Dark mode, OpenDyslexic font style, High Contrast mode.
- **Export & Storage Controls:** Format selection dropdown (CSV export default; hooks for JSON backups).
