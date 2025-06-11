# CS221-Peeking-Blackjack-solved

Download Here: [CS221 Peeking Blackjack solved](https://jarviscodinghub.com/assignment/peeking-blackjack-solution/)

For Custom/Original Work email jarviscodinghub@gmail.com/whatsapp +1(541)423-7793

General Instructions
This (and every) assignment has a written part and a programming part.

The full assignment with our supporting code and scripts can be downloaded as blackjack.zip.

This icon means a written answer is expected in blackjack.pdf.
This icon means you should write code in submission.py.
All written answers must be in order and clearly and correctly labeled to receive credit.

You should modify the code in submission.py between
# BEGIN_YOUR_CODE
and
# END_YOUR_CODE
but you can add other helper functions outside this block if you want. Do not make changes to files other than submission.py.

Your code will be evaluated on two types of test cases, basic and hidden, which you can see in grader.py. Basic tests, which are fully provided to you, do not stress your code with large inputs or tricky corner cases. Hidden tests are more complex and do stress your code. The inputs of hidden tests are provided in grader.py, but the correct outputs are not. To run the tests, you will need to have graderUtil.py in the same directory as your code and grader.py. Then, you can run all the tests by typing
python grader.py
This will tell you only whether you passed the basic tests. On the hidden tests, the script will alert you if your code takes too long or crashes, but does not say whether you got the correct output. You can also run a single test (e.g., 3a-0-basic) by typing
python grader.py 3a-0-basic
We strongly encourage you to read and understand the test cases, create your own test cases, and not just blindly run grader.py.

The search algorithms explored in the previous assignment work great when you know exactly the results of your actions. Unfortunately, the real world is not so predictable. One of the key aspects of an effective AI is the ability to reason in the face of uncertainty.

Markov decision processes (MDPs) can be used to formalize uncertain situations. In this homework, you will implement algorithms to find the optimal policy in these situations. You will then formalize a modified version of Blackjack as an MDP, and apply your algorithm to find the optimal policy.

Problem 1: Value Iteration
In this problem, you will perform the value iteration updates manually on a very basic game just to solidify your intuitions about solving MDPs. The set of possible states in this game is {-2, -1, 0, 1, 2}. You start at state 0, and if you reach either -2 or 2, the game ends. At each state, you can take one of two actions: {-1, +1}.

If you’re in state s and choose -1:

You have an 80% chance of reaching the state s−1.
You have a 20% chance of reaching the state s+1.
If you’re in state s and choose +1:
You have a 30% chance of reaching the state s+1.
You have a 70% chance of reaching the state s−1.
If your action results in transitioning to state -2, then you receive a reward of 20. If your action results in transitioning to state 2, then your reward is 100. Otherwise, your reward is -5. Assume the discount factor γ is 1.
[3 points] Give the value of Vopt(s) for each state s after 0, 1, and 2 iterations of value iteration. Iteration 0 just initializes all the values of V to 0. Terminal states do not have any optimal policies and take on a value of 0.
[3 points] What is the resulting optimal policy πopt for all non-terminal states?
Problem 2: Transforming MDPs
Let’s implement value iteration to compute the optimal policy on an arbitrary MDP. Later, we’ll create the specific MDP for Blackjack.

[3 points] If we add noise to the transitions of an MDP, does the optimal value always get worse? Specifically, consider an MDP with reward function Reward(s,a,s′), states States, and transition function T(s,a,s′). Let’s define a new MDP which is identical to the original, except that on each action, with probability 12, we randomly jump to one of the states that we could have reached before with positive probability. Formally, this modified transition function is:
T′(s,a,s′)=12T(s,a,s′)+12⋅1|{s′′:T(s,a,s′′)>0}|.
Let V1 be the optimal value function for the original MDP, and V2 the optimal value function for the modified MDP. Is it always the case that V1(sstart)≥V2(sstart)? If so, prove it in blackjack.pdf and put return None for each of the code blocks. Otherwise, construct a counterexample by filling out CounterexampleMDP in submission.py.

[3 points] Suppose we have an acyclic MDP for which we want to find the optimal value at each node. We could run value iteration, which would require multiple iterations — but it would be nice to be more efficient for MDPs with this acyclic property. Briefly explain an algorithm that will allow us to compute Vopt for each node with only a single pass over all the (s,a,s′) triples.
[3 points] Suppose we have an MDP with states States and a discount factor γ<1, but we have an MDP solver that can only solve MDPs with discount factor of 1. How can leverage the MDP solver to solve the original MDP?
Let us define a new MDP with states States′=States∪{o}, where o is a new state. Let’s use the same actions (Actions′(s)=Actions(s)), but we need to keep the discount γ′=1. Your job is to define new transition probabilities T′(s,a,s′) and rewards Reward′(s,a,s′) in terms of the old MDP such that the optimal values Vopt(s) for all s∈States are equal under the original MDP and the new MDP.

Hint: If you’re not sure how to approach this problem, go back to Percy’s notes from the first MDP lecture and read closely the slides on convergence, toward the end of the deck.

Problem 3: Peeking Blackjack
Now that we have gotten a bit of practice with general-purpose MDP algorithms, let’s use them to play (a modified version of) Blackjack. For this problem, you will be creating an MDP to describe states, actions, and rewards in this game.

For our version of Blackjack, the deck can contain an arbitrary collection of cards with different face values. At the start of the game, the deck contains the same number of each cards of each face value; we call this number the ‘multiplicity’. For example, a standard deck of 52 cards would have face values [1,2,…,13] and multiplicity 4. You could also have a deck with face values [1,5,20]; if we used multiplicity 10 in this case, there would be 30 cards in total (10 each of 1s, 5s, and 20s). The deck is shuffled, meaning that each permutation of the cards is equally likely.

The game occurs in a sequence of rounds. Each round, the player either (i) takes the next card from the top of the deck (costing nothing), (ii) peeks at the top card (costing peekCost, in which case the next round, that card will be drawn), or (iii) quits the game. (Note: it is not possible to peek twice in a row; if the player peeks twice in a row, then succAndProbReward() should return [].)

The game continues until one of the following conditions becomes true:

The player quits, in which case her reward is the sum of the face values of the cards in her hand.
The player takes a card and “goes bust”. This means that the sum of the face values of the cards in her hand is strictly greater than the threshold specified at the start of the game. If this happens, her reward is 0.
The deck runs out of cards, in which case it is as if she quits, and she gets a reward which is the sum of the cards in her hand. Make sure that if you take the last card and go bust, then the reward becomes 0 not the sum of values of cards.
In this problem, your state s will be represented as a 3-element tuple:

(totalCardValueInHand, nextCardIndexIfPeeked, deckCardCounts)
As an example, assume the deck has card values [1,2,3] with multiplicity 1, and the threshold is 4. Initially, the player has no cards, so her total is 0; this corresponds to state (0, None, (1, 1, 1)). At this point, she can take, peek, or quit.
If she takes, the three possible successor states (each of which has equal probability of 1/3) are:
(1, None, (0, 1, 1))
(2, None, (1, 0, 1))
(3, None, (1, 1, 0))
She will receive a reward of 0 for reaching any of these states. (Remember, even though she now has a card in her hand for which she may receive a reward at the end of the game, the reward is not actually granted until the game ends.)
If she peeks, the three possible successor states are:
(0, 0, (1, 1, 1))
(0, 1, (1, 1, 1))
(0, 2, (1, 1, 1))
She will receive (immediate) reward -peekCost for reaching any of these states. Things to remember about the states after a peek action:
From (0, 0, (1, 1, 1)), taking a card will lead to the state (1, None, (0, 1, 1)) deterministically.
The second element of the state tuple is not the face value of the card that will be drawn next, but the index into the deck (the third element of the state tuple) of the card that will be drawn next. In other words, the second element will always be between 0 and len(deckCardCounts)-1, inclusive.
If she quits, then the resulting state will be (0, None, None). (Remember that setting the deck to None signifies the end of the game.)
As another example, let’s say the player’s current state is (3, None, (1, 1, 0)), and the threshold remains 4.
If she quits, the successor state will be (3, None, None).
If she takes, the successor states are (3 + 1, None, (0, 1, 0)) or (3 + 2, None, None). Note that in the second successor state, the deck is set to None to signify the game ended with a bust. You should also set the deck to None if the deck runs out of cards.
[10 points] Implement the game of Blackjack as an MDP by filling out the succAndProbReward() function of class BlackjackMDP.
[4 points] Let’s say you’re running a casino, and you’re trying to design a deck to make people peek a lot. Assuming a fixed threshold of 20, and a peek cost of 1, design a deck where for at least 10% of states, the optimal policy is to peek. Fill out the function peekingMDP() to return an instance of BlackjackMDP where the optimal action is to peek in at least 10% of states. Hint: Before randomly assinging values, think of the case when you really want to peek instead of blindly taking a card.
Problem 4: Learning to Play Blackjack
So far, we’ve seen how MDP algorithms can take an MDP which describes the full dynamics of the game and return an optimal policy. But suppose you go into a casino, and no one tells you the rewards or the transitions. We will see how reinforcement learning can allow you to play the game and learn its rules & strategy at the same time!

[8 points] You will first implement a generic Q-learning algorithm QLearningAlgorithm, which is an instance of an RLAlgorithm. As discussed in class, reinforcement learning algorithms are capable of executing a policy while simultaneously improving that policy. Look in simulate(), in util.py to see how the RLAlgorithm will be used. In short, your QLearningAlgorithm will be run in a simulation of the MDP, and will alternately be asked for an action to perform in a given state (QLearningAlgorithm.getAction), and then be informed of the result of that action (QLearningAlgorithm.incorporateFeedback), so that it may learn better actions to perform in the future.
We are using Q-learning with function approximation, which means Q^opt(s,a)=w⋅ϕ(s,a), where in code, w is self.weights, ϕ is the featureExtractor function, and Q^opt is self.getQ.

We have implemented QLearningAlgorithm.getAction as a simple ϵ-greedy policy. Your job is to implement QLearningAlgorithm.incorporateFeedback(), which should take an (s,a,r,s′) tuple and update self.weights according to the standard Q-learning update.

[4 points] Now let’s apply Q-learning to an MDP and see how well it performs in comparison with value iteration. First, call simulate using your Q-learning code and the identityFeatureExtractor() on the MDP smallMDP (defined for you in submission.py), with 30000 trials. How does the Q-learning policy compare with a policy learned by value iteration (i.e., for how many states do they produce a different action)? (Don’t forget to set the explorationProb of your Q-learning algorithm to 0 after learning the policy.) Now run simulate() on largeMDP, again with 30000 trials. How does the policy learned in this case compare to the policy learned by value iteration? What went wrong?
[5 points] To address the problems explored in the previous exercise, let’s incorporate some domain knowledge to improve generalization. This way, the algorithm can use what it has learned about some states to improve its prediction performance on other states. Implement blackjackFeatureExtractor as described in the code comments. Using this feature extractor, you should be able to get pretty close to the optimum on the largeMDP.
[4 points] Sometimes, we might reasonably wonder how an optimal policy learned for one MDP might perform if applied to another MDP with similar structure but slightly different characteristics. For example, imagine that you created an MDP to choose an optimal strategy for playing “traditional” blackjack, with a standard card deck and a threshold of 21. You’re living it up in Vegas every weekend, but the casinos get wise to your approach and decide to make a change to the game to disrupt your strategy: going forward, the threshold for the blackjack tables is 17 instead of 21. If you continued playing the modified game with your original policy, how well would you do? (This is just a hypothetical example; we won’t look specifically at the blackjack game in this problem.)
To explore this scenario, let’s take a brief look at how a policy learned using value iteration responds to a change in the rules of the MDP.

First, run value iteration on the originalMDP (defined for you in submission.py) to compute an optimal policy for that MDP.
Next, simulate your policy on newThresholdMDP (also defined for you in submission.py) by calling simulate with an instance of FixedRLAlgorithm that has been instantiated using the policy you computed with value iteration. What is the expected reward from this simulation? Hint: read the documentation (comments) for the simulate function in util.py, and look specifically at the format of the function’s return value.
Now try simulating Q-learning directly on newThresholdMDP instead. What is your expected reward under the new Q-learning policy? Provide some explanation for how the rewards compare, and why they are different.
