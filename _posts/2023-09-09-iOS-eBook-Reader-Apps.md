---
layout: post
title: "iOS eBook Reader Apps"
excerpt: "A somewhat exhaustive (and very opinionated) look at eBook reading solutions for iOS ignited by the question `"Why are all of the best eBook reader apps for iOS abandonware?`""
tags: [
    eBooks,
    Literature,
    Reading,
    iOS,
    Apps,
    Copyright
]
modified: 2023-09-09 11:06:00
date: 2023-09-09 11:06:00
comments: true
image:
  feature: banner.jpg
  thumb: /quicktips/postthumb.png
published: true
---
{% include _toc.html %}

## Intro

The seed for this post came out of a conversation that I had with @mikespub in a github issue's comments about the state of the eBook reader landscape. We both like [Thorium](https://www.edrlab.org/software/thorium-reader/) on desktop, he had some good insights for the Android side, and I surprised him by commenting on the bleak state of things on iOS as I see it.

The two best options for iOS that I know of are both abandonware, **[Kybook 3](https://apps.apple.com/app/id1348198785)** which hasn't seen an update since February of 2019 and **Marvin 3** hadn't seen an update since October of 2017 and got *unpublished from the app store* earlier this year.

Overall the iOS reading community seems to be collectively dreading the day when Apple releases a major iOS update which stops whichever is their favorite app from running.

The following is an organized version of my thoughts and research on the matter starting with an assessment of all 33 apps listed on the [Readium](https://github.com/readium) (from the author of Thorium for desktop use) repo as having been built on that framework and a breakdown of the pros and cons of specific apps that I have experimented with.

## My Current Use Case and Setup

### Media Types
My library contains a large quantity of all of the following 

- Novels and Novellas
  - As ebooks and as audiobooks
- Comics
- Manga
- Fanfiction
- Webnovels

### Constraints
1. I have nearly 1TB of Audiobooks alone and even excluding those my collection is over 500 gigs. This immediately excludes certain scenarios like keeping everything on my phone all of the time and will come into play with certain applications.
2. Because Fanfiction and Webnovels are both serially published any solution which treats ebook files as a static object and cannot handle a book file having chapters appended to it is a non-option.
   1. In addition any option where it is a _major hassle_ to download an updated book file and jump to the end/most recent chapter is also not going to work for me.
3. I like using TTS (Text To Speech) to listen to library items for which audiobooks are unavailable
   1. I am the person who the 3.5x speed option on Audible exists for. I listen to everything at the max speed that every platform and app allows. This kills several otherwise decent app options for me and I will make clear when I get to those that if you're not like me or don't use TTS you might really like the app I'm discussing.

### Current Setup

- **Reader:** [Kybook 3](https://apps.apple.com/app/id1348198785) by Kolyvan aka Konstantin Bukreev
- **Content Server:** OPDS feed from the [mikespub fork of COPS](https://github.com/mikespub-org/seblucas-cops) (originally by [seblucas](https://github.com/seblucas) but he abandoned it in 2019) run in Docker
- **Content Library/Database:** [Calibre](https://calibre-ebook.com/) with some fairly involved customizations
- **Content Downloader/Packager/Updater:** [FanFicFare](https://github.com/JimmXinu/FanFicFare) plugin for Calibre - This downloads and updates my Fanfiction and Webnovels.

I've been working for a while on a separate post all about my Calibre/FanFicFare setup and a guide for all of my fancy features. When that is completed I'll crosslink it here. A lot of my pros for Kybook will become clear via the comparisons I'll be making when assessing the other apps.

## App Assessments

### Readium Based Apps

I started with the [Readium Based Mobile Apps section of the Readium Readme](https://github.com/readium/awesome-readium/blob/master/README.md#apps-based-on-readium-mobile) and thereby identified 33 total apps to investigate. Based on that investigation I found:

| **#** | **Finding**                                    |
|-------|------------------------------------------------|
| 16    | Locked to a specific Ebook Vendor              |
| 14    | Locked to one or more specific Library Systems |
| 3     | Actually a general use ebook reader (on paper) |

30 of the apps could only connect to, load books from, and read from either a specific sales platform, specific libraries who have opted into a given app, or similar. None of that is going to work for me. A spreadsheet of my findings on each app is [available here](https://docs.google.com/spreadsheets/d/1Ua2S9im5kxwJhOxowJaPgbP8s9JOcGCKL6BF4kVS8sE/edit?usp=sharing)

Of the three remaining, two cannot be pointed at any OPDS library feed other than the ones which are built into the application and thus are useless to me. Still I'll do a breakdown of all 3

#### Baobab

##### General Reading Experience and OPDS Support
- ✅ At least can be used to open your own books from the Files app
- ❌ Can't be pointed at my own book feed, only supports baked-in OPDS feeds

#### TTS
- ❌ TTS is completely unusable. There is no pause and resume, only play and stop. Every time it is stopped the next time Play is pushed it begins at the **very first sentence of the book*
- ❌ Does not appear to use or iOS' built in TTS engine/settings/pronunciation corrections and has no inbuilt support for pronunciation correction

#### DITA Reader

This one is hilarious. According to the description this is supposed to be a normal ebook reader for anyone to use. In reality this app is published for an audience of 1, mister Aferdita Muriqui himself (he put his personal gmail and full name in for the support email on the Google Play app store)

##### General Reading Experience and OPDS Support
- ❌ Does not support manually adding books/the Open With command cannot be used to open a book using it
- ❌ Can't be pointed at my own book feed, only supports baked-in OPDS feeds
- ❌ Has a hardcoded OPDS mapping for "Aferdita's Collection" which includes several books which I'm pretty certain he does not have a license to legally redistribute. I have reached out to notify the authors.
- ❌ Does not suport YOUR PHONE'S OS being in dark mode. Not just that the app doesn't have it's own dark mode, if your PHONE is in dark mode then the **whole app** is white text on a white background.
- ❌ If you click the "My Books" navigation at the bottom it doesn't say "My Books" at the top of the page it takes you to, it says "Aferdita's books"

This one is clownshoes

#### Aldiko Next

I've actually looked at this one a few years ago and liked it except for the TTS. That hasn't gotten much better.

- ✅ This is the only one of these that's actually competent!
- ✅ You can actually point it at your own OPDS feeds or any OPDS feed you want!
- ✅ If you don't care about/use TTS stop reading here! You've found your new ebook reader app!
- ✅ TTS uses the iOS native TTS engine and respects your global pronuciation settings which is great!
- ✅ TTS can be paused and resumed! You can skip back and forward a sentence if you missed something!
- ❌ Unfortunately whenever you begin TTS it will **only start at the beginning of a chapter** and you can **only skip ahead one sentence at a time**. If you had been near the end of a chapter when you closed the app RIP you.
- ❌ It pauses for a really long time, somewhere between 1.5 and 2 full seconds after every sentence *no matter what playback speed you have the TTS set to*. Unlistenable, especially if you listen at high speeds.
- ❌ This is a small quality of life thing but it doesn't support bulk downloading from OPDS feeds which Kybook 3 does.

### Non-Readium Based Apps

#### PocketBook Reader

I honestly hadn't looked at this beore purely because the icon and name were both similar to an very bad app that I tried a long time ago on a Windows RT Surface tablet but looking at them more closely I don't think there's any connection.

##### General Reading Experience and OPDS Support

Overall this seems pretty OK!

- ❌ I don't like being asked for push notification permissions and if I'd like "special offers" right away when I first open the app, that's not a good tone-setter.
- ❌ Fiddling with it a bit more think if I daily-drove it I'd get really annoyed that the OPDS feeds are kind of buried several interactions deep. They're not on the home screen and you have to go to books and then, because it's wrapped off the side of the screen, you have to swipe all the way to the right past a bunch of icons I'll never use for Dropbox and Google Drive, and _then_ select the OPDS library you actually want once you're in it.
  - If you could reorder the list so OPDS comes first or hide icons you don't want I could live with it but it would still be a massive quality of life setback from Kybook which lets you pin both OPDS libraries and even *shortcuts* to specific points in the library to your home screen.

I've got quick shortcuts to the **Recently Added** categories in a few of my libraries on the home screen that shows up when you make a new tab right now above the entry for my whole instance ([screencap](https://github.com/mikespub-org/seblucas-cops/assets/7658840/69028996-77b0-417b-a4d7-2cccad1ef9a9))

##### TTS

Text to Speech is *almost* there.

- ✅ Uses iOS built in engine (and thus reflects pronunciation fixes made in the main OS settings menu)
- ❌ No buttons to skip back or forward
- ✅ Does support pause-resume
- ✅ Can highlight a point in the page and have it start reading from where you selected (like you can in Kybook/NaturalReader/VoiceDream)
- ❌ Unfortunately has the same problem as **Aldiko Next** of seemingly having a hard coded pause duration between sentences that does not adjust along with the speaking/playback rate. Not _quite_ as bad as it is in **Aldiko Next** but I'm definitely not switching from Kybook 3 to this unless I have no other choice. If you listen at 1-1.5x speed you might be fine.

Unfortunately I'm the kind of lunatic that listens to audiobooks and podcasts at 3.5 speeds and tts at whatever max speed the settings will suffer. I know I'm kind of an edge case.

#### Bookfusion

- Would be a replacement for using OPDS as well as Kybook since it doesn't support it but has it's own integration extension for Calibre, not a huge ask, also promises to support place-tracking
- ❌ Doesn't have any handling/support for updating the cloud/synced version of an ebook if it is edited and more chapters are added so it's straight up **not usable for serially published stories** like Webfiction and Fanfiction which are my highest turnover files.
- ✅ They seem to actually be in the Cloud Storage business unfortunately, not the Reading App business (kind of like how [McDonalds is in the Real Estate business](https://www.wallstreetsurvivor.com/mcdonalds-beyond-the-burger/), not the hamburger busines)
  - ✅ To use their app/service you have to store your books in their cloud and their max storage cap on the most expensive plan only gives you 100gb of storage. Forgetting my normal literature ebook library for the moment because it's not easy for me to measure the size of it without including the associated audiobooks just my Comics, Manga, Fanfiction and Webfiction libraries come to 544gb so it's a non-starter. If they had a bring-your-own-cloud-storage tier that supported running the whole library live out of NextCloud or Dropbox or something there might be room for a conversation.

#### Speechify
- ✅ AI TTS Voices are legitimately impressive! At least what I can sample of them without paying seems to be.
- ❌ Free version **using the iOS native TTS engine** demands that you pay them **$140/year billed annualy** to increase the speed above 1.1x
  - You get other things like the premium AI voices for that price but paywalling access to functionality made up entirely of the phone's native OS accessibility features is a scumbag move.
- ❌ No OPDS support
- ❌ Claims to support EPUB, various formats, **actually converts all ingested files into flat PDFs with no navigation/TOC.**



## Conclusions

I hope that the Readium landscape improves, it does seem like an awesome framework if anyone was interested in building something new and innovative out of it (besides Aldiko Next) and actually did some market research on the most highly regarded alternatives we might get something really awesome.

We are all poorer for Amazon's market dominance. It has set the whole world back a full decade in both eink display development and ereading applications not locked to a specific book marketplace.

Overall if the author of Kybook had a patreon to support continued development for Kybook I would fund it. If anyone else has any app suggestions for me please let me know and I'll test them out!