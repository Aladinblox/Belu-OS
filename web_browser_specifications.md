# Web Browser Module Specifications

This document outlines the specifications for the Web Browser module. It covers its intended functionality for a proof-of-concept (POC), including minimal HTML rendering, address bar, basic navigation, conceptual tabs, its conceptual API, supported "web standards" (greatly simplified for a POC), limitations, and a hypothetical example of its interface rendering a simple page.

## 1. Overall Web Browser Functionality

The Web Browser module simulates a very basic web browsing experience within the OS POC. It allows users to "navigate" to predefined local "HTML" content, renders a minimal subset of HTML tags, and conceptually manages multiple "web pages" using tabs. It is **not** designed to connect to the internet or render complex, modern websites. All "web content" is predefined and local to the simulation.

## 2. Core Functionality Details

### 2.1. Address Bar
*   **Input Field:** Allows users to type "URLs."
*   **POC URLs:** These are not actual internet URLs but identifiers for locally stored/mocked HTML content (e.g., `"home.html"`, `"about_us.html"`).
*   **Navigation Trigger:** Pressing "Enter" in the address bar or clicking a "Go" button initiates loading of the content associated with the "URL."

### 2.2. Navigation Buttons
*   **Go:** Button adjacent to the address bar to trigger navigation to the URL in the address bar.
*   **Back:** Navigates to the previously viewed "page" in the current tab's history. Disabled if no history exists for the tab.
*   **Forward:** Navigates to the next "page" in the current tab's history (if the user has previously navigated back). Disabled if at the most recent page.
*   **Refresh:** Reloads the current "page" by re-fetching and re-rendering its mock HTML content.

### 2.3. Rendering Area (Viewport)
*   The primary section of the browser window where the "HTML" content is displayed after being processed by the rudimentary renderer.

### 2.4. (Conceptual) Tabs
*   **Multiple "Pages":** UI elements (e.g., buttons in a tab bar) to simulate having multiple "web pages" open simultaneously.
*   **Tab Management:**
    *   "Open New Tab": Adds a new tab, typically displaying a blank page or a default "homepage."
    *   "Close Tab": Removes a tab.
    *   "Switch Tab": Allows the user to select an open tab, bringing its content and history into view.
*   **Independent History:** Each tab maintains its own navigation history (back/forward stack).

### 2.5. HTML Rendering (Highly Simplified)
*   The browser includes a very basic "renderer" that can interpret a small subset of HTML tags from the mock content.
*   Styling is minimal, relying on hardcoded default styles for recognized tags.

## 3. Conceptual API Interactions (e.g., `webBrowser` module)

*   `webBrowser.init(containerElement: HTMLElement, config?: Object)`:
    *   Initializes the browser UI within a specified `containerElement`.
    *   `config` might define a "homepage URL," initial tabs, etc.
*   `webBrowser.setMockHTMLContent(url: string, htmlString: string)`:
    *   **Crucial for POC:** Registers a string of "HTML" content to be associated with a specific "URL." This is how "pages" are defined.
    *   Example: `webBrowser.setMockHTMLContent("home.html", "<h1>Welcome!</h1><p>This is the homepage.</p>");`
*   `webBrowser.setMockImageResource(url: string, actualImagePathOrDataURL: string)`:
    *   (POC specific) Registers an image resource that can be "loaded" via an `<img>` tag's `src` attribute.
*   `webBrowser.openNewTab(url?: string, makeActive: boolean = true): string`:
    *   Creates a new tab. If `url` is provided, it attempts to load it. Returns a conceptual `tabId`.
*   `webBrowser.closeTab(tabId: string)`: Closes the tab identified by `tabId`.
*   `webBrowser.switchToTab(tabId: string)`: Activates the specified tab.
*   `webBrowser.getActiveTabInfo(): { tabId: string, currentUrl: string, title: string }`: Returns info about the currently active tab.
*   `webBrowser.navigateTo(url: string, tabId?: string)`:
    *   Loads and "renders" the content associated with `url` in the specified tab (or active tab if `tabId` is null).
*   `webBrowser.goBack(tabId?: string)`: Triggers back navigation in the specified/active tab.
*   `webBrowser.goForward(tabId?: string)`: Triggers forward navigation.
*   `webBrowser.refresh(tabId?: string)`: Triggers refresh in the specified/active tab.
*   `webBrowser.on(eventName: string, callback: Function)`:
    *   A generic event subscription method.
    *   Example events:
        *   `pageLoadSuccess`: `(tabId, url) => {}` - When a page is "loaded" and "rendered."
        *   `pageLoadError`: `(tabId, url, error) => {}` - If a "URL" is not found in mock content.
        *   `titleChange`: `(tabId, newTitle) => {}` - When a `<title>` tag is processed.
        *   `tabSwitched`: `(newTabId, oldTabId) => {}`
        *   `tabOpened`: `(tabId, url) => {}`
        *   `tabClosed`: `(tabId) => {}`

## 4. Supported "Web Standards" & HTML Rendering (Simplified for POC)

The browser's rendering capability is extremely limited.

*   **HTML Parsing:**
    *   A rudimentary parser (e.g., using regex or simple string processing, **not** a full DOM tree builder).
    *   **Supported Tags (Example List):**
        *   Headings: `<h1>`, `<h2>`, `<h3>` (block, decreasing font size).
        *   Paragraph: `<p>` (block, margins).
        *   Links: `<a>` (inline, `href` attribute is key; if `href` matches a known mock URL, it's navigable).
        *   Images: `<img>` (inline, `src` attribute is key; loads from `setMockImageResource`). `alt` attribute may be displayed if image fails.
        *   Divisions: `<div>` (block container).
        *   Spans: `<span>` (inline container).
        *   Line Break: `<br>` (forces a line break).
        *   Horizontal Rule: `<hr>` (renders a horizontal line).
        *   Lists: `<ul>`, `<ol>`, `<li>` (basic list rendering).
        *   Title: `<title>` (content used for tab title, not displayed in viewport).
    *   **Unsupported Tags:** Ignored or rendered as plain text.
    *   **Attributes:** Primarily `href` (for `<a>`) and `src`, `alt` (for `<img>`). Other attributes are generally ignored.
    *   **HTML Structure:** Ignores `<!DOCTYPE>`, `<html>`, `<head>`, `<body>` as structural requirements; it just looks for supported tags within the provided string.
*   **CSS Handling:**
    *   **No External/Embedded CSS:** `<link rel="stylesheet">` and `<style>` tags are ignored.
    *   **No Inline Styles:** `style="..."` attributes are ignored.
    *   **Default Styles Only:** The browser applies its own hardcoded, minimal default styles for supported tags (e.g., `<h1>` is large and bold, `<a>` is blue and underlined).
*   **JavaScript Execution:**
    *   **None.** All `<script>` tags and JavaScript event attributes (e.g., `onclick`) are completely ignored. Content is static.

## 5. Limitations for HTML Proof-of-Concept

*   **No Internet:** Cannot access any real web resources. All content is mock and predefined.
*   **HTML/CSS Subset:** Only a tiny fraction of HTML tags and no real CSS parsing/application. Complex layouts, modern CSS features, forms, tables (beyond basics), etc., will not render correctly or at all.
*   **Static Content Only:** No JavaScript execution means no dynamic web page behaviors, client-side routing, or interactivity beyond basic link navigation.
*   **No Multimedia:** No `<audio>`, `<video>`, or interactive `<canvas>` elements within the rendered pages.
*   **Stateless:** No cookies, local storage, sessions, or other browser persistence mechanisms. Navigation history is per-tab and in-memory.
*   **Security Not Applicable:** Standard browser security features (sandboxing, HTTPS, CORS, etc.) are irrelevant as no external code or resources are handled.
*   **Performance:** Rudimentary parsing should be quick for small content. Very large mock HTML strings might be slow.
*   **Simplified Tab Functionality:** Tab UI is for concept demonstration; advanced features (drag-drop tabs, groups, pinned tabs) are not included.
*   **No Browser Extensions, DevTools, Downloads, Printing, etc.**

## 6. Hypothetical Interface and Rendering Example

### Browser Interface Layout:
*   **Top Row (Tab Bar):**
    *   Buttons representing open tabs (showing page title or URL).
    *   A `[+]` button to open a new tab.
    *   Each tab has an `[x]` button to close it.
*   **Second Row (Controls):**
    *   Navigation: `[< Back]` `[> Forward]` `[Refresh]` buttons.
    *   Address Bar: `[ input type="text" for URL ]`
    *   `[Go]` button.
*   **Main Area (Viewport):**
    *   A large `div` where the "rendered" HTML content is displayed.

### Rendering Example:
If `webBrowser.setMockHTMLContent("mypage.html", pageHtml)` was called with:
```html
<title>My Test Page</title>
<h1>Welcome to My Page</h1>
<p>This is <b>bold</b> text and a <a href="another.html">link to another page</a>.</p>
<img src="test_image.png" alt="Test Image Alt Text">
<hr>
<p>End of page.</p>
```
And `webBrowser.setMockImageResource("test_image.png", "data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7")` (1x1 transparent pixel).

**Expected Output in POC Browser:**
*   **Tab Title:** "My Test Page"
*   **Address Bar:** `mypage.html`
*   **Viewport Rendering:**
    *   **Welcome to My Page** (large, bold text)
    *   This is bold text and a <span style="text-decoration:underline; color:blue;">link to another page</span>. (The `<b>` tag might be ignored or rendered if added to supported list. The link is styled.)
    *   [A 1x1 transparent image here. If image failed, "Test Image Alt Text" might show].
    *   --- (Horizontal line) ---
    *   End of page.

(Unsupported tags like `<b>` would be ignored by default unless explicitly added to the supported list and given a rendering rule).
```
