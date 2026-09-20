<!-- claude-toolkit:browser:start -->
## Browser Sessions

**Never resize the browser window.** Whatever opens a browser — Claude in Chrome,
Playwright, Puppeteer, a preview server, a screenshot run — uses the window exactly as
the user left it: their current screen size, and full screen stays full screen.

- Do **not** call `resize_window` or any viewport override to emulate a phone, tablet or
  fixed width. No `375x812`, no "mobile view", no device emulation.
- Do **not** open a small window "just for the screenshot" — capture at the real size.
- Screenshots, GIF recordings and visual checks are taken at the user's actual viewport.

The only exception is an explicit instruction in the user's own prompt ("check this at
mobile width", "resize to 1280", "test the responsive breakpoints", "tengok kat mobile
view"). That applies to that request only; the next browser task is back to the user's
real window size.
<!-- claude-toolkit:browser:end -->
