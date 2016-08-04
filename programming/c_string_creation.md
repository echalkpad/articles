# C String Creation

[Original URL](http://maxsi.org/coding/c-string-creation.html)

> You often need to create a string in C. It's important to use the appropriate standard library functions for this, as this avoids a number of common error prone cases. This document shows how a...

You often need to create a string in C. It's important to use the appropriate standard library functions for this, as this avoids a number of common error prone cases. This document shows how a modern C program would best do this. Read the manual pages for the mentioned functions to learn more.

You should use the patterns described here. If you find a need to call strcpy() yourself, it's probable you're writing error prone code and one of the mentioned interfaces would be simpler. If you must roll your own string construction logic, memcpy() is often a better primitive to use.

Pick the least powerful interface below that solves the problem at hand.

## Creating a string of unbounded length

The common case is needing to construct a string whose length could be arbitrary. Functions should usually allocate the memory themselves, rather than using caller provided buffers.

### strdup

```
/* Portability: POSIX 2001 */
#include <string.h>
char *strdup(const char *str);
```

The strdup() function combines strlen(), malloc() and strcpy() and makes it really simple to duplicate an existing string. free() the pointer when done.

```
bool foo_set_bar(struct foo *foo, const char *value)
{
    char *new_bar = strdup(value);
    if (!new_bar)
        return false;
    free(foo->bar);
    foo->bar = new_bar;
    return true;
}
```

### strndup

```
/* Portability: POSIX 2008 */
#include <string.h>
char *strdup(const char *str, size_t n);
```

The strndup() function creates a copy of a prefix of the input string. It can also be used to create a substring if a valid offset is added to the input string.

```
char *substring(const char *str, size_t offset, size_t length)
{
    size_t check_offset = strnlen(str, offset);
    if (check_offset < offset)
        return NULL;
    return strndup(str + offset, length);
}
```

### asprintf

```
/* Portability: Scheduled for next POSIX, but present on all modern systems. */
#include <stdio.h>
int asprintf(char **strp, const char *fmt, ...);
```

The asprintf() function combines the expressive power of printf with automatically mallocing a suitable buffer. This is a reasonably powerful interface that can perform many normal operations, such as string concatenation, conversion of integers to strings, and so on. Like all printf functions, it cannot produced more than INT_MAX bytes and you must check the return value to detect this, but this is needed anyways to check the allocation.

```
    char *path;
    if (asprintf(&path, "%s/.config/%s", home_directory, program_name) < 0)
        return false;
    ...
    free(path);
```

### open_memstream

```
/* Portability: POSIX 2008 */
#include <stdio.h>
FILE *open_memstream(char **ptr, size_t *sizeloc);
```

The open_memstream() function combines the expressive power of stdio streams with automatic buffer resizing. This is even more powerful than asprintf as it can be used to generate strings in a turing complete fashion.

```
void recurse(FILE* fp, const char *str)
{
    if (!*str)
        return;
    fputc((unsigned char) *str, fp);
    recurse(fp, str + 1);
    fputc((unsigned char) *str, fp);
}

char *palindromize(const char *str)
{
    char* result;
    size_t result_size;
    FILE* fp = open_memstream(&result, &result_size);
    if (!fp)
        return NULL;
    recurse(fp, str);
    fflush(fp);
    int waserr = ferror(fp);
    fclose(fp);
    if (waserr) {
        free(result);
        return NULL;
    }
    return result;
}
```

## Creating a string of bounded length

Another common case is that you wish to construct a string whose length will never provably exceed a certain reasonable size.

### strlcpy

```
/* Portability: All modern systems, except glibc. */
#include <string.h>
size_t strlcpy(char *dest, const char *src, size_t dest_size);
```

The strlcpy() function copies a string to an existing destination buffer and truncates it if it's too long. It returns the length of the string (not including nul termination) it attempted to create. The destination is always nul terminated. Note that strlcpy will iterate the whole input string, not just the part it copeis, to calculate the return value. It thus runs in time proportional to the input string length, not the bounded output length. If this is a concern, it might be better to do a manual memcpy() call instead.

```
    char dest[20];
    if (strlcpy(dest, sizeof(dest), src) >= sizeof(dest)) {
        fprintf(stderr, "error: %s is too long\n", src);
        return false;
    }
```

### snprintf

```
/* Portability: C99 */
#include <stdio.h>
int snprintf(char *str, size_t size, const char *format, ...);
```

The snprintf() function printfs into the supplied buffer, truncating if the produced string is too long. The destination is always nul terminated. Like all printf interfaces, it can only produce INT_MAX bytes. If this might be a concern (unbounded input), you should check the return value of snprintf.

```
    char path[64];
    snprintf(path, sizeof(path), "%d.txt", i);
```

## Creating a fixed size string

A rare case is file formats with fixed size character arrays that aren't nessesarily nul terminated, but are zero padded if the string doesn't use the full array. This is the case with some file formats or filesystem directory entries.

### strncpy

```
/* Portability: C89\. */
#include <string.h>
char *strncpy(char *dest, const char *src, size_t n);
```

The strncpy() function copies the source string to the fixed size character array, truncating and not nul terminating if the source string is too long, and zero padding the remainder if too short. The function is poorly named as its name suggests the behavior of strlcpy(), and it is often abused as a bad replacement for strlcpy(), but it does have valid if rare use cases. It was originally designed to handle filesystem directory entries.

```
struct myfs_dirent
{
    myfs_ino_t inode;
    char name[MYFS_FILENAME_MAX];
};

void format_dirent(struct myfs_dirent *dirent,
 myfs_ino_t inode,
 const char* name)
{
    dirent->inode = inode;
    strncpy(dirent->name, sizeof(dirent->name), name);
}
```

Converting the fixed size string back to a normal C string is best done with memcpy.

```
    size_t len = strnlen(dirent->name, sizeof(dirent->name));
    char name[MYFS_FILENAME_MAX + 1];
    memcpy(name, dirent->name, len);
    name[len] = '\0';
```

Alternatively strndup() is useful if you wish a malloc()'d string.

```
bool print_dirent_name(struct myfs_dirent *dirent)
{
    char* name = strndup(dirent->name, sizeof(dirent->name));
    if (!name)
        return false;
    puts(name);
    free(name);
    return true;
}
```
