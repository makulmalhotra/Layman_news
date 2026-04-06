# Layman — Business & Tech News, Made Simple

> Business, tech & startups explained in plain everyday language.

Layman is an iOS news app that fetches real-time articles and rewrites them in simple terms using AI. Ask follow-up questions about any article via the built-in AI chat ("Ask Layman"), save articles for offline reading, and track your daily reading streak.

---

## Screenshots

| Welcome | Home | Article Detail | Ask Layman |
|---|---|---|---|
| <img width="393" height="864" alt="Screenshot 2026-04-06 at 12 56 53 PM" src="https://github.com/user-attachments/assets/65543864-c82d-4279-82bd-c52aa233ea6f" />
 | <img width="399" height="867" alt="Screenshot 2026-04-06 at 12 58 00 PM" src="https://github.com/user-attachments/assets/14aef3b8-9c31-429f-98a7-b346b813eba5" />
 | <img width="399" height="867" alt="Screenshot 2026-04-06 at 12 58 33 PM" src="https://github.com/user-attachments/assets/2619d76e-b600-4781-a090-2f4f764fb4fc" />
| <img width="399" height="868" alt="Screenshot 2026-04-06 at 12 58 59 PM" src="https://github.com/user-attachments/assets/813ab9c2-7ae1-4553-9490-4eefffa7e532" />
 |

| Saved | Profile | Dark Mode |  |
|---|---|---|---|
| <img width="391" height="859" alt="Screenshot 2026-04-06 at 12 59 28 PM" src="https://github.com/user-attachments/assets/91e50720-34d9-45cc-8792-6d2fee287029" />
| <img width="402" height="861" alt="Screenshot 2026-04-06 at 12 59 51 PM" src="https://github.com/user-attachments/assets/f627b6f7-6bd6-4b89-94fd-364463502320" />
 | <img width="397" height="862" alt="Screenshot 2026-04-06 at 1 00 17 PM" src="https://github.com/user-attachments/assets/940dae43-ed85-4938-bfe3-a7766793119b" />
| |

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

---

## Setup

### 1. Download & unzip

Download the zip from GitHub and unzip it.

### 2. Open in Xcode

```bash
open mm_news/mm_news.xcodeproj
```

All API keys are already included in `Config.swift` — no configuration needed.

### 3. Set up Supabase

The app uses a shared Supabase project. To use your own instead:

1. Create a new project at [supabase.com](https://supabase.com)
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
4. Replace the values in `mm_news/mm_news/Core/Config.swift` with your own project URL and anon key.

### 4. Run

Select your simulator or device, press **Run** (⌘R).

---

## Project Structure

```
mm_news/
├── App/
│   ├── mm_newsApp.swift          # Entry point, navigation state, scene phase
│   └── ContentView.swift
├── Core/
│   ├── Config.swift              # API keys
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
claude.md file zipped in the project.
---

## License

GNU
