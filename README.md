# bump
Command line tool for bumping project versions.

## Usage

Add a version.json file to the root of your project.<br>
Use the command line utility in build scripts to increment the version number.<br>
It supports the semver terminology of major, minor and patch.<br>

### Examples

Show current version:

    bump
    
#### Incrementing Version Elements

Performing an increment action reads the file, increments the requested element and writes the file back 
out. This is ideal for automated release scripts.
    
patch:
    
    bump --patch
    
minor:

    bump --minor
    
major:

    bump --major
    
This will load the version file, increment the patch number and write it back out.

#### Creating a New File

    bump --new
    
Creates a new version.json file is the current folder.

## Installation

### MacOS

Make the file executable:

    chmod +x bump

To install the command globally:

    sudo cp bump /usr/local/bin
    