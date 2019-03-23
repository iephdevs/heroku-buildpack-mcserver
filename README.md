# Heroku Minecraft Buildpack

This is a [Heroku Buildpack](https://devcenter.heroku.com/articles/buildpacks)
for running a Minecraft Spigot (1.8 - 1.13) server in a [dyno](https://devcenter.heroku.com/articles/dynos).

[![Deploy to Heroku](https://www.herokucdn.com/deploy/button.png)](https://heroku.com/deploy)

## Usage

Create a [free ngrok account](https://ngrok.com/) and copy your Auth token. Then create a new Git project with a `eula.txt` file:

```sh-session
$ echo 'eula=true' > eula.txt
$ git init
$ git add eula.txt
$ git commit -m "first commit"
```

If preferred, install the [Heroku toolbelt](https://toolbelt.heroku.com/).
Create a Heroku app, set your ngrok token, and push:

```sh-session
$ heroku create
$ heroku buildpacks:add heroku/jvm
$ heroku buildpacks:add jkutner/minecraft
$ heroku config:set NGROK_API_TOKEN="xxxxx"
$ git push heroku master
```

Finally, open the app:

```sh-session
$ heroku open
```

This will display the ngrok logs, which will contain the name of the server
(really it's a proxy, but whatever):

```
Server available at: 0.tcp.ngrok.io:17003
```

Copy the `0.tcp.ngrok.io:17003` part, and paste it into your local Minecraft app
as the server name.



**Note** 
To keep the server online, you can also create a free [Uptime Robot Account](https://uptimerobot.com). Aditional to this, you can set up PointDNS and create a srv record with the following name:
```
_minecraft._tcp.yoururl
```
And the following info:
```
SRV 0 5 yourport 0.tcp.ngrok.io.
```

## Syncing to S3

The Heroku filesystem is [ephemeral](https://devcenter.heroku.com/articles/dynos#ephemeral-filesystem),
which means files written to the file system will be destroyed when the server is restarted.

Minecraft keeps all of the data for the server in flat files on the file system.
Thus, if you want to keep you world, you'll need to sync it to S3.

First, create an [AWS account](https://aws.amazon.com/) and an S3 bucket. Then configure the bucket
and your AWS keys like this:

```
$ heroku config:set AWS_BUCKET=your-bucket-name
$ heroku config:set AWS_ACCESS_KEY=xxx
$ heroku config:set AWS_SECRET_KEY=xxx
```

The buildpack will sync your world to the bucket every 60 seconds, but this is configurable by setting the `AWS_SYNC_INTERVAL` config var.

## Connecting to the server console

The Minecraft server runs inside a `screen` session. You can use [Heroku Exec](https://devcenter.heroku.com/articles/heroku-exec) to connect to your server console.

Once you have Heroku Exec installed, you can connect to the console using 

```
$ heroku ps:exec
Establishing credentials... done
Connecting to web.1 on ⬢ herokuminecraft...
$ screen -r minecraft
```

**WARNING** You are now connected to the Minecraft server. Use `Ctrl-A Ctrl-D` to exit the screen session. 
(If you hit `Ctrl-C` while in the session, you'll terminate the Minecraft server.)

## Customizing

### Minecraft

The server is able to be configured through the many files such as permissions, and ops. The setup is extremely important, as it cannot be changed. You can also add plugins to the plugins folder, and add any other files you'd like such as spigot.yml, or a world. The website can also be configured from opt/index.rhtml 

**Additional Notes** The server version/software can be changed from bin/compile, and replace ```minecraft_url="https://cdn.getbukkit.org/spigot/spigot-1.8-R0.1-SNAPSHOT-latest.jar"```. Additionally, the server already contains EssentialsX and ViaVersion (For Version Support).
