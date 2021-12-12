# Financial Services Example Bot

This is an example chatbot demonstrating how to build AI assistants for financial services and banking with Rasa. It includes pre-built intents, actions, and stories for handling conversation flows like checking spending history and transferring money to another account.

## Install dependencies

Run:
```bash
pip install -r requirements.txt
```

To install development dependencies:

```bash
pip install -r requirements-dev.txt
pre-commit install
python -m spacy download en_core_web_md en
python -m spacy link en_core_web_md en
```

> With pre-commit installed, the `black` and `doctoc` hooks will run on every `git commit`.
> If any changes are made by the hooks, you will need to re-add changed files and re-commit your changes.

## Run the bot

Use `rasa train` to train a model.

Then, to run, first set up your action server in one terminal window, listening on port 5056:
```bash
rasa run actions --port 5056
```

Note that port 5056 is used for the action server, to avoid a conflict when you also run the helpdesk bot as described below in the `handoff` section.

In another window, run the duckling server (for entity extraction):

```bash
docker run -p 8000:8000 rasa/duckling
```

Then to talk to the bot, run:
```
rasa shell --debug
```

You can also try out your bot locally using Rasa X by running
```
rasa x
```

## Overview of the files

`data/nlu/nlu.yml` - contains NLU training data

`data/nlu/rules.yml` - contains rules training data

`data/stories/stories*.yml` - contains stories training data

`actions.py` - contains custom action/api code

`domain.yml` - the domain file, including bot response templates

`config.yml` - training configurations for the NLU pipeline and policy ensemble


## Things you can ask the bot

The bot currently has five skills. You can ask it to:
1. Transfer money to another person
2. Check your earning or spending history (with a specific vendor or overall)
3. Answer a question about transfer charges
4. Pay a credit card bill
5. Tell you your account balance

It also has a limited ability to switch skills mid-transaction and then return to the transaction at hand.

It recognises the following payment amounts (besides actual currency amounts):

- `minimum balance`
- `current balance`

It recognises the following vendors (for spending history):

- `Starbucks`
- `Amazon`
- `Target`

You can change any of these by modifying `actions.py` and the corresponding NLU data.

## Handoff

This bot includes a simple skill for handing off the conversation to another bot or a human.
This demo relies on [this fork of chatroom](https://github.com/RasaHQ/chatroom) to work, however you
could implement similar behaviour in another channel and then use that instead. See the chatroom README for
more details on channel-side configuration.


Using the default set up, the handoff skill enables this kind of conversation with two bots:

<img src="./handoff.gif" width="200">


## Action Server Image

You will need to have docker installed in order to build the action server image. If you haven't made any changes to the action code, you can also use the [public image on Dockerhub](https://hub.docker.com/r/rasa/financial-demo) instead of building it yourself.


See the Dockerfile for what is included in the action server image,

To build the image:

```bash
docker build . -t <name of your custom image>:<tag of your custom image>
```

To test the container locally, you can then run the action server container with:

```bash
docker run -p 5055:5055 <name of your custom image>:<tag of your custom image>
```

Once you have confirmed that the container works as it should, you can push the container image to a registry with `docker push`

It is recommended to use an [automated CI/CD process](https://rasa.com/docs/rasa/user-guide/setting-up-ci-cd) to keep your action server up to date in a production environment.
