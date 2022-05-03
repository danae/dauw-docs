# Documentation for Dauw

This repository contains the **documentation for [Dauw](https://dauw.dev/)**, a high-level, statically typed, object-oriented, general-purpose scripting language, taking inspiration from [Lua](https://en.wikipedia.org/wiki/Lua_(programming_language)), [Nim](https://en.wikipedia.org/wiki/Nim_(programming_language)) and [Python](https://en.wikipedia.org/wiki/Python_(programming_language)).

## Building the docs

1. Make sure you have installed the dependencies:

  - `python` 3.8 or later
  - `sphinx` 4.4 or later, installable via `pip`
  - `make` 4.3 or later
  - `git`

2. Clone the source with `git`:

  ```bash
  $ git clone https://github.com/dauw-lang/dauw-docs.git
  $ cd dauw-docs
  ```

3. Build the documentation using the provided Makefile:

  ```bash
  $ make html
  ```

  The built documentation files will be placed in the `build/` directory relative to the repository root.

## License

Dauw and its associated repositories are distributed under the terms of the [GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.html). See [LICENSE.txt](LICENSE.txt) for details.
