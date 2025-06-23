# journey-techdocs
# Journey Platform: Technical Components & Documentation

This repository contains the  developer-facing documentation for reusable components that power the **ClearThink-AI Journey system**. It serves as the "engine room" for the platform's interactive features and is intended for the engineering team.


---

## Guiding Principles

Development follows a few core principles to ensure the platform remains robust, maintainable, and portable.

* **Vanilla Stack:** Components are built with standards-compliant, vanilla HTML, CSS, and JavaScript (ES6 Modules). This approach avoids framework lock-in, maximizes performance, and ensures components can be easily integrated into various environments.
* **Modularity:** Each feature or component is designed to be self-contained within its own directory, minimizing dependencies and making them easier to maintain or replace.
* **Documentation as Code:** All technical documentation lives directly alongside the code it describes. Documentation is written in Markdown and is versioned with Git, ensuring that it stays synchronized with the code through the same pull request and review process.

## Repository Structure

This repository is organized to separate large, composite features from smaller, general-purpose components.

```
journey-techdocs/
│
├── .github/              # Contains CI/CD workflows, PR templates, etc.
│
├── features/             # Contains self-contained, complex features.
│   │
│   └── journey-story/    # The interactive video/transcript feature.
│       ├── README.md     # A detailed README specific to this feature.
│       ├── css/          # Feature-specific CSS.
│       ├── js/           # Feature-specific JavaScript modules.
│       └── docs/         # In-depth technical guides for this feature.
│
└── components/           # (Future) Smaller, reusable components (e.g., a modal, a button).
    └── ...
```

## Using This Documentation

All documentation is written in Markdown (`.md`). The primary way to consume this documentation is by Browse this repository directly in the GitHub web interface. GitHub automatically renders Markdown files into clean, readable pages. You can navigate the file structure and follow the relative links between documents.

## Core Features & Components

Below is a list of the key features maintained in this repository.

* **Journey Story Feature**
    * An interactive web component that synchronizes a YouTube video with a transcript. It supports a side-by-side **Companion Mode** and a multi-window **Storyboard Mode** for a rich, non-linear learning experience.
    * **[Go to Feature Details](./features/journey-story/README.md)**

---

*This README is a living document and should be updated as new components and features are added to the platform.*
