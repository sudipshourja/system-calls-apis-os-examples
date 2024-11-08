## Windows Specific System Calls
In Windows, performing file operations directly with system calls (often called **Windows API functions** for file I/O) requires using functions from the Windows API, such as `CreateFile`, `ReadFile`, `WriteFile`, and `CloseHandle`. Here’s how the equivalent program would look:

```c
#include <windows.h>
#include <stdio.h>

int main() {
    HANDLE fileHandle;
    DWORD bytesRead, bytesWritten;
    char buffer[100];

    // 1. **CreateFile()** System Call: Open the file
    fileHandle = CreateFile(
        "example.txt",            // Filename
        GENERIC_READ,             // Desired access (read-only)
        0,                        // Share mode (no sharing)
        NULL,                     // Security attributes
        OPEN_EXISTING,            // Opens the file only if it exists
        FILE_ATTRIBUTE_NORMAL,    // File attributes
        NULL                      // Template file
    );

    if (fileHandle == INVALID_HANDLE_VALUE) {
        printf("Error opening the file: %lu\n", GetLastError());
        return 1;
    }

    // 2. **ReadFile()** System Call: Read contents from the file
    if (!ReadFile(fileHandle, buffer, sizeof(buffer) - 1, &bytesRead, NULL)) {
        printf("Error reading the file: %lu\n", GetLastError());
        CloseHandle(fileHandle);
        return 1;
    }

    // Null-terminate the buffer to print as a string
    buffer[bytesRead] = '\0';

    // 3. **WriteFile()** System Call: Output file contents to standard output
    HANDLE stdoutHandle = GetStdHandle(STD_OUTPUT_HANDLE);
    if (!WriteFile(stdoutHandle, buffer, bytesRead, &bytesWritten, NULL)) {
        printf("Error writing to standard output: %lu\n", GetLastError());
        CloseHandle(fileHandle);
        return 1;
    }

    // 4. **CloseHandle()** System Call: Close the file
    CloseHandle(fileHandle);

    return 0;
}
```

### Explanation of System Calls in the Windows Code:

1. **`CreateFile()`**: Opens or creates a file and returns a `HANDLE`, which represents the file in the OS. We use `GENERIC_READ` for read-only access and `OPEN_EXISTING` to ensure it only opens if the file already exists.
   - **System Call**: `HANDLE CreateFile(LPCTSTR lpFileName, DWORD dwDesiredAccess, DWORD dwShareMode, LPSECURITY_ATTRIBUTES lpSecurityAttributes, DWORD dwCreationDisposition, DWORD dwFlagsAndAttributes, HANDLE hTemplateFile);`

2. **`ReadFile()`**: Reads data from the file into a buffer. It fills `bytesRead` with the number of bytes read.
   - **System Call**: `BOOL ReadFile(HANDLE hFile, LPVOID lpBuffer, DWORD nNumberOfBytesToRead, LPDWORD lpNumberOfBytesRead, LPOVERLAPPED lpOverlapped);`

3. **`WriteFile()`**: Writes data to the standard output handle (`STD_OUTPUT_HANDLE`).
   - **System Call**: `BOOL WriteFile(HANDLE hFile, LPCVOID lpBuffer, DWORD nNumberOfBytesToWrite, LPDWORD lpNumberOfBytesWritten, LPOVERLAPPED lpOverlapped);`

4. **`CloseHandle()`**: Closes the file handle, releasing resources.
   - **System Call**: `BOOL CloseHandle(HANDLE hObject);`

### Differences from POSIX and C Standard Library

- **Windows-Specific Syntax**: Windows system calls use `HANDLE` instead of `int` file descriptors.
- **Error Handling**: Windows API functions return `BOOL` or `HANDLE`, so you need to check for `INVALID_HANDLE_VALUE` or zero return values and retrieve error codes using `GetLastError()`.
- **Console Output**: `GetStdHandle(STD_OUTPUT_HANDLE)` provides a handle for the console output in Windows.

This code directly interacts with the OS through Windows-specific system calls, bypassing the C Standard Library, and thus is more Windows-dependent and less portable than POSIX or high-level API alternatives.
