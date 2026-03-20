# Ultimate Teams — Full README

This app is a mobile-friendly team generator for casual ultimate frisbee. It is designed to run as a static site on GitHub Pages and work well from a phone.

## What the app does

The app helps you:
- import and manage player ratings
- mark attendance quickly
- generate balanced teams
- avoid repeating teammate pairings too often
- enforce optional pair rules
- add one-time players
- track injuries
- optionally record game results
- update win/loss ratings with a modified Elo system
- export player ratings and season stats to CSV

## Main concepts

Each player has:
- First Name
- Last Name
- Handling
- Cutting
- Defense
- Win/Loss
- active/inactive status
- attendance status
- injury percentage
- optional temp-player status

The app stores:
- players
- pair rules
- teammate history
- current game
- winner selection for current game
- season totals: games played, wins, losses

## CSV format

All ratings CSV files use this exact format:

First Name,Last Name,Handling,Cutting,Defense,Win/Loss

Example:

Sam,Schrader,7,7,7,0.00
Chris,Trujillo,6,6,6,-0.03
Nathan,Burt,5,5,5,0.08

### Importing
Use:
- Active ratings CSV
- Inactive ratings CSV

The app reads both using the same 6-column format.

### Exporting
The **Download Ratings CSV** button downloads three files:
- active_players.csv
- inactive_players.csv
- season_stats.csv

`active_players.csv` and `inactive_players.csv` use the same 6-column ratings format.

`season_stats.csv` contains:
- First Name
- Last Name
- Games Played
- Wins
- Losses
- Win %
- Win/Loss

## Attendance workflow

1. Import your active and inactive CSV files.
2. In Attendance and player settings, tap player rows to mark them present.
3. Present players are highlighted green.
4. Inactive players can be shown or hidden.
5. Use Clear attendance when starting a new session if needed.

## Team generation logic

The app uses only players marked present and active.

### Base player strength
Player strength is built from:
- Handling
- Cutting
- Defense
- Win/Loss rating

Injury affects ratings like this:
- Cutting: full effect
- Defense: full effect
- Handling: half effect

So handling is reduced less than cutting and defense.

### Effective overall
The app computes a weighted overall:

- Handling × 0.35
- Cutting × 0.35
- Defense × 0.30
- plus Win/Loss

### Initial team generation
The app:
1. shuffles players
2. sorts roughly by strength
3. snakes them onto teams

That gives a random but reasonably fair starting point.

### Optimization
Then the app tries many swaps between teams and keeps swaps that improve fairness.

It scores teams using:
- team size balance
- overall strength balance
- handling balance
- cutting balance
- defense balance
- pair rule penalties
- teammate-history penalties

Lower score is better.

### Pair rules
You can add rules to:
- keep two players together
- keep two players apart

These only show present players in the dropdowns.

### Teammate history
When you press **Save teams + next game**, the app records who played together.

Later generations penalize repeated pairings so lineups rotate more naturally over time.

## Current game / results workflow

After teams are generated:
1. tap the winning team box
2. that team turns green
3. all other teams turn red
4. tap a different team to switch the winner
5. tap the selected team again to clear selection
6. press **Save results**

You do **not** have to save results every time.
If you want to ignore rating updates, just use:
- Generate game
- Save teams + next game

## Modified Elo win/loss system

The app uses a modified Elo-style update for the `Win/Loss` value.

### Team strength
Team strength is the sum of all player effective overall values on that team.

### Expected win chance
Expected win chance is:

1 / (1 + 10^((opp_strength - team_strength) / 4))

This means:
- strong teams are expected to win more often
- weak teams are expected to win less often

### Update size
K = 0.08

For each winner:
- change = K × (1 - expected)

For each loser:
- change = K × (0 - expected)

So:
- strong team wins → small change
- weak team wins → larger change
- no clamp
- no decay

### Season stats
When you save results:
- every player in the current game gets 1 game played
- players on the selected winning team get 1 win
- players on all other teams get 1 loss

This also updates `season_stats.csv`.

## Backup / restore

### Export backup JSON
This saves the full app state, including:
- all players
- ratings
- injuries
- active/inactive status
- teammate history
- pair rules
- current stats

### Restore backup JSON
Use this to restore a previous saved state.

## One-time players

You can add a temp player by:
1. typing a name
2. choosing an existing player to copy
3. marking whether they are for today only

The temp player copies:
- Handling
- Cutting
- Defense
- Win/Loss

## Publishing to GitHub Pages

This package includes a GitHub Actions workflow.

### To publish
1. Create a GitHub repository.
2. Upload all files from this folder to the repo root.
3. Commit to the `main` branch.
4. Go to **Settings → Pages**.
5. Set **Source** to **GitHub Actions**.
6. Wait for the workflow to deploy.
7. Open the site URL in Safari.

### Updating the site
When you upload a new version:
1. replace the repo files
2. commit changes
3. open the site with a cache-busting query like:

https://YOUR_USERNAME.github.io/YOUR_REPO/?v=8

If Safari still shows the old version:
- reload in a private tab
- or clear website data for that site

## Recommended sideline workflow

1. Import your active and inactive CSV files.
2. Mark attendance by tapping players.
3. Generate teams.
4. Play.
5. Tap the winning team and press Save results if you want rating updates.
6. Press Save teams + next game.
7. At the end of the session, press Download Ratings CSV to save:
   - active_players.csv
   - inactive_players.csv
   - season_stats.csv

## Files in this package

- index.html — main app
- manifest.json — installable web app metadata
- service-worker.js — offline caching
- sample_ratings.csv — example CSV in the correct format
- .github/workflows/deploy-pages.yml — GitHub Pages deployment workflow

## Summary

This app gives you:
- quick phone-based attendance
- balanced teams
- teammate rotation
- optional result tracking
- evolving win/loss ratings
- simple CSV import/export
- season stats export


## Import preview

This version adds an import preview tool.

Before importing either active or inactive CSV files, you can press:
- Preview active CSV
- Preview inactive CSV

The preview shows:
- which rows will create new players
- which rows will update existing players
- which ratings will change
- whether active/inactive status will change

This helps prevent accidental overwrites and makes roster updates safer.

### What import actually replaces
When an existing player is matched by full name, importing replaces:
- Handling
- Cutting
- Defense
- Win/Loss
- active/inactive status

Importing does **not** replace:
- attendance
- injury %
- teammate history
- pair rules
- games played
- wins
- losses

### Name matching
Players are matched using:
- First Name + Last Name

So `Sam Schrader` and `Samuel Schrader` are treated as different players.


## Reset season stats

This version adds a **Reset season stats** button.

It clears only:
- Games Played
- Wins
- Losses

It does **not** clear:
- Handling
- Cutting
- Defense
- Win/Loss rating
- active/inactive status
- injury %
- teammate history
- pair rules

Use this at the start of a new season if you want fresh season totals while keeping player ratings.

## Date-based export filenames

CSV and backup exports now save with a date prefix in this format:

`YYYYMMDD_filename.csv`

Examples:
- `20260316_active_players.csv`
- `20260316_inactive_players.csv`
- `20260316_season_stats.csv`
- `20260316_ultimate-teams-backup.json`


## UI cleanup in v12

This version makes the attendance list more streamlined:
- removed the subtitle line under player names in the attendance list
- removed the Session label field
- added guidance ranges next to:
  - Anti-repeat strength: 0–10, typical 3–6
  - Pair rule strength: 0.1–5, typical 0.5–2


## Main Layout Changes In v13

This version reorganizes the app to make sideline use cleaner.

### Header and navigation
- removed the Jump To Teams button
- added a **Data** button at the top right
- added a **Main** button so you can switch back

### Section headers
- removed the numbers from section headers
- changed headers to title case

### Attendance And Player Settings changes
- removed the subtitle line under each player name in the attendance list
- added a clear button for the attendance search box
- put a visible box around the Present Players subsection
- moved Add One-Time Player into Attendance And Player Settings as a boxed collapsible subsection

### Add One-Time Player changes
- removed the Only For Today option
- now defaults to keep until removed
- starts manual ratings at:
  - Handling = 3
  - Cutting = 3
  - Defense = 3
  - Win/Loss = 0.00
- selecting a Rate Like player loads that player's ratings into the inputs, and you can still edit them before adding the player

### Data page
The Data page contains:
- Players count
- Attending count
- History Pairs count
- Load Or Edit Players tools
- Pair Rules tools

This keeps the main page focused on attendance, teams, and gameplay.


## Layout Changes In v14

This version makes a few layout refinements:
- Main button is now on the left and Data is on the right
- the main page no longer shows the player/attending/history counts
- the Add One-Time Player subsection no longer shows a Win/Loss input
- new one-time players now default to Win/Loss = 0.00
- Load Or Edit Players and Pair Rules on the Data page are each inside their own boxed card
- the restore file input remains hidden and only the Restore Backup JSON button is shown
- the bottom bar buttons are centered and displayed as two buttons instead of three slots


## Layout Changes In v15

This version moves Anti-Repeat Strength to the Data page under Load Or Edit Players.

It also changes Current Game behavior:
- Current Game is collapsed by default
- Generate Game opens Current Game and scrolls to the top of the page
- Save Teams + Next Game also opens Current Game and scrolls to the top of the page


## Fix In v16

This version fixes Data page CSV import and preview.

The issue was that the Data page buttons were not reliably reading from the visible Data page textareas.
Now:
- Preview Active CSV reads directly from the Active CSV box on the Data page
- Import Active CSV reads directly from the Active CSV box on the Data page
- Preview Inactive CSV reads directly from the Inactive CSV box on the Data page
- Import Inactive CSV reads directly from the Inactive CSV box on the Data page


## Fix In v17

This version restores the missing CSV import helper functions that the Data page import and preview buttons depend on.

That fixes:
- Paste CSV first appearing incorrectly after pasting into the Data page textareas
- Data page preview/import not reading from the visible CSV boxes reliably


## CSV Compatibility In v18

This version tolerates an optional **Combined** column in imported CSV files.

Accepted formats now include either:

`First Name,Last Name,Handling,Cutting,Defense,Win/Loss`

or:

`First Name,Last Name,Combined,Handling,Cutting,Defense,Win/Loss`

How it works:
- the app will accept the Combined column if it is present
- the app stores it internally for compatibility
- the app does **not** use Combined for team generation
- the app does **not** need to display Combined in the interface

This lets you keep Combined in your CSV files for your own viewing and sorting without breaking imports.


## Header And Button Changes In v19

This version updates the top navigation and a few attendance controls.

Changes:
- moved the title **Ultimate Teams** out of the box and onto the same line as the Main and Data buttons
- the current page button is highlighted in a slightly lighter gray
- removed the word **Data** from the top box on the Data page
- removed the headings above:
  - Show Inactive Players
  - Clear Attendance


## Button And Layout Changes In v20

This version adds a confirmation popup for clearing attendance and cleans up controls.

Changes:
- Clear Attendance now asks: `Clear Attendance?`
- removed the Clear Winner Selection button
- hides the bottom bar on the Data page
- Save Teams + Next Game now uses a forced two-line label:
  - `Save Teams`
  - `+ Next Game`

This keeps the button wrapping consistent on smaller screens.


## Fix In v21

This version hardens the Main/Data page initialization and Generate Game button behavior.

Changes:
- Generate Game and Save Teams buttons explicitly use `type="button"`
- app now initializes by rendering and then forcing the Main page active
- Generate Game now reads Number Of Teams and Anti-Repeat Strength more defensively
- Main/Data page switching is more robust

This is intended to fix cases where the Generate Game button stopped responding after the recent layout changes.


## Fix In v22

This version fixes Pair Rules on the Data page.

The issue was that the Data page was showing a cloned Pair Rules section, but the Add Pair Rule button was still reading from the hidden original controls.

Now the Data-page Pair Rules controls:
- sync their values back to the source controls
- add rules correctly
- clear rules correctly
- keep the rule list refreshed on the Data page


## Fix In v23

This version fixes the remaining Data-page Pair Rules bug.

Root cause:
- the cloned Pair Rules controls on the Data page reused the same element IDs as the hidden source controls
- the browser kept resolving those IDs to the hidden elements in some cases
- that caused `Choose two different present players` even after valid selections

Fix:
- the Data-page cloned Pair Rules controls now get unique IDs
- Add Pair Rule on the Data page now passes the selected values directly
- the Data-page cloned controls are synced from the source controls safely


## Fix In v24

This version moves Pair Rules back to the Main page as a collapsible subsection under Attendance And Player Settings.

Why:
- the repeated Pair Rules issues were caused by the cloned Data-page Pair Rules controls
- moving Pair Rules back to the Main page removes the fragile clone/sync path

Changes:
- Pair Rules is now a boxed collapsible subsection on the Main page
- the Data page no longer shows Pair Rules
- Add Pair Rule on the Main page uses the visible Main-page controls directly
- the Pair Rules list renders on the Main page subsection


## Pair Rules Display In v25

This version makes Pair Rules more compact and easier to scan.

Changes:
- Pair Rules now show whether the rule is:
  - Together
  - Apart
- Pair Rules now show the strength value
- rule cards are color-coded:
  - green for Together
  - red for Apart


## Data Page Tools In v26

This version adds the following tools to the Data page:
- Set Weight text boxes for Handling, Cutting, and Defense
- View Player Ratings
- Show Rating Changes
- Simulate Teams
- Auto-Balance Check
- K-Factor slider

It also adds:
- Lock Pair button in the Main-page Pair Rules subsection

Notes:
- weights affect the base overall formula used for balancing
- K-Factor affects how strongly win/loss ratings move after recorded results
- View Player Ratings sorts players by current effective overall
- Show Rating Changes uses saved Win/Loss history snapshots
- Simulate Teams previews balanced teams without replacing the current game
- Auto-Balance Check highlights obvious outliers for review


## Data Page Improvements In v27

This version improves several of the Data-page tools and result handling.

Changes:
- K-Factor current value is shown more clearly next to the slider
- added Show/Hide Inactive, search, and clear controls to:
  - View Player Ratings
  - Show Rating Changes
  - Auto-Balance Check
- Save Results can only be applied once per generated game
- Simulated Teams modal now includes a Next Team button to generate another simulated set
- Auto-Balance Check is more functional:
  - shows average overall
  - shows spread
  - computes an outlier threshold
  - marks players as high/low outliers when their effective overall is far from the group average
  - explains what the outlier flag means

How Auto-Balance Check works:
- it calculates each player's current effective overall
- computes the average overall for the filtered player set
- computes the spread of the group
- flags players whose overall is far enough from the average
- this helps identify ratings that may deserve review
