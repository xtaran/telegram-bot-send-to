Send Telegram Bot Messages from the Commandline
===============================================

Goal
----

Send system monitoring alerts out via
[Telegram](https://www.telegram.org/) instead of costly SMS.

Result
------

A very compact and generic [Perl](https://www.perl.org/) script with
minimal dependencies (only [Mojolicious](https://mojolicio.us/)) which
is allowing a lot of possible uses.

Plus a wrapper to plug it into the [Xymon](https://www.xymon.com/)
(formerly known as Hobbit) monitoring system for sending out
monitoring alerts via Telegram instead of (or in addition to) e-mail
and SMS.

Usage Preparations
------------------

First you obviously need a Telegram account. This requires a mobile
phone number to which your Telegram account will be
([more or less](https://www.telegram.org/faq#q-how-do-i-change-my-phone-number))
tied.

The usual way to create an account is to use either the
[Telegram App](https://www.telegram.org/apps) (available for Android,
iOS and Windows Phone) or the
[Telegram Desktop App](https://desktop.telegram.org/) available for
Linux (32 and 64 Bit), macOS and Windows.

### Create a Telegram Bot and Get a Bot Token

Start a Telegram chat with [@BotFather](https://t.me/botfather)
([Details](https://core.telegram.org/bots#3-how-do-i-create-a-bot)),
send the command `/newbot` and follow the instructions.

Save the received token in a file named
`/etc/telegram-bot-send-to.token`.

Make sure you set the file owner, file group and permissions so that
only those users who may use the bot can read the file.

### Start a Chat with Your New Bot

Find your bot by e.g. the chosen user name by searching for it in
contacts. Click on the bot and press the Start button shown in bot
chat pane.

This needs to be done for every intended recipient of the
messages. For group chats, the bot needs to be invited to the group
chat.

### Figuring out your Telegram Chat ID

Unfortunately you can't user your chosen Telegram user name as
recipient address. Instead you need the unique "Chat ID" every
Telegram user and group chat has. Unfortunately it's nowhere visible
in the common Telegram apps.

But there are bots who can help you with this:

Start a Telegram chat with
[@get_id_bot](https://t.me/get_id_bot) and tap or type
`/start`. The bot will tell you your Telegram Chat ID.

It can also tell you the Telegram Chat IDs of your contacts or even
Telegram Groups.

In a later version there might be a command line option to show the
Chat IDs known to the bot.

Installation
------------

Install Mojolicious
(e.g. [from CPAN](https://metacpan.org/release/Mojolicious) or from
your favourite distribution's package repository, e.g.
[from the Debian APT repositories](https://packages.debian.org/stable/libmojolicious-perl)

Install the script `telegram-bot-send-to` somewhere in your `$PATH`,
e.g. in `/usr/local/bin/` and make sure it's executable. (You might
need to tweak the shebang line if your `perl` binary is installed
somewhere else than at `/usr/bin/perl`.)

Create the file `/etc/telegram-bot-send-to.token`.

### Additional Steps for Usage with Xymon

Additionally install the script `xymon-telegram-bot-send-to` somewhere
in your `$PATH`, e.g. in `/usr/local/bin/` and make sure it's
executable.

Usage
-----

### Synopsis

```sh
echo 'Hi, this is a test message sent from the commandline' | telegram-bot-send-to your-chat-id-here
```

So you just need to give the recipient's Chat ID as parameter and give
the message on STDIN. Stupidly simple and very unixish. :-)

### Usage with Xymon

Edit `/etc/xymon/alerts.cfg` and use it like this:

```
HOST=www.example.com SERVICES=conn,ssh,http
	SCRIPT /usr/local/bin/xymon-telegram-bot-send-to your-chat-id-here DURATION>15 RECOVERED FORMAT=TEXT
```

You might also want to use macros to avoid repetition:

```
$TGSEND=/usr/local/bin/xymon-telegram-bot-send-to
$ME=your-chat-id-here

HOST=www.example.com SERVICES=conn,ssh,http
	SCRIPT $TGSEND $ME DURATION>15 RECOVERED FORMAT=TEXT

HOST=ftp.example.com SERVICES=conn,ssh,ftp
	SCRIPT $TGSEND $ME DURATION>60 RECOVERED FORMAT=TEXT

HOST=mail.example.com SERVICES=conn,ssh,imap,pop3
	SCRIPT $TGSEND $ME DURATION>15 RECOVERED FORMAT=TEXT
```

etc.

FAQ
---

### I always get "Bad Request" when trying to use the bot.

You haven't (or the recipient hasn't) started a chat with your bot
yet. This seems necessary (probably to avoid spamming with bots) to
make your Telegram bot being able to write to the recipient.

Copyright and License
---------------------

Copyright © 2019  Axel Beckert <abe@debian.org>

This program is free software: you can redistribute it and/or modify
it under the terms of the
[GNU General Public License](https://www.gnu.org/licenses/) as
published by the Free Software Foundation, either version 3 of the
License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but
WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
General Public License for more details.

Disclaimer
----------

The existence of these scripts and this git repository is by no means
an encouragement to use Telegram. It has been created to scratch the
author's own itch and has been shared in true open source and free
software spirit to allow others to scratch themselves if they happen
to have the same itch. ;-)
