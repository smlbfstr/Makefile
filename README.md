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
- `-r` flag for building in release mode without editing the config
