# Makefile
The makefile I use for all of my C projects

## Features
- Help menu with `make help`
- Debug and release build modes
- Configurable variables
- Automatic and recursive dependency resolution based on `#include`s (yes, you can embed source directories)
- `clean` subcommand for fast binary removal
- `run` subcommand for simple testing
- `all` subcommand for quick and easy rebuilding
- `-r` flag for building in release mode without editing the config (as per requested)

```make
# C makefile by Samuel B. Foster (github.com/smlbfstr)

# Config
compiler = gcc
debug_flags = -Wall -O2 -g
release_flags = -Wall -O2
debug = true

build_directory = bin
source_directory = src

# Automatic variables (DO NOT TOUCH ANYTHING BELOW THIS POINT)
target = $(build_directory)/$(notdir $(shell pwd))
sources = $(shell find $(source_directory) -name '*.c')
objects = $(sources:%=$(build_directory)/%.o)
dependencies = $(objects:.o=.d)
include_directories = $(shell find $(source_directory) -type d)
prefix = [\x1b[33mmake\x1b[0m]
compile_flags =

ifeq ($(findstring r,$(firstword -$(MAKEFLAGS))),r)
	debug = false
endif

ifeq ($(debug), true)
	compile_flags += $(debug_flags)
else
	compile_flags += $(release_flags)
endif

# Build rules
$(target): $(objects)
	@printf "$(prefix) \x1b[35mLinking objects\x1b[0m\n"
	@$(compiler) $(objects) -o $@ $(LDFLAGS)

$(build_directory)/%.c.o: %.c
	@mkdir -p $(dir $@)
	@printf "$(prefix) \x1b[32mCompiling\x1b[0m %s\n" $<
	@$(compiler) $(compile_flags) $(addprefix -I,$(include_directories)) -MMD -MP $(CFLAGS) -c $< -o $@
	@chmod go-w $@

-include $(dependencies)

# Subcommands
.PHONY: clean run all

clean:
ifeq ($(wildcard $(build_directory)/.),)
	@printf "$(prefix) \x1b[31mNothing to clean\x1b[0m\n"
else
	@printf "$(prefix) \x1b[31mCleaning binaries\x1b[0m\n"
	@rm -r $(build_directory)
endif

run:
	@printf "$(prefix) \x1b[34mRunning\x1b[0m %s\n" $(notdir $(target))
	@./$(target)

all: clean $(target) run

help:
	@printf "┌─────────────────────────────────────────┐\n"
	@printf "│         \x1b[1mC Makefile by smlbfstr\x1b[0m          │\n"
	@printf "├─────────────┬───────────────────────────┤\n"
	@printf "│ \x1b[32mmake\x1b[0m        │ Compiles your code        │\n"
	@printf "├─────────────┼───────────────────────────┤\n"
	@printf "│ \x1b[2;35mmake\x1b[0;35m -r ...\x1b[0m │ Enables release mode      │\n"
	@printf "├─────────────┼───────────────────────────┤\n"
	@printf "│ \x1b[2;31mmake\x1b[0;31m clean\x1b[0m  │ Removes build directory   │\n"
	@printf "├─────────────┼───────────────────────────┤\n"
	@printf "│ \x1b[2;34mmake\x1b[0;34m run\x1b[0m    │ Runs the compiled binary  │\n"
	@printf "├─────────────┼───────────────────────────┤\n"
	@printf "│ \x1b[2;33mmake\x1b[0;33m all\x1b[0m    │ Does everything           │\n"
	@printf "└─────────────┴───────────────────────────┘\n"
```
