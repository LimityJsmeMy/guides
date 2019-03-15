Pseudonymous Development
=========================

This guide is written for already security-conscious people, so a lot of “basic” things are not covered. You can have more secure system, but this one still offers some comfort and work efficiency while protecting your data against well-equipped adversaries. It’s obviously a trade-off.

## Passwords

Make yourself familiar with good password system like [diceware][] and use some password manager like [KeePassXC][]. Create separate password database for your pseudonymous persona and preferably don’t store it next to your regular password database.

## Separation of Concerns

If you’re involved in multiple activities, separate them thoroughly. Use different passwords and password databases, different disk drives (or at least volumes), accounts, etc. Log out from and restart everything when switching between roles. Work only under one role at one time.

In case something gets exposed, it doesn’t expose everything you’re participating in.

## Disk Encryption

Work on machine with full disk encryption. This way, your personal and system logs are both protected as well any other data. Secondly, try to separate your pseudonymously authored work files from your machine. Find a nice thumb drive or external disk, encrypt it via [VeraCrypt][] or your system’s full disk encryption and store all those files there.

Full disk encryption usually leaves your computer vulnerable if you don’t turn it off. So, try to develop a habit to regularly shut it down—e.g., before you go to bed, when you leave for lunch, or before commute. It’s actually better to restart your machine, not unlock it and then shut it down. This usually clears memory where encryption keys are stored which is the biggest vulnerability of full disk encryption (after weak password of course).

## GitHub Account

Use [Tor][] or no-log VPN to [create a new GitHub account][new-github-account]. Use pseudonymous email address which is accessed only via Tor or no-log VPN as well. Don’t fill out anything unless it’s mandatory (perhaps avatar after clearing metadata from the image can be an exception 😇).

If you’re not using Tor Browser, make sure your browser doesn’t leak information about you to “them clouds” or perhaps even your system volume, disable JavaScript or enable strong content blocker to prevent tracking. Or, just use [Tor Browser][tor-browser].

## Learn About Your Shell History

Some shells like `fish` have private mode which remembers history during terminal session but don’t persist it. Other shells allow you to execute commands while not adding them to history (e.g., by prefixing a command with space).

Do you rely on one liner you wrote half a year ago? Save it as a script next to your pseudonymously authored work.

## Learn About Your System in General

Things like clipboard history can be stored and retained for extended period of time. It may not be such a good idea to have some passwords or pseudonymous email addresses next to sections from your thesis. One trick is to use drag and drop instead copy and paste, but beware over what you’re moving.

Another weak point is a recent documents feature or some modern “smart” applications who may be full of analytics or don’t even offer an option to clear such data.

## Separate SSH

Create a directory in your pseudonymous workspace where you’ll store your keys and configs.

    cd "$PSEUDONYMOUS_MOUNT_POINT"
    mkdir SSH
    cd SSH
    # You may use different key type like RSA, but curves are trendy (for good reason)
    ssh-keygen -t ed25519 -C $PSEUDONYMOUS_EMAIL -f id_ed25519

Create an [`ssh_config(5)`][ssh-config] file similar to the following  `config` file.

    Include /etc/ssh/ssh_config
    Include ~/.ssh/config
    
    Host *
      IdentityAgent none
      IdentityFile "PSEUDONYMOUS_MOUNT_POINT/SSH/id_ed25519"
      ProxyCommand nc -x 127.0.0.1:9150 %h %p
      User PSEUDONYM
      UserKnownHostsFile "PSEUDONYMOUS_MOUNT_POINT/SSH/known_hosts"

## Git Repositories

If you plan to use Git via Tor, create a helper script `git.sh` in SSH directory described in previous section. Don’t forget to make it executable.

    #!/bin/sh
    ssh -F "`dirname "$0"`/config" "$@"

Cloning should be done via Tor or VPN only.

    cd "$PSEUDONYMOUS_MOUNT_POINT"
    # Set GIT_SSH_COMMAND only when using Tor
    env GIT_SSH_COMMAND="$PSEUDONYMOUS_MOUNT_POINT/SSH/git.sh" \
      git clone "$REPOSITORY_URL"
    cd "$REPOSITORY_NAME"
    # Set core.sshCommand only when using Tor
    git config core.sshCommand "$PSEUDONYMOUS_MOUNT_POINT/SSH/git.sh"
    git config user.name "$PSEUDONYM"
    git config user.email "$GITHUB_NOREPLY_EMAIL"

You can find your GitHub no-reply email address in Personal settings → Emails around [Keep my email address private][noreply-email].

[diceware]: https://www.eff.org/dice
[keepassxc]: https://keepassxc.org
[veracrypt]: https://www.veracrypt.fr
[new-github-account]: https://github.com/join
[tor]: https://www.torproject.org
[tor-browser]: https://www.torproject.org/projects/torbrowser.html
[ssh-config]: https://man.openbsd.org/ssh_config
[noreply-email]: https://github.com/settings/emails#toggle_visibility_note
