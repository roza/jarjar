# Jarjar Slack Notifier

Jarjar is a collection of scripts that lets you programmatically send notifications to your Slack team. 

## What can jarjar do for me?

Here are some things _we_ use it for:

- Send reminders to yourself/group.
- Notify users when their jobs (simulations, backups, etc) are completed.
- Combine jarjar with cron to send out [daily positive vibes](http://i.imgur.com/YkqMwCx.png).


## But How?

We have designed two interfaces into jarjar: a shell command and a Python Module.

# The Shell Command

The `sh/` directory contains a shell command `jarjar` and a configuration file `.jarjar`.

Fill the configuration file out with useful defaults. Critically, you'll want to paste in your Slack webhook so that jarjar knows where to send the message. Then put the configuration file in your home directory (`~/`), that's where jarjar will look for it. Don't worry, you can override those defaults later. Here's a sample `.jarjar`:

```sh
channel="@my_username" # or "#general" 
message="Hi! Meesa Jar Jar Binks."
webhook="your-webhook-here"
```

Then, add the `jarjar` _script_ [to your path](https://stackoverflow.com/questions/20054538/add-a-bash-script-to-path). After that, you can use it like so:

```sh
# echo the default message to the default channel
jarjar -e

# echo a message to the #general channel
jarjar -e -u "#general" -m "Hi, everyone!!"

# Send yourself a notification when a script is completed
jarjar -u @username -m "Your job is finished!" python my-script.py
jarjar ./my-script.sh

# send a message to the non-default slack team
jarjar -e -u @username -m "Hi!" -w "their-webhook-url"
```

## Modifiers

| Modifier | Description | 
|   ---    |     ---     |
|   `-e`   | Echo the message. If this flag is not included, jarjar waits until a provided process is completed to send the message. By default (without the `-e` flag), jarjar launches a screen with your script (which terminates when your script ends). You can always resume a screen launched by jarjar by finding the appropriate PID: `screen -ls` and `screen -r PID`. |
|   `-r`   | Attaches screen created by jarjar (when `-e` is not used) |
|   `-m`   | Message to be sent |
|   `-u`   | Username (or channel). Usernames must begin with `@`, channels with `#`. |
|   `-w`   | Webhook for the Slack team. |

# The Python Module

This module implements jarjar's functionality more fluidly within Python scripts. Importing the jarjar module provides a simple class, which can be used to send messages like the shell command.

Installation is simple:

1. `pip install jarjar`
2. _Optional_: create a `~/.jarjar` file with some defaults (this is shared with the shell command).

```sh
channel="@my_username" # or "#general" 
message="Hi! Meesa Jar Jar Binks."
webhook="your-webhook-here"
```


Then, you're good to go! You can use it as follows:

```python
from jarjar import jarjar

# initialize with defaults from .jarjar
jj = jarjar()

# initialize with custom defaults
jj = jarjar(channel='#channel', webhook='slack-webhook-url') 

# initialization is not picky -- provide one or both arguments
jj = jarjar(webhook = 'slack-webhook-url')

# send a text message
jj.text('Hi!') 

# send a message to multiple channels or users
jj.text('Hi!', channel=["@jeffzemla","#channel"])

# send an attachment
jj.attach(dict(status='it\'s all good')) 

# send both
jj.post(text='Hi', attach=dict(status='it\'s all good'))

# override defaults after initializing
jj.attach(dict(status='it\'s all good'), channel = '@jeffzemla')
jj.text('Hi!', channel = '@nolan', webhook = 'another-webhook')
```

## Methods

### `text`

> `jj.text(text, **kwargs)`

Send a text message, specified by a string, `text`. User may optionally supply the channel and webhook in the `kwargs`.

### `attach`

> `jj.attach(attach, **kwargs)`

Send attachments, specified by values in a dict, `attach`. User may optionally supply the channel and webhook in the `kwargs`.

### `post`

> `jj.post(text=None, attach=None, channel=None, webhook=None)`

The generic post method. `jj.text(...)` and `jj.attach(...)` are simply convenience functions wrapped around this method. User may supply text and/or attachments, and may override the default channel and webhook url.


# How to configure a Slack Webhook

You'll need to configure [Incoming Webhooks](https://api.slack.com/incoming-webhooks) for your Slack team. You need to specify a default channel (which jarjar overrides), and Slack will give you a webhook url. That's it! 

When you're setting things up, you can also specify a custom name and custom icon. We named our webhook robot `jar-jar`, and we used [this icon](http://i.imgur.com/hTHrg6i.png), so messages look like this:

![](http://i.imgur.com/g9RG16j.png)

