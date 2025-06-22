# Assumptions & Constraints

This document lists the foundational assumptions and constraints that shaped the design and implementation of the **Journey Story Feature**. Understanding this context is critical for maintaining and extending the feature.

---

## 1. Assumptions

This section lists what we believed to be true about the users, data, and technical environment. If any of these assumptions prove to be false in the future, the feature's design may need to be re-evaluated.

### User & Data Assumptions

* **Need for Interactivity:** We assume users desire more than a standard video player and will derive value from interacting directly with a synchronized transcript and visual storyboard.
* **Data Integrity:** We assume that the system will be provided with an accurate transcript where each paragraph has reliable `data-start` and `data-end` timestamps.
* **Availability of Thumbnails:** We assume that thumbnail images for key sections will be provided and are essential for the `Storyboard Mode` experience.

### Technical Assumptions

* **Modern Browser Environment:** We assume the target audience will be using modern, evergreen desktop browsers (e.g., Chrome, Firefox, Edge, Safari).
* **`BroadcastChannel` API Support:** The multi-window `Storyboard Mode` is fundamentally dependent on the browser's `BroadcastChannel` API for communication. The feature will not work in older browsers that do not support this API (like Internet Explorer).

---

## 2. Constraints

This section lists the non-negotiable rules and limitations that directly influenced our technical architecture.

* **Technology Stack:** The solution was required to be built using only vanilla HTML, CSS, and JavaScript (ES6 Modules). No external front-end frameworks like React, Vue, or Angular were permitted.
* **Client-Side Execution:** The feature must operate entirely on the client-side (in the browser). It cannot rely on a dedicated server-side component for its core logic.
* **Integration with Host Framework:** The component must exist within and not disrupt the parent **Material for MkDocs** CSS framework. This significant constraint led to numerous CSS specificity and layout conflicts, ultimately requiring the "Iframe Teleport" technique (moving the `<iframe>` to the `<body>` via JavaScript) for the pop-out player to render reliably.
* **Video Platform:** The player is built specifically for the **YouTube IFrame Player API**. It is not designed to work with other video platforms (e.g., Vimeo) or local video files without significant refactoring.
