---
layout: post
title:  "How did I setup my Ubuntu 20.04.5 (for development)?"
date:   2023-01-06 20:16:40 +0530
categories: jekyll update
---

## Reference Links
1. Installation link for [Ubuntu 20.04.5][ubuntu-setup]
2. ISO image burning [tool][iso-burning-tool]
3. How to deal with [AppImage][gcs-pub-sub] files?
4. Setup [nvidia-drivers][setup-nvidia-drivers]
5. [Snapscraft][snap] for apps on Ubuntu

## Whats your OS?
> Windows, MacOS, Ubuntu

Lets start with idenitfying what OS we have currently and then the process might slightly vary for each underlying OS.

I am trying to replace my Ubuntu 22.04 LTS to Ubuntu 20.04.5 LTS. I could have simply degraded using some *commands* but I wanted a fresh copy of the OS, so I had to take the longer path. I really appreciate the docs put up by ***Ubuntu*** for the installation and setup of their OS. You can find the link above in the references.

I started off by burning a copy of the *iso* file of the Ubuntu 20.04 on a ***8gb usb stick*** using an iso file burning tool called *balena*, you can find the docs for this tool above. It is an *AppImage* file so you have to follow some steps to make it work in ubuntu and I guess its pretty straight forward in the other operating systems.

Once your usb stick is ready with the iso burned. We restart the system with the stick and navigate to your ***boot menu*** (might vary from motherboard manufacturer to manufacturer, F12 is a common option). I guess the instructions are pretty forward from their given you followed the ***ubuntu docs*** mentioned above.

After the setup is done you might probably get a *display resolution* perfectly set with your monitor or not (depends on if your setup has a GPU). I unfortunately/fortunately do bear a GPU and their a couple of steps to be followed for a perfect resolution and to utilize the processing powers of a GPU.

Dont panic if your resolution is bad, the settings would not show more options for us to change as well. *Nvidia drivers* to the rescue!

## Can I drive your card?
> GPU's are so fun and cool, GPU setup is not so *cool*

Till date I am pretty confused as to how to set the nvidia-drivers up but I will post the last working solution here.

Before we even start with any installations dont forget to run ***sudo apt-get update*** in the terminal, helps you with certain kernel associated links and upgrades.

I basically followed steps directly from this amazing article on how to setup *nvidia-drivers* (link attached above). You are most welcome to ditch this page and follow their steps. The article basically mentions multiple steps on setting up the drivers and I will share the process that worked for me.

Steps to follow,
1. Search for **Software & Updates** in the *Applications*
2. Navigate the tab that says **Additional Drivers**
3. Search for a **proprietary latest driver**, it basically follows a format like ***nvidia-driver-xxx***
4. Hit on apply changes and wait to see the magic happen!

Restart the machine and hopefully you will get your device resolution set. If not you can see more options now in the display settings present in the settings.

## App me
> In a snap

These are some of the apps I use and I used snapcraft to install them on ubuntu, pretty much the best and the easiest option to install something on this OS. (Link attached above for your reference)
1. Code Editor : Sublime Text
2. Commucation : Slack
3. Browser : Firefox
4. Music : Spotify

Some basic packages I would like to use in Sublime Text,
1. Anaconda, this package helps me in formatting my code according to the PEP8 standards and I personally would like to disable linting, I attached my user specific settings below for your reference,
```
{
	"auto_formatting": true,
	"anaconda_linting": false,
}
```

I also use docker containers to containerize my code and the steps to install docker on ubunut as mentioned in this [link][docker-install].

## Venomous Snake
> Yes, we are talking about the installation and setup of *python*

Now you will find many resources to install python and I bet most of them are based on *anaconda*. I tend to follow a different approach because it always seems to work for me. I use a ***python version management*** tool called **PyEnv** and it works awesome and is pretty easy to maintain multiple *python versions* on your local setup.

I faced different errors in installing this tool but I managed to get a working solution for most of the problems and I am gonna mention them over here. The steps are as follows,
1. Before we even start with installation of pyenv, we need to make sure we can make a *curl request* to **raw.githubusercontent.com**, we will be using the *automatic installer* method given by pyenv and this requires making curl request to this link and in most of the DNS providers this basically returns a curl error that says **Connection refused**.
2. We solve this by following the exact same steps as mentioned in this [article][edit-hosts], this link basically adds the ip address of this url to the **/etc/hosts/** file in the ubuntu directories.
3. Now we are set to perform all the instructions given in this [link][pyenv-installation], this is quite different from the official documentation but it is precise and helpful. Dont ignore to set the path after installation (edit the .bashrc file with the given config).
4. Verify the installation using **pyenv --version** and you are ready to go!

I also use miniconda (consider this as a lightweight anaconda) to manage different environments and believe me you will find peace in isolating your python projects using miniconda. On linux the installation is pretty direct,
1. Navigate to the [conda][miniconda-install] installation link and download the installation script according to your architecture.
2. Now navigate to the folder where you installed the shell script and run bash YOUR_FILE_NAME.sh
3. After the installation is done, add this line at the last in .bashrc file located in the root folder, this will help you with running conda commands in the terminal, (replace the YOU_SHELL_NAME with your shell name)
```
eval "$(/home/loukik/miniconda3/bin/conda shell.YOUR_SHELL_NAME hook)"
```
4. Now type conda init in the terminal and now you are good to go.
5. (Optional) If you dont want your terminal to activate base on the startup you can use,
```
conda config --set auto_activate_base false
```
6. Some basic conda commands that might be useful,
```
conda create --name py35 python=3.5 # To create an environment with specific python version
conda list # List all packages in an environment
conda list env # Lists all enviroments in the local machine
conda activate <env name> # Switch between environments
conda deactivate # Deactivate a current environment
```
7. Conda [cheatsheet][conda-cheatsheet]

Now before we enjoy, we should realise that install the tool to install python, so lets install python now, (you can choose to follow along with this [link][pyenv-install-python]),
1. **pyenv install 3.10** (you can mention the version you wish to install)
2. **pyenv global 3.10**, this basically declares the *global* version to be used in your local system.

Now verify the installation of python and pip (Python Package Manager) with,
1. **python --version**
2. **pip --version**

We are all set with python now!

## Conclusion
> Lets rest for a while!

I wanted to also include resources about the install of CUDA, PyTorch and some machine learning related setup. But I guess this should be enough hair pulling for the day. We will talk abuot how to use the GPUs in another article.

Feel free to contact me if you think the contents incorrect at any place.


[ubuntu-setup]: https://ubuntu.com/tutorials/install-ubuntu-desktop
[iso-burning-tool]: https://www.balena.io/etcher/
[app-image-use]: https://itsfoss.com/use-appimage-linux/
[setup-nvidia-drivers]: https://linuxconfig.org/how-to-install-the-nvidia-drivers-on-ubuntu-20-04-focal-fossa-linux
[snap]: https://snapcraft.io/
[pyenv]: https://github.com/pyenv/pyenv#automatic-installer
[edit-hosts]: https://florahuo.medium.com/edit-etc-hosts-file-in-ubuntu-command-line-using-nano-ide-44c3a78e23ca
[pyenv-installation]: https://brain2life.hashnode.dev/how-to-install-pyenv-python-version-manager-on-ubuntu-2004
[pyenv-install-python]: https://github.com/pyenv/pyenv#prefix-auto-resolution
[miniconda-install]: https://docs.conda.io/projects/conda/en/latest/user-guide/install/linux.html
[conda-cheatsheet]: https://docs.conda.io/projects/conda/en/4.6.0/_downloads/52a95608c49671267e40c689e0bc00ca/conda-cheatsheet.pdf
[docker-install]: https://docs.docker.com/engine/install/ubuntu/