# bump
Command line tool for bumping project versions.

## Usage

Add a version.json file to your project.<br>
Use the command line utility in build scripts to increment the version number.<br>
It supports the semver terminology of major, minor and patch.<br>

Example:<br>

    bump --patch

This will load the version file, increment the patch number and write it back out.

## Installation

### MacOS

Make the file executable:

    chmod +x bump

To install the command globally:

    sudo cp bump /usr/local/bin
    