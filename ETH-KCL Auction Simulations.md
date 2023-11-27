# Agent-Based Modeling Simulation

<img src="/Users/william/Documents/PhD/Typora Assets/acution_simulation.png" alt="acution_simulation" style="zoom:50%;" />

# Auction Design

## Simulation Setup

**Strategy Profile:** 12 Naive Players

**EOF Access Probablity: ** 1 for all players

**Individual Delay: **1 step (10ms) for all players

**Auction Interval: **D = 12, std = 0.

**Profit Margin:** random drawn from normal distribution mean=0.00659, std=0.0001, for all players

**Global Delay:** Ranging from 1 step (10ms) to 20 steps (200ms)

10,000 auctions for each value of global delay.



## Impact of Global Delay on Auction Efficiency

The definition of auction efficiency:

$$
\eta = winning.bid / total.signal
$$

<img src="https://github.com/M1kuW1ll/MEV-Boost-Auction-Simulations/tree/main/Fig/efficiency_globaldelay.png" alt="eff_delay" style="zoom: 33%;" />

The median of efficiency typically starts from around 94%, because the average profit margin of 0.006 ETH is about 6% of the average total signal of 0.1 ETH.

When a new MEV opportunity are available near the end of the auction, e.g. a searcher sends another order flow, the builder include it in the new block and submit it with an updated bid. However, under higher delay situations, the auction might be terminated before the bid is accepted by the relay. The winner might fail to include some late-arriving transactions or order flows in his block, even if he wins the auction, contributing to a low efficiency.



## EOF vs Individual Delay

**Here we try to investigate EOF and individual delay, which is more impactful on player's performance**

### Same Individual Delay, Different EOF

Simulation setup: 

10 naive players, 1 step (10ms) individual delay for all

EOF: from 0.8 to 0.98 with 0.02 increment

10,000 simulations

<img src="/Users/william/Documents/PhD/Typora Assets/eof_proba_winrate.png" alt="eof_proba_winrate" style="zoom: 67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/eof_proba_profit.png" alt="eof_proba_profit" style="zoom:67%;" />

**There is no need for investigating profit per win because they are all naive players. We observe the player with the highest EOF access wins 50% of the auctions. **



### Same EOF, Different Individual Delay

Simulation Setup:

10 naive players

EOF Access: 80% for all the players

Individual Delay: From 1 step to 10 steps, with 1 step increment

<img src="/Users/william/Documents/PhD/Typora Assets/eof_delay_winrate-1084284.png" alt="eof_delay_winrate" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/eof_delay_profit-1084292.png" alt="eof_delay_profit" style="zoom:67%;" />





# Strategic Bidding

## Impact of Latency

### Simulation Setup

**Three strategy profiles:**

**Profile 1:** 4 naive, 4 adaptive, 4 last-minute

**Profile 2:** 4 naive, 4 adaptive, 4 stealth

**Profile 3:** 4 naive, 4 adaptive, 4 bluff



**EOF Access Probablity: ** Randomly drawn from uniform distribution (0.8, 1) for all players across all three profiles

**Individual Delay: ** For each strategy group that contains 4 players in a profile, set 1 player with 1 step delay (10ms), 1 player with 2 step delay (20ms), 1 player with 3 step delay (30ms) and 1player with 4 step delay (40ms). This setting is same for all the strategy groups across all 3 strategy profiles.

**Auction Interval: **D = 12, std = 0.

**Profit Margin:** random drawn from normal distribution mean=0.00659, std=0.0001, for all players

**Global Delay:** Ranging from 1 step (10ms) to 10 steps (100ms)

10,000 auctions for each value of global delay.

**Because the simulation results of Profile 1 and Profile 2 are basically the same, only Profile 1 results are presented but can represent both Profiles.**

****

### Impact of Global Delay on Win Rate

**Profile 1&2**

<img src="/Users/william/Documents/PhD/Typora Assets/2.png" alt="2" style="zoom:50%;" />

1. Adaptive player struggle with win rate because of their reactive mechanism. They are already behind other naive-like players, and higher global delays make their reactions even slower.
2. Last-minute/Stealth players do have a clear edge because the revealing time $\epsilon = 0$ , which makes adaptive players not able to make a reaction on their revealing action. If they reveal earlier, we will see similar performance between last-minute/stealth and naive players (refer to the plot below).
3. The increasing of win rate in last-minute/stealth/naive is because adaptive players are losing more. Their bidding decision is not affected by delay, as naive players always update bids aggressively and last-minute/stealth player revealing actions take delay into account.

<img src="/Users/william/Documents/PhD/Typora Assets/winrate_last_eps03.png" alt="winrate_last_eps03" style="zoom:50%;" />



**Profile 3 With Bluff Players**

<img src="/Users/william/Documents/PhD/Typora Assets/Figure_1-0763587.png" alt="Figure_1" style="zoom:50%;" />

1. We see the performance of all the players is basically at the same level, because they are all naive-like players.
2. Adaptive players become naive-like players and do not struggle with higher global delay.



### Impact of Global Delay on Profit per Win

![profitdist_all_global](/Users/william/Documents/PhD/Typora Assets/profitdist_all_global.png)

1. Naive-like bidders, including naive players, last-minute/stealth/bluff players, and adaptive players who are affected by bluff players, have similar distributions of profit per win, which is very close to their profit margin. And their profit per win is not affected by global delay, because they always bid aggressively.

2. For "real" adaptive players, they have a higher profit per win than naive-like players because their reactive mechanisim allows them to win by only placing a small value over the current highest bid, optimizing their profit per win. 

   Moreover, we observe an increase when global delay becomes higher. In the scenarios where an adaptive player is winning the auction, a higher global delay situation gives the adaptive player more time to accumulate signals relative to when the highest bid is decided by the relay and place an extra small value over it to win. Despite possessing a higher aggregated signal, they still win by marginally exceeding the highest bid, thus making greater profits in scenarios with higher global delays. **The fundamental condition is the adaptive player is winning the auction.** 

   Let's say X is submitted at 11.9s, the delay is 0.05s, so it is accepted as the current highest bid and made known to other builders by the relay at 11.95s. We assume only the adaptive player can out bid this X, placing a X+delta to win. Between 11.9s and 11.95s, adaptive players have 0.05s to accumulate signals.

   Same situation but when delay is 0.02s, adaptive will have to bid X+delta at 11.92s, with a smaller window to accumulate signals, potentially leading to a lower profit.



### Impact of Global Delay on Average/Aggregated Profit

Average Profit and Aggregated Profit are basically the same, because average profit is calculated by aggregated profit divided by auction numbers.

<img src="/Users/william/Documents/PhD/Typora Assets/averageprofit_globaldelays.png" alt="averageprofit_globaldelays" style="zoom: 50%;" />

**When the global delay becomes higher, even if adaptive players in Profile 1&2 has a higher profit per win without the influence of bluff players, they still struggle with win rate, contributing to a low average/aggregated profit.**

### Impact of Individual Delay on Win Rate

<img src="/Users/william/Documents/PhD/Typora Assets/indidelay_winrate-0828778.png" alt="indidelay_winrate" style="zoom:25%;" />

For all the strategies, players with lower individual delays have a higher win rate than players with higher individual delay. With a lower individual delay, they can submit their bids later, and they are more likely to include late-arriving signals in their blocks.

*Last-minute/Stealth have a higher win rate than other naive-like bidders because their revealing time $\epsilon$ = 0.

### Impact of Individual Delay on Profit per Win

<img src="/Users/william/Documents/PhD/Typora Assets/profitdist_indidelay-0825283.png" alt="profitdist_indidelay" style="zoom: 25%;" />

Similar to the impact of global delay on profit per win, as the effect of global delay and individual delay is the same in our model, for adatpive players, adaptive players with lower individual delays achieve a higher profit per win than those with higher individual delays. Lower individual delays allow these players to subit their bids later, and this gives them more time to accumulate signals, include more profitable MEV opportunities in the blocks, have a higher aggregated signal, but still place a small value over the highest bid to win with more profit. 

Both global and individual delay do not have an impact on naive-like players' profit per win. Even if they have more time to accumulate signals, possess a higher aggregated signal, they still bid aggressively with their true valuation, and their profit per win is close to profit margin.



### Impact of Individual Delay on Average/Aggregated Profit

<img src="/Users/william/Documents/PhD/Typora Assets/indidelay_averageprofit.png" alt="indidelay_averageprofit" style="zoom: 33%;" />

**Despite Individual delay has no effect on profit per win for naive-like players, they should still optimize their latency, e.g. considering colocation to the relay server, so that they can have a higher win rate. And a higher win rate will give them a higher average/aggregated profit. **



## Impact of Revealing Time ($\epsilon$)

### Simulation Setup

Change the Individual Delay to be 1 for all the players

Fixed Global Delay = 40ms

Varying Epsilon values

The rest remains the same.

### Impact of Revealing Time of Last-minute and Stealth 

<img src="/Users/william/Documents/PhD/Typora Assets/last_winrate.png" alt="last_winrate" style="zoom:33%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/last_profit.png" alt="last_profit" style="zoom:33%;" />

**Bid Submission Time = 12 - $\epsilon$ - $\Delta$ - $\Delta_{last/stealth}$ ** **It means the revealing bid will be accepted by the relay at 12 - $\epsilon$**

$\epsilon < \Delta + \Delta_{adaptive}$ : Adaptive players cannot make an effective reaction based on the revealing action of last-minute/stealth, this makes last-minute/stealth very effective, even outperforming naive players, because adaptive players can still react to the update from normal naive players, but cannot react to the revealing from last-minute/stealth.

$\epsilon \geq \Delta + \Delta_{adaptive}$: Adaptive players have sufficient time to make an effective reaction before the auction terminates at 12. Last-minute/Stealth lose their edge and become normal naive players, adaptive players recover performance.



### Impact of Revealing Time of Bluff

<img src="/Users/william/Documents/PhD/Typora Assets/bluff_winrate-0850361.png" alt="bluff_winrate" style="zoom:33%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/bluff_profit.png" alt="bluff_profit" style="zoom:33%;" />

$\epsilon < \Delta + \Delta_{adaptive}$ : Adaptive players cannot make an effective reaction on bluff players' bid cancelling, which means they will be bidding like naive agents for the entire effective bidding period. We see close performance between all three strategy groups.

$\epsilon \geq \Delta + \Delta_{adaptive}$: Adaptive players have sufficient time to make an effective reaction before the auction terminates at 12, which means adaptive players are truly adaptive under this situation. Then adaptive players struggle with low win rate, and low win rate causes low average profit.

### Impact of Bluff Revealing Time on Adaptive Profit per Win

We also investigate the impact of revealing time of bluff players on adaptive player's profit per win. Since adaptive players are forced to bid their true valuation by bluff players, we observe a lower profit per win for adaptive players in Profile 3. 

With the revealing time of bluff players becoming earlier, adaptive players can have more room to be adaptive, which contributes to an increased profit per win. However, it is still lower, compared to adaptive players in Profile 1&2 without bluff players, this shows the impact of bluff players on adaptive players.

<img src="/Users/william/Documents/PhD/Typora Assets/epslion_adaptprofit.png" alt="epslion_adaptprofit" style="zoom:33%;" />

### Impact of Auction Termination Time ($D$)

### Simulation Setup:

Global Delay = 1, Individual Delay = 1

The Bluff bid is randomly drawn from uniform dist (0.3, 0.4)



### $\epsilon = 0$

$\epsilon = 0$ means that the revealing bid will be accepted at 12. For last-minute, stealth, and bluff players, revealing their true valuation at the final expected moment of the auction is a reasonable and strategic move, as they can potentially benefit from preventing adaptive players from having sufficient time to react. 

However, T is centered around 12 with a std. So whatever std value is, they have a 50% chance of failing to reveal before the auction terminates. So we will observe similar performance under varying std, as long as std is not 0. 

For last-minute/stealth, failing to reveal before termination means bidding too low and losing the auction, low win rate and low average profit. 

For bluff, failing to cancel before termination means bidding too high and winning with negative profit, high winrate and negative average profit.

#### Last-minute/Stealth

<img src="/Users/william/Documents/PhD/Typora Assets/std_winrate_last_eps0-0955369.png" alt="std_winrate_last_eps0" style="zoom:33%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/std_profit_last_eps0-0955358.png" alt="std_profit_last_eps0" style="zoom:33%;" />





#### Bluff

<img src="/Users/william/Documents/PhD/Typora Assets/stdwinrate_bluff_eps0.png" alt="stdwinrate_bluff_eps0" style="zoom:33%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/std_profit_bluff_eps0-0955112.png" alt="std_profit_bluff_eps0" style="zoom:33%;" />

**Because of the impact of bluff players, we see similar performance between adaptive and naive players. **

**However, one interesting thing is that, especially from the win rate plot, we see when the std becomes greater, the performance gap between naive and adaptive becomes greater. This is because, with a greater std, the auction has a larger chance of being terminated at a relatively later time after 12, than with a smaller std.  For example, when the std is 0.2, the auction could terminate at a very late time like 12.5. But it rarely happens when std is 0.1**

**In this case, when the auction terminates at a relatively later time after 12 and bluff players cancel at 12, it means that adaptive can have more time to be truly "adaptive", thus having a lower win rate performance than naive players. **



### $\epsilon > 0 (\epsilon = 0.2)$

**Last-minute/Stealth/Bluff can reveal their bid earlier than 12 to account for the uncertainty.**

**Here we should see when the std becomes greater, the three strategy has a greater chance of failing to reveal before the termination.**

#### Last-minute/Stealth

<img src="/Users/william/Documents/PhD/Typora Assets/std_winrate_last_eps02-0956142.png" alt="std_winrate_last_eps02" style="zoom:33%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/std_profit_last_eps02.png" alt="std_profit_last_eps02" style="zoom:33%;" />



#### Bluff

<img src="/Users/william/Documents/PhD/Typora Assets/std_winrate_bluff_eps02.png" alt="std_winrate_bluff_eps02" style="zoom:33%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/std_profit_bluff_eps02.png" alt="std_profit_bluff_eps02" style="zoom:33%;" />

**Different from $\epsilon = 0$, when $\epsilon = 0.2$, bluff players will cancel their bluff bids at 11.8, so in most cases, adaptive do have time to be truly "adaptive", we then see the difference in performance between naive and adaptive. **

