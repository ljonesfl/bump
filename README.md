# bump
Command line tool for managing project versioning.

## Usage

Add a `.version.json` file to the root of your project.<br>
Use the command line utility in build scripts to increment the version number.<br>

## Strategies

To select a strategy use the --strategy option.

    bump --strategy semver|date

### Semver (default)
The default strategy is semver which looks like:

    {
        "strategy": "semver",
        "major": 0,
        "minor": 1,
        "patch": 4,
        "build": 15
    }

### Date
The date strategy always uses the current date as the version number.

    {
        "strategy": "date",
        "major": 2025,
        "minor": 3,
        "patch": 2,
        "build": 0
    }

## The build number

The build is a **lifetime counter**. Every mutating command advances it -- `--stamp`, `--build`, `--patch`, `--minor` and `--major` -- and nothing ever resets it.

That is deliberate. Apple requires `CFBundleVersion` to increase within a marketing version, and Google Play rejects outright any upload whose `versionCode` is not higher than every build already submitted. A counter that reset on each new version would be illegal on Play and merely tolerated on the App Store, so it does not reset.

Gaps do not matter. Bumping without releasing skips a number, which is normal for any build counter.

Apple marketing versions (`CFBundleShortVersionString`) may have at most three integers, so `bump` keeps the two values apart:

* **Marketing version:** `major.minor.patch` (for example `2026.9.18`)
* **Build number:** the integer `build` (for example `23`)

### What `bump` prints

The printed string doubles as the git tag and the `versionlog.md` heading.

* The **date strategy** always appends the build: `2026.9.18.23`. Every release on a given day shares one marketing version, so the build is the only thing that distinguishes them -- and because it always increases, the tag is always unique.
* The **semver strategy** stays three parts: `0.1.4`. Semver versions never repeat, so there is nothing to disambiguate.

For Flutter apps, `pubspec.yaml` is updated to `2026.9.18+23`.

## Examples

Show current version:

    bump
    
Typing bump by itself in a directory containing a version.json file will show the 
current version on the command line.

e.g.

0.1.4

Under the date strategy the build number is appended, so the same command shows:

2026.9.18.23
    
### Semver Strategy

Performing an increment action reads the file, increments the requested element and writes the file back 
out. This is ideal for automated release scripts.
   
build:

    bump --build
    
patch:
    
    bump --patch
    
minor:

    bump --minor
    
major:

    bump --major
    
This will load the version file, increment the patch number and write it back out.

### Date Strategy

The date strategy uses the --stamp command to set the version to the current date.

    bump --stamp

`--stamp` advances the build number as well as setting the date, so there is no need to check whether today has already been stamped. Two releases in one day give `2026.9.18.23` and then `2026.9.18.24`.

To advance the build number without touching the date:

    bump --build

### Creating a New File

    bump --new
    
Creates a new `.version.json` file is the current folder.

## Installation

### MacOS

Make the file executable:

    chmod +x bump

To install the command globally:

    sudo cp bump /usr/local/bin
    
## Usage with gitflow

A useful technique for creating a release using gitflow is to use the following command
from the develop branch:

     bump | xargs git flow release start
     
 This command will create a new git flow release using the current version number.
 
## Xcode and Flutter

If an Xcode project is detected (and this is not a Flutter app), `agvtool` sets:

* `MARKETING_VERSION` / `CFBundleShortVersionString` to the three-part marketing version
* `CURRENT_PROJECT_VERSION` / `CFBundleVersion` to the integer build

If `pubspec.yaml` contains a Flutter project, `bump` updates `version: x.y.z+build` instead of calling `agvtool`, so App Store Connect never receives a four-part marketing version. Flutter maps that `+build` to `CFBundleVersion` on iOS and to `versionCode` on Android.

`bump` reads both values back as well. If a manual archive pushed `CURRENT_PROJECT_VERSION` or `pubspec.yaml`'s `+build` past `.version.json`, the higher number becomes the floor and the counter carries on from there instead of going backwards.