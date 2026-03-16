
Ultimate Teams – GitHub Pages package

CSV format:
First Name,Last Name,Combined,Handling,Cutting,Defense,Win/Loss Rating

Example:
Sam,Schrader,7,7,7,7,0.00

Win/Loss Rating Logic
---------------------
Team strength = sum of player overall ratings.

Expected win chance =
team_strength / (team_strength + opponent_strength)

K factor = 0.10

Winner change:
+ K × (1 − expected_win_chance)

Loser change:
− K × expected_loser_chance

Meaning:
• strong team wins → very small change
• weak team wins → larger change
• no clamp
• no decay
