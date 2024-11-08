## Standard C Library APIs
To simplify the same file operations example using a higher-level API, we can use the **C Standard Library** functions, which abstract away the underlying system calls. Here’s how it would look:

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    FILE *file;
    char buffer[100];

    // 1. **fopen()** API: Open the file
    file = fopen("example.txt", "r");
    if (file == NULL) {
        perror("Error opening the file");
        exit(EXIT_FAILURE);
    }

    // 2. **fread()** API: Read contents from the file
    size_t bytesRead = fread(buffer, 1, sizeof(buffer) - 1, file);
    if (bytesRead == 0 && ferror(file)) {
        perror("Error reading the file");
        fclose(file);
        exit(EXIT_FAILURE);
    }

    // Null-terminate the buffer to print as a string
    buffer[bytesRead] = '\0';

    // 3. **fputs()** API: Output file contents to standard output
    if (fputs(buffer, stdout) == EOF) {
        perror("Error writing to standard output");
        fclose(file);
        exit(EXIT_FAILURE);
    }

    // 4. **fclose()** API: Close the file
    fclose(file);

    return 0;
}
```

### Explanation of API Calls in the Code:

1. **`fopen()`**: Opens the file with specified permissions ("r" for read-only). It returns a pointer to a `FILE` object.
   - **API Call**: `FILE *fopen(const char *filename, const char *mode);`

2. **`fread()`**: Reads data from the file into a buffer.
   - **API Call**: `size_t fread(void *ptr, size_t size, size_t nmemb, FILE *stream);`

3. **`fputs()`**: Writes data to standard output, a simpler alternative to `write()` for string-based output.
   - **API Call**: `int fputs(const char *s, FILE *stream);`

4. **`fclose()`**: Closes the `FILE` pointer, releasing resources.
   - **API Call**: `int fclose(FILE *stream);`

### How the API Simplifies System Calls:

- **Simpler Syntax**: The `fopen`, `fread`, and `fclose` APIs make code more readable and portable, compared to `open`, `read`, and `close`.
- **Error Handling**: Standard Library functions (like `fread`) include their own error handling mechanisms, so you don’t need to check lower-level errors.
- **Automatic Buffering**: APIs like `fread` and `fwrite` handle buffering efficiently, optimizing performance.
- **Portability**: APIs allow the program to be portable across different OSes, as the library takes care of mapping to appropriate system calls on each platform.

This makes the code much more concise and easier to maintain, while also providing a more consistent and predictable interface for file operations.
