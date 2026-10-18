# PAARA.org Website

This repository contains the PAARA.org website which is hosted on Github Pages.

Club members are encouraged to help file Issues for the problems encountered on the website, but at the same time we are more than happy to receive Pull Requests with fixes or improvements.

This page acts mainly as a HowTo for other contributors. The old readme is now at [ARCHITECT.md](ARCHITECT.md).

## How meeting pages work

Each monthly meeting has its own page, `meetings/YYYY/YYYYMM.md`. The meeting details live in the page's front matter (the block between the `---` lines at the top):

```yaml
---
layout: meeting
title: "November Monthly Meeting"
date: 2026-11-06
topic: "Scalable Phased Arrays with QuadRF"
presenter: "Roy Gross KM6EOO"
raffle: true
---
```

Everything else is generated from it:

* the meeting page header (date, time, topic, presenter, and the Zoom details for the upcoming meeting)
* the **Next club meeting** box and raffle on the homepage, and the top of the [meetings page](https://paara.org/meetings.html)
* the **Past Meetings** archive on the meetings page, including the "with video" tags

The "next meeting" is the earliest meeting whose `date` is today or later. The site is rebuilt on every push, so after a meeting it moves on to the next one the next time anything is pushed.

Optional fields:

* `time:` if the meeting isn't at 07:00 PM Pacific Time
* `presenter_url:` to link the presenter's name
* `presenter:` can be a list when there are several presenters
* `talks:` instead of `topic:`/`presenter:` when there are several talks, as a list of `topic:`/`presenter:` pairs (see `meetings/2026/202610.md`)
* `topic_details:` a list of sub-points shown under the topic

The Zoom link and dial-in number are in `_includes/zoom-details.md`.

## How to add the next meeting

Run this from the top of the repository, any time after the previous meeting:

```
% ./script/new-meeting
Created the December 2026 meeting, dated Friday, December 4, 2026:
  meetings/2026/202612.md
  _includes/raffle/202612.md
  meetings/raffle/2026/202612-1.png
  ...
```

It creates the month after the last meeting held, dated the first Friday, with the topic and presenter "To be announced". If the first Friday is a holiday, change `date:` in the new page. To create a specific month, pass it: `./script/new-meeting 202701`. The script never overwrites existing files, so running it again is harmless.

It needs only Python 3, which macOS and Linux include.

## What to do when we know the topic and presenter?

Edit `topic:` and `presenter:` in the meeting page's front matter, and add the talk description and speaker bio under `## Details`. The homepage picks up the change automatically.

## How to add presentation materials to a meeting?

For some of the meetings, we get a PDF of PPT that we can link in the meeting page (for example <https://paara.org/meetings/2025/202504.html>).

The following steps must be followed:
1. Upload the PDF or PPT into the <https://github.com/PAARA-org/paara.org-attachments> repository
   * the files must be uploaded in meetings/`YYYY`, where `YYYY` is the meeting year (meetings/`2025` for example)
   * the file name must start with YYYY`MM`, where `MM` is the month (2025`04` for example)
   * a full filename example is `meetings/2025/202501_Instant_Tuning_For_A_Manual_Tuner.pdf` or `meetings/2025/202504_Stealth_HF_Antennas_McClary.ppt`
2. In the meeting page, under the `## Presentation materials` heading, add links to the PDF in both Download and Docs Viewer mode (example at <https://github.com/PAARA-org/paara-org.github.io/blob/77a3ad09ce3f91de06af4f51ec856ad1e66e5321/meetings/2025/202501.md?plain=1#L17>)

```md
* *PDF*: [[download](https://github.com/PAARA-org/paara.org-attachments/blob/main/events/2025/202501_Instant_Tuning_For_A_Manual_Tuner.pdf?raw=true)] [[Docs Viewer](https://docs.google.com/viewer?url=https://github.com/PAARA-org/paara.org-attachments/blob/main/events/2025/202501_Instant_Tuning_For_A_Manual_Tuner.pdf?raw=true)]
```

## How to add the YouTube to a meeting

1. Open the YouTube video in your browser
2. Click the `Share` -> `Embed` and copy the **iframe** code:

```html
<iframe width="560" height="315" src="https://www.youtube.com/embed/5QrTYcUS4hU?si=ImhnyzMrJMUEigxn" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
```

3. Add this code into the particular monthly meeting file, just above the `## Presentation materials` line. The meeting is tagged "with video" in the archive automatically.

## How to add a new newsletter (PAARAgraphs issue) via script

1. Ensure you have a Github fine-grained access token
  a. Login to Github
  b. Navigate to **Developer settings** (bottom of left sidebar)
  c. Click **Personal access tokens** -> **Fine-grained tokens**
  d. Click **Generate new token**
  e. Give it a name and your desired expiration. You shouldn't need to change anything else
  f. Click **Generate token**
  g. **Copy the token** - you won't be able to see it again
2. Ensure you've uploaded the PAARAgraphs issue to the [PAARAgraphs repo](https://github.com/PAARA-org/PAARAgraphs/)
3. Fork this repository if you haven't already, and create a new branch for the addition (`git checkout -b <branch>`)
4. Run `GITHUB_TOKEN=<token_from_step_1> ./script/generate-newsletter.py > newsletter.md`
5. Push the repo up and create a pull request (`git push -u origin <branch>`, click the link in the output to create a pull request)

## How to add and synchronize photo albums (Google Drive)

Event photo albums are backed by public Google Drive folders, indexed in `_data/galleries.json`, and displayed with a responsive thumbnail grid and full-screen lightbox viewer with camera EXIF timestamps. On each page visit, thumbnail grids highlight a fresh, continuous sequential slice of photos from the event, while the underlying album always preserves true chronological order (oldest to newest) with a **"▶ Start from oldest"** button.

### 1. Add the album to `photos.md`

Add a new heading and the gallery include snippet in `photos.md`:

```markdown
## Field Day 2027

{% include gallery.html folder_id="YOUR_GOOGLE_DRIVE_FOLDER_ID" %}
```

*(You can also use this include snippet in any event post under `events/YYYYMMDD.md`)*.

### 2. Synchronize and index the photos

Run the photo sync script for the folder:

```bash
go run ./script/sync-photos.go -folder=YOUR_GOOGLE_DRIVE_FOLDER_ID
```

The script automatically:
* Auto-detects the album title from `photos.md` (or use `-title="Custom Title"`).
* Scrapes all photos and videos from the Google Drive folder without the 50-item limit.
* Concurrently extracts camera EXIF capture dates/times using fast HTTP Range requests.
* Sorts the entire album chronologically from earliest to latest (morning to evening).
* Saves the metadata to `_data/galleries.json`.

### 3. Optional parameters for the gallery include

You can customize the preview grid on any page:

```liquid
{% include gallery.html folder_id="YOUR_FOLDER_ID" limit=4 show_meta=true %}
```

* `folder_id` *(required)*: The Google Drive folder ID.
* `limit` *(optional, default: 8)*: Number of preview thumbnails to show on the page. On each page visit, a fresh random continuous slice of `<limit>` photos is displayed.
* `show_meta` *(optional, default: true)*: Displays the item counter, "▶ Start from oldest" button, "View all N" toggle, and Google Drive link.

### 4. Commit the changes

```bash
git add _data/galleries.json photos.md
git commit -m "Add and index photos for Field Day 2027"
git push
```

### Photo Sync Script Command Reference

| Command | Description |
| :--- | :--- |
| `go run ./script/sync-photos.go -folder=ID` | Sync, extract EXIF, and sort a single new folder (title auto-detected from `photos.md`). |
| `go run ./script/sync-photos.go -folder=ID -title="Title"` | Sync a single folder with an explicit custom title. |
| `go run ./script/sync-photos.go` | Scan `photos.md` and synchronize all Google Drive folders. |
| `go run ./script/sync-photos.go -enrich-only` | Re-extract EXIF timestamps and re-sort existing galleries without re-scraping Drive. |
| `go run ./script/sync-photos.go -key="AIza..."` | Optional: Use an official Google Drive API v3 key if available. |

