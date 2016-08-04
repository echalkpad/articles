# bndw/pick

[Original URL](https://github.com/bndw/pick)

> Python README.md A tiny password manager for OS X and Linux. Features JSON formatted data Environment Variable configuration GPG

<span class="language-color">Python</span>

## README.md

A tiny password manager for OS X and Linux.

[![demo](https://github.com/bndw/pick/raw/master/demo.gif)](https://github.com/bndw/pick/raw/master/demo.gif)

## [](https://github.com/bndw/pick#features)Features

- JSON formatted data
- Environment Variable configuration
- GPG for encryption

## [](https://github.com/bndw/pick#dependencies)Dependencies

- GPG

  ```
  # Linux
  sudo apt-get install gnupg
  ```

- xclip (Linux only)

  ```
  # Linux
  sudo apt-get install xclip
  ```

## [](https://github.com/bndw/pick#installation)Installation

1. Clone the repository

  ```
  git clone https://github.com/bndw/pick.git && cd pick
  ```

2. Copy the `pick` executable into your PATH

3. Initialize pick

## [](https://github.com/bndw/pick#usage)Usage

- Write a password (interactive)

- Read a password by alias

- Read all passwords

## [](https://github.com/bndw/pick#advanced)Advanced

### [](https://github.com/bndw/pick#environment-variables)Environment Variables

- Don't want to type in the password everytime?

  ```
  export PICK_TOKEN=<PASSWORD HERE>
  ```

- Don't ever want your passwords printed to stdout?

  ```
  export PICK_CONFIG='{"silent":true}'
  ```

- Want to print additonal metadata along with passwords?

  ```
  export PICK_CONFIG='{"verbose":true}'
  ```
