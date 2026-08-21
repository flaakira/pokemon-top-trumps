# Pokemon Top Trumps

A single file browser game: a six versus six knockout card battle played with the 151 original Pokemon. The markup, the styles, the Pokemon data and all of the game logic live in `index.html`, so there is nothing to install and nothing to build. Open the file in any modern browser and press New game.

[Play the game](https://flaakira.github.io/pokemon-top-trumps/)

## How to play

Every new game shuffles the full list of 151 Pokemon and deals six cards to you and six to the CPU. Your six are shown as sprites on the left of the screen. The enemy six are shown as poke balls, because the enemy team stays secret.

Each round one card from each side steps forward. Your active card is fully visible: name, number, type badges and its five stats. The enemy active card shows only its official artwork, and its types and stats stay behind question marks until the round is resolved.

One side then picks which stat the round is fought over. Who picks alternates every round, and the very first picker is chosen at random. When it is your turn the five stat rows on your card turn into buttons, so you just click the stat you want to fight with. When it is the CPU turn it waits about a second and then picks one of the available stats completely at random.

As soon as a stat is picked the enemy card is revealed, both scores are shown together with the type multipliers that produced them, the result is added to the log at the bottom of the page, and the Next round button appears.

## Who wins, and who gets knocked out

The side with the higher score for the chosen stat wins the round, and the losing card is knocked out for good. It is greyed out in the team strip and it can never come back. The winning card survives untouched, because damage and HP do not exist in this game: every round is a single comparison.

If the two scores are exactly equal the round is a draw. Nobody is knocked out, and the stat that produced the draw is blocked for the next round, so the next picker only has four stats to choose from. The blocked row is disabled and labelled as blocked on your card.

At the end of every round both sides move to a different card from their remaining team, so the same Pokemon almost never fights two rounds in a row. A side only keeps the same card when it has a single Pokemon left.

The first side left with no Pokemon loses the match, and the game ends straight away with a win or lose message.

## The five stats

Rounds are fought over Attack, Defense, Sp. Attack, Sp. Defense and Speed, taken from the Generation 1 base stats of each Pokemon. HP is not part of the game.

## The calculations

### Round score

Each side scores the chosen stat multiplied by its own primary type effectiveness against the primary type of the opponent:

your score = your stat x mult(your primary type, enemy primary type)

enemy score = enemy stat x mult(enemy primary type, your primary type)

The two multipliers are looked up independently, so they are usually different in the same round: a Fire card facing a Grass card gets the bonus while the Grass card takes the penalty at the same time.

Only the first type of each card counts. Secondary types are ignored completely, and immunities are ignored as well and treated as neutral, so a score is never zero.

### The multiplier table

The 18 by 18 type table has been softened compared to the usual chart, and it holds only three possible values:

| Matchup | Multiplier |
| --- | --- |
| Super effective | 1.2 |
| Neutral | 1 |
| Not very effective, and immunities | 0.8 |

Because the spread is narrow, typing is a tie breaker rather than the main event: a stat difference of more than about twenty percent will usually beat any type advantage.

### A worked example

Charizard (primary type Fire, Attack 84) faces Venusaur (primary type Grass, Attack 82) and Attack is the chosen stat. Fire against Grass is super effective, so Charizard scores 84 x 1.2 = 100.8. Grass against Fire is not very effective, so Venusaur scores 82 x 0.8 = 65.6. Charizard wins the round and Venusaur is knocked out, even though the two raw stats were almost identical.

### Rounding

Scores are compared at full precision, and the rounding to one decimal place happens only when a score is printed on the screen or in the log. Two scores that look the same after rounding can still produce a winner.

### Where the randomness sits

The deal is a Fisher Yates shuffle of all 151 Pokemon: the first six go to you and the next six go to the CPU, so no Pokemon can appear twice in a match. The first picker is a fair coin flip. The active card of each side is picked uniformly from the cards still alive, excluding the card that was just used. The CPU stat choice is uniform over the four or five stats that are not blocked, so it never looks at its own stats or at yours.

## Data sources

The Pokemon list holds the 151 Generation 1 species with their primary type, secondary type and five base stats, and the sprites and artwork are loaded from the public PokeAPI sprite repository. The type chart is the standard 18 type matrix with the softened values described above.
