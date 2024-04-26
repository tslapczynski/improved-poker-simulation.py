# improved-poker-simulation.py
# improved code:  first, removed redundant functions and consolidating functionality. Simplified the hand evaluation logic for readability and performance. Streamlined the command-line interface (CLI) for playing poker by removing unnecessary options and commands.

import random
import click

# Function to create a deck of cards
def deck_of_cards():
    suits = ["H", "D", "C", "S"]
    ranks = [str(i) for i in range(2, 11)] + ["J", "Q", "K", "A"]
    return [rank + suit for suit in suits for rank in ranks]

# Function to deal a hand of cards
def deal_hand(deck, n=5):
    return random.sample(deck, n)

# Function to evaluate the poker hand
def evaluate_poker_hand(hand):
    hand = sorted(hand)
    suits = [card[-1] for card in hand]
    ranks = [card[:-1] for card in hand]

    # Check for Royal Flush
    if len(set(suits)) == 1 and ranks == ['10', 'J', 'Q', 'K', 'A']:
        return "Royal Flush"
    # Check for Straight Flush
    elif len(set(suits)) == 1 and all(int(ranks[i]) == int(ranks[i-1]) + 1 for i in range(1, len(ranks))):
        return "Straight Flush"
    # Check for Four of a Kind
    elif len(set(ranks)) == 2 and ranks.count(ranks[0]) in [1, 4]:
        return "Four of a Kind"
    # Check for Full House
    elif len(set(ranks)) == 2:
        return "Full House"
    # Check for Flush
    elif len(set(suits)) == 1:
        return "Flush"
    # Check for Straight
    elif all(int(ranks[i]) == int(ranks[i-1]) + 1 for i in range(1, len(ranks))):
        return "Straight"
    # Check for Three of a Kind
    elif len(set(ranks)) == 3 and ranks.count(ranks[0]) in [1, 3]:
        return "Three of a Kind"
    # Check for Two Pair
    elif len(set(ranks)) == 3:
        return "Two Pair"
    # Check for One Pair
    elif len(set(ranks)) == 4:
        return "One Pair"
    else:
        return "High Card"

# Function to play a poker hand
def play_poker_hand(hand1, hand2):
    hand1_rank = evaluate_poker_hand(hand1)
    hand2_rank = evaluate_poker_hand(hand2)
    # Compare the ranks of the hands
    if hand1_rank != hand2_rank:
        return "Hand 1" if hand1_rank > hand2_rank else "Hand 2"
    else:
        return "Tie"

# Command-line interface setup
@click.group()
def cli():
    pass

# Command to play a poker hand with a bet
@cli.command("play")
@click.option("--bet", default=1, help="Amount of money to bet")
def play(bet):
    # Deal hands and play poker
    deck = deck_of_cards()
    hand1 = deal_hand(deck)
    hand2 = deal_hand(deck)
    winner = play_poker_hand(hand1, hand2)
    # Determine the winner and display the result
    result = f"Hand 1 wins ${bet}!" if winner == "Hand 1" else (f"Hand 2 wins ${bet}!" if winner == "Hand 2" else "It's a tie!")
    click.secho(result, fg="green" if winner == "Hand 1" else "red" if winner == "Hand 2" else "yellow")

# Execute the command-line interface
if __name__ == "__main__":
    cli()
