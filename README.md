Oust
====
A love letter to [Johann Sebastian Joust](http://www.jsjoust.com/), designed for playing outdoors at festivals in the dark. All night long (all night).

![Rpi Oust Setup](sandwich_box.jpg)

A long time ago on a boat far far away
--------------------------------------
When organising [Electromagnetic Wave](https://www.emfcamp.org/wave) in early 2013, we asked some of the [Wild Rumpus](http://thewildrumpus.co.uk/) organisers to run evening games on the boat. A very beta version of Joust was run on the deck to rave reviews.

Fast forward to 2015. We've taken Joust (in both beta and final versions) to [OHM](https://en.wikipedia.org/wiki/Observe._Hack._Make.) in 2013, [EMF](https://www.emfcamp.org) in 2014, and [CCC Camp](https://events.ccc.de/camp/2015/wiki/Main_Page) in 2015. Every time the game runs all night long with almost no supervision.

By this point the game has developed a bit of a following, and people are actively seeking us out to play. But there are problems. Joust requires a laptop to run (meaning we need to be near a power supply, and can't leave it alone), it needs speakers for the music (which we never have), and people have trouble understanding the gameplay without the announcements and cues.

I went to CCC prepared. We had 11 move controllers, three Raspberry Pi's, 46 Ah of portable batteries, and more bluetooth adapters than there are frequency-hopping channels.

Oust is the result. It runs on a Raspberry Pi 2, can run up to 8 controllers (maybe more), and is specifically designed for environments so loud that sign language is required.

It was playtested and tweaked extensively at CCC, at one point with a circle of approximately 150 people playing & watching. The longest continuous game I recorded was 9 hours long.

How to Oust
-----------
1. Turn the Raspberry Pi on.
2. Turn the controllers on.
3. Play!

No really how does one Oust
---------------------------
The game is designed to run on a Raspberry Pi hooked up to a honking great battery pack. There is no interface other than the controllers themselves.

If you were starting out from scratch, this is what it would look like:

1. Install Oust on the Raspberry Pi, including the nasty bluetooth hacks (See below).
2. Plug the Pi into your battery pack.
3. Connect each controller to the Pi in turn using a MiniUSB cable. When the controller goes white it has been paired.
4. Turn on as many controllers as you want to play with.
5. Each player presses the trigger, and the game begins.
6. Optionally put the Pi & battery in a plastic sandwich box.

But what does the game look like?
---------------------------------
1. All the controllers are dark. As the players press the squishy trigger their controller lights up white.
2. When all the controllers are white (or someone presses Triangle) the controllers will vibrate, then flash red/yellow/green as a "get ready" signal.
3. Every controller turns a different colour and the game has begun.
4. The aim of the game is to force all the other players to move their controllers too fast, either by hitting the controller, making them flinch, or the other player doing something stupid.
5. If your controller is going too fast it'll flicker as a warning.
6. If you are knocked out, your controller goes dark and vibrates.
7. The last player standing has their controller flash a beautiful rainbow sequence, and all controllers vibrate to indicate the end of the game.
8. The game resets, people hand their controllers to other people to play. GOTO 1.

Amazing Features
----------------
* Instant setup
* Easy pairing
* Add/remove controllers to the game on the fly
* Battery checking button (Press circle)
* Secret goddamn-it-start-the-game button (Press Triangle)
* "Ready, Steady, Go" start sequence to get players attention
* Going-too-fast warning
* Support for as many controllers as bluetooth interference will allow
* Mostly doesn't crash, but when it does it's a kernel panic and not my fault

How to install Oust
-------------------
1. Get a Raspberry Pi 2
2. Install Raspbian on the SD card
3. Checkout PSMoveAPI to the pi user's home directory (https://github.com/thp/psmoveapi)
4. Build PSMoveAPI standalone (5.b here https://github.com/thp/psmoveapi/blob/master/README.ubuntu)
5. Copy [oust.py](oust.py) to /home/pi/psmoveapi/build/
6. apt-get install supervisor
7. Replace /etc/supervisor with the contents of (supervisor)[supervisor]
8. Copy [scan_enable.sh](scan_enable.sh) to /home/pi/
9. Reboot the pi and pair your controllers!

Things You Should Know
----------------------
* Building PSMoveAPI is horrid. I'll publish debian packages soon.
* The Raspberry Pi has a number of problems. The main one is that the current kernel (or bluez) shipped in Raspbian is broken - as soon as one controller is paired and has an active data link, the bluetooth adapter receives a command telling it to disable connecting to any other devices. Fortunately I was camping with [ms7821](https://twitter.com/marksteward) and [gavan](https://twitter.com/gavanfantom) who are terrifying bluetooth experts and spent a happy afternoon with HCI packet dumps debugging this. [scan_enable.sh](scan_enable.sh) is the hilarious solution they came up with - once a second it pokes all the bluetooth devices and tells them to accept connections again.
* PSMoveAPI has a number of problems. The main one is that it is terrible. On my Mac, plugging and unplugging USB devices can cause the library to crash spectacularly. On the Raspberry Pi it causes a kernel panic. I suggest rebooting and pairing again.
* Bluetooth is limited to 7 devices per adapter. We've had Oust running with 8 devices, but pairing 9 causes unacceptable lag across all controllers. Bluetooth is a frequency-hopping protocol with 42 channels, and having that many active devices causes collisions. We are fairly certain we can limit the bluetooth channels in use per adapter to solve this, but that is future research.

Future Things I May Do
----------------------
* Debian packages of PSMoveAPI for the pi
* A script that does all the install things
* Research using more than 8 controllers