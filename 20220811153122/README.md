# Use Twitch chat with IRC

> As of Sept. 17, 2013, Twitch now requires that you log into IRC using
> an OAuth token instead of your plain text password or hash for
> additional security.

* Generate an `OAuth` token using [this](https://twitchapps.com/tmi/)
* Open up your IRC client

```text
/network add -nick YOUR_TWITCH_USERNAME twitch
/server add -auto -ssl -network twitch irc.chat.twitch.tv 6697 YOUR_OAUTH_TOKEN
/network add -autosendcmd "/quote CAP REQ :twitch.tv/membership" Twitch
/ignore jtv
/save
```

* Connect to Twitch IRC and join your favourite channel

```text
/connect twitch
/join #CHANNEL_NAME
```

    #twitch #irc
