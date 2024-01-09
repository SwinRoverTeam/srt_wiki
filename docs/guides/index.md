# Build Guides

## Dev environment setup instructions
Any, Ubuntu compatible operating system will work for development.
All of the projects under the rocker muncher rover are built navitely though they include Dockerfiles
if you wish to run them in containers, instructions for doing so can be found on their repsective wiki pages.

** Do keep in mind** Ardupilot running in the Docker container will not be able to spawn
mavproxy windows for map and orientation. You will only be able to see information from an external instance of mavproxy
or your ground control system.
The process for doing this is not covered in this wiki but you can find it in the Ardupilot dev wiki.

## To get the Source
Follow the instruction on github to generate and upload a personal [ssh rsa key](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) to your github account.

The general process for cloning a repo will look something like this.
```sh
git clone --recursive git@github.com:SwinRoverTeam/{proj}.git
cd {proj}
```

After you've fixed a bug or added some function commit your changes to save them in your local workspace using a commit
``sh
git commit /path/to/files /path/to/other/files -m "fix wierd bug"
```

You should write your commit messages to follow the imperative of "This commit will"..  This makes it easier for other developers to see.

Keep commit messages short and if you've made large changes describe them in more detail here in the wiki.

To send your changes to the github use a push. It will look somthing like this:
```sh
git push origin dev
```

Finally, [keep your local repo up to date](https://stefanbauer.me/articles/how-to-keep-your-git-fork-up-to-date) with everyone else by using a rebase.
```sh
git rebase upstream master
```

** DO NOT PULL **
Performing a pull request from the upstream location will override all of the changes made on our fork.

## Futher links
checkout the ardupilot build guide here:
[ardupilot build guide](ardupilot.md).

**Note**
Do not Make changes directly to the repos if you're unsure make a pull request or consult a software team lead! thank you :)
