# Layman — Business & Tech News, Made Simple

> Business, tech & startups explained in plain everyday language.

Layman is an iOS news app that fetches real-time articles and rewrites them in simple terms using AI. Ask follow-up questions about any article via the built-in AI chat ("Ask Layman"), save articles for offline reading, and track your daily reading streak.

---

## Screenshots

| Welcome | Home | Article Detail | Ask Layman |
|---|---|---|---|
| _add screenshot_ | _add screenshot_ | _add screenshot_ | _add screenshot_ |

| Saved | Profile | Dark Mode |  |
|---|---|---|---|
| _add screenshot_ | _add screenshot_ | _add screenshot_ | |

---

## Features

- **Live news feed** — Business, tech & startup articles via NewsData.io
- **AI summaries** — Each article broken into 3 swipeable plain-language cards
- **Ask Layman** — Chat with Gemini AI about any article
- **Voice input** — Ask questions by voice in the AI chat
- **Save & offline** — Bookmark articles; saved articles persist offline
- **Reading streaks** — Daily streak tracking with longest-streak record
- **Push notifications** — Daily digest reminder at 9 AM
- **Dark mode** — Full adaptive dark/light theme with warm espresso palette
- **Supabase auth** — Email/password sign-up and sign-in

---

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Swift 6.2 |
| UI | SwiftUI (iOS 26+) |
| Architecture | MVVM — `@Observable` ViewModels |
| Auth & DB | Supabase (email/password + `saved_articles` table) |
| News API | NewsData.io (free tier) |
| AI | Google Gemini Flash Lite (`gemini-flash-lite-latest`) |
| Voice | SFSpeechRecognizer + AVAudioEngine |
| Notifications | UNUserNotificationCenter (local) |

---

## Requirements

- Xcode 26.3 or later
- iOS 26.2 simulator or physical device
- Free accounts for:
  - [Supabase](https://supabase.com)
  - [NewsData.io](https://newsdata.io)
  - [Google AI Studio](https://aistudio.google.com) (Gemini)

---

## Setup

### 1. Clone the repo

```bash
git clone https://github.com/YOUR_USERNAME/layman-news.git
cd layman-news
```

### 2. Create `Config.swift`

`Config.swift` is excluded from git (see `.gitignore`). You must create it manually:

**Path:** `mm_news/mm_news/Core/Config.swift`

```swift
import Foundation

enum AppConfig {

    // MARK: - Supabase
    // Get these from: https://supabase.com → your project → Settings → API
    static let supabaseURL     = "https://YOUR_PROJECT_ID.supabase.co"
    static let supabaseAnonKey = "YOUR_SUPABASE_ANON_KEY"

    // MARK: - Gemini AI
    // Get your key from: https://aistudio.google.com/app/apikey
    static let geminiAPIKey = "YOUR_GEMINI_API_KEY"
    static let geminiModel  = "gemini-flash-lite-latest"

    // MARK: - NewsData.io
    // Get your key from: https://newsdata.io/api-key
    static let newsAPIKey     = "YOUR_NEWSDATA_API_KEY"
    static let newsCategories = "technology,business"
    static let newsLanguage   = "en"
    static let newsPageSize   = 10
}
```

### 3. Set up Supabase

1. Create a new project at [supabase.com](https://supabase.com)
2. Go to **SQL Editor** and run:

```sql
create table saved_articles (
  id          uuid primary key default gen_random_uuid(),
  user_id     uuid references auth.users not null,
  article_id  text not null,
  article_json jsonb not null,
  saved_at    timestamptz default now(),
  unique(user_id, article_id)
);

-- Enable row-level security
alter table saved_articles enable row level security;

create policy "Users can manage their own saved articles"
  on saved_articles
  for all
  using (auth.uid() = user_id);
```

3. In **Authentication → Providers**, make sure **Email** is enabled.

### 4. Open in Xcode

```bash
open mm_news/mm_news.xcodeproj
```

Select your simulator or device, then press **Run** (⌘R).

---

## Environment Variables / Secrets

| Variable | Where to get it | Where to put it |
|---|---|---|
| `supabaseURL` | Supabase → Settings → API → Project URL | `Config.swift` |
| `supabaseAnonKey` | Supabase → Settings → API → anon/public key | `Config.swift` |
| `geminiAPIKey` | [aistudio.google.com](https://aistudio.google.com/app/apikey) | `Config.swift` |
| `newsAPIKey` | [newsdata.io/api-key](https://newsdata.io/api-key) | `Config.swift` |

> **Never commit `Config.swift`** — it is listed in `.gitignore`.

---

## Project Structure

```
mm_news/
├── App/
│   ├── mm_newsApp.swift          # Entry point, navigation state, scene phase
│   └── ContentView.swift
├── Core/
│   ├── Config.swift              # ⚠️ gitignored — create manually
│   ├── Theme.swift               # All colors, fonts, button styles
│   ├── StreakManager.swift       # Daily reading streak logic
│   └── KeychainHelper.swift
├── Models/
│   ├── Models.swift              # Article, SavedArticle
│   └── ChatMessage.swift
├── ViewModels/
│   ├── AuthViewModel.swift
│   ├── HomeViewModel.swift
│   └── AskLaymanViewModel.swift
├── Services/
│   ├── SupabaseService.swift     # Auth + saved articles
│   ├── NewsService.swift         # NewsData.io fetching
│   ├── GeminiService.swift       # AI chat + suggestions
│   ├── SpeechService.swift       # Voice input
│   └── NotificationService.swift # Local push notifications
└── Views/
    ├── Welcome/WelcomeView.swift
    ├── Auth/AuthView.swift
    ├── Home/
    │   ├── HomeView.swift
    │   ├── FeaturedCarouselView.swift
    │   └── ArticleRowView.swift
    ├── ArticleDetail/ArticleDetailView.swift
    ├── AskLayman/AskLaymanView.swift
    └── Main/
        ├── MainTabView.swift
        ├── SavedView.swift
        └── ProfileView.swift
```

---

## .gitignore

Make sure your `.gitignore` at the repo root includes:

```
mm_news/mm_news/Core/Config.swift
*.xcuserstate
xcuserdata/
.DS_Store
```

---

## License

GNU
