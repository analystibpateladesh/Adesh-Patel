# Music App Roadmap (Legal, Ad-Free, Offline-First)

## 1) What to build
A lightweight app that feels like Spotify for a small rotating catalog (about 1000–1200 tracks):
- Search songs quickly
- Stream instantly
- Download for offline listening
- Auto-build playlists from user behavior
- No ads in product experience

## 2) Important legal note
Avoid building a “YouTube-to-MP3 downloader” flow for copyrighted content. That usually violates platform terms and can create legal risk.

Use licensed sources instead:
- Upload/host tracks you have rights to
- Use licensed music APIs/distributors
- Support only user-owned uploads for private conversion (if needed)

## 3) Product features (MVP)
1. Auth + user profile
2. Song catalog (1000–1200 songs)
3. Search by title/artist
4. Play queue
5. Offline download toggle per song
6. “Smart playlist” based on recent searches/plays
7. Admin panel to rotate catalog over time

## 4) Recommended architecture
### Frontend
- Next.js + Tailwind
- Audio player with queue state (Zustand/Redux)
- Service Worker + IndexedDB for offline metadata/cache

### Backend
- Node.js (NestJS or Express)
- PostgreSQL for users, songs, playlists, listening history
- Redis for caching search/autocomplete
- Object storage (S3/R2/GCS) for audio files

### Offline strategy
- User taps “Download playlist”
- Queue downloads one-by-one (background worker)
- Persist local manifest in IndexedDB:
  - song id
  - local file blob key
  - checksum/version
  - expiry policy
- While one song is playing, prefetch the next one in queue

### Recommendation logic (simple first)
Score tracks with:
- +3 if artist searched recently
- +2 if genre listened recently
- +1 if similar BPM/mood tags
- + freshness boost for newly rotated songs

## 5) Data model (minimal)
- users(id, email, created_at)
- songs(id, title, artist, genre, bpm, mood, duration_s, audio_url, is_active)
- playlists(id, user_id, name, created_at)
- playlist_items(id, playlist_id, song_id, position)
- plays(id, user_id, song_id, played_at)
- downloads(id, user_id, song_id, status, downloaded_at, expires_at)
- searches(id, user_id, query, searched_at)

## 6) API sketch
- GET /songs?query=
- GET /songs/:id/stream
- POST /downloads/:songId
- GET /playlists/recommended
- POST /playlists/:id/items
- GET /offline/manifest

## 7) “No ads” business options
- Freemium with capped offline songs
- Low-cost monthly subscription
- Bundle with creator/community membership
- Sponsor-branded playlists (non-intrusive)

## 8) Build phases
### Phase 1 (1–2 weeks)
- Song catalog + search + player
- Basic auth

### Phase 2 (1–2 weeks)
- Offline single-song download
- Playlist creation

### Phase 3 (2 weeks)
- Sequential playlist downloads
- Smart recommendation v1

### Phase 4 (ongoing)
- Catalog rotation automation
- Analytics + retention optimization

## 9) Practical next step
Start with a legal catalog of 50 songs and complete end-to-end flow:
search -> play -> download offline -> play offline.
Then scale the same architecture to 1000+ songs.
