# Course Learner Guide {#learner-guide}

This guide explains how to discover, enroll in, and complete courses on Lingtrain. You will learn how to navigate units, track your progress, build streaks, earn XP, and compete on the leaderboard.

## Browsing the Catalog {#browsing-catalog}

The course catalog is available at `/courses`. It displays all published courses as cards in a responsive grid.

### Filtering {#filtering}

Use the filter bar above the course grid to narrow results:

| Filter | Options |
|--------|---------|
| **Domain** | Language Learning, Programming, General, Writing |
| **Difficulty** | Beginner, Intermediate, Advanced |
| **Language** | Target language of the course |
| **Tags** | Keywords assigned by the course creator |

You can also use the **search box** to find courses by title or summary text.

### Course Cards {#course-cards}

Each card shows:

- Course title and cover image
- Domain badge and difficulty level
- Creator name
- Estimated total duration
- Number of enrolled learners

Click a card to open the course detail page.

## Enrolling in a Course {#enrolling}

On the course detail page you can see:

- Full course description
- Curriculum outline (modules and unit titles)
- Estimated total duration
- Difficulty and domain

Click **Enroll** to join the course. After enrolling, the course appears in your dashboard at `/courses/dashboard`.

## Navigating Units {#navigating-units}

### Unit Reader {#unit-reader}

When you open a unit, the reader displays:

- **Content blocks** -- text, callouts, vocabulary grids, dialogues, code snippets, or parallel bilingual text
- **Activities** -- interactive exercises like multiple choice, matching, fill-in-the-blank, ordering, flashcards, and short text answers
- **Navigation controls** -- Previous / Next buttons to move between units

### Module Sidebar {#module-sidebar}

The sidebar shows the full curriculum with completion checkmarks. Click any unit title to jump directly to it.

### Resume from Last Position {#resume}

When you return to a course, you are taken directly to your last incomplete unit. No need to search for where you left off.

## Tracking Your Progress {#tracking-progress}

### Per-Course Progress {#per-course-progress}

Each enrolled course shows a progress page with:

- Module-by-module accordion showing completion status
- Per-unit scores and time spent
- Overall course completion percentage

### Cross-Course Dashboard {#dashboard}

Your learner dashboard at `/courses/dashboard` shows:

- All enrolled courses with progress bars
- Stats bar with streak, XP, and leaderboard rank
- Quick access to resume any course

## Streaks and XP {#streaks-xp}

Lingtrain uses a lightweight gamification system to encourage daily learning. Streaks and XP are purely motivational -- they never gate access to content.

### Daily Streak {#daily-streak}

Your streak counts consecutive days of learning activity. Completing any unit or passing an assessment increments your streak.

- **Active streak** -- displayed as an orange flame icon with the number of days
- **Broken streak** -- the flame turns gray and the counter resets to 1 on your next activity
- **Grace days** -- you get 1 grace day per 7-day streak window. If you miss one day, the grace day saves your streak. A small shield icon appears next to the flame when a grace day is available

There is no notification when your streak breaks. You will notice it organically on your next visit when the flame turns gray and the counter resets.

### XP (Experience Points) {#xp}

XP is awarded for learning accomplishments:

| Action | XP Awarded |
|--------|-----------|
| Completing a unit | 50 XP |
| Passing an assessment | 100 XP |

When you earn XP, a toast notification slides in from the top right showing the amount (e.g., "+50 XP"). It auto-dismisses after 3 seconds.

Your total XP is displayed in the stats bar as a purple star icon with the accumulated points.

### Stats Bar {#stats-bar}

The stats bar at the top of the dashboard shows three chips:

| Chip | Icon | Color | What It Shows |
|------|------|-------|---------------|
| Streak | Flame | Orange (active) / Gray (inactive) | Current streak in days |
| XP | Star | Purple | Total accumulated XP |
| Rank | Trophy | Gold | Your leaderboard position (or "--" if opted out) |

The rank chip also includes an eye toggle to opt in or out of the leaderboard.

## Leaderboard {#leaderboard}

The global leaderboard is available at `/courses/leaderboard`. It ranks all learners by XP across different time windows.

### Time Windows {#time-windows}

Switch between tabs to see rankings for different periods:

| Tab | Shows |
|-----|-------|
| **7 days** | XP earned in the last 7 days |
| **30 days** | XP earned in the last 30 days |
| **All time** | Total XP earned since registration |

### Table Columns {#table-columns}

| Column | Description |
|--------|-------------|
| Rank | Position in the leaderboard |
| Avatar | User profile picture |
| Nickname | Display name |
| Streak | Flame icon + days |
| XP | Points for the selected time window |

### Your Row {#your-row}

Your row is highlighted in the table. If you are not on the currently visible page, your row is pinned as a sticky row at the bottom of the table so you can always see your position.

### Opting Out {#opting-out}

If you prefer not to appear on the leaderboard:

1. On the dashboard, find the **rank chip** (trophy icon) in the stats bar.
2. Click the **eye icon** next to it to toggle visibility.
3. When opted out, the rank chip shows "--" and you are completely excluded from the leaderboard. Other users will not see your entry.

You can opt back in at any time by clicking the eye icon again.

## FAQ {#faq}

### What happens if I miss a day? {#faq-miss-day}

If you have a grace day available (1 per 7-day streak window), your streak is preserved. If no grace day is available, your streak resets. On your next activity, the streak starts again from 1 day. There is no penalty beyond losing the streak count.

### How is XP calculated? {#faq-xp-calculated}

XP is awarded by the server when you complete a unit (50 XP) or pass an assessment (100 XP). XP totals are recalculated nightly for the 7-day and 30-day leaderboard windows.

### Can I hide from the leaderboard? {#faq-hide-leaderboard}

Yes. Click the eye icon next to the rank chip on your dashboard to opt out. You will be completely removed from the leaderboard. Your streak and XP still accumulate normally.

### How do I resume a course? {#faq-resume}

Go to your dashboard (`/courses/dashboard`) and click on any enrolled course. You will be taken to your last incomplete unit automatically.

### Do streaks or XP unlock content? {#faq-gating}

No. Streaks and XP are purely motivational. All course content is available regardless of your streak or XP level.

### What timezone is my streak based on? {#faq-timezone}

Your streak uses your browser's timezone, detected automatically on your first course interaction. If no timezone is detected, UTC is used as fallback.
