# Youtube-Dl Launcher ([YDL](https://github.com/pukkandan/ydl#ydl))

A Simple GUI Launcher for [my own fork](https://github.com/pukkandan/youtube-dl) of [youtube-dl](https://github.com/ytdl-org/youtube-dl) on Windows using [AHK](https://www.autohotkey.com).

## Prerequisites

* [AHK1.1.\*](https://www.autohotkey.com/download/)
* [Python3.2+](https://www.python.org/downloads/windows/)

#### Optional
* [ffmpeg](https://www.gyan.dev/ffmpeg/builds/) - Required for mergring audio and video files together, extracting audio, embedding metadata etc
* [aria2c](https://github.com/aria2/aria2/releases) - Provides faster downloads for all non-DASH videos.

## How to Use

1. Install [AHK1.1.\*](https://www.autohotkey.com/download/) and [Python3.2+](https://www.python.org/downloads/windows/).
1. Download the [optional](#optional) prerequisites if needed.
1. Download either the entire repository or just [update.ahk](https://github.com/pukkandan/YDL/blob/master/update.ahk) and place the file(s) in an empty folder.
1. Either make sure the all the prerequisites (except AHK) are available in `PATH` variable, or copy them to the script directory.
1. Run [update.ahk](https://github.com/pukkandan/YDL/blob/master/update.ahk) with AHK. This will download the latest version of both the script and `youtube-dl` to the same folder.
1. Run [YDL.ahk](https://github.com/pukkandan/YDL/blob/master/ydl.ahk) with AHK.

## Profiles

Any **.conf** file present in the script directory will be available as a profile. For more information on `youtube-dl` configuration files, refer to the [official documentation](https://github.com/ytdl-org/youtube-dl#configuration). 

The script makes two additional variables available in these configuration files:
* `%ydl_dir%` which expands to the **script directory** AND
* `%ydl_home%` which expands to the **path given in the gui**.

The profile **MUST** define the file naming pattern using the `ydl_home` variable. Eg: `--output "%ydl_home%/%(uploader)s - %(title)s [%(id)s] (%(resolution)s).%(ext)s"`. Otherwise, the file will be downloaded to the path given in the profile (or in the script directory if either a relative path or no path is given).

It is recomended that you make your own profiles instead of editing the exising ones since updates will reset the profile back to default. (This issue will be fixed in a later version)

## Configuration

All settings are saved in the file [ydl.ahk.ini](https://github.com/pukkandan/YDL/blob/master/ydl.ahk.ini). The settings are read when the script starts and saved everytime a download sucessfully launches. All options that is to be passed into `youtube-dl` directly must be properly escaped. The only exception is that `%(` doesn't need to be encoded as `%%(` in **\[Hidden\]/name**

### \[Options\]

Key | Saves the last used value of
------------ | -------------
**prof**|[Profile](#profiles)
**res**|Resolution
**sign**|Sign prefixing Resolution (`>=`/`<=`)
**path**|Path
**opts**|[Options](https://github.com/ytdl-org/youtube-dl#options) to be passed to `youtube-dl`


### \[Hidden\]

These options are not changable in the GUI.

Key | Description
------------ | -------------
**log**| Whether to log the command to [ydl.ahk.log](https://github.com/pukkandan/YDL/blob/master/ydl.ahk.log) (`1`=Log, `0`=Don't log).
**meta**| Whether to embed metadata (`1`=Embed, `0`=Don't embed).
**dash**| Whether to download DASH formats (`1`=Embed, `0`=Don't embed).
**name**| [Naming pattern](https://github.com/ytdl-org/youtube-dl#output-template) to use when no profile is given.
**opts**| These [options](https://github.com/ytdl-org/youtube-dl#options) will be additionally passed to `youtube-dl`. Only the options given in **\[Options\]/opts** may override these.

## FAQ

#### What is the field called Options?

This field accepts all the same [options](https://github.com/ytdl-org/youtube-dl#options) as `youtube-dl`. These options override the settings generated by the script which in turn overrides the options in the [profile](#profiles).

#### The download never completes.

The download process is wrapped in [retry.cmd](https://github.com/pukkandan/YDL/blob/master/retry.cmd) which will keep retrying the download until `youtube-dl` exits without any error. So, if any of the videos is not downloadable (due to the video being removed/private/age-gated, url being wrong, etc), the download will never complete. You can simply break/close the terminal window to stop the download process.

#### The video is getting downloaded to the wrong folder.

The path given in the GUI is simply a guideline for the profile on where to download the video. The profile must define the file naming pattern using this information. See [Profiles](#profiles) for details

#### Why use a custom fork of `youtube-dl`?

The [format selection](https://github.com/pukkandan/YDL/blob/a68f53ad6f2286e516a773f701f71b291884bd01/ydl.ahk#L62) is done by the script using the option `--format-sort` [that I implemented](https://github.com/ytdl-org/youtube-dl/pull/25959) but has not been (and probably never will be) merged with the main repo. Achieving the same effect using the [main repo](https://github.com/ytdl-org/youtube-dl) of `youtube-dl` is unnecessarily tedious and prone to error.

#### Can I use other forks of `youtube-dl` with this?

Other forks of `youtube-dl` can be used as long as [this pull request](https://github.com/pukkandan/youtube-dl/pull/5) is merged into it.

If you are using a different fork, you have to change `https://codeload.github.com/pukkandan/youtube-dl/zip/master` in [update.ahk](https://github.com/pukkandan/YDL/blob/a68f53ad6f2286e516a773f701f71b291884bd01/update.ahk#L7) to the url of the zip file of your desired fork. You might also need to change `"%~dp0\youtube-dl-master\youtube_dl\__main__.py"` in [youtube-dl.cmd](https://github.com/pukkandan/YDL/blob/a68f53ad6f2286e516a773f701f71b291884bd01/youtube-dl.cmd#L2) to the correct path

#### How do I update `YDL`/`youtube-dl`?

There is no auto-update in the current version of `YDL`. To update, you have to manually run [update.ahk](https://github.com/pukkandan/YDL/blob/master/update.ahk). Note that this updates both the script and `youtube-dl`, but not any other dependancies.
