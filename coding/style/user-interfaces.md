# User Interfaces

Applies to any code that renders a user interface (web, mobile, desktop).

## Reuse Before You Build

- Before writing a new component, hook, helper or style, search the shared locations (component library, shared
  packages, utils) for one that does the same job
- Extend an existing component with a variant or prop rather than building a near-copy
- Extract a shared component when the same markup pattern appears a third time, or when two apps or packages need it
- Parts used by more than one app live in a shared package, not in one app that the other copies from

## Design Tokens

- Colours, spacing, type sizes, radii, shadows and motion come from the design tokens only
- No literal values in components: no hex or `rgb()` colours, no arbitrary pixel sizes, no one-off shadows
- A missing value is added to the tokens, not written inline
- Exceptions (e.g. platform safe-area insets) are named and kept in one place

## Themes

- Every screen works in every supported theme (e.g. light and dark), not only the default one
- Theme switching goes through the one central mechanism; components never branch on the theme themselves
- Contrast meets the design system's rules in every theme: text 4.5:1, controls and meaningful graphics 3:1

## Layout and Breakpoints

- Breakpoints come from one shared policy; a screen never invents its own width to switch layouts
- Every screen is checked at each defined breakpoint, smallest first

## Data and Copy

- What the UI offers comes from the data the server or config sends (options, limits, values, labels); the client
  renders it and never re-implements the rules behind it
- User-facing copy and brand names come from their content or config source, not from markup

## Accessibility

- Interactive elements are real controls (buttons, links, inputs) with accessible names
- Every action works with the keyboard; focus is visible, trapped in dialogs and returned on close
- Status changes that matter are announced (live regions)
