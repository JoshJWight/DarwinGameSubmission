# DarwinGameSubmission
Early Bird MimicBot, my submission for the 2020 Less Wrong Darwin Game competition https://www.lesswrong.com/posts/AHTRyQJtiRin22kth/the-darwin-game-1

## Tweaking and Handling:

It's really important that all the CloneBot section of the code be preserved as is to pass the clone check, especially the 0x1Ds, which are all on line 46. On Github I can see them as little boxes. Cloning the repository rather than copying the text should preserve them.

Simulation is potentially quite time consuming. I have tried as much as possible to improve performance, and I'm supposed to only be simulating really simple bots who ought to be fast, but if it is still too slow, the variable SEARCH_DEPTH on line 58 controls how many levels the simulation goes to. Each additional level roughly doubles the time taken, so reducing the number by even 1 or 2 should significantly speed things up.

Setting DISABLE_SIMULATIONS to True on line 60 will disable simulations entirely, if there is no other option.

## Explanation:

This bot has two big, complicated ideas plus a fairly straightforward backup plan.

CloneBot verifies that its copies have the same source code by checking that all code before the "payload" is the same, and that everything in the payload is sufficiently indented that it won't be run before the payload. I made it my mission to get around that and be a MimicBot, able to fully cooperate with the CloneBots but able to run my own code. Their security wasn't easy to break, but there was a weakness. CloneBot sanitizes the source code by calling splitlines() and then joining the lines with \n. splitlines() splits on more characters than just newlines, including the group separator character 0x1D. This allowed me to stick most of the CloneBot source in a comment while still passing the clone check. My own code becomes the body of CloneBot's move() method and does not need to break out the indentation restrictions in the second half of CloneBot's check.

Secondly, this bot is a simulator. Hat tip to Zack M. Davis for publicly providing the code to set up a simulation. Because I expect that there are almost endless ways for other bots to get simulators disqualified and because simulating bots with randomized behavior is mostly pointless, I only simulate bots with two or fewer open parentheses "(" in their code. My hope is that this set is limited to simple, deterministic bots with no anti-simulator landmines, but that it also contains enough bots in the early rounds to be worth exploiting. Every N rounds, I simulate the outcome of every possible sequence of N moves from me from (2, 3). I look for two outcomes: the one where I get the most points overall, and the one where I get the most points without letting the opponent outscore me by too much. I pick one depending on the round and turn, and execute that plan over the next N turns.

If neither cloning nor simulation applies, I use cooperative strategies that are adapted pretty straightforwardly from Zvi's posts, getting more defensive in later rounds. My plan is to use clones and simulations to build a big early game advantage, pull ahead of the other CloneBots by continuing to cooperate with outsiders as the clones attack more and more, then snowball due to the automatic perfect self-cooperation.

## Strategies against different types of opponents:

### Clones:
  
  Before round 90: Perfect clone cooperation.
  
  After round 90: Same as other non-simulated bots.
  
### Simulated Bots:
   
   Before round 20: Look for fair exchange with a score difference margin of 4 for the first 20 turns, only folding if I can't get it, afterwards take the best score.
   
   After round 20: Don't let them outscore me by more than 2.

### Others:

  Before round 10: EquityBot, cooperate and alternate 2/3 even when opponent attacks, fold if opponent plays 3 ten times in a row.
  
  After round 10: DefenseBot, cooperate but don't let them outscore me by more than 2
  
  After round 90: DefenseBot, cooperate but don't let them outscore me by more than 1
