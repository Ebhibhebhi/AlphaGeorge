# AlphaGeorge

A Connect Four AI that learned to play entirely through self-play, using the same algorithm behind DeepMind's AlphaZero.

**[Play against AlphaGeorge →](https://ebhibhebhi.github.io/AlphaGeorge/)**

---

## How It Works

AlphaGeorge combines a deep neural network with Monte Carlo Tree Search (MCTS) to play Connect Four at a high level — without ever being taught any strategy by a human.

**The Neural Network** takes in the current board state (encoded as a 6×7 grid across 3 channels) and outputs two things: a policy (probability distribution over the 7 columns) and a value (who's winning, from -1 to +1). The architecture is a ResNet with 9 residual blocks and 128 hidden channels, totaling roughly 2 million parameters.

**Monte Carlo Tree Search** makes the AI stronger by letting it "think ahead." Instead of just trusting the network's first instinct, MCTS runs hundreds of simulated futures — walking down a game tree, evaluating positions with the neural network, and tracking which moves lead to the best outcomes. The most-explored move is chosen as the best.

## How It Was Trained

AlphaGeorge was trained using AlphaZero-style reinforcement learning, starting from completely random play with no human knowledge beyond the rules of the game.

The training loop repeats three steps across multiple iterations:

1. **Self-play** — AlphaGeorge plays hundreds of games against itself, using MCTS (800 searches per move) to select strong moves. Every board position is recorded along with the MCTS move probabilities and the eventual game outcome.

2. **Train the network** — The neural network is trained on these self-play positions. The policy head learns to predict the MCTS search results. The value head learns to predict game outcomes. Both improve together.

3. **Iterate** — The improved network generates higher-quality self-play games, which produce better training data, which trains an even stronger network.

Training was performed on Google Colab using a T4 GPU, with 100 games played in parallel to batch neural network evaluations efficiently.

## Playing in the Browser

The trained PyTorch model was exported to ONNX format and runs directly in the browser via ONNX Runtime Web (WebAssembly). No server or GPU required — inference happens entirely on your device.

You can adjust two settings while playing:

- **MCTS search depth (0–800)** — At 0, the AI uses only the raw neural network (instant moves). Higher values run live MCTS in the browser for stronger but slower play.
- **Turn timer (1–60s)** — Adds a countdown to your turn. If time runs out, a random move is played.

## Tech Stack

- **Training**: Python, PyTorch, Google Colab (T4 GPU)
- **Model**: ResNet (9 blocks, 128 channels), exported to ONNX
- **Frontend**: Vanilla HTML/CSS/JS, ONNX Runtime Web
- **Hosting**: GitHub Pages (static site, no backend)
