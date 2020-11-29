---
date: "2020-11-27"
draft: false
lastmod: "2020-11-27"
publishdate: "2020-11-27"
tags:
- CV
title: Deploying your ml model via telegram bot
---



## Intro

Let me come to the point without any delay. You can serve your model as you think, and there is no set of rules for this. You can deploy through mobile app / web-app, there are plenty of guidance availble in vast internet. Or else you can get to your customers/users through a simple telegram bot. It is simpler than you think. Let's go build it.





<p align="center"><img src="https://media.giphy.com/media/l4Ki8WjiS8RCQATDi/source.gif" style="zoom:100%;" />





## Pre-requisites:

1. There are plenty of telegram bot wrappers available for python. But mostly one used is [this](https://github.com/python-telegram-bot/python-telegram-bot) ❤️.

2. Before going further you will need an **API key** for your bot, get one using [Botfather 🤖](https://t.me/botfather).
3. Hosting service: Heroku, PythonAnyWhere or any of your choose (you get free tiers)



## ML model

Train one or else use pretrained models. It's best practice to quantize your models as we are going to deploy them on heroku free plan service, we get 500mb ram limit and less storage. If the model is in Tensorflow convert it into TFlite (or) If it is in Pytorch, then convert from PyTorch >> ONNX >> Tensorflow >> Tflite. 



## Bot Code

Write your main bot code by taking reference from echo bot examples [here](https://github.com/python-telegram-bot/python-telegram-bot/blob/master/examples/echobot.py):

Bot code look like this:

```python
import logging
import os

from telegram import Update
from telegram.ext import Updater, CommandHandler, MessageHandler, Filters, CallbackContext
from model import get_predictions  # calling model func

# Enable logging
logging.basicConfig(
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', level=logging.INFO
)

logger = logging.getLogger(__name__)


# Define a few command handlers. These usually take the two arguments update and
# context. Error handlers also receive the raised TelegramError object in error.
def start(update: Update, context: CallbackContext) -> None:
    """Send a message when the command /start is issued."""
    update.message.reply_text('Hi send an image to classify!')


def help_command(update: Update, context: CallbackContext) -> None:
    """Send a message when the command /help is issued."""
    update.message.reply_text('Help!')


def photo(update: Update, context: CallbackContext) -> int:
    user = update.message.from_user
    photo_file = update.message.photo[-1].get_file()
    photo_file.download('user_photo.jpg')
    logger.info("Photo of %s: %s", user.first_name, 'user_photo.jpg')
    update.message.reply_text(
        'Okay now wait a few seconds!!!'
    )
    update.message.reply_text(get_prediction('user_photo.jpg'))


def main():
    """Start the bot."""
    # Create the Updater and pass it your bot's token.
    TOKEN = " " # place your token here
    updater = Updater(TOKEN, use_context=True)
    PORT = int(os.environ.get('PORT', '8443'))

    # Get the dispatcher to register handlers
    dispatcher = updater.dispatcher

    # on different commands - answer in Telegram
    dispatcher.add_handler(CommandHandler("start", start))
    dispatcher.add_handler(CommandHandler("help", help_command))

    # on noncommand i.e message - echo the message on Telegram
    dispatcher.add_handler(MessageHandler(Filters.photo & ~Filters.command, photo))

    updater.start_webhook(listen="0.0.0.0",
                      port=PORT,
                      url_path=TOKEN)
    updater.bot.set_webhook("https://yourapp.herokuapp.com/" + TOKEN)
    updater.idle()

if __name__ == '__main__':
    main()
```



`update.message.reply_text(get_prediction('user_photo.jpg'))`  Here I'm passing the result predictions to user by calling the model, you can replace it according to your model code.



## Deploy

Prepare requirements file, and Proc file. 

Heroku needs `Procfile` for running your code.

look like this: 

```reStructuredText
web: python bot.py
```



Now use heroku-cli to deploy your bot. 



My telegram-bot template [link](https://github.com/ash11sh/bot), Use this for reference.