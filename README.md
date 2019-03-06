# rllite

This is a lite repository for reinforcement learning using PyTorch


## How to install
### Requirements
* Python >= 3.5
* Pytorch >= 0.4
### Install Stable Version
```bash
pip install rllite
```
### Install Latest Version
```bash
git clone https://github.com/ZJU-RL/rllite.git
cd rllite
pip install -e .
```

## How to use
### Simple
```python
from rllite import SAC
model = SAC('Pendulum-v0').learn(1e6)
```
### Normal
```python
from rllite import SAC
# set
model = SAC(
    env_name = 'Pendulum-v0',
    load_dir = './ckpt',
    log_dir = "./log",
    buffer_size = 1e6,
    seed = 1,
    max_episode_steps = None,
    batch_size = 64,
    discount = 0.99,
    learning_starts = 500,
    tau = 0.005,
    save_eps_num = 100
	)
# train
model.learn(1e6)
# eval
for _ in range(10):
    done = False
    obs = model.env.reset()
    while not done:
        action = model.predict(obs)
        obs, reward, done, info = model.env.step(action)
        model.env.render()
model.env.close()
```
### Complex
```python
from rllite import SAC
# set
model = SAC(
    env_name = 'Pendulum-v0',
    load_dir = './ckpt',
    log_dir = "./log",
    buffer_size = 1e6,
    seed = 1,
    max_episode_steps = None,
    batch_size = 64,
    discount = 0.99,
    learning_starts = 500,
    tau = 0.005,
    save_eps_num = 100
	)
timesteps = 0
total_timesteps = 1e2
max_eps_steps = 500
# train
while timesteps < total_timesteps:
    done = False
    eps_steps = 0
    obs = model.env.reset()
    while not done and eps_steps < max_eps_steps:
        action = model.predict(obs)
        new_obs, reward, done, info = model.env.step(action)
        model.replay_buffer.push(obs, action, reward, new_obs, done)
        obs = new_obs
        eps_steps += 1
        timesteps += 1
        if timesteps > model.learning_starts and timesteps % model.train_freq == 0:
            model.train_step()
# eval
for _ in range(10):
    done = False
    obs = model.env.reset()
    while not done:
        action = model.predict(obs)
        obs, reward, done, info = model.env.step(action)
        model.env.render()
model.env.close()
```
