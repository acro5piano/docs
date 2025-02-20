<!--
This file is auto-generated and will be re-generated every time the docs are updated.
To modify it, go to its source at https://github.com/fastlane/fastlane/blob/master/fastlane/lib/fastlane/actions/upload_to_play_store.rb
-->

# upload_to_play_store


Upload metadata, screenshots and binaries to Google Play (via _supply_)




<p align="center">
  <img src="/img/actions/supply.png" width="250">
</p>

###### Command line tool for updating Android apps and their metadata on the Google Play Store

_supply_ uploads app metadata, screenshots, binaries, and app bundles to Google Play. You can also select tracks for builds and promote builds to production.

-------

<p align="center">
    <a href="#features">Features</a> &bull;
    <a href="#setup">Setup</a> &bull;
    <a href="#quick-start">Quick Start</a> &bull;
    <a href="#available-commands">Commands</a> &bull;
    <a href="#uploading-an-apk">Uploading an APK</a> &bull;
    <a href="#uploading-an-aab">Uploading an AAB</a> &bull;
    <a href="#images-and-screenshots">Images</a>
</p>

-------

## Features
- Update existing Android applications on Google Play via the command line
- Upload new builds (APKs and AABs)
- Retrieve and edit metadata, such as title and description, for multiple languages
- Upload the app icon, promo graphics and screenshots for multiple languages
- Have a local copy of the metadata in your git repository
- Retrieve version code numbers from existing Google Play tracks


## Setup

Setup consists of setting up your Google Developers Service Account

{!docs/includes/google-credentials.md!}

### Migrating Google credential format (from .p12 key file to .json)

In previous versions of supply, credentials to your Play Console were stored as `.p12` files. Since version 0.4.0, supply now supports the recommended `.json` key Service Account credential files. If you wish to upgrade:

- follow the <a href="#setup">Setup</a> procedure once again to make sure you create the appropriate JSON file
- update your fastlane configuration or your command line invocation to use the appropriate argument if necessary.
  Note that you don't need to take note nor pass the `issuer` argument anymore.


The previous p12 configuration is still currently supported.


## Quick Start

> Before using _supply_ to connect to Google Play Store, you'll need to set up your app manually first by uploading at least one build to Google Play Store. See [fastlane/fastlane#14686](https://github.com/fastlane/fastlane/issues/14686) for more info.

- `cd [your_project_folder]`
- `fastlane supply init`
- Make changes to the downloaded metadata, add images, screenshots and/or an APK
- `fastlane supply`

## Available Commands

- `fastlane supply`: update an app with metadata, a build, images and screenshots
- `fastlane supply init`: download metadata for an existing app to a local directory
- `fastlane action supply`: show information on available commands, arguments and environment variables

You can either run _supply_ on its own and use it interactively, or you can pass arguments or specify environment variables for all the options to skip the questions.

## Uploading an APK

To upload a new binary to Google Play, simply run

```no-highlight
fastlane supply --apk path/to/app.apk
```

This will also upload app metadata if you previously ran `fastlane supply init`.

To gradually roll out a new build use

```no-highlight
fastlane supply --apk path/app.apk --track beta --rollout 0.5
```

To set the in-app update priority level for a release, set a valid update priority (an integer value from 0 to 5) using option `in_app_update_priority`

```no-highlight
fastlane supply --apk path/app.apk --track beta --in_app_update_priority 3
```

### Expansion files (`.obb`)

Expansion files (obbs) found under the same directory as your APK will also be uploaded together with your APK as long as:

- they are identified as type 'main' or 'patch' (by containing 'main' or 'patch' in their file name)
- you have at most one of each type

If you only want to update the APK, but keep the expansion files from the previous version on Google Play use

```no-highlight
fastlane supply --apk path/app.apk --obb_main_references_version 21 --obb_main_file_size 666154207
```

or

```no-highlight
fastlane supply --apk path/app.apk --obb_patch_references_version 21 --obb_patch_file_size 666154207
```

## Uploading an AAB

To upload a new [Android application bundle](https://developer.android.com/guide/app-bundle/) to Google Play, simply run

```no-highlight
fastlane supply --aab path/to/app.aab
```

This will also upload app metadata if you previously ran `fastlane supply init`.

To gradually roll out a new build use

```no-highlight
fastlane supply --aab path/app.aab --track beta --rollout 0.5
```

To set the in-app update priority level for a release, set a valid update priority (an integer value from 0 to 5) using option `in_app_update_priority`

```no-highlight
fastlane supply --aab path/app.aab --track beta --in_app_update_priority 3
```

## Images and Screenshots

After running `fastlane supply init`, you will have a metadata directory. This directory contains one or more locale directories (e.g. en-US, en-GB, etc.), and inside this directory are text files such as `title.txt` and `short_description.txt`.

Inside of a given locale directory is a folder called `images`. Here you can supply images with the following file names (extension can be png, jpg or jpeg):

- `featureGraphic`
- `icon`
- `promoGraphic`
- `tvBanner`

You can also supply screenshots by creating directories within the `images` directory with the following names, containing PNGs or JPEGs:

- `phoneScreenshots/`
- `sevenInchScreenshots/` (7-inch tablets)
- `tenInchScreenshots/` (10-inch tablets)
- `tvScreenshots/`
- `wearScreenshots/`

You may name images anything you like, but screenshots will appear in the Play Store in alphanumerical filename order.
Note that these will replace the current images and screenshots on the play store listing, not add to them.

## Changelogs (What's new)

You can add changelog files under the `changelogs/` directory for each locale. The filename should exactly match the [version code](https://developer.android.com/studio/publish/versioning#appversioning) of the APK that it represents. You can also provide default notes that will be used if no files match the version code by adding a `default.txt` file. `fastlane supply init` will populate changelog files from existing data on Google Play if no `metadata/` directory exists when it is run.

```no-highlight
└── fastlane
    └── metadata
        └── android
            ├── en-US
            │   └── changelogs
            │       ├── default.txt
            │       ├── 100000.txt
            │       └── 100100.txt
            └── fr-FR
                └── changelogs
                    ├── default.txt
                    └── 100100.txt
```

## Track Promotion

A common Play publishing scenario might involve uploading an APK version to a test track, testing it, and finally promoting that version to production.

This can be done using the `--track_promote_to` parameter. The `--track_promote_to` parameter works with the `--track` parameter to command the Play API to promote existing Play track APK version(s) (those active on the track identified by the `--track` param value) to a new track (`--track_promote_to` value).

## Retrieve Track Release Names & Version Codes

Before performing a new APK upload you may want to check existing track version codes or release names, or you may simply want to provide an informational lane that displays the currently promoted version codes or release name for the production track. You can use the `google_play_track_version_codes` action to retrieve existing version codes for a package and track. You can use the `google_play_track_release_names` action to retrieve existing release names for a package and track.
For more information, see the `fastlane action google_play_track_version_codes` and `fastlane action google_play_track_release_names` help output.

## Migration from AndroidPublisherV2 to AndroidPublisherV3 in _fastlane_ 2.135.0

### New Options
- `:version_name`
  - Used when uploading with `:apk_path`, `:apk_paths`, `:aab_path`, and `:aab_paths`
  - Can be any string such (example: "October Release" or "Awesome New Feature")
  - Defaults to the version name in app/build.gradle or AndroidManifest.xml
- `:release_status`
  - Used when uploading with `:apk_path`, `:apk_paths`, `:aab_path`, and `:aab_paths`
  - Can set as  "draft" to complete the release at some other time
  - Defaults to "completed"
- `:version_code`
  - Used for `:update_rollout`, `:track_promote_to`, and uploading of meta data and screenshots
- `:skip_upload_changelogs`
  - Changelogs were previously included with the `:skip_upload_metadata` but is now its own option

### Deprecated Options
- `:check_superseded_tracks`
  - Google Play will automatically remove releases that are superseded now
- `:deactivate_on_promote`
  - Google Play will automatically deactivate a release from its previous track on promote

:

<hr />


upload_to_play_store ||
---|---
Supported platforms | android
Author | @KrauseFx



## 2 Examples

```ruby
upload_to_play_store
```

```ruby
supply # alias for "upload_to_play_store"
```





## Parameters

Key | Description | Default
----|-------------|--------
  `package_name` | The package name of the application to use | [*](#parameters-legend-dynamic)
  `version_name` | Version name (used when uploading new apks/aabs) - defaults to 'versionName' in build.gradle or AndroidManifest.xml | [*](#parameters-legend-dynamic)
  `version_code` | Version code (used when updating rollout or promoting specific versions) | [*](#parameters-legend-dynamic)
  `release_status` | Release status (used when uploading new apks/aabs) - valid values are completed, draft, halted, inProgress | [*](#parameters-legend-dynamic)
  `track` | The track of the application to use. The default available tracks are: production, beta, alpha, internal | `production`
  `rollout` | The percentage of the user fraction when uploading to the rollout track (setting to 1 will complete the rollout) | 
  `metadata_path` | Path to the directory containing the metadata files | [*](#parameters-legend-dynamic)
  `key` | **DEPRECATED!** Use `--json_key` instead - The p12 File used to authenticate with Google | [*](#parameters-legend-dynamic)
  `issuer` | **DEPRECATED!** Use `--json_key` instead - The issuer of the p12 file (email address of the service account) | [*](#parameters-legend-dynamic)
  `json_key` | The path to a file containing service account JSON, used to authenticate with Google | [*](#parameters-legend-dynamic)
  `json_key_data` | The raw service account JSON data used to authenticate with Google | [*](#parameters-legend-dynamic)
  `apk` | Path to the APK file to upload | [*](#parameters-legend-dynamic)
  `apk_paths` | An array of paths to APK files to upload | 
  `aab` | Path to the AAB file to upload | [*](#parameters-legend-dynamic)
  `aab_paths` | An array of paths to AAB files to upload | 
  `skip_upload_apk` | Whether to skip uploading APK | `false`
  `skip_upload_aab` | Whether to skip uploading AAB | `false`
  `skip_upload_metadata` | Whether to skip uploading metadata, changelogs not included | `false`
  `skip_upload_changelogs` | Whether to skip uploading changelogs | `false`
  `skip_upload_images` | Whether to skip uploading images, screenshots not included | `false`
  `skip_upload_screenshots` | Whether to skip uploading SCREENSHOTS | `false`
  `track_promote_to` | The track to promote to. The default available tracks are: production, beta, alpha, internal | 
  `validate_only` | Only validate changes with Google Play rather than actually publish | `false`
  `mapping` | Path to the mapping file to upload (mapping.txt or native-debug-symbols.zip alike) | 
  `mapping_paths` | An array of paths to mapping files to upload (mapping.txt or native-debug-symbols.zip alike) | 
  `root_url` | Root URL for the Google Play API. The provided URL will be used for API calls in place of https://www.googleapis.com/ | 
  `check_superseded_tracks` | **DEPRECATED!** Google Play does this automatically now - Check the other tracks for superseded versions and disable them | `false`
  `timeout` | Timeout for read, open, and send (in seconds) | `300`
  `deactivate_on_promote` | **DEPRECATED!** Google Play does this automatically now - When promoting to a new track, deactivate the binary in the origin track | `true`
  `version_codes_to_retain` | An array of version codes to retain when publishing a new APK | 
  `changes_not_sent_for_review` | Indicates that the changes in this edit will not be reviewed until they are explicitly sent for review from the Google Play Console UI | `false`
  `in_app_update_priority` | In-app update priority for all the newly added apks in the release. Can take values between [0,5] | 
  `obb_main_references_version` | References version of 'main' expansion file | 
  `obb_main_file_size` | Size of 'main' expansion file in bytes | 
  `obb_patch_references_version` | References version of 'patch' expansion file | 
  `obb_patch_file_size` | Size of 'patch' expansion file in bytes | 
  `ack_bundle_installation_warning` | Must be set to true if the bundle installation may trigger a warning on user devices (e.g can only be downloaded over wifi). Typically this is required for bundles over 150MB | `false`

<em id="parameters-legend-dynamic">* = default value is dependent on the user's system</em>


<hr />



## Documentation

To show the documentation in your terminal, run
```no-highlight
fastlane action upload_to_play_store
```

<hr />

## CLI

It is recommended to add the above action into your `Fastfile`, however sometimes you might want to run one-offs. To do so, you can run the following command from your terminal

```no-highlight
fastlane run upload_to_play_store
```

To pass parameters, make use of the `:` symbol, for example

```no-highlight
fastlane run upload_to_play_store parameter1:"value1" parameter2:"value2"
```

It's important to note that the CLI supports primitive types like integers, floats, booleans, and strings. Arrays can be passed as a comma delimited string (e.g. `param:"1,2,3"`). Hashes are not currently supported.

It is recommended to add all _fastlane_ actions you use to your `Fastfile`.

<hr />

## Source code

This action, just like the rest of _fastlane_, is fully open source, <a href="https://github.com/fastlane/fastlane/blob/master/fastlane/lib/fastlane/actions/upload_to_play_store.rb" target="_blank">view the source code on GitHub</a>

<hr />

<a href="/actions/"><b>Back to actions</b></a>
