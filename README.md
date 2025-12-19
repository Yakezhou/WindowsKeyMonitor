# Windows Key Monitor

Languages: English, [Simplified Chinese](README_CN.md)

## Project Introduction

Windows Key Monitor is a global keyboard hook tool developed based on Windows API, capable of real-time monitoring and recording all keyboard inputs system-wide. This tool supports multiple operating modes, including console display, background hidden operation, and file logging functions.

**⚠️ IMPORTANT LEGAL DISCLAIMER:** This program is for educational and research purposes only. Use it only on devices for which you have legal authorization. Do not use it for illegally monitoring others' keyboard inputs. Users are fully responsible for any consequences arising from the use of this tool.

## Feature Set

### Core Features
- ✅ **Global Keyboard Monitoring**: Capture all keyboard inputs system-wide
- ✅ **Key Recognition**: Accurately identify letters, numbers, symbols, function keys, and control keys
- ✅ **Numpad Marking**: Add `[NUM]` prefix to numpad digits
- ✅ **Timestamp Recording**: Each entry contains precise timestamps
- ✅ **Combination Key Detection**: Support for detecting and recording key combinations

### Operating Modes
- ✅ **Interactive Mode**: Display key logs in real-time on console
- ✅ **Silent Mode**: Run in background with no interface display
- ✅ **File Logging**: Save key logs to specified files
- ✅ **Hidden Window**: Run completely in background with system tray icon support

### Security Features
- ✅ **Admin Privilege Detection**: Automatically detects running privileges
- ✅ **Safe Exit Mechanism**: Use Left Ctrl+Win combination to exit
- ✅ **Resource Cleanup**: Ensures proper release of all system resources
- ✅ **Error Handling**: Detailed error messages and fault handling

## System Requirements

### Operating System
- Windows 7/8/10/11 (32-bit or 64-bit)
- Windows Server 2008 R2 and above

### Runtime Environment
- C++ runtime libraries (statically compiled, no additional installation required)
- Administrator privileges (recommended)

## Installation & Compilation

### Compilation Methods

#### Using MinGW (Recommended)
```bash
g++ -o keymonitor.exe keymonitor.cpp -luser32 -lgdi32 -lshell32 -static -std=c++11
```

#### Using Visual Studio
```bash
cl keymonitor.cpp user32.lib gdi32.lib shell32.lib
```

### Compilation Option Explanations
- `-static`: Static linking, generates standalone executable
- `-std=c++11`: Uses C++11 standard
- `-luser32 -lgdi32 -lshell32`: Links Windows system libraries

## Usage Instructions

### Basic Syntax
```
keymonitor.exe [options]
```

### Usage Examples

#### Example 1: Console Interactive Mode
```bash
# Display all key logs in console
keymonitor.exe
```
**Output Example:**
```
[2024-01-15 14:30:25] H
[2024-01-15 14:30:25] e
[2024-01-15 14:30:25] l
[2024-01-15 14:30:25] l
[2024-01-15 14:30:25] o
[2024-01-15 14:30:26] [Enter]
```

#### Example 2: File Logging Mode
```bash
# Silent operation, logs saved to file
keymonitor.exe -o keylog.txt
```

#### Example 3: Hidden Window Mode
```bash
# Run in background, show message box after program ends
keymonitor.exe -h
```

#### Example 4: Combined Usage
```bash
# Hidden window with file logging (completely silent)
keymonitor.exe -h -o keylog.txt
```

#### Example 5: Show Help
```bash
# View complete usage instructions
keymonitor.exe --help
```

### Command Line Options

| Option | Parameters | Description |
|--------|------------|-------------|
| `-o` | `<filename>` | Output key logs to specified file (automatically enables silent mode) |
| `-h` | None | Hide console window, run in background |
| `--silent` | None | Silent mode, no output displayed |
| `--help` | None | Display help information |

### Program Exit
- **Exit Combination**: Press **Left Ctrl + Win** keys simultaneously
- **System Tray**: Right-click system tray icon, select "Exit"
- **Program Behavior**: Automatically saves all logs and cleans up resources upon exit

## Key Log Format

### Basic Format
```
[Timestamp] [Modifier Keys] + Key Name
```

### Key Representation Rules

#### Regular Keys
- Letters: `a`, `b`, `c`, ...
- Numbers: `1`, `2`, `3`, ...
- Symbols: `!`, `@`, `#`, ...

#### Special Keys
- Function Keys: `[F1]`, `[F2]`, ... `[F12]`
- Arrow Keys: `[Left]`, `[Right]`, `[Up]`, `[Down]`
- Control Keys: `[Enter]`, `[Space]`, `[Tab]`, `[Esc]`
- Editing Keys: `[Home]`, `[End]`, `[Insert]`, `[Delete]`

#### Modifier Keys
- Left Control Keys: `[LCtrl]`, `[LShift]`, `[LAlt]`, `[LWin]`
- Right Control Keys: `[RCtrl]`, `[RShift]`, `[RAlt]`, `[RWin]`
- General Control Keys: `[Ctrl]`, `[Shift]`, `[Alt]`, `[Win]`

#### Numpad Keys
```
[NUM] 0
[NUM] 1
[NUM] 2
[NUM] +
[NUM] -
[NUM] *
[NUM] /
[NUM] .
```

### Log Examples

#### Single Keys
```
[2024-01-15 14:30:25] H
[2024-01-15 14:30:25] e
```

#### Key Combinations
```
[2024-01-15 14:30:30] [LCtrl][LShift] + s
```

#### Numpad Input
```
[2024-01-15 14:30:35] [NUM] 1
[2024-01-15 14:30:35] [NUM] 2
[2024-01-15 14:30:35] [NUM] 3
```

## Output File Format

### File Structure
```plaintext
=== Keyboard Monitoring Session Started at 2024-01-15 14:30:25 ===
[2024-01-15 14:30:25] H
[2024-01-15 14:30:25] e
[2024-01-15 14:30:25] l
[2024-01-15 14:30:25] l
[2024-01-15 14:30:25] o
[2024-01-15 14:30:26] [Enter]
=== Keyboard Monitoring Session Ended at 2024-01-15 14:31:00 ===
```

### File Naming
- Default filename: `keylog.txt`
- Custom filename: Specified via `-o` parameter

## Program Architecture

### Core Components

#### 1. Keyboard Hook System
- **Global Hook**: Uses `SetWindowsHookEx(WH_KEYBOARD_LL)` to install low-level keyboard hook
- **Callback Function**: `LowLevelKeyboardProc` handles all keyboard events
- **Key Conversion**: `GetKeyName` converts virtual key codes to readable strings

#### 2. State Management
- **Key State**: Tracks press/release state of control keys
- **Combination Key Detection**: `IsExitCombination` detects exit combination
- **Running State**: Atomic variables ensure thread safety

#### 3. User Interface
- **Console Interface**: Real-time display of key logs
- **Hidden Window**: Receives system messages and handles tray icons
- **System Tray**: Minimized interface during background operation

#### 4. File System
- **Log Recording**: Uses `std::ofstream` for asynchronous file writing
- **File Management**: Automatic creation, appending, and closing of log files
- **Format Control**: Unified timestamp and format specifications

### Data Flow
```
Keyboard Input → Windows System → Global Hook → Callback Function → Key Processing → Output Display/File Logging
```

## Error Handling

### Common Errors and Solutions

#### Error 1: Insufficient Privileges
```
Keyboard hook installation failed! Error code: 5
Please run this program as administrator!
```
**Solution**: Run program as administrator

#### Error 2: File Access Failure
```
Unable to open log file: keylog.txt
```
**Solution**: Check file path permissions, or use a different filename

#### Error 3: Hook Installation Failure
```
Keyboard hook installation failed! Error code: 1428
```
**Solution**: Restart program or check for conflicts with other programs

### Error Code Reference
- `5`: Access denied, requires administrator privileges
- `87`: Parameter error
- `1428`: Hook requires valid module handle
- `1413`: Hook type error

## Performance Optimization

### Memory Management
- Use smart pointers to manage resources
- Avoid memory leaks through hook cleanup
- Efficient file buffer management

### Performance Characteristics
- Asynchronous file writing, doesn't affect keyboard responsiveness
- Lightweight message loop, low CPU usage
- Optimized string processing to avoid unnecessary copying

## Security Considerations

### Legal Use
1. **Authorized Devices Only**: Use only on devices for which you have legal authorization
2. **Informed Consent**: Ensure other users are aware of monitoring
3. **Data Protection**: Safeguard recorded sensitive information
4. **Legal Compliance**: Follow local privacy protection laws and regulations

### Security Recommendations
1. **Regular Cleanup**: Promptly delete unnecessary log files
2. **Encrypted Storage**: Encrypt sensitive log files
3. **Access Control**: Restrict access permissions to log files
4. **Usage Auditing**: Record program usage

## Development Guide

### Code Structure
```
keymonitor.cpp
├── Global Variable Declarations
├── Utility Functions
│   ├── GetCurrentTimeString()
│   ├── GetKeyName()
│   ├── GetModifierState()
│   └── IsExitCombination()
├── Hook System
│   └── LowLevelKeyboardProc()
├── Command Line Processing
│   └── ParseCommandLine()
├── Interface Management
│   ├── HideConsoleWindow()
│   ├── CreateHiddenWindow()
│   └── WindowProc()
├── Resource Management
│   ├── InitializeProgram()
│   ├── CleanupResources()
│   └── ShowExitInfo()
└── Main Function
    └── main()
```

#### Modifying Exit Combination
Modify detection logic in the `IsExitCombination()` function:
```cpp
// Change to other combination keys
if (vkCode == VK_LCONTROL && vkCode == VK_F12) {
    // Left Ctrl + F12 to exit
}
```

## Frequently Asked Questions

### Q1: Program fails to capture certain keys?
**A**: Some security software or system settings may block global hooks. Try disabling security software or using administrator privileges.

### Q2: Numpad numbers don't show [NUM] marker?
**A**: Ensure you're using the numpad for input. Numbers from the main keyboard area won't display the [NUM] marker.

### Q3: How to exit program in background?
**A**: Press Left Ctrl+Win combination, or right-click system tray icon and select "Exit".

### Q4: Where are log files located?
**A**: Default is `keylog.txt` in current directory, or location specified via `-o` parameter.

### Q5: How to verify program is running?
**A**: Check processes in Task Manager, or look for system tray icon.

## License Information

This project uses the GPLv3 (GNU General Public License v3.0) open-source license and is for educational and research use only. Commercial use or illegal monitoring is prohibited without permission. See the `LEGAL.md` file for details.

---

**Disclaimer**: This tool is for legal purposes only. The developers are not responsible for any misuse. Using this tool indicates your agreement to assume all related responsibilities.
