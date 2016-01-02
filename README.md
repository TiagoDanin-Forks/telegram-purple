Telegram-Purple Unofficial (Beta)
=================================

Telegram-purple is a Libpurple protocol plugin that adds support for the Telegram messenger.

I keep getting many questions about this plugin in my E-mail, so I've created a
[telegram group chat](https://goo.gl/bhmM7N) for
telegram-purple related discussions or questions.

Installation
------------

If you are just interested in using the plugin you probably want to use one of the following binary distributions. Please note that I do not control most of those package sources and the version may lag behind.

If your plattform is not supported or you want to contribute by testing or development, scroll down to "Building form Source".


#### OSX (Adium)

1. Download and execute the [Telegram-Adium bundle] (https://github.com/majn/telegram-purple/releases/download/v1.2.3/telegram-adium-1.2.3.zip)
2. Restart Adium

#### Windows

Eion Robb provides a binary for Windows users:

1. Download and execute the setup from http://eion.robbmob.com/telegram/
2. Restart Pidgin

#### Fedora (22, 23)

The package is available in the Fedora 22 and 23 testing repositories:

     dnf config-manager --set-enabled updates-testing
     dnf install purple-telegram


#### Ubuntu/Mint PPA

Webupd8 provides PPAs for installation, see the [article](http://www.webupd8.org/2014/11/add-telegram-support-to-pidgin-with.html) for additional instructions:

    sudo add-apt-repository ppa:nilarimogard/webupd8
    sudo apt-get update
    sudo apt-get install telegram-purple


#### Arch Linux (AUR)

https://aur.archlinux.org/packages/telegram-purple/


Building From Source
--------------------

Below, you will find the instructions for how to build the libpurple protocol plugin from source.

#### 1. Clone

This repository has submodules, so you need to clone recursively.


        git clone --recursive https://github.com/majn/telegram-purple
        cd telegram-purple


#### 2. Fetch all dependencies

##### Fedora

        sudo dnf install gcc gettext libgcrypt-devel libwebp-devel libpurple-devel zlib-devel


##### Debian / Ubuntu

We are working on a Debian package! Please first check if it's already available to you: `sudo apt-get install telegram-purple`

If the above works, then you should stop here: It is now installed.

If the above fails: Don't worry, just continue building it by yourself. Next you need to install these dependencies:

        sudo apt-get install libgcrypt20-dev libpurple-dev libwebp-dev

##### OpenSUSE

        sudo zypper install gcc glib glib-devel libpurple libpurple-devel zlib-devel libwebp-devel

And the development files for gcrypt, probably `gcrypt-devel` or something.


#### 3. Compile and install

If libwebp is not available, you can disable sticker support by calling ./configure --disable-libweb instead.
Please note that this is usually not necessary.

        ./configure
        make
        sudo make install


Pulling Updates
---------------

This repository contains submodules, and a simple pull just won't be enough to update all the submodules’ files. Pull new changes with:


        git pull
        git submodule sync # just in case the configuration has changed 
        git submodule update --recursive


Usage
-----

#### First Login

The username is your current phone number, including your full country prefix. For Germany, this would be '+49', resulting in a user name like '+49151123456'. Telegram will verify your phone number by sending you a code via sms. You will be prompted for this code, once that happens.

#### Buddy List 

Like in the official Telegram apps, the displayed buddy list consists of all active conversations.

##### Foreign Users

Foreign users, like for example people you only know through a group chat but never had any conversation with, will not be part of your buddy list by default. You can add those users to the buddy list by clicking on "Add contact..." in the users context menu.

#### Using secret chats

You can use Telegram secret chats with this plugin, they will show up as a new buddy with a '!' in front of the buddy name.

One caveat of secret chats in Telegram is that they can only have one endpoint, this is a limitation of the protocol. This means that if you create a secret chat in Pidgin you will not be able to use that chat on your phone. You will be asked whether to accept each secret chat, so you can always choose to accept the chat on a different device if you want. You can set a default behavior for dealing with secret chats (Accept or Decline) in the account settings, if you don't want that prompt to appear every time.

Self destructive messages will be ignored, since I don't know any way to delete them from the conversation and the history.

##### Confirming the key authenticity

Click on the buddy in the buddy list and click on "Show Info" to visualize the key fingerprint.  

##### Initiate secret chats

To initiate a secret chat from Pidgin, click on a Buddy in the Buddy List and hit "Start Secret Chat"

##### Deleting secret chats

If you delete a secret chat from the buddy list, it will be terminated and no longer be usable.


#### Unicode Emojis for Pidgin

The Telegram phone applications for iOS and Android make use of standardized Unicode smileys (called [Emojis](https://en.wikipedia.org/wiki/Emoji)). Pidgin
does not display those smileys natively, but you can install a custom smiley theme like (https://github.com/stv0g/unicode-emoji) or (https://github.com/VxJasonxV/emoji-for-pidgin) and activate it under Settings > Themes > Smiley Theme.


Building the Adium Plugin
-------------------------

Compiling with XCode is a little bit problematic, since it requires you to compile Adium first to get the necessary framework files. My advice is to just use the [prebuilt bundle](https://github.com/majn/telegram-purple/releases), but if you really want to do it, follow these steps:

1. Get the Adium source, compile it with XCode and copy the build output into telegram-adium/Frameworks/Adium. It should contain at least Adium.framework, AdiumLibpurple.framework and AIUitilies.framework
2. Open the Adium source code, go to ./Frameworks and copy libglib.framework and libpurple.framework into telegram-adium/Frameworks/Adium
3. Build the tgl submodule and delete libtgl.so from libs/ (it should only contain libtgl.a)
4. Install libwebp, libgcrypt and gnupg with homebrew:

    brew install webp
    brew install libgcrypt libgpg-error

5. If you already downloaded libwebp/libgcrypt in previous builds make sure that the binaries are up-to-date

    brew update
    brew upgrade webp libgcrypt

6. Installwith homebrew and move it into the appropriate directory so that XCode can find them. Note that the versions might differ, use the one that is 

    mkdir -p ./telegram-adium/Frameworks/Adium
    cp /usr/local/Cellar/webp/0.4.3/lib/libwebp.a ./telegram-adium/Frameworks
    cp /usr/local/Cellar/libgcrypt/1.6.4/lib/libgcrypt.20.dylib ./telegram-adium/Frameworks/Adium
    cp /usr/local/Cellar/libgpg-error/1.20_1/lib/libgpg-error.0.dylib ./telegram-adium/Frameworks/Adium

7. Update the paths in the dylibs, to assure that the resulting binary will load them form within the bundle.

    cd ./telegram-adium/Frameworks/Adium
    install_name_tool -id "@loader_path/../Resources/libgcrypt.20.dylib" ./libgcrypt.20.dylib
    install_name_tool -id "@loader_path/../Resources/libgpg-error.0.dylib" ./libgpg-error.0.dylib
    install_name_tool -change "/usr/local/lib/libgpg-error.0.dylib" "@loader_path/../Resources/libgpg-error.0.dylib" ./libgcrypt.20.dylib

7. Build the XCode-Project and execute the created bundle


Building the Debian Package
---------------------------

If you just need a `.deb`, simply do:

    git checkout debian-master
    fakeroot ./debian/rules binary

And you're done! The `.deb` is in the directory at which you started.
To show some info about it, try this:

    dpkg --info telegram-purple_*.deb

`debian-master` always points to a version that was submitted to Debian. (Note that this doesn't exist yet, as we haven't released to Debian yet.)
`debian-develop` is the candidate for the next submission.

#### Debian Maintainers ####

If you're a maintainer (if you're not sure, then you aren't a
maintainer), you need to produce a lot more files than that.

Here's how you can generate a `.orig.tar.gz`:

    debian/genorigtar.sh

This command requires the original tar to exist (and will fail otherwise,
although the error message will be misleading) will build all further files,
specifically `.debian.tar.xz`,`.dsc`, `.deb`, and `.changes`:

    dpkg-buildpackage

And that already covers the official part of the work-flow. Of course,
you can call small parts of the build process directly, in order to avoid
overhead like rebuilding. For example, if you only need the `.debian.tar.xz`
and `.dsc` files, do this:

    debian/genorigtar.sh
    ( cd .. && dpkg-source -b telegram-purple )

Note that the parenthesis are important.


1.2.3
-----

- Build: Allow compilation on Windows #52 Thanks Eion!
- Build: Drop dependency on LodePNG, Thanks Ben!
- Build: Gettext is now optional

- Fix issue that prevented to send messages to deleted users in certain cases (#174)
- Fix own user being added to the buddy list in certain cases
- Fix that read recipes of own messages are being displayed (#139)
- Fix encoding inconsistencies with Unicode characters (#177)
- Fix auto-joining for chats (#179)
- Fix client not reconnecting anymore under certain circumstances (#173)
- Fix crash on compat-verification (PullRequest #183)

- Remove pointless "create chat" confirmation dialogue
- Improve logging messages
- Always send read recipes when the user is typing or sending a message
- Improve translation and user messages (#139)
- Use native password prompts (Adium)


Discussion / Help
-----------------

#### Custom pubkeys

As we want to avoid OpenSSL, it has become necessary to replace the PEM file format. This means that if you use a custom pubkey (which you really REALLY shouldn't be doing), you have to adapt, sorry.

We no longer ship `tg-server.pub` (old format), but instead `tg-server.tlgpub` (new format). If you have a `.pub` and want to continue using telegram-purple, please use this (hopefully highly portable) tool: [pem2bignum](https://github.com/BenWiederhake/pem2bignum)

You can also write your own conversion tool if you prefer. The format is really simple:

1. `e`, the public exponent, encoded as big endian 32 bit fixed length (e.g. `0x00 01 00 01` for 65537)
2. `n_len`, the length of `n` in bytes, encoded as big endian 32 bit fixed length (e.g. `0x00 00 01 00` for a 2048-bit = 256-byte key)
3. `n_raw`, the raw modulus, encoded as big endian, using the previously indicated length (e.g. `0xC1 50 02 3E [248 bytes omitted] 21 79 25 1F` in the case of telegram's public RSA key.)

If you are interested in developing a non-OpenSSL-licensed converter, look into [insane-triangle-banana](https://github.com/BenWiederhake/insane-triangle-banana).


FAQ
---

- I receive pictures in a chat, but they aren't showing up
  * A: Make sure that you don't have a plugin like "Conversation Colors" that strips HTML from messages and removes the pictures.

#### Group chat

Telegram group chat for telegram-purple or libtgl related discussions or questions:

    - https://goo.gl/bhmM7N


Submitting Bug Reports
----------------------

**IMPORTANT**: if you report bugs PLEASE make sure to always **include as much information as possible**. This should always include **at least the telegram-purple version and (if possible) commit**, where you got telegram-purple from (Source build, package repository, etc.), the Pidgin version (if you use a different messenger please state that too!) and your OS Version.

If you describe some issue please be as precise as possible. Descriptions like "XY doesn't work" will not help me. Describe what you are doing what kind of issue you are experiencing: "If I click on X, Y happens, but instead I would expect Z to happen".

For error reports please include the application logs. To get Pidgin to print a log, [start it from command line, specifying the -d option](https://developer.pidgin.im/wiki/GetABacktrace#TheEasyWay). **ATTENTION**: This log will contain personal information like your phone number, message content or contact or chat names. If you plan on uploading it somewhere public mask those entries in the log.

Bug reports regarding crashes should include a backtrace if possible, there is extended documentation available on [how to get a backtrace for crashes](https://developer.pidgin.im/wiki/GetABacktrace)

Empathy / libtelepathy
----------------------

Empathy doesn't natively support libpurple plugins since its based on libtelepathy, but there is a compatibillity layer called telepathy-haze that can be used to execute libpurple
plugins. This means that you can basically run this plugin thanks to telepathy-haze but you will usually get less features and worse usabillity compared to real libpurple clients. If you use Empathy (or anything else based on libtelepathy) I recommend [telepathy-morse](https://projects.kde.org/projects/playground/network/telepathy/telepathy-morse/repository) which is a connection manager written specifically for your messenger.

Authors
-------

Telegram-Purple was written by:

    - Matthias Jentsch <mtthsjntsch@gmail.com>
    - Vitaly Valtman
    - Ben Wiederhake
    - Christopher Althaus <althaus.christopher@gmail.com>


Acknowledgements
----------------

This software is based on the library [Libtgl](https://github.com/vysheng/tgl), which was written by Vitaly Valtman <mail@vysheng.ru> and others, see (https://github.com/vysheng/tgl/)
