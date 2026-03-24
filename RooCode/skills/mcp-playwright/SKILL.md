---
name: mcp-playwright
description: Control a headless or visible browser to navigate web pages, interact with UI elements, take screenshots, and debug frontend applications. Use this whenever the user asks to test a web app, verify if a local dev server is rendering correctly, debug console errors, or scrape dynamic content.
context: web-automation
compatibility: Uses the `@playwright/mcp` MCP Server. If unavailable, fall back to writing and executing standalone Playwright/Puppeteer Node.js scripts via the terminal.
---

# Playwright Browser Automation

## Tools

| Tool | Use For |
|------|---------|
| **Navigation & State** | |
| `browser_navigate` | Go to a specific URL (e.g., `http://localhost:3000`) |
| `browser_navigate_back` | Go back to the previous page |
| `browser_tabs` | Manage and switch between multiple open browser tabs |
| `browser_wait_for` | Pause execution until a specific element appears or a condition is met |
| **Interaction** | |
| `browser_click` | Click on buttons, links, or any DOM element |
| `browser_fill_form` / `browser_type` | Enter text into input fields, search bars, or forms |
| `browser_press_key` | Simulate keyboard events (e.g., `Enter`, `Escape`, `Tab`) |
| `browser_select_option` | Choose an option from a dropdown menu `<select>` |
| `browser_hover` / `browser_drag` | Perform complex mouse interactions |
| `browser_handle_dialog` | Accept or dismiss JavaScript alerts/prompts |
| **Extraction & Debugging** | |
| `browser_take_screenshot` | Capture the visual state of the page to verify UI changes |
| `browser_snapshot` | Get the current DOM structure (HTML) to find element selectors |
| `browser_console_messages` | Read browser console logs to catch JavaScript errors or warnings |
| `browser_network_requests` | Inspect API calls, failed requests (404/500), and network activity |
| `browser_evaluate` | Run custom JavaScript directly within the page context |
| **Window Management** | |
| `browser_resize` | Change viewport dimensions (e.g., test mobile responsiveness) |
| `browser_close` | Close the browser instance to free up memory |

## When to Use

- **Visual Verification** — "Does the new header look correct on mobile?" (Use `browser_resize` + `browser_take_screenshot`).
- **E2E Testing Simulation** — "Can a user successfully log in and add an item to the cart?"
- **Frontend Debugging** — "Why is the page blank?" (Use `browser_navigate` + `browser_console_messages` + `browser_network_requests`).
- **Web Scraping** — "Extract the latest documentation from this dynamic SPA website."
- **Interacting with Localhost** — Verifying changes immediately after editing React/Vue/HTML files.

## Usage Effectiveness & Best Practices

Browser automation requires dealing with asynchronous behavior and dynamic rendering.

```
# ❌ Bad Automation (Flaky & Blind)
1. `browser_navigate` to a heavy SPA.
2. Immediately `browser_click` a button that hasn't rendered yet.
3. Assuming the page looks fine without checking logs or screenshots.

# ✅ Good Automation (Robust & Verified)
1. `browser_navigate` to the URL.
2. `browser_wait_for` the specific element selector to appear in the DOM.
3. `browser_snapshot` to verify the element's current state/attributes.
4. `browser_click` the element.
5. `browser_console_messages` to ensure no JS errors were thrown by the click.
6. `browser_take_screenshot` to visually confirm the result to the user.
```

### Strategy for Finding Selectors
Before interacting with a page, you need to know *what* to click.
1. Navigate to the page.
2. Use `browser_snapshot` to get the DOM.
3. Analyze the DOM to find stable selectors (e.g., `data-testid`, `id`, `aria-label`, or specific classes).
4. Execute the interaction tool (`browser_click`, `browser_fill_form`) using the discovered selector.

## Why Use This

- **"See" what the user sees** — Instead of guessing why a CSS/JS change isn't working, you can actually look at the rendered page and the browser console.
- **End-to-End Confidence** — Proves that your code modifications actually work in a real browser environment, not just in theory.

## CLI Alternative

If the Playwright MCP server is unavailable, you can write a quick Node.js script using the standard Playwright library and run it:

```javascript
// create script.js
const { chromium } = require('playwright');
(async () => {
  const browser = await chromium.launch();
  const page = await browser.newPage();
  await page.goto('http://localhost:3000');
  await page.screenshot({ path: 'screenshot.png' });
  await browser.close();
})();
// Run: node script.js
```