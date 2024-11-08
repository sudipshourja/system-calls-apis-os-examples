## Unix, Linux, MacOS System Specific System Calls
Here’s an example demonstrating how system calls work in a typical operating system. The example shows basic file operations in C using system calls on a Unix/Linux system:

```c
#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>  // For open()
#include <unistd.h> // For read(), write(), close()

int main() {
    int fileDescriptor;
    char buffer[100];
    ssize_t bytesRead;

    // 1. **open()** System Call: Open a file
    fileDescriptor = open("example.txt", O_RDONLY);
    if (fileDescriptor == -1) {
        perror("Error opening the file");
        exit(EXIT_FAILURE);
    }

    // 2. **read()** System Call: Read contents from the file
    bytesRead = read(fileDescriptor, buffer, sizeof(buffer) - 1);
    if (bytesRead == -1) {
        perror("Error reading the file");
        close(fileDescriptor);
        exit(EXIT_FAILURE);
    }

    // Null-terminate the buffer to print as a string
    buffer[bytesRead] = '\0';

    // 3. **write()** System Call: Output file contents to standard output
    if (write(STDOUT_FILENO, buffer, bytesRead) == -1) {
        perror("Error writing to standard output");
        close(fileDescriptor);
        exit(EXIT_FAILURE);
    }

    // 4. **close()** System Call: Close the file
    if (close(fileDescriptor) == -1) {
        perror("Error closing the file");
        exit(EXIT_FAILURE);
    }

    return 0;
}
```

### Explanation of System Calls in the Code:

1. **`open()`**: Opens a file and returns a file descriptor. In this case, we open `example.txt` in read-only mode.
   - **System Call**: `int open(const char *pathname, int flags);`
   
2. **`read()`**: Reads data from the file into a buffer.
   - **System Call**: `ssize_t read(int fd, void *buf, size_t count);`

3. **`write()`**: Writes data from a buffer to the standard output (console).
   - **System Call**: `ssize_t write(int fd, const void *buf, size_t count);`

4. **`close()`**: Closes the file descriptor, freeing up resources.
   - **System Call**: `int close(int fd);`

### Output

When run, this program will print the contents of `example.txt` to the console. Each of these operations (`open`, `read`, `write`, `close`) is a direct request to the operating system to perform actions on hardware-level resources (the file system in this case). 

System calls enable programs to request such services safely, without directly managing hardware, ensuring secure and controlled access.
