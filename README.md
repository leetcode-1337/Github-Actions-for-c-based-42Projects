![Licence](https://img.shields.io/badge/License-MIT-blue.svg)
![ReadMe](https://img.shields.io/badge/ReadMe-018EF5?logo=readme&logoColor=fff&style=flat-square)
# GitHub Actions for C-based 42 Projects

This README offers examples of how to set up GitHub Actions for 42 Network C-based projects, including norminette checks and build processes. 

## Table of Contents

- [Example 1: Basic Norminette and Build Workflow](#example-1-basic-norminette-and-build-workflow)
- [Example 2: MinilibX Docker Integration Workflow](#example-2-minilibx-docker-integration-workflow)
- [Customization](#customization)
- [Docker Image](#docker-image)

## Example 1: Basic Norminette and Build Workflow

This example demonstrates a basic GitHub Actions workflow for C-based projects, focusing on norminette checks and building the project.

```yaml
---
name: NORM AND FLAGS CHECKS

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - '*'

jobs:
  check-norm:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: 3.12

      - name: Install dependencies
        run: |
          pip install setuptools norminette

      - name: Run norminette checks
        run: |
          norminette

  make-with-flags:
    runs-on: ubuntu-latest
    needs: check-norm
    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Install dependencies
        run: |
          sudo apt-get update
          sudo apt-get install -y make gcc

      - name: Create Makefile for testing
        run: |
          echo -e 'SRCS= $(shell find . -type f -name "*.c")\nINCLUDES= $(shell find . -type f -name "*.h")\nOBJS= $(SRCS:.c=.o)\nCC= cc\nCFLAGS= -Wall -Wextra -Werror\nNAME= uniq_name_][\nall: $(NAME)\n$(NAME): $(OBJS)\n\t$(CC) $(OBJS) -o $(NAME)\n%.o: %.c $(INCLUDES)\n\t$(CC) $(CFLAGS)  -c $< -o $@' > Makefile
      - name: Run Services
        run: |
          make
```

### Explanation

1. **`check-norm` Job:**
   - Runs on the latest Ubuntu.
   - Checks out the code using [actions/checkout@v2](https://github.com/actions/checkout).
   - Sets up Python 3.12 using [actions/setup-python@v2](https://github.com/actions/setup-python).
   - Installs `setuptools` and `norminette` using pip.
   - Runs `norminette` to check the code.

2. **`make-with-flags` Job:**
   - Runs on the latest Ubuntu and depends on the successful completion of `check-norm`.
   - Checks out the code using [actions/checkout@v2](https://github.com/actions/checkout).
   - Installs `make` and `gcc` using `apt-get`.
   - Creates a `Makefile` for testing, which compiles `.c` files and links them into an executable.
   - Runs `make` to build the executable.

## Example 2: MinilibX Docker Integration Workflow

This example demonstrates a GitHub Actions workflow for integrating MinilibX using Docker.

```yaml
---
name: NORM AND FLAGS CHECKS FOR MINILIBX PROJECT

on:
  push:
    branches:
      - '*'
  pull_request:
    branches:
      - '*'

jobs:
  check-norm:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: 3.12

      - name: Install dependencies
        run: |
          pip install setuptools norminette

      - name: Run norminette checks
        run: |
          norminette

  make-with-flags:
    runs-on: ubuntu-latest
    needs: check-norm
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Pull Docker image
        run: |
          docker pull ahlyelamine/minilibx:latest

      - name: Build and run project
        run: |
          docker run --rm -v ${{ github.workspace }}:/opt ahlyelamine/minilibx:latest
```

### Explanation

1. **`check-norm` Job:**
   - Runs on the latest Ubuntu.
   - Checks out the code using [actions/checkout@v2](https://github.com/actions/checkout).
   - Sets up Python 3.12 using [actions/setup-python@v2](https://github.com/actions/setup-python).
   - Installs `setuptools` and `norminette` using pip.
   - Runs `norminette` to check the code.

2. **`make-with-flags` Job:**
   - Runs on the latest Ubuntu and depends on the successful completion of `check-norm`.
   - Checks out the code using [actions/checkout@v3](https://github.com/actions/checkout).
   - Pulls the Docker image [`ahlyelamine/minilibx:latest`](https://hub.docker.com/r/ahlyelamine/minilibx).
   - Builds and runs the project using Docker with the workspace mounted to `/opt` in the container.

## Customization

To customize the branches that trigger these workflows, modify the `on` section:

```yaml
on:
  push:
    branches:
      - main
      - feature-branch
  pull_request:
    branches:
      - main
      - feature-branch
```

This workflow triggers on every push and pull request to the specified branches.

## Docker Image

The Docker image used in Example 2 is available on [Docker Hub](https://hub.docker.com/r/ahlyelamine/minilibx).
