# SmartSubtitleSuite
find subtitle from opensubtitles and subsource download - translate

🎬 Smart Subtitle Suite

A desktop app (Windows, PyQt5) that combines 3 workflows into 1 app: browse movies/TV shows on TMDB → download subtitles from OpenSubtitles/SubSource → translate subtitles with Gemini AI, all in a single interface, with no need to juggle multiple tools. Full Vietnamese / English UI, switchable instantly inside the app.

Built on top of the translation engine from Smart Subtitle Translator Pro (100% of the original translation logic preserved), extended with automatic subtitle search & download.

✨ Features
📥 Downloader tab
Browse movies/TV shows by Trending / Popular / Upcoming (or "On The Air" for TV) / Top Rated — loads 27 posters at a time, automatically merging multiple TMDB pages so large screens don't look empty.
Quick 🎬 Movies / 📺 TV Shows toggle.
Search by title + year (e.g. Inception 2010), or paste a TMDB ID directly.
Click a title → pick OpenSubtitles or SubSource as the source → the app automatically looks up the IMDB ID from TMDB and searches that exact title, with Season/Episode selection for TV shows.
Download subtitles to your chosen folder, then jump straight into "Open in Translate tab" to translate immediately — no extra steps.
🔄 Refresh button to pull the latest data from TMDB anytime.
The poster grid automatically reflows to fill the full window width.
🌐 Translate tab
Translate subtitles with Google Gemini, with round-robin rotation across multiple API keys to work around free-tier rate limits.
Turbo Mode: runs multiple keys in parallel threads once you have 3+ active keys — translates significantly faster.
Automatically detects the movie/show from the filename (via TMDB) for more contextually accurate translations.
Supports .srt .ass .ssa .vtt, translating a single file or an entire folder at once.
Requires a license or trial to use — see License below.
⚙️ Settings tab
The single place to enter OpenSubtitles (API key + username + password) and SubSource (API key) credentials — with a Test button to verify each key works.
Choose a default subtitle download folder.
Choose a default subtitle search language.
Switch the UI language (VI/EN) (also available at the bottom of the sidebar).
🖥️ Requirements
Windows 10/11 (recommended) — the code also runs on macOS/Linux with Python installed.
Python 3.9+ if running from source.
An internet connection (calls to TMDB, OpenSubtitles, SubSource, and the Gemini API).
🚀 Installation
Option 1 — Use the installer (.exe) — for regular users

If the repo has a SmartSubtitleSuite_Setup_vX.X.X.exe file under Releases, download and run it like installing any other app (Next → Next → Install). See packaging/BUILD_GUIDE.md for details on what happens during install.

⚠️ Since the app isn't code-signed yet, Windows SmartScreen/Defender may show an "Unknown publisher" warning on first run — click More info → Run anyway. This is a normal warning for open-source software without a paid certificate, not a sign of malware. See BUILD_GUIDE.md for the full explanation.

Option 2 — Run from source (for developers)
bash
git clone <your-repo-link>
cd smart-subtitle-suite
pip install -r requirements.txt
python main.py
Option 3 — Build your own installer

Full step-by-step guide in packaging/BUILD_GUIDE.md. Quick summary (run on Windows):

bash
pip install pyinstaller
pyinstaller packaging\build_exe.spec
:: build\ and dist\ folders appear at the project root
:: open packaging\installer.iss with Inno Setup Compiler and click Build

Output: dist_installer\SmartSubtitleSuite_Setup_v1.0.0.exe

🔑 API Key Setup Guide

The app needs 3 kinds of API keys. TMDB's key is already hardcoded in the code (no signup needed on your end); the other two are free to register and go into the Settings tab (OpenSubtitles/SubSource), or directly in the Translate tab when adding a key (Gemini).

1. OpenSubtitles (required if you want to download subtitles from this source)
Go to opensubtitles.com → Sign up for a free account.
Verify your email.
Log in → go to opensubtitles.com/consumers → click "Register as a consumer" (or the equivalent API section) to create a new API Key. Name it anything (e.g. "SmartSubtitleSuite").
Copy the API Key.
In the app → Settings tab → OpenSubtitles section → paste the API Key, enter your OpenSubtitles account's username + password (needed to log in and obtain a download token) → click 🧪 Test Login to confirm it works → 💾 Save.

Note: free OpenSubtitles accounts have a daily download limit. If you download a lot, consider their VIP upgrade.

2. SubSource (required if you want to download subtitles from this source)
Go to subsource.net and follow their process for requesting an API key (usually via their contact page/Discord, since they don't have a fixed public self-serve signup form).
Once you have a key → Settings tab → SubSource section → paste the API Key → click 🧪 Test API Key → 💾 Save.
3. Google Gemini API Key (required to use the Translate tab)
Go to Google AI Studio.
Sign in with a Google account.
Click "Create API key" → pick or create a Google Cloud project → copy the key (looks like AIzaSy...).
In the app → Translate tab → click ➕ Add API Key → paste the key, give it a memorable name (e.g. "Main key") → click 🧪 Test API Key to confirm it works → Add.
You can add multiple keys at once (each Google account can create at least one free key) — adding 3 or more keys unlocks ⚡ Turbo Mode for much faster translation.

Gemini's free tier has per-minute and per-day request limits depending on the model. If long translation jobs keep hitting quota errors, wait for the daily reset or add another key.

4. TMDB (already included — no signup needed)

The TMDB API key used for Trending/Popular/Search is already hardcoded in core/tmdb_client.py. If you fork the project and want to use your own TMDB key, register for free at themoviedb.org/settings/api and replace the TMDB_API_KEY value in that file.

🔒 License / Trial

The Translate tab (the AI translation feature) requires a license or trial:

The first time you open the Translate tab, the app asks you to enter a license key, or start a trial (limited by days / number of subtitles, depending on configuration).
Once activated, or while a trial is still valid, you go straight in — no repeated prompts (state is stored in subtitle_translator.license / subtitle_translator.trial next to the executable).
The Downloader and Settings tabs are always free to use, no license required.
🌐 Multi-language UI

The VI | EN toggle at the bottom of the sidebar switches the entire UI language instantly, no restart needed. Your choice is remembered for next time.

📁 Project structure
main.py                        # entry point, loads the app icon
app_icon.ico                   # app icon (dev-mode runtime)
core/
  tmdb_client.py                # trending/popular/upcoming/on_the_air/search + external_ids
  opensubtitles_client.py       # login + search by imdb_id + download
  subsource_client.py           # search by imdb_id + download (ZIP extraction)
  subtitle_models.py            # shared SubtitleResult dataclass
  gemini_translator.py          # Gemini translation, multi-key rotation (original logic preserved)
  subtitle_parser.py            # parse/write .srt .ass .vtt (unchanged)
  tmdb_helper.py                # movie context from filename for better translations (unchanged)
license/
  license_system.py             # HWID + HMAC + Fernet license/trial system (unchanged)
config/
  settings_manager.py           # stores OpenSubtitles/SubSource creds + save path (password encrypted)
  languages.py                  # language mapping between OpenSubtitles <-> SubSource
  i18n.py                       # VI/EN UI translation table
ui/
  main_window.py                # sidebar with 3 tabs + language toggle, license gate
  downloader_tab.py             # search bar + Movie/TV toggle + poster grid
  movie_detail_dialog.py        # source picker popup + result list + download
  settings_tab.py                # credentials form + language switch
  translate_tab.py              # translation UI (embedded from the original MainWindow as a tab)
  activation_dialog.py          # license key entry dialog (unchanged)
  poster_widget.py               # poster card + async image loading (capped at 4 concurrent connections)
  workers.py                     # QThread helper for non-blocking network calls
  theme.py                       # shared dark theme QSS
packaging/
  build_exe.spec                 # PyInstaller spec (onedir, no UPX, version info, icon)
  version_info.txt               # exe metadata
  installer.iss                  # Inno Setup script to build the installer .exe
  app_icon.ico                   # icon used at build time
  BUILD_GUIDE.md                 # build instructions + how to avoid AV false positives
🛠️ Built with

Python 3 · PyQt5 · TMDB API · OpenSubtitles REST API v1 · SubSource API · Google Gemini API (google-generativeai) · cryptography (Fernet) for license/settings storage.

📜 Credits / License
The translation engine (Gemini) is inherited as-is from Smart Subtitle Translator Pro.
This product uses the TMDB API but is not endorsed or certified by TMDB.
OpenSubtitles and SubSource are third-party services — please follow each provider's terms of use when using their API keys.
