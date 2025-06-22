# Functional & UX Specification: Journey Story Feature

This document describes the user-facing functionality, behavior, and interactive states of the Journey Story Feature. It serves as a blueprint for the user experience.

---

## 1. Key Components

The feature is composed of several distinct components that the user interacts with across its different modes.

* **Mode Toggle Button**: A button, labeled "Show Storyboard," located above the transcript. Its sole purpose is to switch the feature between its two primary states: `Companion Mode` and `Storyboard Mode`. When in Storyboard Mode, this control is replaced by a "Close" (×) button to return to Companion Mode.
* **Video Player**: The main video playback element. In `Companion Mode`, it is displayed inside the main content area, next to the transcript. In `Storyboard Mode`, the video is moved to a dedicated, minimalist `Player Window`.
* **Transcript Column**: The primary component in `Companion Mode`. It is an interactive, scrollable list of paragraphs and headings synchronized with the video. The currently playing paragraph is highlighted, and users can click any paragraph to seek the video to that point.
* **Storyboard Grid**: The primary component in `Storyboard Mode`. It replaces the `Transcript Column` and displays a multi-column, horizontally-scrolling grid of all video thumbnails and their associated transcript text.
* **Player Window**: A separate browser window that is launched when the user enters `Storyboard Mode`. It contains only the video player, providing a clean, dedicated view for watching the content.

---

## 2. User Flows & State Transitions

The feature's behavior is defined by its initial state and its two primary interactive states.

### Initial State & URL Navigation

* Upon first load, the feature checks the page URL for a time parameter (e.g., `?t=1m30s` or `?t=90`). If present, the video will automatically seek to that specific time.
* The feature then loads into `Companion Mode`, unless overridden by a user setting.

### State A: Companion Mode (Default View)

* **Layout:** The `Video Player` and the `Transcript Column` are displayed side-by-side. A dedicated area for contextual links is also present.
* **Interactions:**
    * **Playback Sync:** As the video plays, the corresponding paragraph in the `Transcript Column` is automatically highlighted.
    * **Dynamic Contextual Links:** If the active paragraph has associated links, they are dynamically displayed. Clicking any of these links will pause video playback.
    * **Autoscroll Behavior:** The `Transcript Column` auto-scrolls to keep the active paragraph in view. This behavior is paused if the user manually scrolls and resumes on the next playback action.
    * **Paragraph Interaction:** Clicking any paragraph seeks the video to that paragraph's start time. The system ignores clicks when the user is selecting text.

#### Transition: Entering Storyboard Mode
* **Prerequisite:** This transition is only available when the user's "Default Video View" setting is set to **`Enable Storyboard Mode`**.
* **User Action:** The user clicks the **"Show Storyboard"** button.
* **System Response:** A new `Player Window` opens, and the main window transforms into the `Controller Window` displaying the `Storyboard Grid`. Playback state and time are seamlessly preserved.

#### State B: Storyboard Mode (Multi-Window View)
* **Layout:** A two-window state: the `Controller Window` (displaying the `Storyboard Grid`) and the `Player Window`.
* **Interactions:**
    * **Playback Synchronization:** As the video plays in the `Player Window`, the corresponding thumbnail and paragraph in the `Storyboard Grid` are visually highlighted.
    * **Storyboard Interaction:** Clicking any thumbnail or paragraph in the `Storyboard Grid` seeks the video in the `Player Window`.

#### Transition: Exiting Storyboard Mode
* **User Action:** The user exits either by clicking the "Close" (**×**) button or by manually closing the `Player Window`.
* **System Response:** The `Player Window` is closed, and the `Controller Window` seamlessly reverts to `Companion Mode`, preserving the video's playback state and time.

---

## 3. User Configuration

The default behavior of the Journey Story Feature can be modified by a user-specific setting, allowing users to tailor their learning environment.

* **Setting Name:** `Default Video View`
* **Location:** User Profile > Journey Settings
* **Description:** This setting allows a user to choose their preferred experience for all videos that use the Journey Story Feature.
* **Options:**
    * **`Enable Storyboard Mode` (Default):** The "Show Storyboard" button is visible, and the user can switch between Companion and Storyboard modes as described in the flows above.
    * **`Always use Companion Mode`:** The "Show Storyboard" button is hidden for the user on all Journey Stories. The feature will only be available in the side-by-side Companion Mode, and the multi-window functionality is effectively disabled.

---

## 4. User Stories

These stories capture the feature's requirements from the perspective of user goals and motivations.

### Interacting in Companion Mode

* **As a learner,** I want to click on any paragraph in the transcript **so that** I can immediately see the corresponding part of the video.
* **As a student,** I want the transcript to automatically scroll and highlight what's being said **so that** I can easily follow along.
* **As a researcher,** I want to see contextual links related to the current topic **so that** I can explore source material easily.

### Using Storyboard Mode

* **As a user,** I want to switch to a "Storyboard Mode" **so that** I can get a high-level visual overview of the entire video.
* **As a power user,** I want the video in a separate window **so that** I can arrange my workspace across multiple monitors.
* **As a user,** when I close the video window, I want the main page to return to its normal state **so that** I don't lose my place.

### General Functionality

* **As a collaborator,** I want to share a URL with a timestamp **so that** my colleague can open the video at the exact moment I'm referencing.
