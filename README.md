# Bayesian Skill Rating Model

This project implements a Bayesian skill rating model designed to estimate the abilities of players or teams based on match outcomes. The approach uses **Gibbs Sampling** and **Assumed Density Filtering (ADF)**, with a key feature being its basis in the **TrueSkill framework**, famously used on **Xbox Live** to rank gamers.

---

## 1. How Skill is Estimated with Gibbs Sampling

The primary method for skill estimation in this project is **Gibbs Sampling**. Think of it like this: for every match, the model attempts to determine the true skills of the participants ($s_1$ and $s_2$). Since the outcome of a match ($y$) depends on the difference in skills ($t = s_1 - s_2$), the model iteratively refines its guesses for $s_1$, $s_2$, and $t$.

The process is straightforward:

1.  The model starts with an initial guess for each player's skill (which is identical for all the players).
2.  Then, in a loop, for each match:
    * It first samples how the match outcome ($t$) would look based on the current skill estimates for the players.
    * Next, using that outcome sample, it refines the skill estimates for both players, ensuring the winner's skill is estimated higher than the loser's.
3.  This process is repeated many times. The initial "warm-up" guesses (the **burn-in period**) are eventually discarded to ensure the final estimates are stable. The remaining estimates then provide a clear picture of each player's most likely skill.

### Visualizing Skill Changes

When Player 1 wins, for example, the model adjusts. Player 1's estimated skill typically **increases**, and Player 2's estimated skill **decreases**. Crucially, the **spread of the estimates (variance) also shrinks**, indicating increased confidence in these new skill levels.

---

## 2. Refining Estimates: Burn-in and Sample Size

Getting reliable skill estimates requires careful tuning of the sampling process.

### The Importance of "Burn-in"

Like warming up an engine, the model needs a bit of time to get going and settle on good estimates. This initial warm-up phase is called the **burn-in period**. Based on experiments, discarding the first **10% of all samples** is usually sufficient. This ensures that the model has **converged** to a stable skill estimate, typically within about 1,500 samples.

### Finding the Right Number of Samples

Once the model has "warmed up" (after burn-in), the **total number of samples** dictates how precise the skill estimates are. It was found that **10,000 samples** offer a great balance. This number is computationally efficient (runs relatively quickly) and provides results very similar to what would be obtained from much larger, more time-consuming sample sizes like 50,000 or 100,000.

| Number of Samples | Time (s) |
| :---------------- | :------- |
| 1000              | 0.38     |
| 2000              | 0.64     |
| 5000              | 2.98     |
| **10000** | **3.63** |
| 50000             | 17.55    |
| 100000            | 35.11    |

This balance between precision and speed makes 10,000 samples a practical choice for this skill rating system.

---

## 3. Smarter Skill Updates with Truncated Gaussian Sampling

Instead of just sampling skills and checking if they meet conditions (like the winner having a higher skill than the loser), the model uses a more efficient technique called **Assumed Density Filtering (ADF)** with **truncated Gaussian sampling**.

This means that when, say, Team 1 wins, the model directly samples skill values where Team 1's skill is *guaranteed* to be higher than Team 2's. This avoids wasting computational effort on "bad" samples and makes the process much more efficient.

### Real-World Application: Serie A 2018/2019

Applying this method to the **Serie A 2018/2019** football season provided fascinating insights:

* **Skill and Consistency:** The estimated skill for each team includes an **expected value** and a **standard deviation**. A **higher standard deviation** means the team's performance was more **unpredictable**, while a **lower standard deviation** indicates **consistent** performance in line with its expected skill.
* **The Juventus Anomaly:** Interestingly, **Juventus** had a surprisingly **low expected skill** at the end of the season despite winning the league. This is easily explainable: they clinched the title early, allowing the coach to field weaker players in later matches, leading to unexpected losses against lower-ranked teams. The model accurately captured this effect, showing how real-world strategic decisions can influence skill estimates.
* **Impact of Match Order:** It was also found that the final team rankings could change slightly if the order of the matches in the season was shuffled. This highlights how the sequential updates of skill estimates are sensitive to the particular sequence of wins and losses encountered.

Here's a snapshot of the final skill estimates for Serie A teams:

| Pos | Team       | Expected Skill | Skill Volatility (Std Dev) | Actual Points |
| :-- | :--------- | :------------- | :------------------------- | :------------ |
| 1   | Juventus   | 25.95          | 1.13                       | 90            |
| 2   | Napoli     | 28.60          | 1.17                       | 79            |
| 3   | Atalanta   | 29.96          | 1.80                       | 69            |
| 4   | Inter      | 29.14          | 1.21                       | 69            |
| 5   | Milan      | 28.98          | 1.34                       | 68            |
| 6   | Roma       | 29.53          | 1.54                       | 66            |
| 7   | Torino     | 28.56          | 1.15                       | 63            |
| 8   | Lazio      | 27.28          | 0.98                       | 59            |
| 9   | Sampdoria  | 26.82          | 1.13                       | 53            |
| 10  | Bologna    | 29.47          | 1.21                       | 44            |
| 11  | Sassuolo   | 25.66          | 1.15                       | 43            |
| 12  | Udinese    | 28.53          | 1.21                       | 43            |
| 13  | SPAL       | 27.06          | 1.03                       | 42            |
| 14  | Parma      | 25.41          | 0.99                       | 41            |
| 15  | Cagliari   | 26.58          | 0.93                       | 41            |
| 16  | Fiorentina | 24.24          | 0.85                       | 41            |
| 17  | Genoa      | 25.13          | 1.10                       | 38            |
| 18  | Empoli     | 27.94          | 1.13                       | 38            |
| 19  | Frosinone  | 25.53          | 0.89                       | 25            |
| 20  | Chievo     | 26.17          | 0.98                       | 17            |


---

## 4. Euro 2024 Analysis

The skill model was also applied to the **Euro 2024** football games, using data from the open-source StatsBomb dataset.

* **Top Team:** The model correctly identified **Spain** as the team with the highest skill, aligning with their victory in the tournament.
* **Overall Fit:** While the top team was accurate, the model's overall skill rankings for all teams did not perfectly match the official tournament results. This suggests that while powerful, such models are simplifications of complex real-world dynamics.
* **Prediction Accuracy:** When used for prediction, the model achieved a **55.9% accuracy** on the Euro 2024 dataset.
## Euro 2024 Analysis Results

The resulting skill estimates are roughly aligned with the actual tournament outcomes.

| Team           | Expected Skill | Skill Volatility (Std Dev) | Actual Final Place |
| :------------- | :------------- | :------------------------- | :----------------- |
| Spain          | 30.20          | 2.52                       | Champion           |
| Germany        | 27.87          | 2.18                       | Quarter-finalist   |
| Switzerland    | 27.82          | 2.22                       | Quarter-finalist   |
| England        | 27.14          | 2.17                       | Runner-up          |
| France         | 27.01          | 1.95                       | Semi-finalist      |
| Portugal       | 26.61          | 1.92                       | Round of 16        |
| Netherlands    | 26.42          | 2.05                       | Semi-finalist      |
| Georgia        | 25.20          | 2.11                       | Quarter-finalist   |
| Italy          | 25.13          | 2.15                       | Round of 16        |
| Turkey         | 25.04          | 1.68                       | Quarter-finalist   |
| Austria        | 25.03          | 1.79                       | Quarter-finalist   |
| Slovenia       | 25.00          | 2.89                       | Round of 16        |
| Belgium        | 24.87          | 1.64                       | Round of 16        |
| Croatia        | 24.64          | 2.62                       | Group Stage        |
| Hungary        | 24.51          | 1.90                       | Group Stage        |
| Ukraine        | 24.22          | 1.88                       | Group Stage        |
| Slovakia       | 23.79          | 1.91                       | Round of 16        |
| Serbia         | 23.68          | 2.43                       | Group Stage        |
| Romania        | 23.59          | 1.58                       | Round of 16        |
| Denmark        | 23.17          | 2.38                       | Round of 16        |
| Scotland       | 23.13          | 2.24                       | Group Stage        |
| Albania        | 22.75          | 2.14                       | Group Stage        |
| Poland         | 22.47          | 1.92                       | Group Stage        |
| Czech Republic | 22.15          | 2.07                       | Group Stage        |
---

## 5. Potential Applications

The **ELO-like** skill ratings derived from this model have various practical applications, especially in competitive scenarios:

* **Match Prediction:** The most direct application is predicting the outcome of future matches. By comparing the expected skill values of two competing teams or players, one can estimate the probability of each winning. This is particularly useful for sports analytics, fantasy leagues, or betting markets.
* **Player/Team Ranking Systems:** The model can continuously update and maintain dynamic rankings for players or teams in any competitive domain (e.g., esports, card games, other sports leagues). These rankings would reflect current form and skill more accurately than static leaderboards.
* **Fair Matchmaking:** For platforms like Xbox Live (where TrueSkill is used), these ratings can be used to create balanced and competitive matches, pairing players of similar skill levels. This improves player experience by ensuring games are neither too easy nor too difficult.
* **Performance Analysis:** The model's output provides both an expected skill and a "skill volatility" (standard deviation). This allows for deeper performance analysis—identifying players or teams whose performance is consistently predictable versus those who are more erratic.
* **Strategic Insights:** For coaches or managers, understanding the skill dynamics can offer insights into team strengths and weaknesses, helping in player development or strategic planning.

---

## 6. Get Started

## How to set up virtual environment
1. Create a virtual environment:

`python -m venv venv`

2. Activate the virtual environment:

`source venv/bin/activate`

3. Install required packages

`pip install -r requirements.txt`


## Running the code

To run the code form  Jupyter notebook, use the following command:
`jupyter notebook runme.ipynb`

