# RSS News Feed App

An iOS app for reading news from RSS feeds. You add a publication by entering its URL, and the app fetches and displays that publication's articles as a scrollable feed with thumbnails and headlines.

> **Note on this repository:** it currently contains only a screen recording of the app
> (`rssNewsApp_ScreenRecording.mov`). The Xcode project and Swift sources are not committed yet.
> The description below reflects the app's behaviour as demonstrated in that recording.

---

## Demo

[`rssNewsApp_ScreenRecording.mov`](rssNewsApp_ScreenRecording.mov) — a one-minute walkthrough of the full flow.

## Features

- **Add feed sources by URL.** Enter a publication's address into the input field and tap **+** to add it to your list of sources.
- **Fetch and display articles.** The app retrieves the feed and renders each item as a row with a thumbnail image and headline.
- **Scrollable article list** covering everything returned by the feed, across news, politics, sport and finance.
- **Open an article** by tapping its row.
- **Back navigation** to return to the feed list and source management.

## Screens

| Screen         | Purpose                                                          |
| -------------- | ---------------------------------------------------------------- |
| Add Links      | Enter a feed URL, add it, and see the sources you've added        |
| Fetched Feeds  | Article list with thumbnails and headlines for the selected source |
| Article        | The selected article's content                                    |

## Tech stack

<!-- TODO: confirm and complete once the source is committed. -->

- Swift, UIKit
- Feed retrieval over HTTP, with XML parsing of the RSS document
- Asynchronous thumbnail loading into table view cells

## Getting started

<!-- TODO: fill in once the Xcode project is committed. -->

```bash
git clone <repository-url>
cd RSSNewsFeedApp
open RSSNewsFeedApp.xcodeproj
```

Build and run on a simulator or device, then add a feed URL on the first screen.

## Roadmap

- Commit the Xcode project and Swift sources
- Persist added feed sources between launches
- Error and empty states for unreachable or malformed feeds
- Pull-to-refresh
- Offline caching of fetched articles
