# RUNES: How to go from 0 to 100% indexed in under 48 hours
### Inscribed on May 9, 2024

This guide will help you get a fully indexed Bitcoin and ord set up in minimal time. This borrows heavily from the excellent "Mac beginner's guide" by ETS in The Ordicord, with some heavy modifications on my part to improve the speed of indexing and get you up to date as soon as possible.

This guide assumes you have a Mac and an external SSD, which will be storing the bulk of your Bitcoin and ord indexes.

## Configure SSD

**Ensure your external SSD is formatted as APFS prior to continuing.** Note that formatting a drive erases all of its contents, so don't do this on a drive you are already using for other things. Or if you do, back them up first as they'll be deleted. 

**Rename your external SSD to SSD to make these future code entries easier.**

Create two folders on your SSD: One named `Bitcoin` and one named `ord`

## Download the latest ord index

This is technically optional, but **highly** recommended as it will save you a ton of time. Rather than your computer doing all the indexing, we're going to download a pre-built index and allow your computer to continue indexing from that point forward.

Visit the following page to download the latest index, choosing the latest version (as of this writing is 0.18): [https://ordstuff.info/indexes/](https://ordstuff.info/indexes/)

If you're only wanting to deal with Runes, choose the file that includes **without** to save time and space. If you plan to also inscribe on specific sats, then choose the **with** option.

You'll need to use a torrent app like TransmissionBT to download this file. After you download it, you'll need to unzip it, after which you'll be left with a file called something like `index-0.18-without-838791.redb`.

After unzipping, rename this file to `index.redb` to make it easier to identify.

**Decision time! You can either:**
1. Move this index.redb file into the `ord` directory on your SSD
2. Keep it on your Mac in `/Users/YourName/Library/Application Support/ord`. This is the better option, but does take up over 70GB of space.

## Install Homebrew

Homebrew is a package manager, and will basically help us install the Bitcoin software and allow it to run in the background. 

Open up Terminal (Applications > Utilities) and enter this command:

    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

You'll likely have to input your main Mac password during installation, and won't see any letters as you type. Hit `Enter` to continue.

**Be mindful of the "Next Steps" at the end of brew installation. You'll need to input the two commands it gives you into your Terminal so that Terminal understands the `brew` command that we use in subsequent steps.**

## Install Bitcoin

After Homebrew has finished installing, enter this command in Terminal:

`brew install bitcoin`

After installation, do not run Bitcoin yet! Before we run the Bitcoin software, we need to configure some things.

## Configure Bitcoin

By default, Bitcoin will create everything directly on your Mac, but we need to point it to your SSD instead. We're going to use Terminal to make a new bitcoin.conf file that Bitcoin will use by default.

Enter these commands in Terminal. You can copy and paste this entire thing in one go:

    mkdir ~/Library/Application\ Support/Bitcoin
    echo blocksdir=/Volumes/SSD/Bitcoin >> ~/Library/Application\ Support/Bitcoin/bitcoin.conf
    echo datadir=/Volumes/SSD/Bitcoin >> ~/Library/Application\ Support/Bitcoin/bitcoin.conf
    echo txindex=1 >> ~/Library/Application\ Support/Bitcoin/bitcoin.conf
    echo server=1 >> ~/Library/Application\ Support/Bitcoin/bitcoin.conf
    echo assumevalid=00000000000000000002a7c4c1e48d76c5a37902165a270156b7a8d72728a054 >> ~/Library/Application\ Support/Bitcoin/bitcoin.conf

## Start Bitcoin

Once your bitcoin.conf file has been created, you can start Bitcoin.

There are a couple different ways to do this:

### Option A: Run Bitcoin as a service
You can run Bitcoin as a service, meaning it will run in the background on your computer without any window open, and will automatically start back up if you ever restart your Mac.

In Terminal:
`brew services start bitcoin`

You'll want to monitor the outputs of the file, at least initially, to ensure it properly read your bitcoin.conf file and is saving things on your SSD. Take a look inside your Bitcoin folder in SSD/Bitcoin and verify that some new files have been created there, including new blocks and indexes folders.

If you ever want to stop the service, just run `brew services stop bitcoin`

### Option B: Run bitcoind
You can also run bitcoind, which I prefer (at least during the initial index) because you can better monitor its progress.

To start, simply type in Terminal:
`bitcoind`

You'll want to monitor the outputs of the file, at least initially, to ensure it properly read your bitcoin.conf file and is saving things on your SSD. Take a look inside your Bitcoin folder in SSD/Bitcoin and verify that some new files have been created there, including new blocks and indexes folders.

**Do not continue until you have verified that Bitcoin is saving to your external SSD.**

## Install ord

Now that Bitcoin is syncing, we are ready to install ord. Open up a new Terminal window `Cmd + T` and type:

`brew install ord`

This will unpack a bunch of things that the ord software depends on. Just let it run.

Once that's done, type this into Terminal to verify ord has been installed:

`ord --version`

Which should output something like `ord 0.18.1`

## Configure ord

Just like we made a bitcoin.conf file, we need to make an ord.yaml file which will act as a settings file for ord.

Enter these commands in your Terminal:
    mkdir ~/Library/Application\ Support/ord

    echo bitcoin_data_dir: /Volumes/SSD/Bitcoin >> ~/Library/Application\ Support/ord/ord.yaml
    echo chain: mainnet >> ~/Library/Application\ Support/ord/ord.yaml
    echo index_runes: true >> ~/Library/Application\ Support/ord/ord.yaml
    echo index_sats: false >> ~/Library/Application\ Support/ord/ord.yaml

If you opted to store the ord index.redb file on your SSD, also enter this command:

    echo data_dir: /Volumes/SSD/ord >> ~/Library/Application\ Support/ord/ord.yaml

## Run ord

Now that we've configured ord, we can start it. 

To start indexing ord, run this in Terminal:

`ord server`

If you don't see any errors, then you are basically done! Wait for ord to finish indexing.

**TIP:** Download and install a free app called Jiggler to keep your computer awake while Bitcoin and ord finish indexing. And during this time, keep your computer in a safe place because this will prevent it from locking!

## Next Steps

Once ord has finished indexing, you should be able to go to http://localhost/runes and see UNCOMMON•GOODS listed there. You won't see any other Runes there until after the halving!

Check out the section below for Signet instructions which are useful for testing things!

Below is a sample code to mint a Rune. Note that the "destination" flag is optional but great for moving your Runes to a wallet like [Leather](https://leather.io) and saving on fees!

    ord wallet mint --fee-rate 100 --rune UNCOMMONGOODS --destination bc1pdfmqcefzn3t28lzdvv8s5kv7t25pnq49t4sqtnkyujt5kytt4njqydkqup

## Some final tips

### If you ever need to stop ord or bitcoind

Just press `Ctrl + C` **one time** in your Terminal window, which will stop indexing cleanly. If you just exit Terminal, it's equivalent to force quitting the app, and ord will likely have to go through a length repair process the next time you try to run it.

### If you opted for Bitcoin to run as a service

These commands will be useful to you if you ever need to start or stop bitcoin.

    brew services stop bitcoin
    brew services start bitcoin
    brew services restart bitcoin

### Some antivirus software doesn't like this

You may need to whitelist the "Bitcoin" and "ord" folders in your antivirus to avoid any false positives. If your antivirus takes action on a file in these folders, it could corrupt your whole index and require you to start over.

### Need to update bitcoin or ord?

To update bitcoin, simply run `brew upgrade bitcoin`

To update ord, simply run `brew upgrade ord`

Note that in some cases, upgrading ord may require a full reindex. In that case, you may want to re-download a prebuilt index as mentioned near the beginning of this guide!

## Signet setup

You'll want to practice minting Runes to familiarize yourself with the process. Assuming you followed the guide above, you can use this code to append Signet settings to your existing bitcoin.conf file:

    echo \[signet] >> ~/Library/Application\ Support/Bitcoin/bitcoin.conf
    echo txindex=1 >> ~/Library/Application\ Support/Bitcoin/bitcoin.conf
    echo server=1 >> ~/Library/Application\ Support/Bitcoin/bitcoin.conf

Then to start Bitcoin in Signet mode and begin syncing, simply type:
`bitcoind -signet`

We're now going to create an alternate version of ord.yaml that will be used for Signet. To do that, press `CMD + T` to open a new Terminal tab and enter this code:

    mkdir ~/Library/Application\ Support/ord

    echo bitcoin_data_dir: /Volumes/SSD/Bitcoin >> ~/Library/Application\ Support/ord/signet.yaml
    echo chain: signet >> ~/Library/Application\ Support/ord/signet.yaml
    echo index_runes: true >> ~/Library/Application\ Support/ord/signet.yaml
    echo index_sats: false >> ~/Library/Application\ Support/ord/signet.yaml

Then to run ord in Signet mode, you'd type:

`ord --config ~/Library/Application\ Support/ord/signet.yaml server`

Signet is fairly small, so you shouldn't need to pre-download an index like you did for Mainnet. Just let it finish on its own!

## Testing in Signet

Input these commands in a new Terminal tab (`CMD + T`) to create your wallet:

`ord --config ~/Library/Application\ Support/ord/signet.yaml wallet create`

`ord --config ~/Library/Application\ Support/ord/signet.yaml wallet receive`

Find a Signet faucet to send some BTC to your address. Once you have received BTC, you can mint your first rune! Try this:

`ord --config ~/Library/Application\ Support/ord/signet.yaml wallet mint --fee-rate 1 --rune UNCOMMONGOODS`

From there you should be able to track its creation on mempool.space and once confirmed, it'll show up in your balances:

`ord --config ~/Library/Application\ Support/ord/signet.yaml wallet balance`