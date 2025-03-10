import random
import matplotlib.pyplot as plt

# Payoff Matrix
# (Player A, Player B)
payoff_matrix = {
    ('C', 'C'): (3, 3),  # Both cooperate
    ('C', 'D'): (0, 5),  # A cooperates, B defects
    ('D', 'C'): (5, 0),  # A defects, B cooperates
    ('D', 'D'): (1, 1)   # Both defect
}

# Strategies
class AlwaysCooperate:
    def play(self):
        return 'C'

class AlwaysDefect:
    def play(self):
        return 'D'

class TitForTat:
    def __init__(self):
        self.opponent_last_move = 'C'

    def play(self):
        return self.opponent_last_move

    def update(self, opponent_move):
        self.opponent_last_move = opponent_move

# Simulation function
def play_game(strategy1, strategy2, rounds=100):
    score1, score2 = 0, 0
    moves1, moves2 = [], []

    for _ in range(rounds):
        move1 = strategy1.play()
        move2 = strategy2.play()

        # Update scores based on moves
        payoff1, payoff2 = payoff_matrix[(move1, move2)]
        score1 += payoff1
        score2 += payoff2

        # Track moves
        moves1.append(move1)
        moves2.append(move2)

        # Update Tit-for-Tat if applicable
        if isinstance(strategy1, TitForTat):
            strategy1.update(move2)
        if isinstance(strategy2, TitForTat):
            strategy2.update(move1)

    return score1, score2, moves1, moves2

# Run simulations
strategies = [AlwaysCooperate(), AlwaysDefect(), TitForTat()]
labels = ["Always Cooperate", "Always Defect", "Tit-for-Tat"]

results = []
for i in range(len(strategies)):
    for j in range(len(strategies)):
        score1, score2, moves1, moves2 = play_game(strategies[i], strategies[j])
        results.append((labels[i], labels[j], score1, score2))

# Plot Results
for result in results:
    print(f"{result[0]} vs {result[1]} => Score: {result[2]} - {result[3]}")

plt.figure(figsize=(10, 5))
for i, result in enumerate(results):
    plt.bar(i, result[2], color='blue', label='Player 1' if i == 0 else "")
    plt.bar(i, result[3], color='orange', label='Player 2' if i == 0 else "")

plt.xticks(range(len(results)), [f"{r[0]} vs {r[1]}" for r in results], rotation=90)
plt.ylabel("Score")
plt.title("Prisoner's Dilemma Simulation")
plt.legend()
plt.tight_layout()
plt.show()
