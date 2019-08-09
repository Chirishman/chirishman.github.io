---
layout: post
title: "Advanced Audiobook Generation"
excerpt: "Authoring Richly Formatted Audiobooks"
tags: [
    Audiobooks,
    OpenSource,
    HowTo,
    Stubbornness
]
modified: 2019-07-12 21:33:00
date: 2019-07-12 21:40:00
comments: true
image:
  feature: banner.jpg
  thumb: /postthumbs/2019-07-12-Advanced-Audiobook-Generation.svg
published: true
---
{% include _toc.html %}

## Why Bother

If you're anything like me (and I recognize that most people aren't) then you listen to a lot of audiobooks in a year. As technology advances though, there can be a temptation to either rebuy audiobooks which you bought on other media like CD in the past or to skip content that is only available in older formats due to inconveniences like lack of bookmarking, inability to skip to a desired chapter, and the player not remembering your position between sessions.

Now I've not been one to let these things stop me, I figure there always has to be a way to author advanced audiobooks if you try hard enough. I know it's possible to convert books in a very basic way simply by merging all of the CD tracks into one file and converting it to M4A (AAC) and renaming the file extension and I've done so in the past. Unfortunately the only thing that helps with is the basic player position memory and iTunes doesn't include a way to merge the CD tracks into a single file. There used to be applications for converting and adding chapters/art but unfortunately they all depended on old versions of iTunes and on Quicktime which can no longer even be installed on Windows.

Fortunately, OpenSource is here to rescue us. The rest of this post will be a full soup-to-nuts writeup on setting up and using the excellent [m4b-tool](https://github.com/sandreas/m4b-tool) including advice on sourcing chapter titles.

## Setup

### Ubuntu

For my implementation I created an Ubuntu VM because the Windows instructions/install path looked... less than optimal. Honestly my advice is that it's simpler/easier to spin a Ubuntu VM using Microsoft's new **VM QuickCreate** tool than to mess around with installing PHP and using non-official releases of libraries. If I find myself overburdened with free time at some point maybe I'll write a [Chocolatey](https://chocolatey.org) package that deals with all of that.

1. Run the installation command from the m4b-tool readme

```Bash
# install all dependencies
sudo apt install ffmpeg mp4v2-utils fdkaac php-cli

# install / upgrade m4b-tool
sudo wget https://github.com/sandreas/m4b-tool/releases/download/v.0.4.0/m4b-tool.phar -O /usr/local/bin/m4b-tool && sudo chmod +x /usr/local/bin/m4b-tool

# check installed m4b-tool version
m4b-tool --version
```

**NB:** On Ubuntu in order to use the higher quality *fdk-aac* encoder you need to manually recompile *ffmpeg*. For reference on that see the [m4b-tool readme](https://github.com/sandreas/m4b-tool/blob/master/README.md) or [this guide](https://trac.ffmpeg.org/wiki/CompilationGuide/Ubuntu)

### Mac

Because one of the target readers of this post is a Mac user I will also include the setup instructions for OSX.

1. Start by installing the [Homebrew package manager](https://brew.sh) for OSX. You can do so most easily by running the below command (from their official website) in a Terminal prompt.

```Bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

2. Copy and paste these installation commands into your prompt (from the [m4b-tool readme](https://github.com/sandreas/m4b-tool/blob/master/README.md)) in order to use the **brew** command to perform the installs.

```Bash
# install ffmpeg with best audio quality options
brew tap varenc/ffmpeg
brew tap-pin varenc/ffmpeg
brew uninstall ffmpeg
brew install ffmpeg --with-chromaprint --with-fdk-aac

# tap m4b-tool repository
brew tap sandreas/tap
brew tap-pin sandreas/tap

# install m4b-tool
brew install m4b-tool

# check installed m4b-tool version
m4b-tool --version
```

## Workflow

### 1. Organize the files

Put your source audio file(s) all into a folder and use the book title as the name for that folder. Keep track of where this is as you will need this full path later and will be adding new files which you create to this location. Make sure that all files sort in the correct order (add padding zeroes to the front of any track numbers if necessary so that "ABCD 20" does not precede "ABCD 3" when sorted by name).

### 2. Find the Chapter Names/Count of Chapters

There are a few websites that list chapter names and your best source will vary. I've had great luck recently with [Type40 Books](http://www.type40.com/Books/index.asp)

[![Example Chapter List](/images/articleresources/2019-07-12-Advanced-Audiobook-Generation/Lord-Fouls-Bane-Chapters.png)](http://type40.com/books/bChapters.asp?ID=19)

Fan wikis for a given series can also be a good source of chapter names, though often a bit more digital legwork is involved. Obviously if you have a physical or ebook copy of the book in question you can use the Table of Contents and sometimes you can get lucky and the pages included in Amazon's "Look Inside" feature will include a ToC as well.

### 3. Find a Cover Image

Googling mostly works, but always try to find the highest quality copy available. You can often source a good quality cover image from product pages or fan sites.

Once found, name the file `cover.jpg` and place it in the directory with the source files.

### 4. Create a Description  file

Save the book's description into a new text file called `description.txt` and save it also into the source directory. This file __must__ be saved as a UTF-8 text file according to the readme.

### 5. Merge the Audio Files

Use the m4b-tool to merge all of the files into one. The first path (directly after the merge command) should be the path to the __folder__ which contains all of the individual input audio tracks. Second path should be where you want the file to come out, including the filename and extension. Make sure not to accidentally overwrite anything here by making sure that nothing already exists with that desired name in the folder in question.

```Bash
m4b-tool merge --name="Book Title" "/path/to/my-audio-book/" --output-file="/path/to/merged.m4b"
```

A useful bonus tip: Here is an example from the m4b-tool documentation outlining how to make sure that books in a series are sorted correctly in your media player rather than alphabetized. The `--series="Series Name" --series-part="1"` portion from the example below can be added to whatever you have already worked up for the above command

```Bash
m4b-tool merge --name="Harry Potter and the Chamber of Secrets" --series="Harry Potter" --series-part="2" --output-file="/path/to/output/Harry Potter and the Chamber of Secrets.m4b" "/path/to/input/Harry Potter and the Chamber of Secrets"
```

### 6. Find the Chapter Timecodes

This is the most time consuming part. Open the merged audio file (Recommended: Use [Audacity](https://www.audacityteam.org) or some other audio editing tool which both shows the timecode down to three decimal places and which lets you visually see the waveform which makes it much easier to find the dead air/quiet spots which are usually the chapter breaks).

[![Identify Chapters](/images/articleresources/2019-07-12-Advanced-Audiobook-Generation/identify-chapters_550.png)](/images/articleresources/2019-07-12-Advanced-Audiobook-Generation/identify-chapters.png)

When selecting the time signature for a chapter marker, wherever possible try to put it at least a few milliseconds and up to maybe half a second into the silence before the reading of the chapter heading begins. Obviously this will vary depending on how generous the chapter pauses are.

[![Scrub for Precise Time Signature](/images/articleresources/2019-07-12-Advanced-Audiobook-Generation/scrub-for-precise-time-signature_550.png)](/images/articleresources/2019-07-12-Advanced-Audiobook-Generation/scrub-for-precise-time-signature.png)

I find it easiest to keep track of the timecodes and chapters in a spreadsheet as shown below (obviously adding a column for the fractional seconds). As you can see I've added an "intro" and "outro" section, these really help listeners to do things like skip back to the start of chapter 1 without having to listen to the "opening credits" portion again and the marking of any outro credits and/or preview of the next book can aid users in knowing how much actual book they have left.

[![Example Excel Table](/images/articleresources/2019-07-12-Advanced-Audiobook-Generation/audiobook-chapter-offsets_550.png)](/images/articleresources/2019-07-12-Advanced-Audiobook-Generation/audiobook-chapter-offsets.png)

### 7. Prepare the Timecodes

Once the timecodes have been collected they need to be saved to a new text file named `chapters.txt` formatted like so:

```Text
00:00:00.000 Intro
00:04:19.153 This is
00:09:24.078 A way to add
00:14:34.500 Chapters manually
```

You now have two options.

a) Put the `chapters.txt` file into the directory with your merged m4b file and run the below command, appending the chapter info to the existing file

```Bash
mp4chaps -i /path/to/merged.m4b
```

b) Put the `chapters.txt` file into source folder with the other text files and your master files and re-run your command from Step 5, performing a clean export all over again - this time with chapter data. The benefit here is that you can then zip up and archive the whole source folder for this book and know that you can use it again with this tool in future with no further fiddling needed should you ever need the data.

## Other notes

### Performance

For best performance you should also append the flag `--jobs=X` where X is equal to or less than the number of CPU cores in your computer. Preferably equal to, though that will likely use all available resources, preventing you from doing other things with your computer while it runs.

```Text
      --jobs[=JOBS]                              Specifies the number of jobs (commands) to run simultaneously [default: 1]
```

### Metadata

Aside from the chapter names you will probably want to set things like the copyright info, the author, who encoded it et cetera. Here is a list of the metadata commands. Note: Some of these such as `--cover` and `--description` should be skipped in most cases when using the workflow above as they are being set already from the files which you are including in the folder with the source files. Usage for all of these is `--setting="VALUEHERE"` and can simply be appended to the main `m4b-tool` command.

```Text
--name[=NAME]
--sortname[=SORTNAME]
--album[=ALBUM]
--sortalbum[=SORTALBUM]
--artist[=ARTIST]
--sortartist[=SORTARTIST]
--genre[=GENRE]
--writer[=WRITER]
--albumartist[=ALBUMARTIST]
--year[=YEAR]
--cover[=COVER]
--description[=DESCRIPTION]
--longdesc[=LONGDESC]
--comment[=COMMENT]
--copyright[=COPYRIGHT]
--encoded-by[=ENCODED-BY]
--series[=SERIES]
--series-part[=SERIES-PART]
```

### Quality Settings

Listed here are the main quality\output settings broken into categories. There are a few you shouldn't need to touch, a few only used when trying to compress/shrink the book as much as possible to fit older/smaller devices, and a few which should definitely be considered/set.

#### Don't Touch

```Text
--audio-format[=AUDIO-FORMAT]
--audio-channels[=AUDIO-CHANNELS]
```

The `--audio-format` setting should not need to be touched in normal operation.

You shouldn't ever need to set the `--audio-channels` unless you are trying to mix stereo down to mono to save space.

#### Shrink Settings

```Text
--audio-profile[=AUDIO-PROFILE]
--adjust-for-ipod
```

The `--audio-profile` setting is used to select presets for extremely low bit rate/small files. Ubuntu users: this largely ends up being unplayable on a lot of players unless the fdkaac encoder is used.

The `--adjust-for-ipod` setting should likewise be ignored in most scenarios. Old school iPods have a problem playing files which are too long/large. This works around that problem by lowering the quality as far as possible in order to make the file playable.

#### Pay Attention

```Text
--audio-bitrate[=AUDIO-BITRATE]            audio bitrate, e.g. 64k, 128k, ... [default: ""]
--audio-samplerate[=AUDIO-SAMPLERATE]      audio samplerate, e.g. 22050, 44100, ... [default: ""]
--audio-codec[=AUDIO-CODEC]                audio codec, e.g. libmp3lame, aac, ... [default: ""]
```

The bitrate, samplerate and codec settings should be set carefully and the bitrate and samplerate should, where possible, be set to match your source file to maintain maximum possible quality. For best quality the audio codec should be set to use fdk-aac as previously mentioned.

```Text
--no-conversion                            skip conversion (destination file uses same encoding as source - all encoding specific options will be ignored)
```

This parameter is a *flag*, also known as a *switch*. A **flag** or **switch** means that no `="whatever"` value is required, the presence or absence of this in a command is itself a setting.

This flag  should be used when your source files are already in the correct codec (here AAC) and/or the file has already been merged together and you simply need to add/change some metadata such as the description or some chapter offsets.

### Full Command Reference

```Text
Description:
  Merges a set of files to one single file

Usage:
  merge [options] [--] <input> [<more-input-files>...]

Arguments:
  input                                          Input file or folder
  more-input-files                               Other Input files or folders

Options:
      --logfile[=LOGFILE]                        file to dump all output [default: ""]
      --debug                                    enable debug mode - sets verbosity to debug, logfile to m4b-tool.log and temporary files are not deleted
  -f, --force                                    force overwrite of existing files
      --no-cache                                 do not use cached values and clear cache completely
      --ffmpeg-threads[=FFMPEG-THREADS]          specify -threads parameter for ffmpeg [default: ""]
      --platform-charset[=PLATFORM-CHARSET]      Convert from this filesystem charset to utf-8, when tagging files (e.g. Windows-1252, mainly used on Windows Systems) [default: ""]
      --ffmpeg-param[=FFMPEG-PARAM]              Add argument to every ffmpeg call, append after all other ffmpeg parameters (e.g. --ffmpeg-param="-max_muxing_queue_size" --ffmpeg-param="1000" for ffmpeg [...] -max_muxing_queue_size 1000) (multiple values allowed)
  -a, --silence-min-length[=SILENCE-MIN-LENGTH]  silence minimum length in milliseconds [default: 1750]
  -b, --silence-max-length[=SILENCE-MAX-LENGTH]  silence maximum length in milliseconds [default: 0]
      --max-chapter-length[=MAX-CHAPTER-LENGTH]  maximum chapter length in seconds - its also possible to provide a desired chapter length in form of 300,900 where 300 is desired and 900 is max - if the max chapter length is exceeded, the chapter is placed on the first silence between desired and max chapter length [default: "0"]
      --audio-format[=AUDIO-FORMAT]              output format, that ffmpeg will use to create files [default: "m4b"]
      --audio-channels[=AUDIO-CHANNELS]          audio channels, e.g. 1, 2 [default: ""]
      --audio-bitrate[=AUDIO-BITRATE]            audio bitrate, e.g. 64k, 128k, ... [default: ""]
      --audio-samplerate[=AUDIO-SAMPLERATE]      audio samplerate, e.g. 22050, 44100, ... [default: ""]
      --audio-codec[=AUDIO-CODEC]                audio codec, e.g. libmp3lame, aac, ... [default: ""]
      --audio-profile[=AUDIO-PROFILE]            audio profile, when using extra low bitrate - valid values (mono, stereo): aac_he, aac_he_v2  [default: ""]
      --adjust-for-ipod                          auto adjust bitrate and sampling rate for ipod, if track is to long (may lead to poor quality)
      --name[=NAME]                              provide a custom audiobook name, otherwise the existing metadata will be used [default: ""]
      --sortname[=SORTNAME]                      provide a custom audiobook name, that is used only for sorting purposes [default: ""]
      --album[=ALBUM]                            provide a custom audiobook album, otherwise the existing metadata for name will be used [default: ""]
      --sortalbum[=SORTALBUM]                    provide a custom audiobook album, that is used only for sorting purposes [default: ""]
      --artist[=ARTIST]                          provide a custom audiobook artist, otherwise the existing metadata will be used [default: ""]
      --sortartist[=SORTARTIST]                  provide a custom audiobook artist, that is used only for sorting purposes [default: ""]
      --genre[=GENRE]                            provide a custom audiobook genre, otherwise the existing metadata will be used [default: ""]
      --writer[=WRITER]                          provide a custom audiobook writer, otherwise the existing metadata will be used [default: ""]
      --albumartist[=ALBUMARTIST]                provide a custom audiobook albumartist, otherwise the existing metadata will be used [default: ""]
      --year[=YEAR]                              provide a custom audiobook year, otherwise the existing metadata will be used [default: ""]
      --cover[=COVER]                            provide a custom audiobook cover, otherwise the existing metadata will be used
      --description[=DESCRIPTION]                provide a custom audiobook short description, otherwise the existing metadata will be used
      --longdesc[=LONGDESC]                      provide a custom audiobook long description, otherwise the existing metadata will be used
      --comment[=COMMENT]                        provide a custom audiobook comment, otherwise the existing metadata will be used
      --copyright[=COPYRIGHT]                    provide a custom audiobook copyright, otherwise the existing metadata will be used
      --encoded-by[=ENCODED-BY]                  provide a custom audiobook encoded-by, otherwise the existing metadata will be used
      --series[=SERIES]                          provide a custom audiobook series, this pseudo tag will be used to auto create sort order (e.g. Harry Potter or The Kingkiller Chronicles)
      --series-part[=SERIES-PART]                provide a custom audiobook series part, this pseudo tag will be used to auto create sort order (e.g. 1 or 2.5)
      --skip-cover                               skip extracting and embedding covers
      --fix-mime-type                            try to fix MIME-type (e.g. from video/mp4 to audio/mp4) - this is needed for some players to prevent video window
  -o, --output-file=OUTPUT-FILE                  output file
      --include-extensions[=INCLUDE-EXTENSIONS]  comma separated list of file extensions to include (others are skipped) [default: "aac,alac,flac,m4a,m4b,mp3,oga,ogg,wav,wma,mp4"]
  -m, --musicbrainz-id=MUSICBRAINZ-ID            musicbrainz id so load chapters from
      --mark-tracks                              add chapter marks for each track
      --no-conversion                            skip conversion (destination file uses same encoding as source - all encoding specific options will be ignored)
      --batch-pattern[=BATCH-PATTERN]            multiple batch patterns that can be used to merge all audio books in a directory matching the given patterns (e.g. %a/%t for author/title) (multiple values allowed)
      --dry-run                                  perform a dry run without converting all the files in batch mode (requires --batch-pattern)
      --jobs[=JOBS]                              Specifies the number of jobs (commands) to run simultaneously [default: 1]
  -h, --help                                     Display this help message
  -q, --quiet                                    Do not output any message
  -V, --version                                  Display this application version
      --ansi                                     Force ANSI output
      --no-ansi                                  Disable ANSI output
  -n, --no-interaction                           Do not ask any interactive question
  -v|vv|vvv, --verbose                           Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug
```

## Conclusion

There's more to be said, including about batch conversion, trying to process multiple books at the same time but I think this is plenty to get you going.

I had fun figuring this out and it has given new life to my audiobook CD collection. Hopefully this helps someone else out too ^__^
