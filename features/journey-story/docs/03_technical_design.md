# Technical Design: Journey Story Feature

This document provides a detailed technical overview of the `Journey Story Feature`. It is intended for developers who are maintaining or extending the component's functionality.

---

## 1. Architecture Overview

The Journey Story Feature is designed as a self-contained, framework-agnostic web component built with vanilla JavaScript (ES6 Modules), CSS3, and standard HTML5. The entire functionality is encapsulated within a main `YTVideoPlayer` class, which manages the feature's state, user interactions, and all DOM manipulation.

The component's architecture is designed to support two primary user-facing modes: a default **Companion Mode** (side-by-side video and transcript) and a **Storyboard Mode**. The transition between these modes is handled by the `YTVideoPlayer` class, which manipulates CSS classes on the main container to trigger different layouts defined in the component's stylesheet.

For its most advanced feature—the multi-window Storyboard Mode—the architecture uses a "Master/Remote" pattern. When activated, a minimalist **Player Window** is launched, which acts as the "master" controller for video playback. The original main window becomes a "remote control," displaying the storyboard and sending user interaction commands.

Cross-window communication is managed by a `CommunicationManager` class that abstracts the browser's native **`BroadcastChannel` API**. This provides a robust and decoupled way to send state and commands (e.g., `play`, `seek`, `timeUpdate`) between the windows. To handle cases where the user manually closes the pop-out window, a `setInterval` "watchdog" runs in the main window to detect the closure and gracefully revert the component back to its single-window state.

To overcome CSS conflicts with the parent framework (Material for MkDocs), the pop-out Player Window employs a "DOM Escape" or "Iframe Teleport" strategy. Upon loading, JavaScript identifies the core `<iframe id="player">` element, detaches it from its original complex parent structure, and re-appends it directly to the `<body>` tag. This frees the iframe from all inherited styles, allowing for a clean, conflict-free, fullscreen presentation.
## 2. `YTVideoPlayer` Class Deep Dive

The `YTVideoPlayer` class is the core controller for the entire feature. It encapsulates all state, manages user interactions, and orchestrates the behavior of the component across all modes.

### Key Properties

This table lists the essential properties of the class that are used to manage its state.

| Property | Type | Description |
| :--- | :--- | :--- |
| `player` | `object` \| `undefined` | Holds the official `YT.Player` instance returned by the YouTube IFrame API after initialization. |
| `playerWindow` | `Window` \| `null` | Holds the window reference object for the pop-out player. It is `null` in single-window mode. |
| `viewRole` | `string` | Defines the instance's role. Can be `'transcript'` (default, main window) or `'player'` (pop-out window). |
| `commManager` | `object` | An instance of the `CommunicationManager` class, responsible for handling `BroadcastChannel` communication. |
| `overviewing` | `boolean` | A state flag that is `true` if the component is in Storyboard Mode, and `false` if it is in Companion Mode. |
| `autoscrolling` | `boolean` | A state flag that is `true` by default. It is set to `false` if the user manually scrolls the transcript, pausing the auto-scroll-to-active-paragraph behavior. |
| `closeMonitorInterval` | `number` \| `null` | Holds the ID for the `setInterval` "watchdog" timer, which is used to detect when the pop-out player window is manually closed by the user. |

### Key Methods

The following methods are central to the component's operation.

* **`constructor(ytVideoContainerId, commMode, viewRole)`**
    * Initializes all state properties like `playerWindow`, `viewRole`, etc. It instantiates the `CommunicationManager`, scrapes the DOM to find key elements, and attaches all initial event listeners, including the `beforeunload` page exit listener.

* **`initializePlayer()`**
    * This is the entry point for creating the embedded YouTube player. It executes `new YT.Player(...)` and registers the necessary event handlers (`onReady`, `onStateChange`) with the YouTube IFrame API.

* **`handleCommand(command, payload)`**
    * Acts as the central command hub for all actions, whether initiated locally or received from another window via `BroadcastChannel`. It uses the `viewRole` property to ensure only the "master" player executes control commands like `play` or `pause`, while the "remote control" instance only processes UI updates.

* **`updateAppearanceForTime(timestamp)`**
    * The core rendering function that runs on every `timeUpdate`. It contains a guard clause to do nothing if the instance is a `player`-only view. Its responsibilities include updating the active transcript paragraph, highlighting the active thumbnail in Storyboard Mode, and determining if the local video overlay should be positioned or hidden.

* **`_handleOverviewToggle(evt)`**
    * The primary controller for switching between Companion Mode and the multi-window Storyboard Mode. It manages the creation and closing of the pop-out `playerWindow`, adds/removes the `.player-hidden` and `.overview` CSS classes, connects/disconnects the `commManager`, and starts/stops the "watchdog" timer.

* **`_revertToSinglePlayerMode()`**
    * The dedicated cleanup method called when the pop-out window is closed. It nullifies the `playerWindow` reference, removes the `.player-hidden` class, disconnects the `commManager` to revert to `single` mode, and restarts the local instance's polling (`_rafTick`) loop.

* **`_startCloseMonitor()`** / **`_stopCloseMonitor()`**
    * These methods manage the `setInterval` "watchdog" timer. The monitor periodically checks if the pop-out window has been manually closed by the user and triggers `_revertToSinglePlayerMode` if it has.

## 3. Communication Protocol

To keep the main Controller Window and the pop-out Player Window synchronized, the system uses the native browser **`BroadcastChannel` API**. This API allows for a robust, many-to-many messaging system where windows on the same origin can subscribe to a named channel and broadcast messages to all other subscribers.

This functionality is abstracted into the `CommunicationManager` class. All messages sent across the channel follow a consistent structure.

### Message Structure

Every message sent through the `commManager` is an object with two properties:

* **`command`** (`string`): A string identifier for the action or state update being sent.
* **`payload`** (`object`): An object containing the data necessary to execute the command.

### Command Reference

The following table lists all the commands currently used by the `Journey Story Feature`.

| Command | Payload | Sender(s) | Description |
| :--- | :--- | :--- | :--- |
| **`timeUpdate`** | `{ time: number }` | Active Player | The "heartbeat" of the system. Broadcast continuously while the video is playing to synchronize the UI (active paragraph/thumbnail) across all windows. |
| **`seek`** | `{ time: number }` | Any Window | Instructs the master player to jump to a specific timestamp. Sent in response to a user clicking a paragraph, thumbnail, or chapter link. |
| **`play`** | `{}` | Any Window | Instructs the master player to begin or resume video playback. |
| **`pause`** | `{}` | Any Window | Instructs the master player to pause video playback. |
| **`togglePlayPause`** | `{}` | Any Window | A request for the master player to toggle its current playback state. The master player itself determines whether to call `play()` or `pause()`. |

## 4. CSS Strategy

The visual appearance and layout of the Journey Story Feature are managed through a state-driven CSS architecture. The `YTVideoPlayer` JavaScript class acts as the controller, dynamically adding and removing specific classes on key HTML elements. The component's stylesheet (`youtube-transcript-player-journey.css`) contains all the rules that respond to these state changes to render the correct view.

### Key Controlling Classes

The entire system relies on a few primary CSS classes that define the component's state.

* **`.sidebar` (Companion Mode)**
    * This class is applied to the main `#YTVideo` container to render the default view. It uses `display: flex` to create the side-by-side layout with the video player on one side and the transcript column on the other.

* **`.overview` (Storyboard Mode)**
    * This class replaces `.sidebar` when the user enters Storyboard Mode. It reconfigures the layout to be a multi-column, horizontally-scrolling view that displays all thumbnails and transcript paragraphs.

* **`.player-hidden`**
    * This is a "modifier" class applied to the `#YTVideo` container in addition to `.overview`. Its presence signals that the multi-window mode is active. It is the key selector used to hide the local video player element (`#video`) in the main window.

* **`.player-view-mode`**
    * This class is fundamentally different from the others. It is applied to the `<body>` tag of the pop-out **Player Window** itself. Its purpose is to trigger a set of rules that hide all surrounding page chrome (headers, footers, sidebars, etc.) to create a clean, minimalist "kiosk mode" for the video.

### The "Iframe Teleport" Strategy

To ensure the `.player-view-mode` styles work reliably, the application uses a JavaScript-based technique we've called the "Iframe Teleport." On page load, if the `view=player` URL parameter is present, the `<iframe id="player">` is programmatically moved from its original position deep inside the HTML structure to become a direct child of the `<body>`. This frees it from all parent containers and their conflicting framework styles, making it trivial to style with simple, effective CSS.

### Overriding Framework Styles

The component exists within the broader Material for MkDocs CSS framework. In several cases, it was necessary to write highly specific CSS selectors (e.g., `#YTVideo.player-hidden.overview #video`) or use the `!important` flag to ensure our component's styles would reliably override the framework's default styles. This approach was necessary to resolve layout conflicts, particularly with the framework's default `flexbox` behavior.
