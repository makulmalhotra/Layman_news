# Layman — Business & Tech News, Made Simple

> Business, tech & startups explained in plain everyday language.

Layman is an iOS news app that fetches real-time articles and rewrites them in simple terms using AI. Ask follow-up questions about any article via the built-in AI chat ("Ask Layman"), save articles for offline reading, and track your daily reading streak.

---

## Screenshots

| Welcome | Home | Article Detail | Ask Layman |
|---|---|---|---|
| <img width="393" height="864" alt="Screenshot 2026-04-06 at 12 56 53 PM" src="https://github.com/user-attachments/assets/65543864-c82d-4279-82bd-c52aa233ea6f" /> | <img width="399" height="867" alt="Screenshot 2026-04-06 at 12 58 00 PM" src="https://github.com/user-attachments/assets/14aef3b8-9c31-429f-98a7-b346b813eba5" />| <img width="399" height="867" alt="Screenshot 2026-04-06 at 12 58 33 PM" src="https://github.com/user-attachments/assets/2619d76e-b600-4781-a090-2f4f764fb4fc" />| <img width="399" height="868" alt="Screenshot 2026-04-06 at 12 58 59 PM" src="https://github.com/user-attachments/assets/813ab9c2-7ae1-4553-9490-4eefffa7e532" />
 |

| Saved | Profile | Dark Mode |  |
|---|---|---|---|
| <img width="391" height="859" alt="Screenshot 2026-04-06 at 12 59 28 PM" src="https://github.com/user-attachments/assets/91e50720-34d9-45cc-8792-6d2fee287029" />| <img width="402" height="861" alt="Screenshot 2026-04-06 at 12 59 51 PM" src="https://github.com/user-attachments/assets/f627b6f7-6bd6-4b89-94fd-364463502320" />| <img width="397" height="862" alt="Screenshot 2026-04-06 at 1 00 17 PM" src="https://github.com/user-attachments/assets/940dae43-ed85-4938-bfe3-a7766793119b" />
| |

---

## Features

### Core
- **Live news feed** — Real-time business, tech & startup articles via NewsData.io
- **AI summaries** — Each article broken into 3 swipeable plain-language cards
- **Ask Layman** — Chat with Gemini AI about any article; ask follow-up questions in plain English
- **Save articles** — Bookmark any article for later reading
- **Offline reading** — Saved articles are stored locally and available without internet
- **Auth** — Email/password sign-up and sign-in via Supabase

### Bonus Features
- **Voice input** — Ask questions in the AI chat entirely by voice using on-device speech recognition (SFSpeechRecognizer + AVAudioEngine); mic button in the chat input bar activates live transcription
- **Push notifications** — Daily digest reminder at 9 AM via local notifications; toggle on/off from the Profile screen
- **Reading streaks** — Tracks how many consecutive days you've read an article; shows current streak and longest streak on the Profile screen with a 🔥 animation
- **Real-time sync** — Saved articles sync automatically when the app returns to the foreground
- **Dark mode** — Full adaptive dark/light theme with a warm espresso palette; every screen supports dark mode including Welcome, Auth, Home, Article Detail, and Ask Layman
- **Haptic feedback** — Haptics on every meaningful interaction (bookmarks, send, tab switches, swipe completion)

---

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Swift 6.2 |
| UI | SwiftUI (iOS 26+) |
| Architecture | MVVM — `@Observable` ViewModels |
| Auth & DB | Supabase (email/password + `saved_articles` table) |
| News API | NewsData.io (free tier) |
| AI | Google Gemini (`gemini-flash-lite-latest`) |
| Voice input | SFSpeechRecognizer + AVAudioEngine |
| Notifications | UNUserNotificationCenter (local) |
| Streaks | UserDefaults |

---

## Requirements

- Xcode 26.3 or later
- iOS 26.2 simulator or physical device

---

## ⚠️ Before You Run — Required API Keys

This project requires API keys that you must supply yourself. The app will not work without them.

### 1. Gemini API Key (for Ask Layman AI chat) — Required

1. Go to [aistudio.google.com/app/apikey](https://aistudio.google.com/app/apikey)
2. Sign in with a Google account
3. Click **Create API key** → **Create in new project**
4. Copy the key
5. Open `mm_news/mm_news/Core/Config.swift` and replace:
```swift
static let geminiAPIKey = "YOUR_GEMINI_API_KEY"
```

---

### 2. Supabase — Optional (use your own instance)

The project ships with a shared Supabase instance for auth and saved articles. It works out of the box, but if you want your own isolated database:

1. Create a free project at [supabase.com](https://supabase.com)
2. Go to **SQL Editor** and run:

```sql
create table saved_articles (
  id           uuid primary key default gen_random_uuid(),
  user_id      uuid references auth.users not null,
  article_id   text not null,
  article_json jsonb not null,
  saved_at     timestamptz default now(),
  unique(user_id, article_id)
);

alter table saved_articles enable row level security;

create policy "Users can manage their own saved articles"
  on saved_articles
  for all
  using (auth.uid() = user_id);
```

3. In **Authentication → Providers**, enable **Email**.
4. Go to **Settings → API**, copy your **Project URL** and **anon/public key**
5. Replace in `Config.swift`:
```swift
static let supabaseURL     = "https://YOUR_PROJECT_ID.supabase.co"
static let supabaseAnonKey = "YOUR_SUPABASE_ANON_KEY"
```

---

## Setup

### 1. Download & unzip

Download the zip from GitHub and unzip it.

### 2. Add your Gemini API key

Follow the steps above — this is required for the AI chat to work.

### 3. Open in Xcode

```bash
open mm_news/mm_news.xcodeproj
```

### 4. Run

Select your simulator or device, press **Run** (⌘R).

> **Note:** Voice input and push notifications require a **physical device** — they do not work on the iOS simulator.

---

## Project Structure

```
mm_news/
├── App/
│   ├── mm_newsApp.swift          # Entry point, navigation state, scene phase
│   └── ContentView.swift
├── Core/
│   ├── Config.swift              # ⚠️ Add your API keys here
│   ├── Theme.swift               # All colors, fonts, button styles
│   ├── StreakManager.swift       # Daily reading streak logic (UserDefaults)
│   └── KeychainHelper.swift
├── Models/
│   ├── Models.swift              # Article, SavedArticle
│   └── ChatMessage.swift
├── ViewModels/
│   ├── AuthViewModel.swift
│   ├── HomeViewModel.swift
│   └── AskLaymanViewModel.swift
├── Services/
│   ├── SupabaseService.swift     # Auth + saved articles sync
│   ├── NewsService.swift         # NewsData.io fetching
│   ├── GeminiService.swift       # AI chat + question suggestions
│   ├── SpeechService.swift       # Live voice transcription
│   └── NotificationService.swift # Local push notifications scheduler
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

## License

GNU
