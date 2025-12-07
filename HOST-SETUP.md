Host Setup
==========

This doc outlines all the stuff I do when I open a new host, as best as I remember. This is just a guide to myself since I only do this once every blue moon.

## 1. SSH
I rent servers from Hetzner, and they allow me to put a public part of a SSH key into the server on first setup. Nice and easy. I keep something like this in my local `~/.ssh/config` file:
```
# Shared settings for all hosts, all users
Host *
  IdentitiesOnly yes

# Shared settings for all users on <specific-host>
Host <specific-host>
  HostName <ip-address>

# Settings for root on N hosts
Match User root Host *
  IdentityFile ~/.ssh/<name-of-ssh-id-file>
```

Important, the wild cards can get a little dicey, make sure to keep an explicit section for things like git:
```
# Explicit git config
Host github.com
  HostName github.com
  User <github username>
  IdentityFile ~/.ssh/<name-of-ssh-github-key>
```

Then I log in with `ssh root@<specific-host>`, and it'll prompt you for whatever password you applied to your private key. I like to have blank pw, yolo.

## 2. Set up own user
Once in as `root`, I like to add my own user, log out of `root`, and never log back in as `root` if I can avoid it.
```
sudo adduser claus
usermod -aG sudo claus
```

Add this to local ssh config:
```
# Settings for claus on N hosts
Match User claus Host *
  IdentityFile ~/.ssh/<name-of-ssh-id-file>
```

## 3. Log in with new user
Locally `ssh claus@<specific-host>`, and then I like to:
```
sudo apt update
sudo apt upgrade
```

## 4. `tmux` and `mosh`
I just like these set up.

### mosh
Locally and on server:
**MacOS**
```
brew install mosh
```
**Debian**
```
sudo apt install mosh
```

### tmux
On server:
```
sudo apt install tmux
```

Add to server's `/.bash_profile`:
```
# Auto-start/attach tmux
if [ -z "$TMUX" ] && command -v tmux >/dev/null 2>&1; then
    tmux attach -t main || tmux new -s main
fi
```

Then `source ~/.bash_profile`.

Here's what my `/.tmux.conf` looked like as of writing this:
```
# More scrollback
set -g history-limit 100000

# Toggle mouse mode with Ctrl+b m
bind m \
    set -g mouse\; \
    display-message "mouse: #{?mouse,ON,OFF}"

# Faster key repeat
set -s escape-time 10
```

## 5. `nginx` setup
```
sudo apt install nginx
sudo nginx -t
sudo systemctl reload nginx
```

Go to your server's IP in your browser, and you should see "Welcome to nginx" splash screen

## 6. `deploy` user and `www-data` group
I love to make changes to my server by running `rsync` and `ssh` commands/scripts from my local machine. And I like to use the user `deploy` for these things. And I don't want to use `sudo` commands and worry about password and whatever, so as much as possible, I like to just give ownership to `deploy` or `www-data`.

```
sudo adduser deploy
usermod -aG sudo deploy
usermod -aG www-data deploy
```

## 7. Setup dirs and permissions
The way I have built the `webconfig` repo, there are a few basic dirs that need to exist. And some permissions I like to change. I could script my way out of it, but I find it simpler to just add them as TODOs here:

**`nginx` configs**
```
sudo chgrp -R www-data /etc/nginx
sudo chmod g+ws /etc/nginx
```
This makes it so `www-data` is the group for the dir, makes it so the group can write to the dir, and makes it so all future child dirs also belong to group `www-data`, I do this with group, because I'd like to keep `root` as the owning user. 

**Backups**
```
sudo mkdir /etc/nginx-backups
sudo chgrp -R www-data /etc/nginx-backups
sudo chmod g+ws /etc/nginx-backups
```
Same pattern as above, these are where I keep full backups of entire nginx config folder when I do config updates.

**Logs**
```
sudo mkdir /var/log/webconfig /var/log/sites
sudo chgrp -R www-data /var/log/webconfig /var/log/sites
sudo chmod g+ws /var/log/webconfig /var/log/sites
```
`release_log.txt` and `rollout_log.txt` for a given site will like in `/var/log/sites/site-name/...`.
`update_logs.txt` will be in `/var/log/webconfig`.

**Site Location**
I like to keep my site data in `/srv/www`.
```
sudo mkdir /srv/www
sudo chgrp -R www-data /srv/www
sudo chmod g+ws /srv/www
```
`site-name` will live in `/srv/www/site-name`, and now that `deploy` user via `www-data` has permissions, it will create the site folders as part of site releases.

## 8. Reloading `nginx` permissions
Dir permissions is one thing, another is running the commands needed to reload a new `nginx` configeration. In order to not run `sudo` commands from my local machine, I add these lines to `visudo`:
```
sudo visudo
```

Add:
```
deploy ALL=(root) NOPASSWD: /usr/sbin/nginx -t
deploy ALL=(root) NOPASSWD: /usr/bin/systemctl reload nginx
```

Making sure those are the correct paths to `systemctl` and `nginx`.
