# Nethack Learning Environment Language Wrapper 

Language Wrapper for the [Nethack Learning Environment (NLE)](https://github.com/facebookresearch/nle) and [MiniHack](https://github.com/facebookresearch/minihack)

## Description
The wrapper inherits from the [Gym Wrapper](https://github.com/openai/gym/blob/9e66399b4ef04c1534c003641802e2ac1363e8a6/gym/core.py#L286-L421) and translates the non-language observations from NLE into similar language representations.  Actions can also be optionally provided in text form which are converted to the Discrete actions of the NLE.

```
Inventory:
a: a blessed +1 mace (weapon in hand)
b: a +0 robe (being worn)
c: a blessed +0 small shield (being worn)
d: 4 potions of holy water
e: a clove of garlic
f: a sprig of wolfsbane
g: a spellbook of stone to flesh
h: a spellbook of identify

Stats:
Strength:15/15
Dexterity:10
Constitution:12
Intelligence:12
Wisdom:18
Charisma:9
Depth:1
Gold:0
HP:14/14
Energy:6/6
AC:7
XP:1/0
Time:1
Position:46|14
Hunger:Not Hungry
Monster Level:0
Encumbrance:Unemcumbered
Dungeon Number:0
Level Number:1
Score:0
Alignment:Neutral
Condition:None

Cursor:Yourself a priestess

Observation:
vertical closed door far westnorthwest
horizontal wall near north and northwest
vertical wall very near northeast and east
vertical closed door very near eastnortheast
southeast corner very near southeast
horizontal wall very near south and southwest
tame kitten adjacent northeast

Message:
Hello Agent, welcome to NetHack!  You are a neutral human Priestess.
```

## Getting Started


### Requirements 

Requires `python>=3.7` and `cmake>=3.15`.

 To install 

 CMake can be installed on macos using homebrew
 ```
 brew install cmake
 ```
 Alternatively, and for other platforms follow the instructions at https://cmake.org/install/


### Installation

To use the environment you can install from the PyPI.
```
pip install nle-language-wrapper
```

### Development

For development on the environment clone the repository and install in development mode.
```
git clone https://github.com/ngoodger/nle-language-wrapper --recursive
pip install -e ".[dev]"
```
To update the library with changes to the C++ recompile by running 
```
python -m setup develop
```

## TODO Agent

There is a [Sample Factory](https://github.com/alex-petrenko/sample-factory) based agent included achieving TODO reward after TODO steps.  This agent uses a small transformer model to encode the language observations for the policy model and value function model.  The algorithm used is Asynchronous Proximal Policy Optimization (APPO) described in [Sample Factory: Egocentric 3D Control from Pixels at 100000 FPS with Asynchronous Reinforcement Learning](https://arxiv.org/abs/2006.11751v2).

### Hardware Requirements
The default configuration was tested on an Nvidia 3090 with 24Gbyte and a Ryzen 1700 CPU. Training runs at approximately 7k/FPS.  To train on less GPU memory a smaller model could be configured, or a smaller max token length or batch size could be used.
```
    --transformer_hidden_size" (default=64)
    --transformer_hidden_layers" (default=2)
    --transformer_attention_heads (default=2)
    --max_token_length (default=256)
    --batch_size (default=1024)
```

### Running the agent
There is a pre-trained agent included with the project.
```
python nle_language_wrapper/agents/sample_factory/enjoy.py \
--env nle_language_env \
--encoder_custom nle_language_transformer_encoder \
--experiment nle_competition_language_agent \
--algo APPO
```

### Training the agent
To train a new agent simply run the following module and the set the experiment name to the desired value.
```
python nle_language_wrapper/agents/sample_factory/train.py \
--env nle_language_env \
--encoder_custom nle_language_transformer_encoder \
--experiment my_experiment_1 \
--algo APPO \
--batch_size 2048 \
--num_envs_per_worker 24 \
--num_workers 8 \
--reward_scale 0.1
```

## Usage

The wrapper can be used simply by instantiating the base environment from NLE or MiniHack and passing it to the wrapper constructor.  E.g. 
```
import nle
from nle_language_wrapper import  NLELanguageWrapper
env = NLELanguageWrapper(gym.make("NetHackChallenge-v0"))
obsv = env.reset()
obsv, reward, done, info = env.step("wait")
```

Alternatively to utilize the discrete actions rather than language actions specify `use-text-action`.
```
env = NLELanguageWrapper(gym.make("NetHackChallenge-v0"),  use_language_action=text)
obsv = env.reset()
wait_action = 17
obsv, reward, done, info = env.step(wait_action)
```

For a detailed walkthrough of the environment see TODO notebook.

## Manual play

```
python -m nle_language_wrapper.scripts.play
```

## Licence
MIT License
