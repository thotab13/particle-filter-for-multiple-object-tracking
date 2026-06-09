# Portfolio - 2
## Sensor Fusion - Particle Filter

This project demonstrates a Python implementation of a particle filter designed to track multiple objects simultaneously. The code simulates the 2D projectile motion of several balls and then uses a sophisticated particle filter to estimate their trajectories (position and velocity) based on noisy and incomplete sensor data.

This implementation is notable for its ability to handle key challenges in multi-object tracking:
* **Data Association:** Correctly assigning sensor observations to the right object when multiple objects are present.
* **Observation Dropout:** Maintaining tracking accuracy even when the sensor temporarily fails to provide any data.
* **Noise and Uncertainty:** Operating with realistic, imperfect sensor measurements.

---

## How It Works

The code is divided into three main parts: the physics simulation that provides the "ground truth"  the particle filter that performs the tracking and  Visualization & Analysis  where results are plotted for observation.

### 1. Physics Simulation
The `simulate_multiple_balls` function creates the trajectories for a configurable number of balls. It models projectile motion under gravity, including simple ground bounces. To create a realistic tracking challenge, the simulation introduces:

* **Observation Noise:** Each recorded position is perturbed using a multivariate normal distribution (`OBSERVATION_NOISE_COV`).
* **Sensor Dropout:** There is a probability (`dropout_prob`) that the sensor will fail at any given timestep, producing no observations at all.
* **Variable Timesteps:** The time delta (`dt`) between sensor readings can vary, simulating an irregular sensor.

### 2. The Multi-Ball Particle Filter
The `MultiBallParticleFilter` class is the core of the tracking algorithm. It works through a predict-update-resample cycle:

*  **Initialization**: The filter starts by creating a large number of "particles" (`num_particles`). Each particle represents a complete hypothesis for the state (x, y, vx, vy) of *all* balls being tracked.
*  **Predict**: In the `predict_particles` step, the filter uses the laws of physics to move every particle forward in time, anticipating the next state of the system.
*  **Update**: When a new set of observations is received, the `update_weights` method calculates a "weight" for each particle. This weight represents how well that particle's predicted ball positions match the actual observations.
    > **Data Association**: A key feature here is the `_compute_assignment_likelihood` function. Since it's unknown which observation belongs to which ball, the filter calculates the likelihood of *all possible permutations* and uses the best match. This robustly solves the data association problem.
*  **Resample**: The `resample_particles` step creates a new generation of particles by preferentially selecting particles with higher weights. This critical step focuses the filter's computational resources on the most promising hypotheses, allowing it to converge on the true state.
*  **Estimate**: Finally, the `estimate_states` function uses the **DBSCAN clustering algorithm** to analyze the positions of the highest-weighted particles. By finding the dense clusters in the particle cloud, it can robustly determine a single, final estimate for each ball's position and velocity.

### 3. Analysis and Visualization

After the tracking is complete, the script uses several functions to analyze the performance of the particle filter and generate plots:

* **Error Calculation**: The Root Mean Square Error (RMSE) is calculated for each ball by comparing the estimated positions to the ground truth positions. The overall RMSE is also computed to provide a single metric for the filter's accuracy.

* `plot_trajectories`: This function creates the primary visualization, overlaying the true trajectories, the noisy sensor observations, and the filter's estimated trajectories. This gives an immediate, intuitive sense of the tracking quality.

* `plot_dropout_error_analysis`: This function generates a time-series plot of the position error for each ball. Crucially, it highlights the periods where sensor data was dropped out.
* `plot_velocity_vs_distance`: This function generates a separate, detailed plot for each ball, comparing the true velocity to the estimated velocity as a function of horizontal distance. This provides a deeper analysis of the filter's ability to estimate the objects' dynamic states, not just their positions.
---

## Requirements
The script requires the following Python libraries:
* `numpy`
* `matplotlib`
* `scipy`
* `scikit-learn`

You can install them using pip from within a Jupyter cell by running:
```{r}
!pip install numpy matplotlib scipy scikit-learn
```


---

## How to Run

1.Navigate to the file particle_filter.ipynb

2.Run three code blocks one by one clicking on run icon or use Shift + Enter


The script will execute the simulation, run the particle filter, print the final Root Mean Square Error (RMSE) for each ball, and then display a series of plots.

---

##Understanding the Output

The script generates three sets of plots to visualize the filter's performance:

1. Multi-Ball Tracking with Particle Filter
This plot shows the ground truth trajectories (solid lines), the noisy sensor observations (gray 'x' markers), and the particle filter's final estimated trajectories (dashed lines with circles). It provides a high-level visual assessment of the tracking performance.

2. Analysis: Tracking Error vs. Sensor Dropout
This graph plots the position error (the distance between the true and estimated position) for each ball over time. The red shaded areas indicate "Dropout Windows" where the sensor provided no data.

3. Velocity vs. Distance Analysis
This set of plots (one for each ball) compares the true velocity magnitude against the filter's estimated velocity as a function of horizontal distance traveled. This provides a deeper insight into the filter's ability to accurately estimate not just the position, but also the dynamic state (velocity) of the objects.

---

##Configuration

You can easily modify the simulation and filter behavior by changing the parameters in the "Simulation Setup" section of the script:



*  `N_BALLS`: The number of balls to simulate and track.
*   `DT`: The base time step for the simulation.
*   `TOTAL_TIME`: The total duration of the simulation in seconds.


*   `LAUNCH_PARAMS`: A list of dictionaries defining the initial launch conditions (position, speed, angle) for each ball.



*   `OBSERVATION_NOISE_COV`: The covariance matrix for the observation noise. Higher values mean noisier measurements.
*   `TRANSITION_COV`: The covariance for the process noise, which adds a small amount of randomness to the physics model during the prediction step.

*   `num_particles`: The number of particles used by the filter. More particles can lead to higher accuracy but will increase computation time.










# ----------The End-----------------
