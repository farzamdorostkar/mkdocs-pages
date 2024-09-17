# Welcome to MkDocs

For full documentation visit [mkdocs.org](https://www.mkdocs.org).

## Commands

* `mkdocs new [dir-name]` - Create a new project.
* `mkdocs serve` - Start the live-reloading docs server.
* `mkdocs build` - Build the documentation site.
* `mkdocs -h` - Print help message and exit.

## Project layout

    mkdocs.yml    # The configuration file.
    docs/
        index.md  # The documentation homepage.
        ...       # Other markdown pages, images and other files.

### Codeblocks

Sample C code

```c
#include <stdio.h>

int main() {
    printf("Hello, World!\n");
    return 0;
}
```

Codeblock with title and line numbers

```c title="test.c" linenums="1"
#include <stdio.h>

int main() {
    printf("Hello, World!\n");
    return 0;
}
```