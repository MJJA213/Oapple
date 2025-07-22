# Build Configurations

A `.xcconfig` file is used as a supplemental file to a specific build configuration. A configuration can have an associated xcconfig file which allows for additional changes to the target's build settings from outside the project editor.

Reference: https://help.apple.com/xcode/mac/10.0/#/dev745c5c974

## Structure

Each Target should have it's own configuration which can override included common configurations. Inverted it looks like this:

* RobloxBase.xcconfig
  * RobloxMobile.xcconfig
    * RobloxGlobal.xcconfig
    * RobloxTencent.xcconfig
    * RobloxVNGGames.xcconfig
    * RobloxAppExtension.xcconfig
      * RobloxNewExtension.xcconfig

### RobloxBase.xcconfig

Anything common across everything

### RobloxAppExtension.xcconfig

Mostly contains some default values for provisioning profiles such that new extensions can limit worry with set up, but still allows for override from specific extensions.

### Local.xcconfig

If `Configuration/Local.xcconfig` exists, the parent xcconfig will include it. This allows for unique customized for an engineer's local development environment for any experiments they wish to conduct.

A template is provided via `Configuration/Local.xcconfig.tmpl`. Copy the file to `Configuration/Local.xcconfig` and make any desired changes there. To prevent accidental commit to Git, it is set to be ignored.

When adding new `.xcconfig` file, to ensure local changes always supercede, the following *must* always be added as final line: `#include? "Local.xcconfig"`

## Provisioning Profile

As defined in https://roblox.atlassian.net/wiki/x/dwhMWw; thus requires some variables as defined below. This is easier and automatable when using Roblox-provided profiles, thus external profiles are best-guess while still allowing override during build-time.

Format: `${AppName}_${Platform}_${Distributor}_(Category:App|Ext_${ExtName})_${ProfileType}`

Variables:

* `RBX_PROVISIONING_PROFILE_APP_NAME`: currently only `RobloxApp`
* `RBX_PROVISIONING_PROFILE_PLATFORM`: currently only `iOS`
* `RBX_PROVISIONING_PROFILE_DISTRIBUTOR`: options: `Roblox`, `VNGGames`
* `RBX_PROVISIONING_PROFILE_TYPE`: as provided by Apple DevPortal: `Adhoc`, `AppStore, `Develop` (default)`

Note: Casing matters!

### Local vs. CI Systems

To allow for differences between local build environment (where Automatic signing is preferred), and CI systems (GitHubActions or TeamCity), `RBX_BUILD_ENV` can be used for different parameters.

To use "Automatic Signing", `PROVISIONING_PROFILE_SPECIFIER` must be blank/empty. Thus for local development, it is set up such that it doesn't resolve to anything.

## CodeSigning

All applicable profiles *must* use the same signing identity.

## New Extensions

Check with App Build team for setting up `RBX_PROVISIONING_PROFILE_EXT_NAME`

Use `NewExtensionTemplate.xcconfig` as starting point as it has some default values that make things easier.

## Future Expansion

This could expand to have different configs per Target/App for each of QA, Staging, Release, etc.
