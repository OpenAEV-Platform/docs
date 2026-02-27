# Design System

This page covers the OpenAEV Design System foundations that developers should follow when building or modifying the platform's user interface. These rules ensure visual consistency and readability across all screens and tools.

!!! info "Full Design System"

    The complete Design System is maintained in [Figma](https://www.figma.com/design/Gpp4y1YOpvJ6ZCuez3tgzO/DS_Design_System---WIP?node-id=25-547). This page documents the key principles developers need to apply.

## Typography

The typography system is built around a **clear hierarchy**, not decoration. It helps users understand content structure at a glance and keeps layouts consistent across tools.

### Fonts

OpenAEV uses two fonts with distinct roles:

| Font | Role | Usage |
|------|------|-------|
| **Geologica** | Titles | Headings and structural elements |
| **IBM Plex Sans** | Content | Body text, labels, descriptions |

### Heading scale

Each heading level (H1 through H6) has a defined size, weight, and line-height with a specific use case. The scale follows a consistent ratio, creating a predictable visual rhythm across screens.

**Key principles:**

- Each heading level has a **specific use case** — do not choose a heading level for its visual appearance alone.
- Follow the hierarchy strictly: avoid skipping heading levels (e.g., jumping from H1 to H4).
- Avoid ad-hoc text styles — always use the defined heading levels and body styles.

## Writing rules

Writing rules focus on **clarity and consistency**, especially in UI labels and messages. They reduce ambiguity and ensure similar actions are expressed the same way across tools.

### Capitalization

| Rule | When to use | Examples |
|------|-------------|---------|
| **Sentence case** | Most UI elements (buttons, labels, descriptions, tooltips, form fields) | "Create a new simulation", "Select an injector" |
| **Title case** | Structural elements only (page titles, navigation items, tabs) | "Atomic Testing", "Security Control Validation" |

### Dates, numbers, and relative times

Dates, numbers, and relative times follow shared formatting rules to avoid inconsistencies between screens. Always use the platform's formatting utilities rather than custom formatting.

### Benefits

Following these writing rules ensures that:

- Similar actions are expressed the same way across tools.
- Interfaces feel more predictable to users.
- Users don't have to re-interpret wording from one screen to another.
