# DRL Homework 01

## Task 01

Formalizing chess as a MDP:

- set of states
    - finite
        - 8x8 grid, 64 tiles in total
        - 32 pieces
    - constricted to the set of legal states
    - extremely large
        - https://web.archive.org/web/20200523062243/http://archive.computerhistory.org/projects/chess/related_materials/text/2-0%20and%202-1.Programming_a_computer_for_playing_chess.shannon/2-0%20and%202-1.Programming_a_computer_for_playing_chess.shannon.062303002.pdf
        - https://github.com/tromp/ChessPositionRanking
    - constricted further by the turn-based nature of the game
        - agent is presented with the board only after the opponent has made their move
        - agent only encounters a subset of the set of legal states i.e. the legal states that result from the opponent making a move
        - does this matter if the agent learns to play as both colors?
            - yes, the set of legal moves differ for each color (left vs. right bishop, queen/king starting positions), the agent needs to learn to recognize this 
- set of actions
    - notations: standard algebraic notation, coordinate algebraic notation
    - finite
        - limited number of pieces
        - can't move more than one piece per turn (except when you can(castling))
    - constricted to the set of legal moves
        - can't move a piece through a tile occupied by another piece (except when you can(knight)), can't capture your own pieces,...
    - constricted further when in check
        - **constricted to a subset which is a function of the state!**
    - changes as the game progresses
    - If an invalid action happened, this is ignored and taken another action until a valid one
- state dynamics
    - the board is discrete, chess pieces always end up where you put them
    - **BUT** the agent has to predict its opponent's move -> the state dynamics are probabilistic
    - opponent chooses from a limited number of moves, each of which can be assigned a probability
    - so if the agent models its opponents behavior, doesn't that mean it learns how to play against itself? 
       - i think no, because modelling the opponents behavior can be more likely a part of guessing the opponent's next move
- reward dynamics
    - intuitively we want to issue a positive reward to the agent for winning the game and a negative reward for losing the game
    - it might take the agent a really long time to learn anything that way though since the path to victory is complex and it is unlikely that the agent is going to reach it through random moves without any breadcrumbs to guide it there
    - maybe we should issue positive rewards for capturing pieces and negative rewards for losing pieces
        - if rewards aren't chosen well the agent could learn to maximize reward by losing
        - in chess theory pieces are sometimes assigned different values. By assigning different rewards to different pieces the agent might learn to trade pieces in a way that is conducive to winning
    - the way that the rewards are weighed could influence whether the agent plays more offensively or defensively
        - maybe the rewards can be adjusted in training? (we can change the rewards while training but this corresponds to changing the policy actions)
    - when agent goes from one state to another state with a higher possibility of winning (meaning, with a better policy), the agent can get a positive reward
- initial state distribution (?)
    - as White, the initial state is always the same
    - as Black, the initial state is one of the 20(?) states that can occur after White has made their first move 
    
Formalizing a policy for chess:

The policy depends on what reward dynamics were chosen. Generally speaking however the agent should prefer moves that maximize its chances of winning i.e. to checkmate the opponent's king.




## Task 02

Formalizing lunar landing gym as a MDP:

- set of states
    - According to the documentation, the state of the lander consists of 8 values: 2-D coordinates, vertical velocity, horizontal velocity, angle, angular velocity, and one boolean per leg which indicates whether the leg is touching the ground or not
    - The landing pad is always in the same place and the lander is guided towards it by rewards, so it does not need to be an explicit part of the state?
 		-In my opinion, It should be a part of the state, because this is the coordinate we are trying to get
    - The shape of the lunar surface changes with each iteration, but the agent appears to be blind to this?
    - The lander has a shape and size and can collide with the terrain. This needs to be represented in the state somehow
    - Two versions of the environment exist, one discrete and the other continuous
- set of actions
    There exist four discrete actions: A(S) = {'do nothing', 'fire left orientation engine', 'fire main engine', 'fire right orientation engine'}
- state dynamics 
    - States transition into each other according to a simple physics simulation. Wind can be enable optionally
- reward dynamics:
> Reward for moving from the top of the screen to the landing pad and coming
    to rest is about 100-140 points.
    If the lander moves away from the landing pad, it loses reward.
    If the lander crashes, it receives an additional -100 points. If it comes
    to rest, it receives an additional +100 points. Each leg with ground
    contact is +10 points.
    Firing the main engine is -0.3 points each frame. Firing the side engine
    is -0.03 points each frame. Solved is 200 points.

- initial state distribution: The lander always appears at the top center with a random initial force applied to it

Formalize a policy for the lander:
- Land in the (0,0) which is landing pad with the both legs touching the ground

## Task 03

Explain what the environment dynamics (i.e. reward function and state transition function) are and give at least two examples
 
The reward function gives the expected value that the agent will be presented with a reward r when it takes an action a in state s. The state transition function gives the probability of the environment changing from state s to state s' if the agent takes an action a in state s.

Examples:
1) In the game of chess, If the agent is in a spesific state that can take a spesific action for winning, there is a high possibility that agent end up in a winning state with the reward.
2) In the lunarlander game, from a closer state to landingpad, going to (0,0) position with one of the 4 action has a high possibility of getting reward and endind up accomplishing the game. 

Discuss: Are the environment dynamics generally known and can practically be used to solve a problem with RL?

No, they are not generally known. Though we can accurately model simple or simulated environments, this becomes much more difficult for general real-world applications. The agent's designer may not know everything about the physics of the environment, how the environment works,  and to an even greater extent they may not know what reward to expect from certain actions. We should expect the agent to explore its environment to learn the environment dynamics.
