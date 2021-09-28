<img src="https://raw.githubusercontent.com/geerlingguy/mac-dev-playbook/master/files/Mac-Dev-Playbook-Logo.png" width="250" height="156" alt="Mac Dev Playbook Logo" />

# Endowus Mac Development Ansible Playbook

Automates installation of common development tools and programs.

Forked from [geerlingguy/mac-dev-playbook](https://github.com/geerlingguy/mac-dev-playbook).

## Prerequisites
  1. OSX version should be Big Sur. (Other OSX versions are not tested)
  2. Admin/sudo permissions for the login account running the playbook.

## Installation

  1. Ensure Apple's command line tools are installed (`xcode-select --install` to launch the installer).
  2. [Install Ansible](https://docs.ansible.com/ansible/latest/installation_guide/index.html):

     1. Upgrade Pip: `sudo pip3 install --upgrade pip`
     2. Install Ansible: `pip3 install ansible`

  3. Clone or download this repository to your local drive.
  4. Run the following command inside this directory to install required Ansible roles.
   ```
   $HOME/Library/Python/3.8/bin/ansible-galaxy install -r requirements.yml
  ```
   
  5. Run the following command inside this directory to execute the playbook. Enter your macOS account password when prompted for the 'BECOME' password.
   ```
   $HOME/Library/Python/3.8/bin/ansible-playbook main.yml --ask-become-pass
   ```

> Note: If some Homebrew commands fail, you might need to agree to Xcode's license or fix some other Brew issue. Run `brew doctor` to see if this is the case.

### Use with a remote Mac

You can use this playbook to manage other Macs as well; the playbook doesn't even need to be run from a Mac at all! If you want to manage a remote Mac, either another Mac on your network, or a hosted Mac like the ones from [MacStadium](https://www.macstadium.com), you just need to make sure you can connect to it with SSH:

  1. (On the Mac you want to connect to:) Go to System Preferences > Sharing.
  2. Enable 'Remote Login'.

> You can also enable remote login on the command line:
>
>     sudo systemsetup -setremotelogin on

Then edit the `inventory` file in this repository and change the line that starts with `127.0.0.1` to:

```
[ip address or hostname of mac]  ansible_user=[mac ssh username]
```

If you need to supply an SSH password (if you don't use SSH keys), make sure to pass the `--ask-pass` parameter to the `ansible-playbook` command.

### Running a specific set of tagged tasks

You can filter which part of the provisioning process to run by specifying a set of tags using `ansible-playbook`'s `--tags` flag. The tags available are `dotfiles`, `homebrew`, `mas`, `extra-packages` and `osx`.

    ansible-playbook main.yml -K --tags "dotfiles,homebrew"

## Overriding Defaults

Not everyone's development environment and preferred software configuration is the same.

You can override any of the defaults configured in `default.config.yml` by creating a `config.yml` file and setting the overrides in that file. For example, you can customize the installed packages and apps with something like:

```yaml
homebrew_installed_packages:
  - cowsay
  - git
  - go

mas_installed_apps:
  - { id: 443987910, name: "1Password" }
  - { id: 498486288, name: "Quick Resizer" }
  - { id: 557168941, name: "Tweetbot" }
  - { id: 497799835, name: "Xcode" }

composer_packages:
  - name: hirak/prestissimo
  - name: drush/drush
    version: '^8.1'

gem_packages:
  - name: bundler
    state: latest

npm_packages:
  - name: webpack

pip_packages:
  - name: mkdocs

configure_dock: true
dockitems_remove:
  - Launchpad
  - TV
dockitems_persist:
  - name: "Sublime Text"
    path: "/Applications/Sublime Text.app/"
    pos: 5
```

Any variable can be overridden in `config.yml`; see the supporting roles' documentation for a complete list of available variables.

## Included Applications / Configuration (Default)

Take a look at `homebrew_cask_apps` and `homebrew_installed_packages` in [default.config.yml](default.config.yml).

Applications (installed with Homebrew Cask):

  - [ChromeDriver](https://sites.google.com/chromium.org/driver/)
  - [Docker](https://www.docker.com/)
  - [Dropbox](https://www.dropbox.com/)
  - [Firefox](https://www.mozilla.org/en-US/firefox/new/)
  - [Google Chrome](https://www.google.com/chrome/)
  - [Homebrew](http://brew.sh/)
  - [LICEcap](http://www.cockos.com/licecap/)
  - [Slack](https://slack.com/)
  - [Sublime Text](https://www.sublimetext.com/)
  - [Jetbrains Toolbox](https://www.jetbrains.com/toolbox-app/)
  - [JDK 11](https://adoptium.net/)
  - [SDKMAN!](https://sdkman.io/)

### Notes

  - Jetbrains Toolbox is recommended as it:
    - manages all Jetbrains apps in one place
    - can update and rollback/downgrade application versions
  - JDK 11 is the version that our backend apps are running on in production.
  - SDKMAN! is installed along with 2 versions of Scala. After running the playbook, there will be a `.zshrc` file created. If `ohmyzsh` or any other plugin that creates zsh configuration is installed, please append the SDKMAN! config to the end of the new .zshrc file.

Common [dotfiles](https://github.com/williamhaw/mac-dotfiles) are also installed into the current user's home directory. You can disable dotfiles management by setting `configure_dotfiles: no` in your configuration.

Finally, there are a few other preferences and settings added on for various apps and services.

## Future additions

- Download security software.
- Setup ssh key.

## Testing the Playbook

It's a good idea to test on a Big Sur VM in [VirtualBox](https://www.virtualbox.org/wiki/Downloads).