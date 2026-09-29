# NewsFeed

An iOS news reader built on [NewsAPI](https://newsapi.org). Browse top headlines by category, search for any topic, filter by date and country, and read the full article in an in-app reader.

Written in Swift with UIKit and storyboards, following an MVVM structure.

---

## Features

- **Top headlines by category** — Business, Technology, Entertainment, General, Health, Science and Sports, each presented as an image tile.
- **Free-text search.** Typing a category name loads that category's headlines; anything else runs a full-text query against NewsAPI's `everything` endpoint, sorted by publication date.
- **Date filtering** via a date picker dialog, covering any day in the last three months.
- **Country selection** through a searchable country picker with flags.
- **Infinite scroll.** Reaching the bottom of the list fetches the next page of 20 articles and appends the new rows in place, with an "up to date" notice once the result set is exhausted.
- **In-app article reading** using `SFSafariViewController` with Reader mode enabled automatically where the page supports it.
- **Loading and failure states** — a centred activity indicator during fetches, and an alert when a request fails.

## Architecture

```
NewsItemController2        search bar + category collection + article table
NewsListenerController     article list for one category or query
          |
   ArticleListViewModel    owns the article array, paging state and row counts
   ArticleViewModel        per-article presentation values
          |
      WebService           NewsAPI URL construction and URLSession requests
          |
       Article             Decodable model matching the NewsAPI payload
```

`WebService` builds one of two NewsAPI URLs depending on the query. A search term becomes a `q=` parameter against `/v2/everything` with `sortBy=publishedAt&language=en`; a category becomes a `category=` parameter against `/v2/top-headlines`. Both carry the current page number so results can be requested incrementally.

Responses are decoded twice: once into a `Payload` struct to read `status` and `totalResults`, and once into `ArticleList` for the articles themselves. `totalResults` is what tells the list when to stop asking for more pages.

The view controllers hold no article state of their own — they ask `ArticleListViewModel` for section and row counts and for the view model of the article at a given index.

## Project structure

```
GoodNews/
├── Model/Article.swift                 Decodable models (Article, ArticleList, Payload)
├── View Model/ArticleViewModel.swift   List and per-article view models
├── WebService/WebService.swift         NewsAPI requests and URL construction
├── Controllers/
│   ├── NewsItemController.swift        Category list
│   ├── NewsItemController2.swift       Combined search, categories and feed
│   ├── NewsListenerController.swift    Article list with paging and date filter
│   └── NewsWebViewController.swift     Article web view
├── Cells/                              Article, category and news item cells
├── datepicker/                         Vendored date picker dialog
└── Base.lproj/Main.storyboard          UI layout
```

## Requirements

- Xcode 12 or later
- iOS 13.4+ deployment target
- Swift 5.0
- CocoaPods
- A free NewsAPI key from [newsapi.org](https://newsapi.org)

### Dependencies

| Pod             | Purpose                              |
| --------------- | ------------------------------------ |
| SKCountryPicker | Country selection list with flags    |

## Getting started

```bash
git clone <repository-url>
cd NewsApp
pod install
open NewsFeed.xcworkspace
```

Open the workspace, not the `.xcodeproj` — CocoaPods requires it.

### Supplying your API key

Set your NewsAPI key in `GoodNews/WebService/WebService.swift` before building. Keep it out of version control: put it in a gitignored `Secrets.xcconfig` or a local plist and read it at runtime rather than committing the literal value.

Build and run on a simulator or device. Note that NewsAPI's free developer tier only serves requests from `localhost` and is rate limited, so a key on the free plan will work in the simulator during development but not in a distributed build.

## Known limitations

- **Country selection does not affect results.** The picker's callback calls `UserDefaults.standard.set("code", forKey: country.countryCode)`, which stores the string `"code"` under a key named after the country — the value and key are the wrong way round, so the `string(forKey: "code")` lookup in `WebService` never finds it. Separately, `getNewsURL` hardcodes `country=in` on the top-headlines path. Choosing a country currently only changes the navigation title.
- **Article images are fetched with `Data(contentsOf:)`** on a global queue, with no caching, no cancellation, and no check that the cell is still displaying the same article. Scrolling quickly can put an image in the wrong row, and images are re-downloaded every time a cell is reused.
- **Two overlapping controllers.** `NewsItemController` (categories only) and `NewsItemController2` (search, categories and feed together) duplicate much of the same logic; the first is effectively superseded.
- **The API key is read from a source literal**, which is why the note above matters.
- **`Pods/` and Xcode `xcuserdata/` are committed.** Both should be gitignored — the first is regenerable from the `Podfile.lock`, the second is per-developer editor state.
- **No unit tests.** The view model layer is separated from the controllers and would be straightforward to test, but no test target exists yet.
