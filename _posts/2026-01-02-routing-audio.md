---
layout: default
author: Fulton Byrne
title: Routing Audio with Pipewire + Helvum
---

Working on a distributed team it is key to make time to have fun together. Operating together in "non-work" mode teaches
you a lot about the other people that you work with and can make the work you do together much more seamless. I am always on
the lookout for good games to play and was very excited to try out Jackbox Games' latest release: [Hearsay](https://www.jackboxgames.com/games/hear-say).

In Hearsay, all players receive the same prompt each round and have to record an audio response to the prompt with their phone. Prompts
such as your best dolphin impression, what sound does a chicken make, and other pressing questions. Unfortunately, all the audio plays out
on the _host's_ machine and therefore the rest of the players have a _sub-optimal_ experience of everyone's dolphin chirps.

Could I route the audio from the game into a screenshare like Google Meet or Zoom? Definitely!

I use archlinux at home with [Pipewire](https://pipewire.org/) for managing audio and video output/input connections. I use
[Helvum](https://github.com/relulz/helvum) as a graphical manager ontop of Pipewire to simplify management.

Opening Helvum, I can see I have several output nodes on the left, and several input nodes with outputs on the right. The Firefox
nodes are very easy to spot and identify which tabs they correspond to. I can easily find an audio input for my Google Meet. The
Hearsay node is more difficult to identify, but it was easy to guess that the "Vulkan" node likely came from a game.

Now that the nodes I want to connect are identified I drag the left and right stereo channels from the game into the mono input
of the Google Meet tab. et voila! The game audio _and_ my microphone audio are clearly audible in the meeting.

![Patching audio from the game to the meeting](/assets/2026-01-02-pipewire-helvum/patching_audio.png)

I really enjoy the design behind Pipewire as it eliminates a minor annoyance (sharing desktop audio on meetings), but also opens
unlimited possibilities (i.e. patching audio and video through filters). I also really love the facsimile of the patchbay Helvum
provides as this connects me to a broader and well trod creativity space.
