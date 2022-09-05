Introduction
------------

orpheusbetter is a script which automatically transcodes and uploads these
files to Orpheus.

The following command will scan through every FLAC you have ever
downloaded or uploaded (if it is in , determine which formats are needed, transcode
the FLAC to each needed format, and upload each format to Orpheus -- automatically.

    $ orpheusbetter

Installation
------------

You're going to need to install a few dependencies before using
orpheusbetter.

    $ sudo apt-get update && sudo apt-get upgrade

First and foremost, you will need Python 3.6 or newer. NOTE: this version
has been ported to Python 3.x and will not run under Python 2.x.

    $ sudo apt install python3

Once you've got Python installed, 
```
   $ sudo apt-get install python3-pip
   $ sudo apt-get install git
   $ sudo apt install aptitude
```
	$ git clone https://github.com/daygumon/orpheusbetter-crawler
	$ cd orpheusbetter-crawler/

you will need a few modules: mechanize,
mutagen, and requests. Try this:

    $ pip3 install -r requirements.txt

	
If you are on a seedbox, or a system without root priviliages, try this:


    $ pip3 install --user -r requirements.txt


Furthermore, you need several external programs: mktorrent 1.1+, flac,
lame, and sox. The method of installing these programs varies
depending on your operating system, but if you're using something like
Ubuntu you can do this:

    # sudo aptitude install mktorrent flac lame sox
	

If you are on a seedbox and you lack the privilages to install packages,
you could contact your provider to have these packages installed.

At this point you may execute the following command:

    $ ./orpheusbetter

And you will receive a notification stating that you should edit the
configuration file \~/.orpheusbetter/config (if you're lucky).

Configuration
-------------

You've made it far! Congratulations. Open up the file
\~/.orpheusbetter/config in a text editor. 

$ cd
$ cd ./orpheusbetter
$ vim config

You're going to see something
like this:

    [whatcd]
    username =
    password = 
    data_dir =
    output_dir =
    torrent_dir =
    formats = flac, v0, 320, v2
    media = sacd, soundboard, web, dvd, cd, dat, vinyl, blu-ray
    24bit_behaviour = 0
    tracker = https://home.opsfet.ch/
    api = https://orpheus.network
    mode = both
    source = OPS

To edit the config file with vim, after entering the file via `$ vim config`, press 'Esc' key, type `:i`, press 'Enter' key, make whatever edits you need, press 'Esc' key, type `:wq` , press 'Enter' key.

`username` and `password` are your Orpheus login credentials. 

`data_dir` is the directory where your downloads are stored. 

`output_dir` is the directory where your transcodes will be created. If
the value is blank, `data_dir` will be used. You can also specify
per format values such as `output_dir_320` or `output_dir_v0`.

	Output directory can be bitrate-specific:

	output_dir_320 = /mnt/h/OPS-MP3_320
	output_dir_V0 = /mnt/h/OPS-MP3_V0
	output_dir_V2 = /mnt/h/OPS-MP3_V2 -> V2 is planned to be depreciated/deleted and already does not generate BP so no point in using anymore

`torrent_dir` is the directory where you want the newly created by orpheusbetter torrent files to go in (e.g.,
your watch directory). Same per format settings as output_dir apply.

	data_dir = /mnt/c/FLAC -> For a C/ drive. The data directory is where the FLACs are at.
	output_dir = /mnt/d/OPS-MP3
	torrent_dir = /mnt/d/OPS-torrents
	
If you want to mount a networked drive, from a NAS or another server, enter:

	$ sudo mkdir /mnt/d
	$ sudo mount -t drvfs D: /mnt/d

`formats` is a list of formats that you'd like to support
(so if you don't want to upload V2, just remove it from this list).

`media` is a list of lossless media types you want to consider for
transcoding. The default value is all What.CD lossless formats, but if
you want to transcode only CD and vinyl media, for example, you would
set this to 'cd, vinyl'.

`24bit_behaviour` defines what happens when the program encounters a FLAC 
that it thinks is 24bits. If it is set to '2', every FLAC that has a bits-
per-sample property of 24 will be silently re-categorized. If it set to '1',
a prompt will appear. The default is '0' which ignores these occurrences.

`tracker` is the base url to use in the torrent files.

`api` is the base url to use for api requests.

`mode` is which list of torrents to search for candidates. One of:

 - `snatched` - Your snatched torrents.
 - `uploaded` - Your uploaded torrents.
 - `both`     - Your uploaded and snatched torrents.
 - `seeding`  - Better.php for your seeding torrents.
 - `all`      - All transcode sources above.
 - `none`     - Disable scraping.

 `source` is the source flag to put on created torrents, leave blank if you're
 running mktorrent 1.0.




You should end up with something like this:

    [whatcd]
    username = RequestBunny
    password = clapton
    data_dir = /srv/downloads
    output_dir =
    torrent_dir = /srv/torrents
    formats = flac, v0, 320
    media = cd, vinyl, web
    24bit_behaviour = 0
    tracker = https://home.opsfet.ch/
    api = https://orpheus.network
    mode = both
    source = OPS

Alright! Now you're ready to use orpheusbetter.

Usage
-----

```
usage: orpheusbetter [-h] [-s] [-j THREADS] [--config CONFIG] [--cache CACHE]
                     [-U] [-E] [--version] [-m MODE] [-S] [-t TOTP]
                     [-o SOURCE]
                     [release_urls [release_urls ...]]

positional arguments:
  release_urls          the URL where the release is located (default: None)

optional arguments:
  -h, --help            show this help message and exit
  -s, --single          only add one format per release (useful for getting
                        unique groups) (default: False)
  -j THREADS, --threads THREADS
                        number of threads to use when transcoding (default: 7)
  --config CONFIG       the location of the configuration file (default:
                        ~/.orpheusbetter/config)
  --cache CACHE         the location of the cache (default:
                        ~/.orpheusbetter/cache)
  -U, --no-upload       don't upload new torrents (in case you want to do it
                        manually) (default: False)
  -E, --no-24bit-edit   don't try to edit 24-bit torrents mistakenly labeled
                        as 16-bit (default: False)
  --version             show program's version number and exit
  -m MODE, --mode MODE  mode to search for transcode candidates; snatched,
                        uploaded, both, seeding, or all (default: None)
  -S, --skip            treats a torrent as already processed (default: False)
  -t TOTP, --totp TOTP  time based one time password for 2FA (default: None)
  -o SOURCE, --source SOURCE
                        the value to put in the source flag in created
                        torrents (default: None)
```

Examples
--------

Running orpheusbetter from the orpheusbetter-crawler directory will start the script to transcode and upload every snatch you've ever downloaded along with all
your uploads (this may take a while):

    $ cd /orpheusbetter-crawler/
    $ ./orpheusbetter

If you want to only transcode specific torrents, copy the permalink from the site -> [PL], and in the orpheusbetter-crawler dirctory, create a new file using:

	$ touch list.txt
	
Paste each permalink on its own line in this list.txt file. Do not leave any blank lines in the file. Example list.txt file:

	https://orpheus.network/torrents.php?id=725581&torrentid=1589696#torrent1589696
	https://orpheus.network/torrents.php?id=13596&torrentid=1925158#torrent1925158
	https://orpheus.network/torrents.php?id=696123&torrentid=1533810#torrent1533810
	fix https://orpheus.network/torrents.php?id=696123&torrentid=1533810#torrent1533810

To transcode from the list, enter:

	$ ./orpheusbetter list

To transcode and upload a specific release (provided you have already downloaded the FLAC and it is located in your `data_dir`), copy the permalink from the site -> [PL], and add a backslash before the '&torrentid':

	$ ./orpheusbetter https://orpheus.network/torrents.php?id=725581\&torrentid=1589696#torrent1589696


And that's about it!

Note that if you specify a particular release(s), orpheusbetter will
ignore your configuration's media types and attempt to transcode the
releases you have specified regardless of their media type (so long as
they are lossless types).

    $ ./orpheusbetter -U https://orpheus.network/torrents.php?id=1000\&torrentid=1000000

Your first time running orpheusbetter might take a while, but after it has
successfully gone through and checked everything, it'll go faster any
consecutive runs due to its caching method
