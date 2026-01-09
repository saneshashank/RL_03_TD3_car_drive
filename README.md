# Autonomous Car Navigation with TD3

This project implements a self-driving car simulation using **Twin Delayed Deep Deterministic Policy Gradient (TD3)**, a state-of-the-art reinforcement learning algorithm. The car learns to navigate a city map, avoid obstacles (non-road areas), and reach sequential target destinations.

## 🤖 The TD3 Algorithm Explained

TD3 (Twin Delayed DDPG) improves upon the standard DDPG algorithm by addressing the problem of *overestimation bias*, where the agent systematically overestimates the value of its actions. It does this through three key mechanisms ("Tricks"):

1.  **Clipped Double Q-Learning (Twin Critics):**
    *   TD3 uses *two* separate critic networks (Critic 1 & Critic 2).
    *   When calculating the target value for training, it takes the **minimum** Q-value from both critics.
    *   This prevents the agent from being too optimistic about the value of an action, leading to more stable learning.

2.  **Delayed Actor Updates:**
    *   The Actor network (Policy) is updated less frequently than the Critic networks.
    *   In this implementation, the actor is updated **every 2 steps** (`POLICY_FREQ`).
    *   This allows the Critic's value estimates to stabilize before the Actor tries to optimize against them, reducing divergence.

3.  **Target Policy Smoothing:**
    *   When training the Critic, random noise is added to the target action.
    *   This acts as a regularizer, preventing the agent from overfitting to sharp peaks in the Q-value function.
    *   Result: The agent learns a policy that is robust and safer, rather than one that chases unstable high-value spikes.

## 🌟 Features

*   **Advanced RL Algorithm:** Uses **TD3** (Twin Delayed DDPG) for stable continuous control.
    *   **Twin Critics:** Reduces overestimation bias.
    *   **Delayed Actor Updates:** Improves stability.
    *   **Target Policy Smoothing:** Makes the policy more robust to noise.
*   **Interactive Simulation:**
    *   **Real-time Visualization:** Watch the car learn and explore.
    *   **Sensor Visualization:** See what the car "sees" (lidar-like raycasts).
    *   **Reward Chart:** Live graph of training performance.
*   **Map System:**
    *   **Load Custom Maps:** Import any city map image (road must be lighter color).
    *   **Multiple Targets:** Click to set a sequence of waypoints for the car to follow.
*   **Robust Physics:**
    *   Realistic sensor raycasting.
    *   Continuous steering and speed control.
    *   Collision detection based on map pixel brightness.
*   **Save/Load System:**
    *   Save trained models ("brains") to resume training later.
    *   Save/Load specific map configurations.

## 🛠️ Requirements

*   **Python 3.8+**
*   **PyQt6:** For the graphical user interface.
*   **PyTorch:** For the neural networks and RL logic.
*   **NumPy:** For numerical operations.

### Installation

```bash
pip install torch numpy PyQt6
```

## 🚀 Usage

1.  **Run the Application:**
    ```bash
    python citymap_navigation_t3d.py
    ```

2.  **Setup the Environment:**
    *   **Load Map:** Click **"📂 LOAD MAP"** to select a map image (e.g., `citymap.png`).
        *   *Note:* The car considers bright pixels as "road" and dark pixels as "obstacles".
    *   **Place Car:** Click anywhere on the road to set the **Starting Position**.
    *   **Set Targets:** Click on the map to place **Targets**. You can place multiple targets to create a path.

3.  **Start Training:**
    *   Click **"▶ START (Space)"** or press the **Spacebar**.
    *   The car will begin exploring. It might crash often at first—this is normal! It needs to fill its memory buffer before meaningful learning begins.

4.  **Monitor Progress:**
    *   **Reward Chart:** Watch the graph in the left panel. An upward trend indicates learning.
    *   **Stats:** Real-time display of epsilon (noise), score, and crash count.

5.  **Controls:**
    *   **Move Map:** Right-click and drag to pan around large maps.
    *   **Zoom:** Mouse wheel to zoom in/out.

## 🧠 Hyperparameters & Tuning

The "Brain" has been tuned for this specific navigation task. Key parameters include:

*   **Architecture:**
    *   **Actor:** 3-layer FC network (State -> [Angle, Speed]).
    *   **Critic:** Twin 3-layer FC networks (State + Action -> Q-Value).
*   **Sensors:**
    *   `SENSOR_DIST`: 150px (Look-ahead distance).
    *   `SENSOR_ANGLE`: 160° (Field of view).
*   **Training:**
    *   `BATCH_SIZE`: 256
    *   `GAMMA`: 0.99 (Discount factor).
    *   `LR`: 0.001 (0.0005 effectively used).
    *   `EXPL_NOISE`: Decays from ~0.2 to 0.02 to balance exploration and exploitation.

## 📂 Code Structure

*   **`CarBrain`:** The core logic class. Handles the RL agent (Actor/Critic), memory buffer (`deque`), and training loop (`optimize`).
*   **`Actor` & `Critic`:** PyTorch `nn.Module` definitions for the neural networks.
*   **`NeuralNavApp`:** The main PyQt6 window class handling UI events and the game loop.
*   **`CarItem`, `SensorItem`, `TargetItem`:** QGraphicsItems for rendering game objects.

## 💾 Saving & Loading

*   **Save Model:** Click **"💾 SAVE MODEL"** to save the current brain to `last_brain.pth`. This includes network weights, optimizer states, and current map targets.
*   **Load Model:** Click **"📂 LOAD MODEL"** to restore the state from `last_brain.pth`.

---

