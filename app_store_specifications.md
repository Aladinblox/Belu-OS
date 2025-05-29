# App Store (Conceptual) Module Specifications

This document outlines the specifications for the conceptual App Store module. It details its intended functionality within an HTML proof-of-concept (POC), including browsing mock applications, conceptual installation/uninstallation processes, its conceptual API, the definition of an "app package" for the POC, limitations, and a hypothetical example of its interface and app listings.

## 1. Overall App Store Functionality

The App Store module simulates a marketplace for "applications" within the OS POC. It enables users to browse a curated list of available mock applications, view their details, and conceptually "install" or "uninstall" them. This module does **not** connect to any real app repository, nor does it manage or execute actual software packages. Its primary purpose is to demonstrate the concept of application management within the simulated OS.

## 2. Core Functionality Details

### 2.1. App Listings (Catalogue View)
*   **Display:** Presents a collection of available "applications" in a user-friendly format (e.g., a grid or list of cards).
*   **Information per App:** Each listing minimally shows the app's name, a representative icon (if available), a brief description, and its current installation status or an action button (e.g., "Install", "Uninstall", "View Details").

### 2.2. App Discovery (Basic)
*   **(Optional for POC) Categories:** A simple mechanism to filter applications by predefined categories (e.g., "Utilities," "Games," "Productivity").
*   **(Optional for POC) Search:** A basic client-side text search functionality, primarily for finding apps by name.

### 2.3. (Conceptual) App Installation
*   **Trigger:** User clicks an "Install" button associated with an app.
*   **POC Simulation:**
    1.  The App Store updates the app's status to "Installing" and then "Installed" within its internal data.
    2.  An event (e.g., `onAppInstalled`) is emitted, notifying the main OS module.
    3.  The OS module (conceptually) adds the app to its list of available applications (e.g., in a start menu, app launcher, or desktop).
    4.  The UI in the App Store updates (e.g., "Install" button changes to "Uninstall" or "Open").
*   **No Actual Download/Execution:** This process is entirely simulated. No files are downloaded, and no code is executed.

### 2.4. (Conceptual) App Uninstallation
*   **Trigger:** User clicks an "Uninstall" button for an app marked as "Installed."
*   **POC Simulation:**
    1.  The App Store updates the app's status to "Uninstalling" and then "Not Installed."
    2.  An event (e.g., `onAppUninstalled`) is emitted.
    3.  The OS module (conceptually) removes the app from its application lists.
    4.  The UI in the App Store updates (e.g., button changes back to "Install").

### 2.5. App Details View
*   **Access:** User clicks on an app listing (e.g., the app's name or card, not directly on the "Install" button) to see more information.
*   **Content:** This dedicated view typically displays:
    *   App Name, Icon (larger).
    *   Full Description.
    *   Conceptual metadata: Version, Publisher, Release Date, Category.
    *   (Conceptual) Screenshots: A gallery of mock images showcasing the app.
    *   An "Install" or "Uninstall" button, reflecting the current status.
    *   A "Back" button to return to the main catalogue view.

## 3. Conceptual API Interactions (e.g., `appStore` module)

*   `appStore.init(config: { availableApps: Array<AppPackage> })`:
    *   Initializes the App Store with a predefined list of `availableApps`.
*   `appStore.listAvailableApps(filter?: { category?: string, searchTerm?: string }): Array<AppPackage>`:
    *   Returns an array of `AppPackage` objects, optionally filtered by category or search term.
*   `appStore.getAppDetails(appId: string): AppPackage | null`:
    *   Retrieves the full `AppPackage` object for the given `appId`. Returns `null` if not found.
*   `appStore.installApp(appId: string): Promise<{ success: boolean, message?: string }>`:
    *   Simulates installing the app. Updates internal status and emits `appInstalled` event.
*   `appStore.uninstallApp(appId: string): Promise<{ success: boolean, message?: string }>`:
    *   Simulates uninstalling the app. Updates internal status and emits `appUninstalled` event.
*   `appStore.getAppStatus(appId: string): 'Not Installed' | 'Installed' | 'Installing' | 'Uninstalling' | 'Error'`:
    *   Returns the current conceptual installation status of an app.
*   `appStore.on(eventName: 'appInstalled' | 'appUninstalled' | 'appStatusChanged', callback: Function)`:
    *   Registers a callback for specific events.
        *   `appInstalled`: `(appDetails: AppPackage) => void`
        *   `appUninstalled`: `(appId: string) => void`
        *   `appStatusChanged`: `(appId: string, newStatus: string) => void`
*   `appStore.registerApps(appDefinitions: Array<AppPackage>)`:
    *   Allows adding more apps to the store's catalog after initial `init`.

## 4. "App Package" Definition (Simplified for POC)

An "App Package" is a JavaScript object describing a mock application. It contains metadata and configuration for the OS POC, **not** executable code.

```javascript
// Example AppPackage structure
{
  id: "unique_app_id_string",         // e.g., "simple_text_editor_v1"
  name: "Application Name",           // e.g., "Simple Text Editor"
  version: "1.0.0",                   // Conceptual version
  icon: "path/to/icon.png",           // Path to a mock icon image file
  description_short: "Brief summary.",// For list views
  description_full: "Detailed explanation of the app's features and purpose.",
  publisher: "App Creator Name",      // e.g., "OS POC Development Team"
  releaseDate: "YYYY-MM-DD",          // Conceptual release date
  category: "Utilities",              // e.g., "Productivity", "Games", "Education"
  screenshots: [                      // Array of paths to mock screenshot images
    "path/to/screenshot1.png",
    "path/to/screenshot2.png"
  ],
  // Crucial for POC: How the OS should "launch" this app
  launchConfig: {
    moduleToLoad: "TextViewerModule", // Identifies an existing OS POC module
    windowTitle: "My Text File",      // Title for the app's window
    initialData: { content: "" },     // Any data the module needs to start
    // Other module-specific parameters
  },
  status: "Not Installed"             // Initial status: "Not Installed" or "Installed"
}
```
The `launchConfig` tells the OS simulation which pre-existing UI module (like a text viewer, image viewer, or a more complex stubbed application) to instantiate when the "app" is "opened."

## 5. Limitations for HTML Proof-of-Concept

*   **Simulation Only:** "Installation" and "uninstallation" are purely conceptual, involving state changes and events within the POC, not actual file operations or code execution.
*   **No Real Software:** Apps are metadata objects (`AppPackage`), not functional programs.
*   **Predefined Catalogue:** The list of available apps is fixed at design time or through `registerApps`; no dynamic discovery from external sources.
*   **No Updates/Versioning:** The POC will not manage app updates or different versions.
*   **No Financial Transactions:** No simulation of payments, subscriptions, or in-app purchases.
*   **User Accounts:** No user authentication or accounts for managing purchased/installed apps.
*   **Security Irrelevant:** As no actual code is downloaded or run, app security, permissions, and sandboxing are not applicable.
*   **Basic Discovery:** Search and categorization features, if implemented, will be rudimentary and operate on the static metadata of the `AppPackage` objects.
*   **No Dependencies:** The concept of apps requiring other apps or libraries is not handled.
*   **No User Reviews/Ratings:** Social features like reviews or ratings are out of scope.

## 6. Hypothetical Interface and App Listing Example

### App Store Interface Layout:
*   **Header:**
    *   Title: "OS POC App Store"
    *   (Optional) Search Bar: `[ Search apps... ]`
*   **Sidebar (Optional):**
    *   List of Categories: "All", "Productivity", "Utilities", "Games".
*   **Main Content Area (Catalogue View):**
    *   A grid of "App Cards." Each card represents an app.

### Example App Card (in Catalogue View):
```
+--------------------------------------+
| [AppIcon.png]  **Awesome App v1.2**  |
|                                      |
| Category: Productivity               |
| Publisher: POC Creators              |
|                                      |
| A brief description of what this     |
| awesome app does for the user.       |
|                                      |
| [ View Details ]  [ Install ]        |
+--------------------------------------+
```

### App Details View (Example):
(Accessed by clicking "View Details" or the card's main body)

```
+----------------------------------------------------+
| **Awesome App v1.2**                               |
| [Back to List]                                     |
|----------------------------------------------------|
| [LargeAppIcon.png]  |  Full description spanning   |
|                     |  multiple lines, detailing   |
|                     |  all the fantastic conceptual|
|                     |  features of Awesome App.    |
|                     |                              |
| Publisher: POC Creators   Version: 1.2             |
| Release Date: 2024-03-01                           |
|----------------------------------------------------|
| Screenshots:                                       |
| [ss1.png] [ss2.png] [ss3.png]                      |
|----------------------------------------------------|
|                      [ Install App ]               |
+----------------------------------------------------+
```

**Interaction Flow Example:**
1.  User opens the App Store. They see a list of app cards.
2.  User clicks "Install" on the "Awesome App v1.2" card.
3.  The button changes to "Installing..." then to "Uninstall."
4.  The `appStore.installApp("awesome_app_v1.2")` API is called.
5.  An `appInstalled` event is fired.
6.  The main OS module receives this event and adds "Awesome App v1.2" to its (conceptual) start menu or app launcher, using the `launchConfig` from the app's package to know how to "open" it.
```
