---
layout: post
published: true
title: Selfhosted Obsidian File Syncing
truncated_preview: true
---

For anyone who knows me, I have had an on-and-off struggle with settling on software for my second brain, but have finally settled on [Obsidian](https://obsidian.md/), which is a fantastic software for developing your own personal knowledge management system. It offers a variety of wiki-esque tools that allow you to link your thinking, and the files that are made aren't locked into a file format that isn't viewable by a text editor. I wanted something that I could use on the go, that was easily readable, and in the event of the software going EOL, I could at least rely on the contents being intact and importable into a different kind of software. Most importantly, I had total control over the files and didn't need to worry about data breaches from the company that I entrusted -- all of my files were in my own hands, and I could treat them as securely as I wanted to. 

# Where the problem started
So while the features of Obsidian lined up with exactly what was needed, I did stumble into a big problem one thing that I missed from Notion - file syncing. Natively, Obsidian offers a feature called Obsidian Sync which allows users to pay a monthly subscription for the ability to store notes in their private cloud service, and sync notes between their devices, but I didn't want to trust that, or pay for it. This forced me to into the DIY route of self-hosting my own file syncing service. For this to work, I had a few requirements:
- Storage had to be deployed within my own private network.
- Some form of authentication needed to be present on the storage end. 
- Syncing had to function between a Windows PC and a mobile iOS device.
- Plugin data needed to be synced correctly

Thankfully for me, the Obsidian community was way ahead of me, and thanks to the community plugins' section, I was able to narrow down a few candidates for file syncing:

- [obsidian-livesync](https://github.com/vrtmrz/obsidian-livesync)
- obsidian-git (Deprecated)
- [remotely-save](https://github.com/remotely-save/remotely-save)

I tried out Obsidian Live-Sync, and it was a *phenomenal* experience, just like Notion's live updating. Spinning up the docker container was easy enough, and the settings were easy to plug in. It also had a built-in encryption on the database, which was cool. The biggest issue, however, came to how the plugins were synced. At the time, it simply did not sync plugin data, which was a dealbreaker for me. 

I moved on to Obsidian Git, but quickly moved on from that, as it seemed that getting it to function on iOS required a lot of hoops and hurdles to jump over.

The last stop for me was Remotely Save, which offered almost everything I needed. I tried out a temporary instance which required some manual setup, but I got it to work. It wasn't as slick as Obsidian Live-Sync, but it got the job done, and it managed to sync plugin data - perfect! There were a few different ways I could host storage:

- WebDAV
- DropBox
- OneNote
-  S3 Bucket

I ended up opting for WebDAV, but I ran into one problem. Every WebDAV container I threw at this plugin for setting up seemed to error out. According to some Github issues regarding WebDAV and this plugin, there might have been an issue with CORS being properly implemented in other containers. This plugin was originally tested using [WsgiDAV](https://github.com/mar10/wsgidav), which had CORS set up by default. So, I decided to construct my own Docker container using it.

The solution to my problem

So, because I couldn't find any WebDAV containers to deploy that worked at the time, I got fed up enough to make my own Docker image and publish it to Github.

```dockerfile
FROM python:3

RUN mkdir -p /app
WORKDIR /app

RUN pip install wsgidav cheroot lxml python-pam
RUN mkdir -p /var/dav

CMD wsgidav -c config.yaml

EXPOSE 8080
```

Then using a configured `config.yaml` file, I built the image and uploaded it to [DockerHub](https://hub.docker.com/r/whitekr0w/wsgidav) for others to use. 

Spin up the container, do domain name configurations, punch in the settings, and there we go, a fully functional file sync that works on every single device I have for free. 

# Final remarks
This was an interesting uphill battle. Probably unnecessary, but it got me to learn the basics of Docker image building, some Javascript troubleshooting, and it forced a public contribution out of me, so it all worked out. I'm happy with what I published, and it works flawlessly for my needs.
