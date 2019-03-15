---
title: Mobile-first media queries
submitted_by: Rico Sta. Cruz (@rstacruz)
submitted_on: 2019-03-15
approved_on: null
---

## Summary

Let's say a few rules for media queries to ease confusion, make things readable, and push forward a 'mobile-first' approach to writing CSS.

- Use min-width, never max-width. This means we'll be doing a 'mobile-first' approach.
- Store preset breakpoints in Sass variables. Queries will be written as `@media (min-width: $viewport-7) { ... }`.

See the guidelines below for more information.

## The guidelines

1. **Always use min-width with media queries.** <br> Avoid using `max-width`. This will avoid confusion. make things more readable, and force CSS to be written with mobile as a priority.

   ```css
   /* OK */
   @media (min-width: 480.1px) { ... }

   /* Avoid: at first glance, it looks the same as the one above. */
   @media (max-width: 480px) { ... }
   ```
  
2. **In Sass, store preset breakpoints in `$viewport-X` variables.** <br> Sass offers many different ways to store preset media queries; this is simply a case of us picking one for the sake of consistency.

   ```scss
   $viewport-4: 480.1px;
   $viewport-7: 768.1px;
  
   /* OK: Using breakpoint variables */
   @media (min-width: $viewport-7) {
     ...
   }
   
   /* Avoid: interpolated media queries */
   @media (#{$small-screen}) { ... }
   
   /* Avoid: mixin media queries */
   @include small-screen() { ... }
   ```
   
3. **Name breakpoints according to their nearest-hundredth number.** <br> Avoid using viewport names like `$mobile` or `$small-screen`, which can be ambiguous (what does "small" mean, and does "mobile" refer to landscape or portrait?). Instead, name them like `$viewport-4` for a viewport that's `480.1px` wide.

   ```scss
   $viewport-4: 480.1px;
   $viewport-7: 768.1px;
   $viewport-9: 992.1px;
   $viewport-12: 1200.1px;
   ```

4. **Add `.1` to viewports.** <br> The biggest issue we want to avoid is the use of `min-width: 768px`, which deceivingly _includes_ the iPad in portrait mode. By using `min-width: 768.1px`, we will be targeting things screens bigger than iPads.

   ```scss
   /* See example above */
   ```
 
## Implementation plan

For existing projects:

- Don't adapt the `$viewport-4` style of writing breakpoints if another style already exists in the codebase.

- Don't change existing viewport numbers. If it's already using `480px`, changing it to `480.1px` may break things.

- Avoid writing `max-width` queries for new changes and new components, favoring `min-width` only approach.

For new projects:

- Adapt the above guidelines as needed.

## Prior art

- Bootstrap 4.0 introduces breakpoints that are off by `.02`. We choose to use `.1` though, simply because it's easier to remember. https://getbootstrap.com/docs/4.3/layout/overview/#responsive-breakpoints
