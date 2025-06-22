# Journey Story Feature

**Status:** Actively Developed

This directory contains the source code and technical documentation for the **Journey Story Feature**, a self-contained web component that provides an interactive video and transcript experience for the ClearThink-AI Journeys platform.

This component is built with vanilla JavaScript (ES6 Modules) and is designed to be framework-agnostic.

---

## Key Capabilities

* **Companion Mode:** A synchronized, side-by-side view of the video player and a scrolling transcript.
* **Storyboard Mode:** A high-level, multi-column overview of the entire video with thumbnails and text.
* **Multi-Window Player:** In Storyboard Mode, the video is launched in a separate, minimalist pop-out window to support multi-monitor workflows.
* **Interactive Transcript:** All paragraphs are clickable to seek the video to that point in time.
* **URL-based Time Seeking:** The player can start at a specific timestamp via a URL parameter (`?t=`).

## Documentation

This feature is documented using our First Principles approach. The detailed guides can be found below.

### ▻ Developer Documentation (in this Repository)

These documents describe how the feature is designed, built, and maintained.

* **[00_assumptions_and_constraints.md](./docs/00_assumptions_and_constraints.md)**: The foundational rules and context that shaped the feature.
* **[01_vision_and_goals.md](./docs/01_vision_and_goals.md)**: The "Why" — The core problem this feature solves.
* **[02_functional_spec.md](./docs/02_functional_spec.md)**: The "What" — A detailed description of all user-facing behaviors.
* **[03_technical_design.md](./docs/03_technical_design.md)**: The "How" — The technical architecture and implementation details.

### ▻ User & Author Guides (in the Content Repository)

Documentation for end-users and for content creators on how to *use* this feature in a Journey is located in the `journey-content` repository.

* **[View the User & Author Guide](https://github.com/clearthink-ai/journey-content-repo/path/to/feature-guides)** *(Note: This URL is a placeholder and should be updated).*

---
**Owner:** Learning Experience Team
