# File Explorer Module Specifications

This document outlines the specifications for the File Explorer module, including its intended functionality, conceptual API interactions, limitations within an HTML proof-of-concept (POC), and hypothetical examples of file structures and actions.

## 1. Core Functionality

The File Explorer module aims to provide a user-friendly interface for browsing a hierarchical file system structure.

*   **Display:**
    *   Render a hierarchical list of files and folders.
    *   Clearly distinguish between files and folders (e.g., using icons or textual cues).
    *   Display the names of files and folders.
    *   (Optional for POC) Display basic metadata like file size or last modified date.
*   **Navigation:**
    *   Allow users to click on a folder to navigate into it and view its contents.
    *   Provide a mechanism to navigate back to the parent folder (e.g., an "Up" button or breadcrumbs).
    *   (Optional for POC) Display the current path or breadcrumbs for orientation.
*   **File/Folder Actions (Simulated in POC):**
    *   **Open File:** When a file is clicked, an event should signify an "open" attempt. In a POC, this might trigger displaying mock content or a notification.
    *   **Select Item:** Allow a single file or folder to be "selected" (e.g., highlighted) upon clicking.
*   **Conceptual Future Enhancements (Likely out of scope for initial HTML POC):**
    *   Create new folder.
    *   Delete a file or folder.
    *   Rename a file or folder.
    *   Search or filter the list of files/folders.
    *   Context menus for more actions.
    *   Drag and drop functionality.

## 2. Conceptual API Interactions

These interactions describe how the File Explorer component might be controlled or interacted with, typically via JavaScript in an HTML context.

*   **`fileExplorer.setInitialData(data: Object)`:**
    *   **Purpose:** To load the mock file system structure into the explorer.
    *   **Input:** `data` - A JavaScript object representing the entire directory tree.
        *   Example `data` structure:
            ```javascript
            {
              "root": {
                "type": "folder",
                "children": {
                  "documents": {
                    "type": "folder",
                    "children": {
                      "report.docx": { "type": "file", "size": "12KB" },
                      "notes.txt": { "type": "file", "size": "2KB" }
                    }
                  },
                  "images": {
                    "type": "folder",
                    "children": {
                      "logo.png": { "type": "file", "size": "5KB" }
                    }
                  },
                  "README.md": { "type": "file", "size": "1KB" }
                }
              }
            }
            ```
    *   **Action:** Initializes the file explorer to display the root of the provided data.

*   **`fileExplorer.getCurrentPath(): string`:**
    *   **Purpose:** To retrieve the absolute path of the currently displayed directory.
    *   **Output:** A string representing the path (e.g., `"/documents/work/"`).

*   **`fileExplorer.listDirectory(path: string): Array<Object>`:** (Internal or for advanced use)
    *   **Purpose:** To get the contents of a specific directory path from the loaded data.
    *   **Input:** `path` - The path of the directory to list.
    *   **Output:** An array of objects, each representing a file or folder.
        *   Example item: `{ name: "MyFile.txt", type: "file", path: "/documents/MyFile.txt" }` or `{ name: "MyFolder", type: "folder", path: "/documents/MyFolder" }`.
    *   **Note:** In the POC, this would query the internal mock data structure.

*   **`fileExplorer.navigateTo(path: string)`:**
    *   **Purpose:** To change the current view to the specified directory path.
    *   **Input:** `path` - The target directory path.
    *   **Action:** Updates the explorer's display to show the contents of the new path.

*   **`fileExplorer.goUp()`:**
    *   **Purpose:** To navigate to the parent directory of the current path.
    *   **Action:** Updates the explorer's display to show the parent directory's contents. Does nothing if already at the root.

*   **Event: `onFileOpen(callback: Function)`:**
    *   **Purpose:** To allow external code to react when a user attempts to "open" a file.
    *   **Callback Signature:** `callback(fileInfo: Object)` where `fileInfo` could be `{ name: "report.docx", path: "/documents/report.docx", type: "file" }`.

*   **Event: `onSelectionChange(callback: Function)`:**
    *   **Purpose:** To notify when the selected item (file or folder) changes.
    *   **Callback Signature:** `callback(selectedItemInfo: Object)` where `selectedItemInfo` provides details of the newly selected item.

## 3. Limitations for HTML Proof-of-Concept (POC)

*   **No Real File System Interaction:** The POC will operate on a predefined JavaScript object representing the file system. It will not read from or write to the user's actual disk.
*   **Simulated Operations:**
    *   "Opening" a file will not launch external applications. It will trigger an event that the parent application can handle (e.g., by showing mock content or an alert).
    *   Operations like create, delete, or rename will only modify the internal JavaScript data structure and update the view. These changes will be ephemeral and lost on page reload unless explicitly persisted (e.g., using LocalStorage, which is an optional extension).
*   **Performance:** Displaying a very large number of files/folders within a single directory might have performance implications, as it will rely on standard DOM manipulation. Virtualization or pagination are typically out of scope for an initial POC.
*   **Visuals:** May initially lack sophisticated icons for file types, relying on simple visual cues or text.
*   **Advanced Features:** Features like search, filtering, multi-select, context menus, and drag-and-drop will likely be omitted for simplicity in the initial POC.

## 4. Hypothetical File Structure Examples

These illustrate the kind of data the `setInitialData` method would expect.

**Example 1: Simple Project Structure**
(Represented as the `data` object for `setInitialData`)

```javascript
{
  "root": {
    "type": "folder",
    "children": {
      "documents": {
        "type": "folder",
        "children": {
          "report.docx": { "type": "file", "size": "256KB" },
          "presentation.pptx": { "type": "file", "size": "1.2MB" }
        }
      },
      "images": {
        "type": "folder",
        "children": {
          "logo.png": { "type": "file", "size": "15KB" },
          "background.jpg": { "type": "file", "size": "450KB" }
        }
      },
      "src": {
        "type": "folder",
        "children": {
          "main.js": { "type": "file", "size": "5KB" },
          "utils.js": { "type": "file", "size": "2KB" }
        }
      },
      "index.html": { "type": "file", "size": "1KB" },
      "README.md": { "type": "file", "size": "2KB" }
    }
  }
}
```

**Example 2: More Nested Structure**

```javascript
{
  "root": {
    "type": "folder",
    "children": {
      "Users": {
        "type": "folder",
        "children": {
          "Alice": {
            "type": "folder",
            "children": {
              "Desktop": {
                "type": "folder",
                "children": {
                  "screenshot.png": { "type": "file", "size": "300KB" }
                }
              },
              "Documents": {
                "type": "folder",
                "children": {
                  "Work": {
                    "type": "folder",
                    "children": {
                      "project_alpha": {
                        "type": "folder",
                        "children": {
                          "plan.txt": { "type": "file", "size": "3KB" }
                        }
                      },
                      "report_final.pdf": { "type": "file", "size": "2.5MB" }
                    }
                  },
                  "Personal": {
                    "type": "folder",
                    "children": {
                      "todo.txt": { "type": "file", "size": "1KB" }
                    }
                  }
                }
              },
              "Downloads": {
                "type": "folder",
                "children": {
                  "archive.zip": { "type": "file", "size": "10MB" }
                }
              }
            }
          }
        }
      },
      "System": {
        "type": "folder",
        "children": {
          "config.sys": { "type": "file", "size": "0.5KB" }
        }
      }
    }
  }
}
```
*(Note: The actual path for an item would be derived by traversing this structure, e.g. "logo.png" is at "/images/logo.png")*

## 5. Action Examples (User Interaction Flow)

*   **Initial Load:**
    1.  Application calls `fileExplorer.setInitialData(mockData)`.
    2.  File explorer renders the root directory (e.g., showing "documents", "images", "src", "index.html", "README.md" from Example 1).
    3.  `fileExplorer.getCurrentPath()` returns `"/"`.

*   **User Clicks on "documents" Folder (from root):**
    1.  The click event on the "documents" folder item internally calls a function similar to `fileExplorer.navigateTo("/documents/")`.
    2.  The view updates to display the contents of `/documents/`: `report.docx` and `presentation.pptx`.
    3.  The `onSelectionChange` event might be triggered, indicating "documents" was selected.
    4.  `fileExplorer.getCurrentPath()` now returns `"/documents/"`.

*   **User Clicks on "report.docx" File:**
    1.  The click event on the "report.docx" file item triggers the `onFileOpen` callback with details like `{ name: "report.docx", path: "/documents/report.docx", type: "file" }`.
    2.  The application listening to this event might then display "Simulating opening report.docx..." or show predefined mock content associated with this file.
    3.  "report.docx" is visually highlighted as the selected item, and `onSelectionChange` is triggered.

*   **User Clicks "Up" Button (while in `/documents/Work/project_alpha/`):**
    1.  The click event on the "Up" button calls `fileExplorer.goUp()`.
    2.  The view updates to show the contents of the parent directory, `/documents/Work/`.
    3.  `fileExplorer.getCurrentPath()` now returns `"/documents/Work/"`.
```
