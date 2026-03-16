# Ultimate Teams — GitHub Pages package v4

This version implements modified Elo win/loss ratings.

CSV columns:
- First Name
- Last Name
- Combined
- Handling
- Cutting
- Defense
- Win/Loss Rating

Recommended starting value: 0.00

Modified Elo logic:
- Player effective overall = weighted base skill + Win/Loss Rating
- Team strength = sum of effective overall ratings
- Expected win chance = 1 / (1 + 10^((opp - team) / 4))
- K factor = 0.08
- Winners gain: K × (1 - expected)
- Losers lose: K × expected

This means:
- strong team wins -> tiny change
- weak team wins -> larger change
- no clamp
- no decay

Result recording is optional. You can still just Generate game or Save teams + next game without touching win/loss ratings.
