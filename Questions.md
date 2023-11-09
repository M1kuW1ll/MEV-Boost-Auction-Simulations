# Things we want to look into or whatever

**Three big common factors (parameters) that we can play with: Global Delay, Individual Delay, EOF (Probability of accessing private signals)** 

We want to look into how these three factors affect the performances of different strategies, some of them may have huge impact on certain strategies, some of them may have 0 impact on certain strategies

When we look into the impact of one of these factors, we should keep the other two to be the same across all players, or to be the same in certain forms.



**Other small factors (parameters) that we can play with later: Epsilon (reveal time of last-minute, stealth, bluff), Delta (Adaptive extra value over the highest)** , D (auction time), impact of number of players on efficiency



**Which strategy is better? Or under certain situations, which strategy is the best response? **

Payoff: Aggregated Profit, Average Profit, Win Rate

We may start with something easy, put away some strategies, fixed and limited number of players, same delay for everyone

then we can try to include the dynamics, such as delays



**Average or Aggregated profit**

It depends on the definition of the "game". If the game is to win the maximum profit in the next 1,000 games, then we should use aggregated profit as payoff. If the game is to win the maximum profit in the next one game, we should use average profit.



the definition of average profit

"Average" = **Aggregated Profit/Total numbers of auctions** (average profit per auction)

= Aggregated Profit/Wins (average auction per win)

Variance

equilibrium average+/-std



#  Auction Mechanism (Naive Only)

## Impact of Global Delay on Profit

### Simulation Setup

**10 Naive Agents (Fixed Strategy)**

**Auction Interval (Random): **random variable drawn from normal distribution mean=12, std=0.1

Time step length: 0.01s per step

**Probability of EOF (Fixed):** set to 1 for all players, which means all the players will have full access to private signals

**Individual Delay (Fixed):** set to 1 step (10ms) for all players

**Profit Margin (Random)： ** random variable drawn from normal distribution mean=0.00659, std=0.0001

**Global Delay:** From 1 step (10ms) to 10 steps (100ms)

5,000 games for one value of global delay, 50,000 times in total



### Profit on Different Global Delays

Suppose that the winning bid is submitted by the player A at time t. 
$$
Profit = A.Aggregated.Signal_t - A.Bid (Winning.Bid)
$$
$$
u_i = S_i(t) - b_i,_t
$$

$$
S_i(t) = P(t) + E_i(t)
$$

![avgprofit_globaldelay](/Users/william/Documents/PhD/Typora Assets/avgprofit_globaldelay.png)

![profitdist_globaldelay](/Users/william/Documents/PhD/Typora Assets/profitdist_globaldelay.png)



## Impact of EOF on Win Rate

### Simulation Setup

**10 Naive Agents (Fixed Strategy)**

**Auction Interval (Random): **random variable drawn from normal distribution mean=12, std=0.1

Time step length: 0.01s per step

**Probabbility of EOF (Fixed):** start from 0.8 to 0.98, 0.02 increments

**Individual Delay (Fixed):** set to 1 step (10ms) for all players

**Profit Margin (Random)： ** random variable drawn from normal distribution mean=0.00659, std=0.0001

**Global Delay (Fixed):** 1 step 

10,000 Games

![eof_winrate](/Users/william/Documents/PhD/Typora Assets/eof_winrate.png)



## Impact of EOF on Profit

<img src="/Users/william/Documents/PhD/Typora Assets/eof_profitdist.png" alt="eof_profitdist" style="zoom:67%;" />



Having a higher probability to access private signals does not necessarily contribute to a higher profit per game. This is because naive strategy is an aggressive bidding strategy, even if players with a high probability tend to access more signals, they simply keep raising their bids.

But the aggregated profit will be higher for players with higher probability, because they are winning more games.

<img src="/Users/william/Documents/PhD/Typora Assets/eof_aggregate.png" alt="eof_aggregate" style="zoom:67%;" />

## Efficiency

### Impact of Global Delay on Efficiency


$$
efficiency = Winning.Bid / Total.Signal.Value
$$

<img src="/Users/william/Documents/PhD/Typora Assets/avgeff_globaldelay.png" alt="avgeff_globaldelay" style="zoom:67%;" />

winner's aggregated signal / total signal

<img src="/Users/william/Documents/PhD/Typora Assets/effdist_globaldelay.png" alt="effdist_globaldelay" style="zoom:67%;" />

0.085 public signal per step  0.00027	

0.04 private signal per step

10 steps of delay means the players may fail to include 0.85 public signal + 0.4 private signal in their bid

1.25 / 150   0.008

### Impact of number of players on efficiency

Simulation setup: 

EOF: uniform dist (0.8, 1)

Individual delay: randint (1, 5)

Global delay: 1 step

Number of players: from 1 to 10

average efficiency of 10,000 auctions 

<img src="/Users/william/Documents/PhD/Typora Assets/playernumber.png" alt="playernumber" style="zoom:67%;" />



# Strategic Bidding

## Simulation Setup (Strategy Profile)

**16 Uniform Agents:** 4 Naive, 4 Adaptive, 4 Last-minute, 4 Stealth, 0 Bluff

**Time step length:** 0.01s per step

**Auction Interval:** randomly drawn form a normal distribution with mean=12 and std=0.1

**Probability of EOF:** randomly drawn from a uniform distribution (0.8, 1.0) for all players every game

**Global Delay:** From 1 step (10ms) to 10 steps (100ms)

**Individual delay:** Each strategy has one player with an individual delay of 1, 2, 3, and 4

10,000 times of simulation (10,000 games) for each value of global delay ranging from 1 to 10, 100,000 times (100,000 games) in total for one run

**Epsilon Value:** Last-minute and Stealth strategy acts 40 to 50 steps (0.4-0.5s) plus delay before their estimation, estimation set to 12

**Delta Value: 0.0001** Adaptive Agents will place an extra value of 0.0001 over the highest bid available

**Bluff strategy is not considered in the profile to make sure the performance of adaptive strategy**

**Agent ID Distribution: Naive: 0-3, Adaptive 4-7, Last-minute 8-11, Stealth 12-15. Individual delay value is in sequence. For example, naive agent id 0 has 1 individual delay, naive agent 1 has 2 individual delay, adaptive agent id 4 has 1 individual delay, adaptive agent id 5 has 2 individual delay, and so on so forth.**



## Impact of Global Delay on Win Rate

EOF (probability): for all players, drawn randomly from uniform Dist (0.8, 1)

Individual delay: for all strategies, we have 1 player with 1, 2, 3, 4 steps of individual delay

### Win Rate by Strategy on Different Global Delays

![st_winrate_global](/Users/william/Documents/PhD/Typora Assets/st_winrate_global.png)



### Win Rate by Player on Different Global Delays

![st_winrate_player](/Users/william/Documents/PhD/Typora Assets/st_winrate_player.png)

### Win Rate of Adaptive Players on Different Global Delays

![st_adaptwin_global](/Users/william/Documents/PhD/Typora Assets/st_adaptwin_global.png)

**We know adaptive strategy players's performance are affected by global delay. For adaptive players with different individual delays, are they going to suffer the similar level of impact of global delay?**

**Similar Plots for naive, last-minute and stealth strategy is not necessary, simply because they are not affected by global delay.**





### Conclusions

- **Naive Strategy:** The win rate of naive strategy basically stays stable across all values of global delay, as they consistently update their bid aggressively as long as they receive a new signal. Their win rate is not affected by the change of global delay, and their win rate goes up slightly when the delay becomes higher, typically because adaptive players are losing more and more.
- **Last-minute and Stealth Strategy:** The win rate of last-minute and stealth strategy also stays stable across all values of global delay, as they hold their bid and reveal their true value based on their own estimation. After revealing their true bid, they turn to use naive strategy, so their win rate is very similar to naive strategy. Their reveal time is calculated as 

$$
12 - ε - global.delay - individual.delay
$$

​		which means, the reveal time is not affected by the global delay. Their win rate also goes up slightly when the delay becomes 		higher as adaptive player loses.

- **Adaptive Strategy:**  From the first two plots, the win rate of adaptive strategy goes lower when the global delay becomes higher. This is because, when the global delay is higher, they will have a slower reaction, which means they will react to an older bid. The situation might already have changed during this interval before adaptive players make a reaction. And also, their reaction will also be revealed later, and until their reaction has been revealed, the situation might change again.
- **The global delay's impact level on adaptive strategy when individual delay is different:** By looking into the plot about the win rate of adaptive players on different global delays, we can to some extent identify if the impact level of global delay is different when the individual delay is different for adaptive players. We can see the gradients of the 4 lines in the plot are very close, which means the impact level of global delay is very similar for adaptive players with different individual delays. This is very reasonable because global delay and individual delay serve the same function in the model, even if they have different real-world representations.
- **Optimistic and non-optimistic relay: Adaptive strategy players Naive, Last-minute and Stealth strategy players  **



## Impact of Global Delay on Profit

EOF (probability): for all players, drawn randomly from uniform Dist (0.8, 1)

Individual delay: for all strategies, we have 1 player with 1, 2, 3, 4 steps of individual delay



### Profit Distribution by Strategy on Different Global Delays (Profit Per Win)

EOF (probability): for all players, drawn randomly from uniform Dist (0.8, 1)

Individual delay: for all strategies, we have 1 player with 1, 2, 3, 4 steps of individual delay

**Specific Players**

If we want to be more rigorous here to see the impact of global delay on the profit, we can check the performance of one specific player of a strategy, so that we keep individual delay the same here. 

<img src="/Users/william/Documents/PhD/Typora Assets/naive_profitdist.png" alt="naive_profitdist" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/adapt_profitdist.png" alt="adapt_profitdist" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/lastminute_profitdist.png" alt="lastminute_profitdist" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/stealth_profitdist.png" alt="stealth_profitdist" style="zoom:67%;" />

![all_profitdist](/Users/william/Documents/PhD/Typora Assets/all_profitdist.png)

**Naive/Last-minute/Stealth:** Very similar. Last-minute and stealth players will turn naive near the end of the auction, as these three strategies always bidding aggressively, their bidding action is not affected by global delay, so their profit is not affected by global delay either. 

**Adaptive: ** We observe that when the global delay becomes higher, adaptive players tend to have higher profits.

Suppose that Global Delay = 10 steps. **The winning bid is chosen at 12 (1200th time step), and it is won by an adaptive player.** Let this winning bid value be X+0.0001, as the global delay is 10, it is submitted by the adaptive player at the step of 1191. Well, from this we can know that at the step of 1190, the highest bid on the relay is X (adaptive delta 0.0001). This X bid is submitted by someone at the step of 1181. 

**Please bear in mind that X+0.0001 is the highest winning bid, therefore, from step 1181 to 1191, no bid higher than X+0.0001 is submitted by any player.**

| Time Step             | 1200 (end of auction)  | 1191              | 1190                | 1181 (X submitted) |
| --------------------- | ---------------------- | ----------------- | ------------------- | ------------------ |
| Highest on the relay  | X+0.0001 (winning bid) |                   | X (current highest) |                    |
| Adaptive Player's Bid |                        | X+0.0001 (Submit) |                     |                    |

Now Suppose that Global Delay = 5 steps. Let's see what happens.

| Time Step             | 1200 (end of auction)  | 1196              | 1195                | 1191 (X submitted) |
| --------------------- | ---------------------- | ----------------- | ------------------- | ------------------ |
| Highest on the relay  | X+0.0001 (winning bid) |                   | X (current highest) |                    |
| Adaptive Player's Bid |                        | X+0.0001 (Submit) |                     |                    |

**Again, please bear in mind that this analysis is based on adaptive player winning the auction with X+0.0001. **

**The difference is: When the global delay is higher, the adaptive player will have more time to accumulate signals, outbid the current highest bid with the same bid value (X+0.0001), and win the auction.  More specifically, in the second situation when global delay = 5, the adaptive player 5 steps (1191-1195) to accumulate his signals to bid X+0.0001. But in the first situation when global delay is 10, adaptive players have 10 steps (1181-1190) to accumulate signals to bid the exact same X+0.0001. Therefore, in the case above, the adaptive player may have a higher aggregated signal when the delay is higher, but the winning bid value is the same, so it contributes to higher profit.  ** 



**Make it short: **

Fundamental condition: Adaptive player wins the auction with X+delta

X is submitted at step n and is the highest among all the bids submitted at step n, and is going to be accepted by relay at step n+global_delay. After X is accepted, adaptive player will bid X+delta, and wins the auction. Therefore, the length of global delay is the time for adaptive players to accumulate signals. 



### Aggregated Profit by Strategy on Different Global Delays

![all_aggregate](/Users/william/Documents/PhD/Typora Assets/all_aggregate-8245943.png)

Aggreagated Profit: 1. Times of winning (win rate)  2. Profit per win 

**Naive/Last-minute/Stealth Strategy:** Very similar. They are winning more times, because adaptive players are losing as they suffer from high delay. So aggregated profits are increasing.

**Adaptive Players:** Even though they have more profits per win when the global delay is higher, their win rate is decreasing, they are winning less auctions, so their aggregated signals are decreasing.



## Impact of Individual Delay on Profit

![allplayer_profitdist](/Users/william/Documents/PhD/Typora Assets/allplayer_profitdist.png)

We can observe that, for all 4 strategies, players with lower individual delays tend to have higher profits.

Let's break it down. Let's consider the two situation below:

Situation 1: Suppose that Global Delay is 1 step. The winning bid is chosen at the step of 1200.  **Let's assume X is the value of the bid that is needed to win the auction.** The player has a individual delay of 1 step. In this situation, the latest time point for this player to bid X and win the auction is step 1199. It means, the player will have 1199 steps of time to accumulate his signals. 

Situation 2: Same setup. Global Delay is 1 step. The winning bid is chosen at the step of 1200. **Let's assume X is the value of the bid that is needed to win the auction. But The player has a individual delay of 5 step.** In this situation, the latest time point for this player to bid X and win the auction is step 1195. It means, the player will have 1195 steps of time to accumulate his signals. 



In situation 1, the player with a lower individual delay can have more time to accumulate signals. As the winning bid is the same in both situations, the player have more time to accumulate signals, meaning that the player tend to have more aggregated signal, and therefore more profit. 





## Difference between Global Delay and Individual Delay

It is a little bit confusing. When I am trying to explain the global delay, we say a higher global delay can give players more time to accumulate signals. But when it comes to individual delay, we say lower individual delay can give players more time to accumulate signals. **Is it contradictory? No, it is not.**

- **Global Delay**: When the global delay is higher, the entire system takes longer to process the bids. Like a "frozen" window during the auction, **all players** are affected. **It is an exclusive advantage to adaptive players (when the adaptive players are winning), because longer global delay allows them more time to accumulate signals relative to when the highest bid is decided by the system, and place an extra delta over it to win. ** But this advantage is not available to naive players, because even if they have more time to accumulate signals, they will bid everything they have. 
- **Individual Delay**: The lag between the player's bid submission and that bid being accepted. A lower individual delay allows the player to wait longer before submitting a bid, providing him more time to accumulate signals. Therefore, when the bids are accepted by the relay at the same time, players with lower individual delay can submit later and have more time to accumulate signals, but players with higher individual delay have to submit earlier.

- In both cases, having more time to accumulate signals (and bidding the same) is key reason for having more profit. 

- For global delay: **Exclusive for adaptive players when adaptive players win the auction. ** Adaptive players have a larger window to accumulate signals, but only bid X+delta to win the auction.

- For individual delay: **Available for all players.** Players with lower individual delay can submit their bid later, and have more time to accumulate signals.  

  

## Impact of Individual Delay on Win Rate

<img src="/Users/william/Documents/PhD/Typora Assets/winrate_naive_indi.png" alt="winrate_naive_indi" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/winrate_adapt_indi.png" alt="winrate_adapt_indi" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/winrate_last_indi.png" alt="winrate_last_indi" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/winrate_stealth_indi.png" alt="winrate_stealth_indi" style="zoom:67%;" />

**Conclusion:** For all 4 strategies, players with lower individual delays have higher win rates. 



## Impact of Epsilon

Same simulation setup as above.

Global Delay set to 1.

### Win Rate

<img src="/Users/william/Documents/PhD/Typora Assets/winrate-8066200.png" alt="winrate" style="zoom:67%;" />

### Aggregated Profit

![Figure_1](/Users/william/Documents/PhD/Typora Assets/Figure_1-8066223.png)



## Including Bluff

Simulation Setup: Replacing 4 stealth agents with 4 bluff agents, no other changes,

Epsilon: 0.4

### Win Rate

<img src="/Users/william/Documents/PhD/Typora Assets/winrate_4bluff.png" alt="winrate_4bluff" style="zoom:67%;" />

### Aggregated Profit

![aggregated_profit](/Users/william/Documents/PhD/Typora Assets/aggregated_profit-8662824.png)

## Std = 0 (Auction interval fixed to 12.00s), change Epsilon

### Simulation Setup

We assume there will always be naive agents.

4 Naive Agents + 4 Adaptive Agents + 4 Last-minute/stealth/bluff Agents, 12 Agents in total

Fixed D std = 0, meaning the auction will always terminate at 12 seconds

Individual delay set to 1 for all players

We change epsilon (the reveal time of true bid value) to see the how the strategies' effectiveness changes

### Last-minute Strategy

#### std = 0, epsilon = 0

For last-minute/stealth/bluff agents, true-value bid submission time = 1200 - epsilon - global_delay - individual_delay

Epsilon  = 0, meaning that they will reveal their true bid value until 1200, which is the last time step of the auction. Therefore, adaptive agents do not have time to react to their true bid. 

More accurate description: Their true bid value will be accepted by the relay at 1200, because their action takes delay into account.

<img src="/Users/william/Documents/PhD/Typora Assets/last_eps0-8664042.png" alt="last_eps0" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/last_eps0.1.png" alt="last_eps0.1" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/profit_last_eps0.png" alt="profit_last_eps0" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/adaptprofit_last_eps0.png" alt="adaptprofit_last_eps0" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/adaptprofit_last_eps0-8668063.png" alt="adaptprofit_last_eps0" style="zoom:67%;" />



#### std = 0, epsilon = 0.1

1190

<img src="/Users/william/Documents/PhD/Typora Assets/last_eps0.1.png" alt="last_eps0.1" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/aggregated_last_eps0.1.png" alt="aggregated_last_eps0.1" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/adaptprofit_last_eps0.1.png" alt="adaptprofit_last_eps0.1" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/lastprofit_last_eps0.1.png" alt="lastprofit_last_eps0.1" style="zoom:67%;" />

We observe a huge drop for adaptive agents' win rate when global delay is 10 steps. This is because, epsilon is  0.1 (also 10 steps), meaning that last-minute agents are going to reveal their true bid value 10 steps before the auction terminates. So there's no way adaptive agents are capable of reacting to this, their delay is 11 steps (10 global + 1 individual), their reaction bid is not going to make it to the relay before the end of the auction.

#### Std = 0, epsilon = 0.2

<img src="/Users/william/Documents/PhD/Typora Assets/last_eps0.2.png" alt="last_eps0.2" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/aggregated_last_eps0.2.png" alt="aggregated_last_eps0.2" style="zoom:67%;" />





#### Std = 0, epsilon = 0.3

<img src="/Users/william/Documents/PhD/Typora Assets/last_eps0.3.png" alt="last_eps0.3" style="zoom:67%;" />



#### Std = 0, epsilon =0, 0.1, 0.2, 0.3 ...

We don't really need to take delay into account here. We can keep the global delay fixed to 1, and see how the performance of last-minute strategy changes with different epsilon values. 

If epsilon > adaptive agents global delay + individual delay 

adaptive can react

submission time = 12 - epsilon - globaldelay - individual

<img src="/Users/william/Documents/PhD/Typora Assets/epsilon_winrate-8935477.png" alt="epsilon_winrate" style="zoom:67%;" />

![epsilon](/Users/william/Documents/PhD/Typora Assets/epsilon-8935485.png)



### Stealth Strategy

#### std = 0, epsilon = 0

<img src="/Users/william/Documents/PhD/Typora Assets/stealth_eps0.png" alt="stealth_eps0" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/aggregated_eps0.png" alt="aggregated_eps0" style="zoom:67%;" />





#### std = 0, epsilon = 0.1

<img src="/Users/william/Documents/PhD/Typora Assets/stealth_eps0.1-8669815.png" alt="stealth_eps0.1" style="zoom:67%;" />

#### std = 0, epsilon = 0.2

<img src="/Users/william/Documents/PhD/Typora Assets/stealth_eps0.2.png" alt="stealth_eps0.2" style="zoom:67%;" />

#### std = 0, epsilon = 0.3

<img src="/Users/william/Documents/PhD/Typora Assets/stealth_eps0.3-8666402.png" alt="stealth_eps0.3" style="zoom:67%;" />

#### Std = 0, epsilon =0, 0.1, 0.2, 0.3 ...

![epsilon_winrate](/Users/william/Documents/PhD/Typora Assets/epsilon_winrate-8762404.png)

![epsilon_profit](/Users/william/Documents/PhD/Typora Assets/epsilon_profit-8762416.png)



### Bluff Strategy

#### Std = 0, epsilon =0

<img src="/Users/william/Documents/PhD/Typora Assets/winrate_eps0.png" alt="winrate_eps0" style="zoom:67%;" />

We can oberseve that, under the impact of bluff agents, adaptive agents no longer struggle with high global delay as they bid like naive agents. 

It seems adaptive agents's win rate is decerasing consistently at global delay 7,8,9,10. It's just a matter of luck. 

<img src="/Users/william/Documents/PhD/Typora Assets/winrate_eps0_2.png" alt="winrate_eps0_2" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/winrate-8747068.png" alt="winrate" style="zoom:67%;" />

And we can also see the profit per win distribution of adaptive agents on different global delays. If they are behaving like "adaptive", their profit per win should increase when global delay becomes higher. But profit per win is very stable, and close to profit margin, so they are behaving like naive, which means their win rate should not be affected by global delay either. 



<img src="/Users/william/Documents/PhD/Typora Assets/adaptprofit_eps0.png" alt="adaptprofit_eps0" style="zoom:67%;" />

![allprofit_eps0](/Users/william/Documents/PhD/Typora Assets/allprofit_eps0-8746942.png)

![aggregated_eps0](/Users/william/Documents/PhD/Typora Assets/aggregated_eps0-8748217.png)

**Comparison between impact of bluff and impact of last-minute/stealth**

- Naive agents can sometimes out-perform bluff agents. But naive agents never out-perform last-minute and stealth.
- Under the impact of bluff, adaptive strategy can sometimes out-perform naive agents and bluff agents.



#### Std = 0, epsilon = 0.1

<img src="/Users/william/Documents/PhD/Typora Assets/winrate_eps0.1.png" alt="winrate_eps0.1" style="zoom:67%;" />

When epsilon = 0.1, bluff agents reveals true bid value at 11.9s, meaning that adaptive agents will start to behave "adaptive" during the last 10 time steps of the auction. Therefore, adaptive agents start to struggle with high delay in terms of win rate. 

Because adaptive agents now can bid adaptive at the last 10 steps, we can obeserve a higher profit per win for adaptive agents than naive agents. But for the aggregated profit, it is decreasing, because adaptive is winning fewer games when delay goes up.

![allprofit_eps0.1](/Users/william/Documents/PhD/Typora Assets/allprofit_eps0.1.png)

![aggregated_eps0.1](/Users/william/Documents/PhD/Typora Assets/aggregated_eps0.1.png)

When global delay is 10 steps, all the player will not be able to take the action of bidding after 1190. So technically, the bidding interval terminates at 1190. Epsilon is 0.1, meaning that bluff will reveal their true bid until 1190. This means during the entire bidding interval 1190, adaptive agents are bidding like naive agents all the time, which makes them have very high win rate and high aggregated profit. 



We can also take a look at the profit per win of adaptive players. Normally, we expect it to increase when global delay becomes higher (when no bluff in game).  But we don't see it here. This is probably caused by the impact of bluff agents. With epsilon = 0.1, adaptive players can only be adaptive at the last 10 time steps (competely naive when global delay = 10), during the first 11.9 seconds, adaptive players are bidding like naive, they cannot accumulate their profit by placing only an extra delta over the highest bid.

<img src="/Users/william/Documents/PhD/Typora Assets/adaptprofit_eps0.1.png" alt="adaptprofit_eps0.1" style="zoom:67%;" />



#### Std = 0, epsilon = 0.2

<img src="/Users/william/Documents/PhD/Typora Assets/winrate_eps0.2.png" alt="winrate_eps0.2" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/aggregated_eps0.2.png" alt="aggregated_eps0.2" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/allprofit_eps0.2.png" alt="allprofit_eps0.2" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/adaptprofit_eps0.2.png" alt="adaptprofit_eps0.2" style="zoom:67%;" />

With epsilon = 0.2, Adaptive agents can behave "adaptive" for the last 20 steps. This will make them have a higher profit per win, compared to epsilon = 0.1. Also higher aggregated profit compared to epsilon = 0.1.



#### std = 0, epsilon = 0.3

<img src="/Users/william/Documents/PhD/Typora Assets/winrate_eps0.3.png" alt="winrate_eps0.3" style="zoom:67%;" />

![aggregated_eps0.3](/Users/william/Documents/PhD/Typora Assets/aggregated_eps0.3.png)

<img src="/Users/william/Documents/PhD/Typora Assets/adaptprofit_eps0.3.png" alt="adaptprofit_eps0.3" style="zoom:67%;" />



#### Std = 0, epsilon = 0, 0.1, 0.2, 0.3...

Global delay = 1, Individual delay = 1

![epsilon_winrate](/Users/william/Documents/PhD/Typora Assets/epsilon_winrate-8934718.png)

![epsilon_profit](/Users/william/Documents/PhD/Typora Assets/epsilon_profit-8881184.png)

Global Delay = 4, Individual Delay = 1

![epsilon_winrate_delay4](/Users/william/Documents/PhD/Typora Assets/epsilon_winrate_delay4.png)

<img src="/Users/william/Documents/PhD/Typora Assets/epsilon_profit_delay4.png" alt="epsilon_profit_delay4" style="zoom:67%;" />



## Fixed Epsilon, change std of D (Auction Interval)

Changing the value of std (std != 0) means that the auction termination time is going to be random. So it is possible that last-minute/stealth/bluff agents will miss their reveal time, because the auction might be terminated by the auctioneer before they reveal their true bid value. For last-minute/stealth, they will lose the chance of winning because of bidding too low (last-minute bid only 0, stealth bid only public signal). For bluff agents, they will win the auction with their bluff bid, but they will lose profit.

### Epsilon = 0, change D std

<img src="/Users/william/Documents/PhD/Typora Assets/eps0_last:stealth_winrate.png" alt="eps0_last:stealth_winrate" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/eps0_last:stealth_profit.png" alt="eps0_last:stealth_profit" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/eps0_bluff_winrate-8928771.png" alt="eps0_bluff_winrate" style="zoom:67%;" />

![eps0_bluff_profit](/Users/william/Documents/PhD/Typora Assets/eps0_bluff_profit-8928787.png)

When epsilon = 0, last-minute/stealth/bluff will always reveal their bid at 12 seconds. While D is centered around 12, so no matter what value the epsilon is, the chance of revealing the bid successfully before the auction termination is 50%. That's why we see the win rate and profit is similar whatever the epsilon value is. 

### Epsilon = 0.2, change D std

<img src="/Users/william/Documents/PhD/Typora Assets/eps0.2_laststealth_winrate.png" alt="eps0.2_laststealth_winrate" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/eps0.2_laststealth_profit.png" alt="eps0.2_laststealth_profit" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/eps0.2_bluff_winrate.png" alt="eps0.2_bluff_winrate" style="zoom:67%;" />

<img src="/Users/william/Documents/PhD/Typora Assets/eps0.2_bluff_profit.png" alt="eps0.2_bluff_profit" style="zoom:67%;" />

## Naive VS Adapt and Adapt VS Last-minute

5 Naive VS 5 Adapt (delta 0.0001)

Naive Agents Winning: 5238
Adaptive Agents Winning: 4762

Average Profit for Naive Agents: 34.31805434378255
Average Profit for Adaptive Agents: 32.08033769163845



5 Adapt (delta 0.0001) VS 5 Last-minute (epsilon uniform 0.1-0.2) 

Naive Agents Winning: 4841
Adaptive Agents Winning: 5159

Average Profit for Naive Agents: 66.58247953756855
Average Profit for Adaptive Agents: 33.7988614802406

