# WELCOME TO NITISH'S HASS REPO! 
May you find what you seek.  Truly.

<br />

**HERE'S WHAT THIS README HAS TO OFFER:**
- [Introduction to HASS (home assistant)]()  
- [How to setup HASS](https://gitlab.com/nitishlobo/hass_config/master/README.md#software-setup-for-home-assistant-hass)  
- [Prepping your computer for git-crypt](https://gitlab.com/nitishlobo/hass_config/master/README.md#pre-software-setup-for-git-crypt)  
- [How to setup git-crypt in order to ensure certain files remain private](https://gitlab.com/nitishlobo/hass_config/master/README.md#software-setup-for-git-crypt)

<br />

## INTRODUCING HASS:
**What is HASS?**  
HASS stands for home assistant. It is a tool for home automation which involves a  
web UI and an app for monitoring and managing your home or better yet letting your home do it for you.  

Think IoT, think futuristic tech and you'll get the idea.  
And if you didn't get that mumbo jumbo, then the next best explanation is some sort of JARVIS (from Iron Man).

**Why should I care about HASS?**  
Allow me to build a case for HASS:
- Ever walked up to the steps of your home and fumbled in the dark for your keys while juggling a handful of grocery bags?  
What if instead your home knew it was you, switched its lights on and opened its door by itself for you?  
- Now imagine you continue walking both hands full with those heavy grocery bags, from room to room inside the home and  
lights automatically turned on in the rooms you entered and turned off in the room you left? Now, wouldn't that be helpful?

- Been in a rush and wanted those slow garage door and gates to pre-open before you hop into the car rather than be in the car tapping your fingers away?  
My friend, I believe the magic word you are looking for here is `done`.

- Ever left home in a rush and wondered if you left that stove on or the iron or anything else that could burn your home down?  
What about any other devices (like giant TV, heater, etc.) that 2x those power bills?  
What if instead your home knew that neither you nor your family/friends are in the home and just turned everything you wanted off, well, off?

- During your busy work day, a burgular jumps across your fence and decides to do some 'work' of his own.  
Your home is aware of this and takes a photo of the burgular, rings the police via a home AI (eg. google assistant), emails them this photo    
and then sends you a text message/email to let you know that bad things have happened and its been taken care of.

- Wanted to play a movie and wanted the lights to automatically dim to set the mood?

- But all this is only the tip of the iceberg, the possibilities are endless and just like the rest of life, its what you make out of it.

<br />

## SOFTWARE SETUP FOR HOME ASSISTANT (HASS):
1. Install dependencies if they are not already installed.  
      `sudo pacman install python3 python3-venv python3-pip`
    
2. Add an account for Home Assistant called homeassistant.  
   Since this account is only for running Home Assistant the extra argument of -rm is added to create a system account and create a home directory.  
   The arguments -G uucp adds the user to the dialout group (so that useraccount has control of RS-232 serial ports and devices connected to them).  
   Uucp/dialout group is required for using Z-Wave and Zigbee controllers.  
      `sudo useradd -rm homeassistant -G uucp`

3. Create a directory for the installation of Home Assistant and  
   change the owner to the homeassistant account.  
      `cd /srv`  
      `sudo mkdir homeassistant`  
      `sudo chown homeassistant:homeassistant homeassistant`

4. Create and change to a virtual environment for Home Assistant.  
   This will be done as the homeassistant account.  
      `sudo -u homeassistant -H -s`  
      `cd /srv/homeassistant`  
      `python3 -m venv .`  
      `source bin/activate`  

5. Once you have activated the virtual environment (notice the prompt change), install a required python package.  
      `python3 -m pip install wheel`

6. Now install homeassistant via pip  
      `pip3 install homeassistant`

7. Start Home Assistant for the first time.  
   Doing the below will complete the installation, create the .homeassistant configuration  
   directory in the /home/homeassistant directory and install any basic dependencies.  
      `cd /srv/homeassistant`  
      `hass`

7. You can now reach your installation on your Raspberry Pi/PC over the web interface on http://ipaddress:8123.  
   Change the 'ipaddress' part to be whatever your ip address is.

**SOURCE:**  
https://www.home-assistant.io/docs/installation/raspberry-pi/

8. Next you can setup HASS so that it auto-starts on laptop boot up.
   Most linux distro's use systemd to manage daemons.  
   Check this is the case with your distro by doing:  
      `ps -p 1 -o comm=`  
   If the return value is systemd, then continue.

9. Navigate command line to the config folder of this repo. Then execute:  
      `scp home-assistant\@homeassistant.service /etc/systemd/system/`  
   Note that you may need to sudo to do this command.  
   Note also that you need to do \@ and not just @.

10. Reload systemd to make the daemon aware of the new configuration  
      `sudo systemctl --system daemon-reload`

11. Have Home Assistant start automatically at boot, enable the service.  
      `sudo systemctl enable home-assistant@homeassistant`

12. To start Home Assistant now, use this command:  
      `sudo systemctl start home-assistant@homeassistant`

    Note: You can also substitute the 'start' above with 'stop' to stop Home Assistant,
    'restart' to restart Home Assistant, and ‘status’ to see a brief status report.

**ADDITIONAL INFO:**
- To disable the automatic start, use this command:  
      `sudo systemctl disable home-assistant@homeassistant`  
- To get Home Assistant’s logging output, simple use journalctl:  
      `sudo journalctl -f -u home-assistant@homeassistant`  
- Because the log can scroll quite quickly, you can select to view only the error lines:  
      `sudo journalctl -f -u home-assistant@homeassistant | grep -i 'error'`  
- When working on Home Assistant, you can easily restart the system and  
  then watch the log output by combining the above commands using &&  
      `sudo systemctl restart home-assistant@homeassistant && sudo journalctl -f -u home-assistant@homeassistant`

**SOURCE:**  
https://www.home-assistant.io/docs/autostart/systemd/

<br />

## PRE-SOFTWARE SETUP FOR GIT-CRYPT:
1. Check whether you have GPG installed already (ie. type gpg --version).  
   Otherwise download the GPG command line tool from https://www.gnupg.org/download/  
   Unpack and install it, ie.  
      `tar -xf [downloaded file name]`

   Navigate to the unpacked directory and then do:  
      `./configure`  
      `make`  
      `make check`  
      `make install`  

2. Generate a GPG key pair:  
      `gpg --full-generate-key`

   When prompted choose:  
   the default key (RSA and RSA),  
   max. key size (4096),  
   and no expiry (0)

   To list GPG keys for which you have both a public and private key type:  
   (A private key is required for signing commits or tags.)  
      `gpg --list-secret-keys --keyid-format LONG`

3. Copy the GPG key ID that starts with sec. In the following example, it's 30F2B65B9246B6CA.  
      ```
      sec   rsa4096/30F2B65B9246B6CA 2017-08-18 [SC]  
            D5E4F29F3275DC0CDA8FFC8730F2B65B9246B6CA  
      uid                   [ultimate] Mr. Robot <mr@robot.sh>  
      ssb   rsa4096/B7ABC0813E4028C0 2017-08-18 [E]  
      ```
      
4. Export the public key of that ID (replace your key ID from the previous step)  
   This will print the GPG key ID, in ASCII armor format.  
      `gpg --armor --export [your own GPG key ID goes here, eg. 30F2B65B9246B6CA]`

5. Add the generated GPG key to your Gitlab account: https://gitlab.com/profile/gpg_keys

6. Now tell git which key to use.  
      `git config --global user.signingkey [your own GPG key ID goes here, eg. 30F2B65B9246B6CA]`

**SOURCE:**  
https://docs.gitlab.com/ee/user/project/repository/gpg_signed_commits/index.html

<br />

## SOFTWARE SETUP FOR GIT-CRYPT:
1. Download git crypt from:  
   https://www.agwa.name/projects/git-crypt/downloads/git-crypt-0.6.0.tar.gz

2. Extract the file and install it with the following:  
      `cd git-crypt-0.6.0` (ie. cd the terminal to whatever name you downloaded git-crypt as).  
      `make`  
      `make install`  

3. List your gpg key (that you added earlier) by doing:  
      `gpg -k`  
   Copy the pub USER_ID key. Eg. D5E4F29F3275DC0CDA8FFC8730F2B65B9246B6CA

4. Navigate to your repo and add gpg user key so that you can share the repo with yourself or others:  
      `cd repo`  
      `git-crypt add-gpg-user [USER_ID]`  
   Replace the '[USER_ID]' part with the key you copied in step 3.

5. Navigate to your repo and then initiate git-crypt:  
      `cd repo`  
      `git-crypt init`

6. Specify files to encrypt by creating a .gitattributes file in the repository, like this:  
      `secretfile filter=git-crypt diff=git-crypt`  
      `*.key filter=git-crypt diff=git-crypt`

**NOTE A:**  
After cloning a repository with encrypted files, unlock with:  
      `git-crypt unlock`

**NOTE B:**  
The .gitattributes file is documented in the gitattributes(5) man page.  
The file pattern format is the same as the one used by .gitignore,  
as documented in the gitignore(5) man page, with the exception that  
specifying merely a directory (e.g. /dir/) is NOT sufficient to  
encrypt all files beneath it.  

Also note that the pattern dir/* does not match files under  
sub-directories of dir/.  To encrypt an entire sub-tree dir/, place the  
following in dir/.gitattributes:  

      * filter=git-crypt diff=git-crypt
      .gitattributes !filter !diff

The second pattern is essential for ensuring that .gitattributes itself is not encrypted.

**SOURCE:**  
https://www.agwa.name/projects/git-crypt/
