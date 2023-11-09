## 7.13 Meeting 

1. Cumulative Poisson process. The expression only describes the number of incoming signals. 

   

2. Two kinds of delays. One individual delay only affects bid submission, one global delay for everyone also only affects bid submission. Non-optimistic and optimistic relays.



## 7.26 Meeting Changed Signal Part

**Public Signal:**

The number of the signals N(t) is still represented as a cumulative poisson process, N(t) = Poisson(λ*t), where λ is randomly drawn from a log-normal distribution. The value of each signal is also randomly drawn from a log-normal distribution.

Questions:

1. Why use log-normal distribution to describe the rate parameter λ of the Poisson Process, and the value of each signal?
2. Is the rate parameter λ fixed across all the time? Like, we simply draw a sample randomly from log-normal and use its value for all time. Or, will λ change? Like, for each time step, we draw a new sample (new value to λ) and calculate the Poisson.
3. Is it better to set it a fixed value instead of drawing a random sample? For the private signal, we can draw the value randomly from the distribution for the private rate parameter, because everyone will probably have access to different numbers of private signals. But public signals, everyone is the same. 



**Private Signals:**

1. Representation

2. Correlation



## Simulation Assumptions

1. Time is modeled as **discrete time** steps with Mesa. The length of each time step can be defined as whatever we want, currently we have 0.05s for one step. 50ms seems to be reasonable for real-world situation. 

2. Agents (Players) have to **place a bid every time step**. For naive agents, they will place a new bid based on their changed signals. For adaptive agents, changed signals and highest available bid. For last-minute (bluff) agents, they will bid 0 (bluff value) every step before reveal time. 

3. **Delay**: Both individual and global delay only affect the **bid submission**.

4. **Simultaneous Activation**. All the agents will be updated simultaneously, meaning they all observe and react to the current state of the model simultaneously. This approach eliminates potential biases introduced by sequential updates and allows for a more synchronized behavior among the agents.

5. **Honest Proposer**. We assume the proposer will only call getHeader once and terminate the auction. This is for bid cancellation by bluff strategy players.

6. **Private signals**: Private signals are available to all players at the same time, but with a different probability for different players. The probability is consistent during the entire auction. In real-world situation, it means, if a searcher is sending a bundle to multiple builders, it is received by the builders at the same time.

   

## Recap

Block builders are bidders. They receive public signals and private signals as their funds. Public signals are transactions with priority fees in the public mempool submitted by users, which are accessible for all the builders. Private signals are private transaction bundles with private priority fees sent from searchers to builders. Most of private signals are correlated and shared between multiple builders, because searchers send their bundle to multiple builders. The number of public and private signals are defined using two Poisson distributions. The value of each signal is drawn randomly from a log-normal distribution. 

Time is modelled as discrete time steps using Mesa.



Five Strategies:

Naive Strategies: Players consistently bid aggressively.

Adaptive Strategies: Players make a reaction based on the highest bid of the last time step. If they can out bid the highest bid of last time step, they add an extra delta to the highest bid. If they cannot out-bid the highest bid, they will turn naive.

Last-minute Strategies: Player hold their bids until their reveal time, and bid aggressively after reveal time (naive). The reveal time is an estimation made by player, delay is also accounted, so this strategy will not be affected by delay. But they may still miss the reveal time and lose the chance to win the game, due to a bad estimation.

Stealth Strategy: Similar to Last-minute Strategy, but players only bid their public signals to hide their true value until reveal time.

Bluff Strategy: Also similar to last-minute and stealth strategy. Players will make a super high bluff bid before reveal time, and after reveal time cancel the bluff bid and reveal their true value. But if their estimation is bad, they may end up winning the game with their bluff bid.



## Short Meeting with Thomas

**Private Signal:** Private Signals are private transaction bundles with private priority fees sent from searchers to builders. The correlation lies in the fact that searchers can send the same bundle to multiple builders, and because the transactions are the same, MEV opportunities are **pretty much close**, so the value yielded from the transactions (the value of this private signal) is **pretty much close** for the builders (players). 



**My thoughts:** We can still consider the private signals as **independent** for the builders for the following reasons: 

1. If a searcher is sending a bundle to multiple builders, this private signal is correlated or shared between these builders. Yes, the transactions are the same, and this determines the most part of the value of this signal. But different builders may use different methods to extract MEV, which means MEV can be a little different. Also, the extra private priority fee paid by the searcher can be different for different builders, according to the builder's reputation, network status, etc.

   In a word, the value of a correlated private signal can be slightly different for different builders.

2. If a searcher is sending a bundle to multiple builders, the timing when the builders receive the signal can be different. There can be many factors here, delay from the searcher, delay from the builders. 



And I think it is reasonable to model independent private signal, if we want to put more emphasis on the strategies, or questions like which strategy is better. Having the signals with different values and different times gives the players/strategies more room to act accordingly. 



**Current Design:**

We will use the **probability** to represent the correlation of private signals between different players. The probability means, at one specific time step during the auction (simulation), if there is a private signal available, the private signal is available to all the players, but with a probability. **So, the only difference between private signal and public signal is that there is a probability for each builder to access it.**

In real-world situations, searchers will send the bundles to all the builders, with the same value and at the same time. But there is a probability for each builder to have the signal or not. If the builder is unlucky and fails to get the signal, we can assume that the searcher is not sending it to that builder. **We can also think of this probability as a proportion, which the players will get among total number of signals. Note that it is the number of signals is proportional, signal value is not proportional because each signal may have a different value randomly drawn from the log-normal distribution.**

The probability of accessing a private signal for each player is randomly assigned, and consistent across the entire auction. We should consider the situation where some builders do connect most of the searchers have access to most of the private signals.

In this situation, pretty much all the players are having the signals with the same value at the same time, except for the probability.

 <img src="/Users/william/Documents/PhD/Typora Assets/Independent_sim-3388538.png" alt="Independent_sim" style="zoom:67%;" /><img src="/Users/william/Documents/PhD/Typora Assets/probability_simulation-3388546.png" alt="probability_simulation" style="zoom:67%;" />



## Some simulation results



### Potential Research Directions

1. If naive strategy is the dominant strategy across all delays when bluff strategy is absent

2. The effect of bluff strategy's introduction on other strategies

3. The effect of delay on all strategies

   

### **Strategy Profile 1** (16 Agents without Bluff)

16 Agents: 4 Naive, 4 Adaptive, 4 Last-minute, 4 Stealth, 0 Bluff

Time step length: 10ms/0.01s per step (around 1200 steps for an auction interval)

Global Delay: From 1 step (10ms) to 10 steps (100ms)

Individual delay: randomly from 3 to 5 steps (30/40/50ms)

3000 times of simulation for each delay, 30,000 times in total for one run

**Last-minute and Stealth strategy acts 8 to 10 steps (80ms-100ms) plus delay before their estimation**

**Delta Value: Adaptive Agents will place an extra value over the highest bid available**

#### Delta 0.0001 Signal value average is 0.00025-0.00027

**Run 1**

Naive Agents Winning: 9914 33.04%
Adaptive Agents Winning: 5481 18.27%
Last-minute Agents Winning: 7278 24.26%
Stealth Agents Winning: 7327 24.42%

<img src="/Users/william/Documents/PhD/Typora Assets/uniform_16Agents_run1.png" alt="uniform_16Agents_run1" style="zoom:67%;" />

Average Profit for Naive Agents: 0.007568412264657571
**Average Profit for Adaptive Agents: 0.007695557172049094**
Average Profit for Last-minute Agents: 0.007607675074861929
Average Profit for Stealth Agents: 0.0074580601009739496

<img src="/Users/william/Documents/PhD/Typora Assets/16Uniform_run1.png" alt="16Uniform_run1" style="zoom:67%;" />

**Run 2**

Naive Agents Winning: 9977 33.26%
Adaptive Agents Winning: 5514 18.38%
Last-minute Agents Winning: 7201 24.00%
Stealth Agents Winning: 7308 24.36%

<img src="/Users/william/Documents/PhD/Typora Assets/uniform_16Agents_run2.png" alt="uniform_16Agents_run2" style="zoom:67%;" />

Average Profit for Naive Agents: 0.007359017733775458
**Average Profit for Adaptive Agents: 0.008130704803763513**
Average Profit for Last-minute Agents: 0.0076445793916418234
Average Profit for Stealth Agents: 0.007484236101781262

<img src="/Users/william/Documents/PhD/Typora Assets/16Uniform_run2.png" alt="16Uniform_run2" style="zoom:67%;" />

**Run 3**

Naive Agents Winning: 10097 33.66%
Adaptive Agents Winning: 5462 18.21%
Last-minute Agents Winning: 7229 24.10%
Stealth Agents Winning: 7212 24.04%  

<img src="/Users/william/Documents/PhD/Typora Assets/uniform_16Agents_run3.png" alt="uniform_16Agents_run3" style="zoom:67%;" />

Average Profit for Naive Agents: 0.007599139188876529
Average Profit for Adaptive Agents: 0.007669837749883914
Average Profit for Last-minute Agents: 0.007506729165956853
**Average Profit for Stealth Agents: 0.00774021866375823**

<img src="/Users/william/Documents/PhD/Typora Assets/16Uniform_run3.png" alt="16Uniform_run3" style="zoom:67%;" />

#### Delta 0.0002

**Run 1**

Naive Agents Winning: 9844 32.81%
Adaptive Agents Winning: 6078 20.26%
Last-minute Agents Winning: 7073 23.58%
Stealth Agents Winning: 7005 23.35%

<img src="/Users/william/Documents/PhD/Typora Assets/delta0.0002_run1-4426162.png" alt="delta0.0002_run1" style="zoom:67%;" />

Average Profit for Naive Agents: 0.008099746046760703
Average Profit for Adaptive Agents: 0.007706846014425955
Average Profit for Last-minute Agents: 0.007535894108731579
Average Profit for Stealth Agents: 0.00761354734436324

<img src="/Users/william/Documents/PhD/Typora Assets/16Uniform(delta0.0002)_run1.png" alt="16Uniform(delta0.0002)_run1" style="zoom:67%;" />

**Run 2**

Naive Agents Winning: 9764 32.55%
Adaptive Agents Winning: 6233 20.78%
Last-minute Agents Winning: 7074 23.58%
Stealth Agents Winning: 6929 23.10%

<img src="/Users/william/Documents/PhD/Typora Assets/delta0.0002_run2-4426182.png" alt="delta0.0002_run2" style="zoom:67%;" />

Average Profit for Naive Agents: 0.007442752147916859
Average Profit for Adaptive Agents: 0.007336163896860445
**Average Profit for Last-minute Agents: 0.007806937267986007**
Average Profit for Stealth Agents: 0.007514463334015354

<img src="/Users/william/Documents/PhD/Typora Assets/16Uniform(delta0.0002)_run2.png" alt="16Uniform(delta0.0002)_run2" style="zoom:67%;" />

**Run 3**

Naive Agents Winning: 9760 32.53%
Adaptive Agents Winning: 6184 20.61%
Last-minute Agents Winning: 7070 23.57%
Stealth Agents Winning: 6986 23.29%

<img src="/Users/william/Documents/PhD/Typora Assets/delta0.0002_run3-4426192.png" alt="delta0.0002_run3" style="zoom:67%;" />

Average Profit for Naive Agents: 0.00729543131859099
**Average Profit for Adaptive Agents: 0.009147894732505947 (0.0074649664179254034 excluding the highest)**
Average Profit for Last-minute Agents: 0.007375048774896795
Average Profit for Stealth Agents: 0.00757093576524748

<img src="/Users/william/Documents/PhD/Typora Assets/16Uniform(delta0.0002)_run3.png" alt="16Uniform(delta0.0002)_run3" style="zoom:67%;" />

#### Delta 0.001 

**Run 1**

Naive Agents Winning: 9335 31.11%
Adaptive Agents Winning: 7724 25.75%
Last-minute Agents Winning: 6456 21.52%
Stealth Agents Winning: 6485 21.62%

<img src="/Users/william/Documents/PhD/Typora Assets/delta0.001_run1-4427497.png" alt="delta0.001_run1" style="zoom:67%;" />

**Run 2**

Naive Agents Winning: 9422 31.41%
Adaptive Agents Winning: 7786 25.95%
Last-minute Agents Winning: 6427 21.42%
Stealth Agents Winning: 6365 21.22%

<img src="/Users/william/Documents/PhD/Typora Assets/delta0.001_run2-4427504.png" alt="delta0.001_run2" style="zoom:67%;" />

**Run 3**

Naive Agents Winning: 9319 31.065333333333333
Adaptive Agents Winning: 7785 25.95%
Last-minute Agents Winning: 6462 21.54%
Stealth Agents Winning: 6434 21.45%

<img src="/Users/william/Documents/PhD/Typora Assets/delta0.001_run3-4427514.png" alt="delta0.001_run3" style="zoom:67%;" />

#### Analysis on this profile

1. **Last-minute and Stealth Strategy:** The win rates for these two strategies should be stable as they are not affected by the delay. They are going up a little simply because the win rate of adaptive strategy is going down.

   

2. **Naive Strategy:** Naive agents have the highest win rate, about 33% across all runs. I think naive strategy is more favorable under the current simulation design. If we exclude last-minute and stealth strategy players because they are quite special, the major competition of the highest bid during most time of the auction is between naive players and adaptive players. As the current design of the signals, the public signal and private signal are available to the players at the same time, it is easier to have the highest bid by using naive bidding. 

   

3. **Adaptive Strategy:** The win rate of adaptive player goes down as the delay goes up. Adaptive players make their bids based on the highest available bid at the current time step. With a higher delay, adaptive players will have a slower reaction, which means they will react to an older bid. The situation might already have a change during this interval before adaptive players make an reaction. And also, their reaction will also be revealed later, and until their reaction has been revealed, situation might change again.

   

   Small example to make it clear:

   Fei is a naive strategy player. Stefanos is an adaptive strategy player, add an extra 1 to the highest bid.

   At time step 0, both fei and stefanos receive a signal of 100 at the same time. Fei bids 100 because he is naive. Stefanos will not make a bid because he will make a bid based on Fei's bid.

   At time step 1, they both receive another signal of 50 at the same time. This time, fei bids 150. Stefanos knows that last time step Fei bidded 100, so Stefanos bids 101. At the end of time step 1, Fei's bid is 150, Stefanos' bid is 101.

   At time step 2, they don't receive any signal. Fei does not take any action. Stefanos knows that last time Fei bidded 150, and there's no way that he can bid 151, so Stefanos will bid 150 naively. 



Let's create a **best-case scenario** for adaptive players near the end of the auction under the cuurrent simulation design.   

We have the following assumptions:

- Total delay is 10 steps for all the players

- profit margin is not considered

- A new signal will come every 12 steps 100public signals

- The total signal value before step 1180 is X-y, and highest bid before 1180 is X-y. X-y can be a bid from a player with any strategy.

- We assume one adaptive player have access to all the signals, so he also bids X-y before 1180 (he cannot outbid X-y so he bids what he has naively)

  

  At step 1180, all players receive a new signal with a value y. y is greater than delta, so adaptive players will not be naive. 

  At step 1191, we have a new signal, this time, we assume that this signal is exclusive to the adaptive player.

|          | Before 1180 | 1180      | 1181-1188 | 1189 | 1190         | 1191-1198 | 1199                 | 1200    |
| :------- | ----------- | --------- | --------- | ---- | ------------ | --------- | -------------------- | ------- |
| Highest  |             | X         |           |      |              |           |                      |         |
| Relay    | X-y         | X-y       | X-y       | X    | X            | X         | X(also for adaptive) | X+delta |
| Adaptive | X-y         | X-y+delta |           |      | X(bid naive) | X+delta   | X+delta              |         |

We may see the problem here. Even if adaptive players can out-bid X, while others cannot, if the auction terminates during step 1191-1198, adaptive players still loses. When all the players receive a new signal, other players updates their bids directly, while adaptive players are still reacting to the highest bid (which is not going to be the highest after the delay). After the delay, adaptive players find out the highest bid is updated, and then make the reaction, but they still have to wait for the delay. During this time period, the auction may terminate, and when the delay is higher, the acution has a greater probability to terminate during this period. 

This is even the best-case scenario for adaptive players. If adaptive players have a low probability of accessing private signals, or they have a higher individual delay, the situation will be worse. If a naive player have 100% probability to access the private signals, he 100% wins the game. But if an adaptive player have 100% to access the private signals, he may still lose the game.



**Current model/simulation desgins, especially the representation of signals and the discrete time modelling with Mesa, are not very friendly for Adaptive Strategy and significantly restricted adaptive players' bidding action. Signals are available to all the players at the same time with the same value, and discrete time modelling means all the players will take their actions of bidding at the same time as well (if they are going to take actions). Therefore, there is not much room for adaptive players to operate. Also, due to the discrete time modelling, adaptive players are not able to know the situation at the current time step, and only knows the situations of previous time steps, which means that they are already reacting one step late even though there is no delay. A higher delay under this situation will be a disaster. **



### Strategy Profile 2 (17 Agents 1 Bluff)

17 Agents: 4 Naive, 4 Adaptive, 4 Last-minute, 4 Stealth, 1 Bluff

Time step length: 10ms/0.01s per step (around 1200 steps for an auction interval)

Global Delay: From 1 step (10ms) to 10 steps (100ms)

Individual delay: randomly from 3 to 5 steps (30/40/50ms)

**Last-minute, Stealth and Bluff strategy acts 8 to 10 steps plus delay before their estimation**

**Delta Value:  Adaptive Agents will place an extra value over the highest bid available**

3000 times of simulation for each delay, 30,000 times in total for one run

#### Adaptive Delta 0.0001 

**Run 1**<img src="/Users/william/Documents/PhD/Typora Assets/run1.png" alt="run1" style="zoom:67%;" />



**Run 2**<img src="/Users/william/Documents/PhD/Typora Assets/run2.png" alt="run2" style="zoom:67%;" />

**Run 3**<img src="/Users/william/Documents/PhD/Typora Assets/run3.png" alt="run3" style="zoom:67%;" />



1. Bluff Strategy have the highest winning because they may miss time to cancel the bid and win the auction with their bluff bid.
2. Adaptive strategy no longer suffer from high delay, because under the influence of bluff strategy, adaptive strategy turns to naive because they cannot out-bid the bluff bid.



Because there is a chance that bluff strategy misses the timing to reveal the true bid and win the auction with the bluff bid. We can consider it as an **invalid win**. If we do not count invalid wins, simulation results will be like:

**Run 1**

Naive Agents Winning: 6388 
Adaptive Agents Winning: 4859 
Last-minute Agents Winning: 5185 
Stealth Agents Winning: 5162 
Bluff Agents Winning: 8406 
Bluff Agents Winning with true bid value: 1571 
**Bluff Agents Winning with bluff bid value: 6835**

<img src="/Users/william/Documents/PhD/Typora Assets/run1_true.png" alt="run1_true" style="zoom:67%;" />

**Run 2**

Naive Agents Winning: 6457
Adaptive Agents Winning: 4880 
Last-minute Agents Winning: 5279 
Stealth Agents Winning: 5068 
Bluff Agents Winning: 8316 
Bluff Agents Winning with true bid value: 1636 
**Bluff Agents Winning with bluff bid value 6680**

<img src="/Users/william/Documents/PhD/Typora Assets/run2_true.png" alt="run2_true" style="zoom:67%;" />



**Run 3**

Naive Agents Winning: 6445 
Adaptive Agents Winning: 4881 
Last-minute Agents Winning: 5117 
Stealth Agents Winning: 5161 
Bluff Agents Winning: 8396 
Bluff Agents Winning with true bid value: 1608
**Bluff Agents Winning with bluff bid value 6788**

<img src="/Users/william/Documents/PhD/Typora Assets/run3_true.png" alt="run3_true" style="zoom:67%;" />



#### Adaptive Delta 0.0002

**Run 1**

Naive Agents Winning: 6387
**Adaptive Agents Winning: 4954** 
Last-minute Agents Winning: 5039 
Stealth Agents Winning: 5147
Bluff Agents Winning: 8473
Bluff Agents Winning with true bid value: 1567 
**Bluff Agents Winning with bluff bid value 6906**

<img src="/Users/william/Documents/PhD/Typora Assets/delta0.0002_run1.png" alt="delta0.0002_run1" style="zoom:67%;" />



**Run 2**

Naive Agents Winning: 6282 
**Adaptive Agents Winning: 5090** 
Last-minute Agents Winning: 5098 
Stealth Agents Winning: 5163
Bluff Agents Winning: 8367 
Bluff Agents Winning with true bid value: 1552 

**Bluff Agents Winning with bluff bid value 6812**

<img src="/Users/william/Documents/PhD/Typora Assets/delta0.0002_run2.png" alt="delta0.0002_run2" style="zoom:67%;" />



**Run 3**

Naive Agents Winning: 6420 
**Adaptive Agents Winning: 4994** 
Last-minute Agents Winning: 5040 
Stealth Agents Winning: 5129 
Bluff Agents Winning: 8417
Bluff Agents Winning with true bid value: 1591 
Bluff Agents Winning with bluff bid value 6826

<img src="/Users/william/Documents/PhD/Typora Assets/delta0.0002_run3.png" alt="delta0.0002_run3" style="zoom:67%;" />



#### Adaptive Delta 0.001

**Run 1**

Naive Agents Winning: 6228 
**Adaptive Agents Winning: 5611** 
Last-minute Agents Winning: 4828 
Stealth Agents Winning: 4960 
Bluff Agents Winning: 8373
Bluff Agents Winning with true bid value: 1551
**Bluff Agents Winning with bluff bid value 6822**

<img src="/Users/william/Documents/PhD/Typora Assets/delta0.001_run1-4197510.png" alt="delta0.001_run1" style="zoom:67%;" />

**Run 2**

Naive Agents Winning: 6283 
**Adaptive Agents Winning: 5567** 
Last-minute Agents Winning: 4860 
Stealth Agents Winning: 4894
Bluff Agents Winning: 8396 
Bluff Agents Winning with true bid value: 1543 
**Bluff Agents Winning with bluff bid value 6853**

<img src="/Users/william/Documents/PhD/Typora Assets/delta0.001_run2.png" alt="delta0.001_run2" style="zoom:67%;" />

**Run 3**

Naive Agents Winning: 6307
Adaptive Agents Winning: 5486 
Last-minute Agents Winning: 4897 
Stealth Agents Winning: 4916 
Bluff Agents Winning: 8394 
Bluff Agents Winning with true bid value: 1520 
**Bluff Agents Winning with bluff bid value 6874**

<img src="/Users/william/Documents/PhD/Typora Assets/delta0.001_run3.png" alt="delta0.001_run3" style="zoom:67%;" />

### **Strategy Profile 3 (20 Agents Uniform)**

20 Agents: 4 for each strategy

Time step length: 10ms/0.01s per step (around 1200 steps for an auction interval)

Global Delay: From 1 step (10ms) to 10 steps (100ms)

**Last-minute, Stealth and Bluff strategy acts 8 to 10 steps plus delay before their estimation**

3000 times of simulation for each delay, 30,000 times in total for one run



**Run 1**

Naive Agents Winning: 3528
Adaptive Agents Winning: 2755 
Last-minute Agents Winning: 3018 
Stealth Agents Winning: 3060 
Bluff Agents Winning: 17639 
Bluff Agents Winning with true bid value: 3293
**Bluff Agents Winning with bluff bid value 14346**

<img src="/Users/william/Documents/PhD/Typora Assets/run1-4019848.png" alt="run1" style="zoom:67%;" />



**Run 2**

Naive Agents Winning: 3540 
Adaptive Agents Winning: 2838 
Last-minute Agents Winning: 2984 
Stealth Agents Winning: 2973 
Bluff Agents Winning: 17665 
Bluff Agents Winning with true bid value: 3390
**Bluff Agents Winning with bluff bid value 14275**

<img src="/Users/william/Documents/PhD/Typora Assets/run2-4019855.png" alt="run2" style="zoom:67%;" />

**Run 3**

Naive Agents Winning: 3636 
Adaptive Agents Winning: 2769 
Last-minute Agents Winning: 3107
Stealth Agents Winning: 3094
Bluff Agents Winning: 17394 
Bluff Agents Winning with true bid value: 3343 
**Bluff Agents Winning with bluff bid value 14051**

<img src="/Users/william/Documents/PhD/Typora Assets/run3-4019865.png" alt="run3" style="zoom:67%;" />

## Naive Strategy Pairwise Simulation

16 Agents: 8 Naive + 8 Other

Time step length: 10ms/0.01s per step (around 1200 steps for an auction interval)

Global Delay: From 1 step (10ms) to 10 steps (100ms)

**Last-minute, Stealth strategy acts 8 to 10 steps plus delay before their estimation**

3000 times of simulation for each delay, 30,000 times in total for one run



### Naive VS Adaptive

#### Delta 0.0001

**Run 1**

Naive Agents Winning: 19164
Adaptive Agents Winning: 10836

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8adapt_run1.png" alt="8naive8adapt_run1" style="zoom:67%;" />

Average Profit for Naive Agents: 0.007425164745618952
**Average Profit for Adaptive Agents: 0.0075288137343545135**

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8adapt(delta0.0001)_run1.png" alt="8naive8adapt(delta0.0001)_run1" style="zoom:67%;" />



**Run 2**

Naive Agents Winning: 19203
Adaptive Agents Winning: 10797

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8adapt_run2.png" alt="8naive8adapt_run2" style="zoom:67%;" />

Average Profit for Naive Agents: 0.007492457869752563
**Average Profit for Adaptive Agents: 0.008179098680399316**

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8adapt(delta0.0001)_run2.png" alt="8naive8adapt(delta0.0001)_run2" style="zoom:67%;" />



**Run 3**

Naive Agents Winning: 19343
Adaptive Agents Winning: 10657

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8adapt_run3.png" alt="8naive8adapt_run3" style="zoom:67%;" />

Average Profit for First Agents: 0.0078088961291265715
Average Profit for Second Agents: 0.007581832464442376

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8adapt(delta0.0001)_run3.png" alt="8naive8adapt(delta0.0001)_run3" style="zoom:67%;" />

#### Delta 0.0002

**Run 1**

Naive Agents Winning: 18369
Adaptive Agents Winning: 11631

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8adapt_run2-5719470.png" alt="8naive8adapt_run2" style="zoom:67%;" />

**Run 2**

Naive Agents Winning: 18331
Adaptive Agents Winning: 11669

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8adapt(delta0.0002)_run2.png" alt="8naive8adapt(delta0.0002)_run2" style="zoom:67%;" />

**Run 3**

Naive Agents Winning: 18382
Adaptive Agents Winning: 11618

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8adapt(delta0.0002)_run3.png" alt="8naive8adapt(delta0.0002)_run3" style="zoom:67%;" />

#### Delta 0.001

**Run 1**

First Agents Winning: 16692
Second Agents Winning: 13308

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8adapt(delta0.001)_run1.png" alt="8naive8adapt(delta0.001)_run1" style="zoom:67%;" />

**Run 2**

First Agents Winning: 16643
Second Agents Winning: 13357

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8adapt(delta0.001)_run2.png" alt="8naive8adapt(delta0.001)_run2" style="zoom:67%;" />

**Run 3**

First Agents Winning: 16718
Second Agents Winning: 13282

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8adapt(delta0.001)_run3.png" alt="8naive8adapt(delta0.001)_run3" style="zoom:67%;" />

### Naive VS Last-minute

**Run 1**

Naive Agents Winning: 17704
Last-minute Agents Winning: 12296

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8lastminute_run1.png" alt="8naive8lastminute_run1" style="zoom:67%;" />

**Run 2**

Naive Agents Winning: 17530
Last-minute Agents Winning: 12470

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8lastminute_run2.png" alt="8naive8lastminute_run2" style="zoom:67%;" />

**Run 3**

Naive Agents Winning: 17734
Last-minute Agents Winning: 12266

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8lastminute_run3.png" alt="8naive8lastminute_run3" style="zoom:67%;" />

### Naive VS Stealth

**Run 1**

Naive Agents Winning: 17735
Stealth Agents Winning: 12265

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8stealth_run1.png" alt="8naive8stealth_run1" style="zoom:67%;" />

**Run 2**

Naive Agents Winning: 17594
Other Agents Winning: 12406

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8stealth_run2.png" alt="8naive8stealth_run2" style="zoom:67%;" />

**Run 3**

Naive Agents Winning: 17626
Other Agents Winning: 12374

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8stealth_run3.png" alt="8naive8stealth_run3" style="zoom:67%;" />

## Adaptive Strategy Pairwise Simulation

### Adaptive VS Last-minute

#### Delta 0.0001

**Run 1**

Adaptive Agents Winning: 8134
Last-minute Agents Winning: 21866

<img src="/Users/william/Documents/PhD/Typora Assets/8adaptive8lastminute(delta0.0001)_run1.png" alt="8adaptive8lastminute(delta0.0001)_run1" style="zoom:67%;" />

**Run 2**

Adaptive Agents Winning: 8161
Last-minute Agents Winning: 21839

<img src="/Users/william/Documents/PhD/Typora Assets/8adaptive8lastminute(delta0.0001)_run2.png" alt="8adaptive8lastminute(delta0.0001)_run2" style="zoom:67%;" />

**Run 3**

Adaptive Agents Winning: 8112
Last-minute Agents Winning: 21888

<img src="/Users/william/Documents/PhD/Typora Assets/8adaptive8lastminute(delta0.0001)_run3.png" alt="8adaptive8lastminute(delta0.0001)_run3" style="zoom:67%;" />

#### Delta 0.0002

**Run 1**

Adaptive Agents Winning: 9044
Last-minute Agents Winning: 20956

<img src="/Users/william/Documents/PhD/Typora Assets/8adaptive8lastminute(delta0.0002)_run1.png" alt="8adaptive8lastminute(delta0.0002)_run1" style="zoom:67%;" />

**Run 2**

Adaptive Agents Winning: 9193
Last-minute Agents Winning: 20807

<img src="/Users/william/Documents/PhD/Typora Assets/8adaptive8lastminute(delta0.0002)_run2.png" alt="8adaptive8lastminute(delta0.0002)_run2" style="zoom:67%;" />

**Run 3**

Adaptive Agents Winning: 8993
Last-minute Agents Winning: 21007

<img src="/Users/william/Documents/PhD/Typora Assets/8adaptive8lastminute(delta0.0002)_run3.png" alt="8adaptive8lastminute(delta0.0002)_run3" style="zoom:67%;" />

#### Delta 0.001

**Run 1**

Adaptive Agents Winning: 13177
Last-minute Agents Winning: 16823

<img src="/Users/william/Documents/PhD/Typora Assets/8adaptive8lastminute(delta0.001)_run1.png" alt="8adaptive8lastminute(delta0.001)_run1" style="zoom:67%;" />

**Run 2**
Adaptive Agents Winning: 13118
Last-minute Agents Winning: 16882

<img src="/Users/william/Documents/PhD/Typora Assets/8adaptive8lastminute(delta0.001)_run2.png" alt="8adaptive8lastminute(delta0.001)_run2" style="zoom:67%;" />

**Run 3**

Adaptive Agents Winning: 13079

Last-minute Agents Winning: 16921

<img src="/Users/william/Documents/PhD/Typora Assets/8adaptive8lastminute(delta0.001)_run1-4520472.png" alt="8adaptive8lastminute(delta0.001)_run1" style="zoom:67%;" />

### Adaptive VS Stealth

#### Delta 0.0001

**Run 1**

Adaptive Agents Winning: 7995
Stealth Agents Winning: 22005

<img src="/Users/william/Documents/PhD/Typora Assets/8adapt8stealth(delta0.0001)_run1-4450914.png" alt="8adapt8stealth(delta0.0001)_run1" style="zoom:67%;" />

**Run 2**

Adaptive Agents Winning: 8202
Stealth Agents Winning: 21798

<img src="/Users/william/Documents/PhD/Typora Assets/8adapt8stealth(delta0.0001)_run2.png" alt="8adapt8stealth(delta0.0001)_run2" style="zoom:67%;" />

**Run 3**

Adaptive Agents Winning: 8081
Stealth Agents Winning: 21919

<img src="/Users/william/Documents/PhD/Typora Assets/8adapt8stealth(delta0.0001)_run3.png" alt="8adapt8stealth(delta0.0001)_run3" style="zoom:67%;" />

#### Delta 0.0002

**Run 1**

Adaptive Agents Winning: 9047
Stealth Agents Winning: 20953

<img src="/Users/william/Documents/PhD/Typora Assets/8adapt8stealth(delta0.0002)_run1-4529784.png" alt="8adapt8stealth(delta0.0002)_run1" style="zoom:67%;" />

**Run 2**

Adaptive Agents Winning: 9185
Stealth Agents Winning: 20815

<img src="/Users/william/Documents/PhD/Typora Assets/8adapt8stealth(delta0.0002)_run2-4529792.png" alt="8adapt8stealth(delta0.0002)_run2" style="zoom:67%;" />

**Run 3**

Adaptive Agents Winning: 9141
Stealth Agents Winning: 20859

<img src="/Users/william/Documents/PhD/Typora Assets/8adapt8stealth(delta0.0002)_run3.png" alt="8adapt8stealth(delta0.0002)_run3" style="zoom:67%;" />



#### Delta 0.001

**Run 1**

Adaptive Agents Winning: 13459
Stealth Agents Winning: 16541

<img src="/Users/william/Documents/PhD/Typora Assets/8adapt8stealth(delta0.001)_run1.png" alt="8adapt8stealth(delta0.001)_run1" style="zoom:67%;" />

**Run 2**

First Agents Winning: 13379
Second Agents Winning: 16621

<img src="/Users/william/Documents/PhD/Typora Assets/8adapt8stealth(delta0.001)_run2.png" alt="8adapt8stealth(delta0.001)_run2" style="zoom:67%;" />

**Run 3**

First Agents Winning: 13570
Second Agents Winning: 16430

<img src="/Users/william/Documents/PhD/Typora Assets/8adapt8stealth(delta0.001)_run3.png" alt="8adapt8stealth(delta0.001)_run3" style="zoom:67%;" />

***

## Bluff Strategy Pairwise Simulation

### Naive VS Bluff

8 Naive Agents and 8 Bluff Agents

Time step length: 10ms/0.01s per step (around 1200 steps for an auction interval)

Global Delay: From 1 step (10ms) to 10 steps (100ms)

3000 times of simulation for each delay, 30,000 times in total for one run

**Run 1**

Bluff Agents Winning: 23659
Bluff Agents Winning with true bid value: 5863 
Bluff Agents Winning with bluff bid value 17796
Naive Agents Winning: 6341 

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8bluff_run1.png" alt="8naive8bluff_run1" style="zoom:67%;" />

**Run 2**

Bluff Agents Winning: 23756
Bluff Agents Winning with true bid value: 6045 
Bluff Agents Winning with bluff bid value 17711
Naive Agents Winning: 6244 

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8bluff_run2.png" alt="8naive8bluff_run2" style="zoom:67%;" />

**Run 3**

Bluff Agents Winning: 23768 
Bluff Agents Winning with true bid value: 5923 
Bluff Agents Winning with bluff bid value 17845
Naive Agents Winning: 6232 

<img src="/Users/william/Documents/PhD/Typora Assets/8naive8bluff_run3.png" alt="8naive8bluff_run3" style="zoom:67%;" />

**Across 3 runs:**

Naive winning: 18817

Bluff winning with true bids: 17831 

Bluff winning with bluff bids (invalid win): 53352



### Adaptive VS Bluff

8 Adaptive Agents and 8 Bluff Agents

Time step length: 10ms/0.01s per step (around 1200 steps for an auction interval)

Global Delay: From 1 step (10ms) to 10 steps (100ms)

3000 times of simulation for each delay, 30,000 times in total for one run

**Run 1**

Bluff Agents Winning: 24811
Bluff Agents Winning with true bid value: 6910
Bluff Agents Winning with bluff bid value: 17901
Adaptive Agents Winning: 5189 

<img src="/Users/william/Documents/PhD/Typora Assets/8adapt8bluff_run1-3994551.png" alt="8adapt8bluff_run1" style="zoom:67%;" />

**Run 2**

Bluff Agents Winning: 24791
Bluff Agents Winning with true bid value: 7106
Bluff Agents Winning with bluff bid value: 17865
Naive Agents Winning: 5029 

<img src="/Users/william/Documents/PhD/Typora Assets/8adapt8bluff_run2-3994559.png" alt="8adapt8bluff_run2" style="zoom:67%;" />

**Run 3**

Bluff Agents Winning: 24929
Bluff Agents Winning with true bid value: 7085
Bluff Agents Winning with bluff bid value: 17844
Naive Agents Winning: 5071

<img src="/Users/william/Documents/PhD/Typora Assets/8adapt8bluff_run3-3994572.png" alt="8adapt8bluff_run3" style="zoom:67%;" />

**Across 3 runs:**

Adaptive winning: 15289

Bluff winning with true bids: 19266

Bluf winning with bluff bids: 53584

### Stealth VS Bluff

**Run 1**

Bluff Agents Winning: 24025 1.9768781370854933
Bluff Agents Winning with true bid value: 6178 
Bluff Agents Winning with bluff bid value 17847
Stealth Agents Winning: 5975 

<img src="/Users/william/Documents/PhD/Typora Assets/8stealth8bluff_run1.png" alt="8stealth8bluff_run1" style="zoom:67%;" />

**Run 2**

Bluff Agents Winning: 24125 
Bluff Agents Winning with true bid value: 6234
Bluff Agents Winning with bluff bid value 17891
Stealth Agents Winning: 5875 

<img src="/Users/william/Documents/PhD/Typora Assets/8stealth8bluff_run2.png" alt="8stealth8bluff_run2" style="zoom:67%;" />

**Run 3**

Bluff Agents Winning: 24249 
Bluff Agents Winning with true bid value: 6364
Bluff Agents Winning with bluff bid value 17885
Stealth Agents Winning: 5751

<img src="/Users/william/Documents/PhD/Typora Assets/8stealth8bluff_run3.png" alt="8stealth8bluff_run3" style="zoom:67%;" />

**Across 3 runs:**

Stealth winning: 17601

Bluff winning with true bids: 18776

Bluff winning with bluff bids: 53623

### Last-minute VS Bluff

**Run 1**

Bluff Agents Winning: 24241
Bluff Agents Winning with true bid value: 6225
Bluff Agents Winning with bluff bid value 18016
Last-minute Agents Winning: 5759

<img src="/Users/william/Documents/PhD/Typora Assets/8lastminute8bluff_run1.png" alt="8lastminute8bluff_run1" style="zoom:67%;" />

**Run 2**

Bluff Agents Winning: 23972
Bluff Agents Winning with true bid value: 6227
Bluff Agents Winning with bluff bid value 17745
Last-minute Agents Winning: 6028

<img src="/Users/william/Documents/PhD/Typora Assets/8lastminute8bluff_run2.png" alt="8lastminute8bluff_run2" style="zoom:67%;" />

**Run 3**

Bluff Agents Winning: 24046 
Bluff Agents Winning with true bid value: 6242
Bluff Agents Winning with bluff bid value 17804
Last-minute Agents Winning: 5954

**Across 3 runs**

Last-minute winning: 17741

Bluff winning with true bids: 18694

Bluff winning with bluff bids: 53565



## Things to be finalised on simulation setups (After Sep 13th Meeting)

1. **Estimation mechanism** for last-minute, stealth, and bluff strategy.

   Current mechanism: The time interval of the auction is a random variable drawn from a normal distribution (mean=12, sd=0.1). The estimation made by players with these strategies is also a random variable drawn from the same distribution. Then the reveal times of their true bids are **(estimation time - delta - individual_delay - global_delay).** So if the estimation is bad, which means the value of (estimation - delta) is superior to the auction time, the player will miss the reveal time of his true bid. 

   This setting causes a situation, where there is bluff strategy player in the game, quite a number of games will be won by bluff bids. In strategy profile 2 (17 Agents with 1 bluff), about 23% is won by bluff bid. In strategy profile 3 (20 Agents uniform 4 bluff), nearly 50% is won by bluff bid.

   **What if we assume all the players expect the auction to terminate at 12? We can set 12 as the estimation for all the players. Mathematically, the probability of missing the reveal time will be reduced.  Or, we can make them act a bit earlier before their estimation. **

   

2. **Signal Representation and Discrete Time Modelling.** These two are the main reasons why adaptive players suffer, or cannot outperform naive players. As both public and private signals are available to all the players at the same time and with the same value, and also with the restriction of discrete time modeling, naive players can quickly update their bids at the current time step once they receive a new signal, adaptive players have to wait for the next step to react, because they are not able to know what is happening at the current time step, thus cannot make a reaction immediately. The situation is worse when the delay is higher, and even worse when adaptive players have a higher individual delay, because it means the reaction slower.

   

3. Adaptive players' **delta values** significantly affect their behaviors.

   Do we keep the value consistent between all adaptive players, or do we set it differently for different players?

   

4. **Tiebreaker mechanism.** What if two or even more players end up winning the auction with the same bid? 

   It is a very very rare situation in the current simulation setup, because we have indeed a lot of randomness in our simulation. The profit margin, the probability, and the individual delay for each player are random. 

   



**What if we adjust the quantity of time steps of the game, the time length of one time step?** 

Currently we are having about 1200 time steps in total, 1 step represents 0.01 seconds in real-world. The poisson rates of public signal and private signal are about 1 public signal per 12 steps (about 100 public signals in total) and 1 private signal per 27 steps (about 45 private signals in total). The lag update in our simulation is basically determined by the poisson rate of the signal, so about 120ms average for naive/adaptive players.

The bid count of naive and adaptive strategy players of an auction is about 100 (also depend on probability). However, in real-world situations, according to [Empirical analysis of Builders’ Behavioral Profiles (BBPs)](https://ethresear.ch/t/empirical-analysis-of-builders-behavioral-profiles-bbps/16327), the bid count per slot is about 20 at most. We can modify the length of one time step to achieve a similar situation.



![img](/Users/william/Documents/PhD/Typora Assets/b781e206004c2c2ef82be78830f88d5e7b3560b2.png)



# Sep 26th Meeting with Thomas

1. **Estimation mechanism and Auction time:**

   - We can assume the players expect the auction to be terminated at the time point of 12.00 seconds. We set the **expectation of auction termination time fixed as 12 seconds** (1200 time steps) for last-minute, stealth, and bluff strategy players. So players will act at:

     
     $$
     12 - ε - global.delay - individual.delay
     $$
     where epsilon is the player's estimation of when to bid to account for the uncertainty. Right now we've set the epsilon to be between 0.4 and 0.5.

     

   - **For bluff strategy:** If I am a bluff strategy player, I will try to make sure that I always cancel the bid before the termination, never win with my bluff bid. So we can make bluff strategy bluff for the first 10 to 11 seconds, and then reveal their true values. In this way, by bluffing for the first 11 seconds, bluff strategy has achieved its purpose to some extent, which is to force the adaptive players to bid naive for 11 seconds, adaptive players have to reveal their true bids.

     

   - **For last-minute and stealth strategy:** We need to decide the reveal time of these two strategies somehow. 

     By referring to the real-world situation and empirical data, some builders only place 5 to 6 bids per slot on average, we can identify them as last-minute strategy players. To allow them to place 5 or 6 bids before the auction termination in our simulation design, we need to make them act 40-50 time steps (0.4-0.5 seconds) in advance before their estimation. During this 40-50 time steps, we probably will have 3-4 public signals and 1-2 private signals, allowing 5-6 bid updates.  

     ![newplot](/Users/william/Documents/PhD/Typora Assets/newplot.png)
     
   - Auction time: We can adjust the auction time interval by adjusting the value of std of the normal distribution to see the performance difference.

     

2. **Discrete Time Modelling and Signal Representation:**

   We can still keep this design. Instead, we can adjust the value of the delay (both individual and global) and let the delay be small enough so that adaptive players can have a faster reaction. Then we see how adaptive players perform under a low-delay environment. Knowing the strategy suffers under high delays, when the network traffic is serious, or when some builders have high individual delays, they should avoid using adaptive.

   

3. **Profit: ** Plotting average profit for different strategies across different delays

   

4. Try to think about the metrics to measure the fairness of the game

order fairness

the coming sequence of public and private signals





# Simulation Round 2

## 16 Uniform Agents

### 1 Individual delay for all

16 Uniform Agents: 4 Naive, 4 Adaptive, 4 Last-minute, 4 Stealth, 0 Bluff

Time step length: 0.01s per step

Global Delay: From 1 step (10ms) to 4 steps (40ms)

Individual delay: 1 step for all players

5,000 times of simulation for each delay, 20,000 times in total for one run

**Last-minute and Stealth strategy acts 40 to 50 steps (0.4-0.5s) plus delay before their estimation, estimation set to 12**

**Delta Value: 0.0001 Adaptive Agents will place an extra value over the highest bid available**



**Run 1**

Naive Agents Winning: 5164 
Adaptive Agents Winning: 4492 
Last-minute Agents Winning: 5197
Stealth Agents Winning: 5147 

<img src="/Users/william/Documents/PhD/Typora Assets/16Uniform_all1delay_run1-5980270.png" alt="16Uniform_all1delay_run1" style="zoom:67%;" />

**Run 2**

Naive Agents Winning: 5127 
Adaptive Agents Winning: 4338 
Last-minute Agents Winning: 5289 
Stealth Agents Winning: 5246 

<img src="/Users/william/Documents/PhD/Typora Assets/16Uniform_all1delay_run2.png" alt="16Uniform_all1delay_run2" style="zoom:67%;" />

**Run 3 **

Naive Agents Winning: 5211 
Adaptive Agents Winning: 4377
Last-minute Agents Winning: 5151
Stealth Agents Winning: 5261 

<img src="/Users/william/Documents/PhD/Typora Assets/16Uniform_all1delay_run3.png" alt="16Uniform_all1delay_run3" style="zoom:67%;" />

### 1 Individual delay for adaptive, 2 for others

**Run 1**

Naive Agents Winning: 5074
Adaptive Agents Winning: 4869 
Last-minute Agents Winning: 5102 
Stealth Agents Winning: 4955 

<img src="/Users/william/Documents/PhD/Typora Assets/16Uniform_2delayother_run1.png" alt="16Uniform_2delayother_run1" style="zoom:67%;" />

**Run 2**

Naive Agents Winning: 5087
Adaptive Agents Winning: 4824 
Last-minute Agents Winning: 5085 
Stealth Agents Winning: 5004 

<img src="/Users/william/Documents/PhD/Typora Assets/16Uniform_2delayother_run2.png" alt="16Uniform_2delayother_run2" style="zoom:67%;" />

**Run 3**

Naive Agents Winning: 5096 
Adaptive Agents Winning: 4801 
Last-minute Agents Winning: 5048 
Stealth Agents Winning: 5055

<img src="/Users/william/Documents/PhD/Typora Assets/16Uniform_2delayother_run3.png" alt="16Uniform_2delayother_run3" style="zoom:67%;" />

### 1 Individual delay for adaptive, 3 for others

**Run 1**

Naive Agents Winning: 4801 
Adaptive Agents Winning: 5246 
Last-minute Agents Winning: 4871 
Stealth Agents Winning: 5082 

<img src="/Users/william/Documents/PhD/Typora Assets/16Uniform_3delayother_run1.png" alt="16Uniform_3delayother_run1" style="zoom:67%;" />

**Run 2**

Naive Agents Winning: 5039 
Adaptive Agents Winning: 5144 
Last-minute Agents Winning: 4851 
Stealth Agents Winning: 4966 

<img src="/Users/william/Documents/PhD/Typora Assets/16Uniform_3delayother_run2.png" alt="16Uniform_3delayother_run2" style="zoom:67%;" />

**Run 3**

Naive Agents Winning: 4991 
Adaptive Agents Winning: 5177 
Last-minute Agents Winning: 4931 
Stealth Agents Winning: 4901

<img src="/Users/william/Documents/PhD/Typora Assets/16Uniform_3delayother_run3.png" alt="16Uniform_3delayother_run3" style="zoom:67%;" />



### Analysis (Things to be discussed on SEP 29th)

**Last-minute and Stealth Strategy:** We adjust their estimation mechanism. With current parameter values, they will reveal their true bids between 11.5-11.6 seconds. The auction is not very likely to end before this period (0.0000287%). So they are going to bid naive near the end of the auction, and have a close win rate to naive players.

**Naive:** Nothing to say about it. Competitive and consistent. 

**Adaptive:** We finally see some advantages of adaptive strategies. When the global delay is low, and adaptive players have a lower individual delay than other players, adaptive strategy has a not small advantage. In real-world situations, it means that, if the network/relay is responding quickly to bids submitted from builders, and some builders have a better connection to the network/relay, they can use adaptive strategy to achieve a higher win rate.



Modified Potential Research Directions:

- **Delay related**, mainly about adaptive strategy
- **Probability related:** Knowing the probability of accessing private signals, and also other conditions like global delay, what other players' strategies are, what could be the best strategy to have the best win rate or profit?

- **Reveal time** of the true value for last-minute and stealth strategy, also bluff strategy potentially. What will be their best reveal time under different situations?

- EGTA: 

1. We have a fixed number of players in the game. Knowing what strategies are used by other players, what strategy should be used as a best response, to have the best win rate or maybe the highest profit. We might identify some kind of "dilemma", where one strategy have high win rate low profit and the other strategy have low win rate high profit.
2. We have a fixed number of players in the game. We run simulations for different strategy profiles, to see the win rates or profits (payoff matrix), and see if we can identify an equilibrium, where in one or some strategy profiles, no player wants to change their strategy. 



### 8 Adaptive (1 delay) VS 8 Naive (3 Delay)

Global Delay = 1, Adaptive Delta = 0.0001

**Run 1**

<img src="/Users/william/Documents/PhD/Typora Assets/8naive(3delay)vs8adapt(1delay)_run1.png" alt="8naive(3delay)vs8adapt(1delay)_run1" style="zoom:67%;" />

**Run 2**

<img src="/Users/william/Documents/PhD/Typora Assets/8naive(3delay)vs8adapt(1delay)_run2.png" alt="8naive(3delay)vs8adapt(1delay)_run2" style="zoom:67%;" />

**Run 3**

<img src="/Users/william/Documents/PhD/Typora Assets/8naive(3delay)vs8adapt(1delay)_run3.png" alt="8naive(3delay)vs8adapt(1delay)_run3" style="zoom:67%;" />

### 8 Naive (1 delay) VS 8 Naive (3 delay)

**Run 1**

<img src="/Users/william/Documents/PhD/Typora Assets/8naive(3delay)vs8naive(1delay)_run1.png" alt="8naive(3delay)vs8naive(1delay)_run1" style="zoom:67%;" />

**Run 2**

<img src="/Users/william/Documents/PhD/Typora Assets/8naive(3delay)vs8naive(1delay)_run2.png" alt="8naive(3delay)vs8naive(1delay)_run2" style="zoom:67%;" />

**Run 3**

<img src="/Users/william/Documents/PhD/Typora Assets/8naive(3delay)vs8naive(1delay)_run3.png" alt="8naive(3delay)vs8naive(1delay)_run3" style="zoom:67%;" />



**With a lower individual delay, Naive strategy is a much better choice than Adaptive Strategy. ** 



### Adaptive VS Last-minute NEW RUN

Epsilon Value: 40-50 time steps

**Run 1**

Adaptive Agents Winning: 10715
Last-minute Agents Winning: 19285

<img src="/Users/william/Documents/PhD/Typora Assets/8adapt8last(0.4-0.5)_run1.png" alt="8adapt8last(0.4-0.5)_run1" style="zoom:67%;" />

**Average Profit for Adaptive Agents: 0.007896800037513974**
Average Profit for Last-minute Agents: 0.007748972534413282

<img src="/Users/william/Documents/PhD/Typora Assets/8adapt8last_newrun1.png" alt="8adapt8last_newrun1" style="zoom:67%;" />

**Run 2**

Adaptive Agents Winning: 10678
Last-minute Agents Winning: 19322

<img src="/Users/william/Documents/PhD/Typora Assets/8adapt8last(0.4-0.5)_run2.png" alt="8adapt8last(0.4-0.5)_run2" style="zoom:67%;" />

**Average Profit for Adaptive Agents: 0.007736160115429317**
Average Profit for Last-minute Agents: 0.007404635998012862

<img src="/Users/william/Documents/PhD/Typora Assets/8adapt8last_newrun2.png" alt="8adapt8last_newrun2" style="zoom:67%;" />

**Run 3**

Adaptive Agents Winning: 10703
Last-minute Agents Winning: 19297

<img src="/Users/william/Documents/PhD/Typora Assets/8adapt8last(0.4-0.5)_run3.png" alt="8adapt8last(0.4-0.5)_run3" style="zoom:67%;" />

**Average Profit for Adaptive Agents: 0.007582198767073773**
Average Profit for Last-minute Agents: 0.007545144336884135





## Simulation Round 3

16 Uniform Agents: 4 Naive, 4 Adaptive, 4 Last-minute, 4 Stealth, 0 Bluff

Time step length: 0.01s per step

Global Delay: From 1 step (10ms) to 10 steps (40ms)

Individual delay: 1 step for naive agent 1, 2 steps for naive agent 2, 3 steps for naive agent 3, 4 steps for naive agent 4, and the same for other strategies

10,000 times of simulation for each delay, 100,000 times in total for one run

**Last-minute and Stealth strategy acts 40 to 50 steps (0.4-0.5s) plus delay before their estimation, estimation set to 12**

**Delta Value: 0.0001 Adaptive Agents will place an extra value over the highest bid available**



### Winning Counts (Win Rate) by Strategy

**Run 1**

Naive Agents Winning: 27095
Adaptive Agents Winning: 18683
Last-minute Agents Winning: 27207
Stealth Agents Winning: 27015

<img src="/Users/william/Documents/PhD/Typora Assets/winrate_run1.png" alt="winrate_run1" style="zoom:67%;" />

**Run 2**

Naive Agents Winning: 27138
Adaptive Agents Winning: 18804
Last-minute Agents Winning: 26892
Stealth Agents Winning: 27166

<img src="/Users/william/Documents/PhD/Typora Assets/winrate_run2.png" alt="winrate_run2" style="zoom:67%;" />

**Run 3**

Naive Agents Winning: 27138
Adaptive Agents Winning: 18804
Last-minute Agents Winning: 26892
Stealth Agents Winning: 27166

<img src="/Users/william/Documents/PhD/Typora Assets/winrate_run3.png" alt="winrate_run3" style="zoom:67%;" />

### Average Profit by Strategy

**Run 1**

Average Profit for Naive Agents: 0.00752439988853462
Average Profit for Adaptive Agents: 0.007252505086437007
Average Profit for Last-minute Agents: 0.007281880800242043
Average Profit for Stealth Agents: 0.007194334032581384

<img src="/Users/william/Documents/PhD/Typora Assets/profit_run1.png" alt="profit_run1" style="zoom:67%;" />

**Run 2**

Average Profit for Naive Agents: 0.007190261772306844
Average Profit for Adaptive Agents: 0.007207633931120342
Average Profit for Last-minute Agents: 0.007467555755131635
Average Profit for Stealth Agents: 0.007253353595320308

<img src="/Users/william/Documents/PhD/Typora Assets/profit_run2.png" alt="profit_run2" style="zoom:67%;" />

**Run 3**

Average Profit for Naive Agents: 0.007212034354458995
Average Profit for Adaptive Agents: 0.007235176475067134
Average Profit for Last-minute Agents: 0.007256638320742016
Average Profit for Stealth Agents: 0.007293457316739021

<img src="/Users/william/Documents/PhD/Typora Assets/profit_run3.png" alt="profit_run3" style="zoom:67%;" />

### Profit Distribution by Strategy

**Run 1**

Naive
Q1: 0.00649569
Median: 0.00657105
Q3: 0.00668818

Adaptive
Q1: 0.00650696
Median: 0.00659353
Q3: 0.00679980

Last-minute
Q1: 0.00649647
Median: 0.00656919
Q3: 0.00668255

Stealth
Q1: 0.00649686
Median: 0.00657185
Q3: 0.00668590

<img src="/Users/william/Documents/PhD/Typora Assets/profitdistribution_strategy_run1.png" alt="profitdistribution_strategy_run1" style="zoom:67%;" />

**Run 2**

Naive
Q1: 0.00649555
Median: 0.00657117
Q3: 0.00668683


Adaptive
Q1: 0.00650924
Median: 0.00659493
Q3: 0.00679313


Last-minute
Q1: 0.00649565
Median: 0.00657001
Q3: 0.00668578

Stealth
Q1: 0.00649347
Median: 0.00656892
Q3: 0.00668372

<img src="/Users/william/Documents/PhD/Typora Assets/profitdistribution_strategy_run2.png" alt="profitdistribution_strategy_run2" style="zoom:67%;" />

### 

**Run 3**

Naive
Q1: 0.00649499
Median: 0.00657056
Q3: 0.00668383

Adaptive
Q1: 0.00650843
Median: 0.00659270
Q3: 0.00679436


Last-minute
Q1: 0.00649630
Median: 0.00657078
Q3: 0.00668783


Stealth
Q1: 0.00649319
Median: 0.00656850
Q3: 0.00668242

<img src="/Users/william/Documents/PhD/Typora Assets/profitdistribution_strategy_run3.png" alt="profitdistribution_strategy_run3" style="zoom:67%;" />



### Profit Distribution by Player

**Run 1**

<img src="/Users/william/Documents/PhD/Typora Assets/profitdistribution_player_run1.png" alt="profitdistribution_player_run1" style="zoom:67%;" />

**Run 2**

<img src="/Users/william/Documents/PhD/Typora Assets/profitdistribution_player_run2.png" alt="profitdistribution_player_run2" style="zoom:67%;" />

**Run 3**

<img src="/Users/william/Documents/PhD/Typora Assets/profitdistribution_player_run3.png" alt="profitdistribution_player_run3" style="zoom:67%;" />



**Win rate related:**

1. There is not too much difference between naive strategy, last-minute, and stealth strategy, if we set the reveal time to be between 11.5-11.6 seconds for last-minute and stealth strategy, as they will always turn naive in the end.
2. In terms of win rate, adaptive players suffer. When the delays (both global and individual) becomes higher, they suffer more.

**Profit average plot related:**

1. Players will have more profits when the delay becomes higher, because they may receive new signals near the end of the auction, but don't get the chance to update their bids.

**Profit distribution plot related:**

1. Adaptive players have a higher profit. This is because they are winning by adding only a little bit to the highest bid, instead of bidding everything aggressively. 
2. In terms of individual delay, it seems that players with a higher individual delay tend to win with more profits. This could be explained the same as the effect of global delay 



If we use aggregated profit as the payoff metric, it is possible that, out of 10,000 simulations, in one game, one player gets an exclusive private signal with super high value, and wins the game with very high profit. So this profit will boost the aggregated profit of the strategy. What do we do with this super high value profit?

It actually happened one time in previous simulation. One player wins the game with a profit of about 10 ETH, while the median is about 0.0066 ETH. 



EGTA

quiesce

heuristic payoff table  number of players using the strategy

Open-spiel  alpha-rank

