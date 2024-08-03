# Github-Actions-for-c-based-42Projects
offer a github action implementation example for 42Network c based projects

```yml
---
  name: NORM AND FLAGS CHECKS

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
        - name: Checkout repository
          uses: actions/checkout@v2

        - name: install docker-compose
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
---
This is a GitHub Actions workflow file written in YAML. It defines two jobs: `check-norm` and `make-with-flags`.
---
1. `check-norm` job:
   - It runs on the latest version of Ubuntu.
   - It checks out your code using the `actions/checkout@v2` action.
   - It sets up Python 3.12 using the `actions/setup-python@v2` action.
   - It installs `setuptools` and `norminette` using pip.
   - It runs `norminette` to check your code.


---
2. `make-with-flags` job:
   - It also runs on the latest version of Ubuntu.
   - It depends on the `check-norm` job, meaning it will only run if `check-norm` completes successfully.
   - It checks out your code using the `actions/checkout@v2` action.
   - It installs `make` and `gcc` using `apt-get`.
   - It creates a `Makefile` for testing. The `Makefile` compiles all `.c` files found in the repository and links them into a single executable named `uniq_name_][`.
   - It runs `make` to build the executable.

This workflow is triggered on every push and pull request to any branch of your repository.

you can customize it by specifying on which branches to run this workflow :

```yaml
  on:
    push:
      branches:
        - main
        - and so on for example
    pull_request:
      branches:
        - main
        - and so on for example

```