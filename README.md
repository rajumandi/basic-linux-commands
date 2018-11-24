# basic-linux-commands


Package management with APT
IconsPage/apt.png Package management via apt-get runs hand-in-hand with the /etc/apt/sources.list file. For information on editing or updating your sources list see SourcesList.

Introduction
This page describes how to handle the packages on your system using apt-get and related commands. For example, you can install a new package, remove an installed package, or update all installed packages to the latest versions.

Commands
IconsPage/info.png All these commands except the search commands must be run as root or with superuser privileges, see sudo for more information.

IconsPage/example.png Example:

sudo apt-get install ubuntu-desktop 
Installation commands
apt-get install <package_name> 
This command installs a new package.
apt-get build-dep <package_name> 
This command searches the repositories and installs the build dependencies for <package_name>. If the package is not in the repositories it will return an error.

aptitude install <package_name>
Aptitude is an Ncurses viewer of packages installed or available. Aptitude can be used from the command line in a similar way to apt-get. Enter man aptitude for more information.

APT and aptitude will accept multiple package names as a space delimited list. For example:
apt-get install <package1_name> <package2_name> <package3_name>
IconsPage/note.png Use the -s flag to simulate an action. For example: "apt-get -s install <package_name>" will simulate installing the package, showing you what packages will be installed and configured.

auto-apt
auto-apt run <command_string>
This command runs <command_string> under the control of auto-apt. If a program tries to access a file known to belong in an uninstalled package, auto-apt will install that package using apt-get. This feature requires apt and sudo to work.

Auto-apt keeps databases which need to be kept up-to-date in order for it to be effective. This is achieved by calling the commands auto-apt update, auto-apt updatedb and auto-apt update-local.
Usage example
IconsPage/example.png You're compiling a program and, all of a sudden, there's an error because it needs a file you don't have. The program auto-apt asks you to install packages if they're needed, stopping the relevant process and continuing once the package is installed.

     # auto-apt run ./configure
It will then ask to install the needed packages and call apt-get automatically. If you're running X, a graphical interface will replace the default text interface.
Maintenance commands
apt-get update
Run this command after changing /etc/apt/sources.list or /etc/apt/preferences . For information regarding /etc/apt/preferences, see PinningHowto. Run this command periodically to make sure your source list is up-to-date. This is the equivalent of "Reload" in Synaptic or "Fetch updates" in Adept.

apt-get upgrade
This command upgrades all installed packages. This is the equivalent of "Mark all upgrades" in Synaptic.
apt-get dist-upgrade
The same as the above, except add the "smart upgrade" checkbox. It tells APT to use "smart" conflict resolution system, and it will attempt to upgrade the most important packages at the expense of less important ones if necessary.
IconsPage/note.png "apt-get dist-upgrade" does not upgrade from a previous version of Ubuntu. For more information of upgrading from a previous version of Ubuntu see http://www.ubuntu.com/getubuntu/upgrading .

apt-get check
This command is a diagnostic tool. It does an update of the package lists and checks for broken dependencies.
apt-get -f install
This command does the same thing as Edit->Fix Broken Packages in Synaptic. Do this if you get complaints about packages with "unmet dependencies".

apt-get autoclean
This command removes .deb files for packages that are no longer installed on your system. Depending on your installation habits, removing these files from /var/cache/apt/archives may regain a significant amount of diskspace.

apt-get clean
The same as above, except it removes all packages from the package cache. This may not be desirable if you have a slow Internet connection, since it will cause you to redownload any packages you need to install a program.

The package cache is in /var/cache/apt/archives . The command

du -sh /var/cache/apt/archives
will tell you how much space cached packages are consuming.
dpkg-reconfigure <package_name>
Reconfigure the named package. With many packages, you’ll be prompted with some configuration questions you may not have known were there.
IconsPage/example.png For example:

dpkg-reconfigure fontconfig-config
will present you with a "wizard" on configuring fonts in Ubuntu.
echo "<package_name> hold" | dpkg --set-selections
This command places the desired package on hold.
IconsPage/info.png This command may have the unintended side effect of preventing upgrades to packages that depend on updated versions of the pinned package. apt-get dist-upgrade will override this, but will warn you first. If you want to use this command with sudo, you need to use echo "<package_name> hold" | sudo dpkg --set-selections not sudo echo "<package_name> hold" | dpkg --set-selections.

echo "<package_name> install" | dpkg --set-selections
This command removes the "hold" or "locked package" state set by the above command. The note above about sudo usage applies to this command.
Removal commands
apt-get remove <package_name>
This command removes an installed package, leaving configuration files intact.
apt-get purge <package_name>
This command completely removes a package and the associated configuration files. Configuration files residing in ~ are not usually affected by this command.
+ operator
IconsPage/example.png If you want to remove package1 and install package2 in one step:

apt-get remove <package1> <package2>+
.
apt-get autoremove
This command removes packages that were installed by other packages and are no longer needed.
apt-get autoremove <package_name>
This command removes an installed package and dependencies.
While there is no built in way to remove all of your configuration information from your removed packages you can remove all configuration data from every removed package with the following command.

dpkg -l | grep '^rc' | awk '{print $2}' | xargs dpkg --purge
Search commands
apt-cache search <search_term>
Each package has a name and a description. This command lists packages whose name or description contains <search_term>.

dpkg -l *<search_term>*
This will find packages whose names contain <search_term>. Similar to apt-cache search, but also shows whether a package is installed on your system by marking it with ii (installed) and un (not installed).

apt-cache show <package_name>
This command shows the description of package <package_name> and other relevant information including version, size, dependencies and conflicts.

dpkg --print-avail <package_name>
This command is similar to "apt-cache show".
dpkg -L <package_name>
This command will list files in package <package_name>.

dpkg -c foo.deb
This command lists files in the package "foo.deb". Note that foo.deb is a pathname. Use this command on .deb packages that you have manually downloaded.

dlocate <package_name>
This command determines which installed package owns <package_name>. It shows files from installed packages that match <package_name>, with the name of the package they came from. Consider this to be a "reverse lookup" utility.

IconsPage/info.png In order to use this command, the package dlocate must be installed on your system.

dpkg -S <filename_search_pattern>
This command does the same as dlocate, but does not require the installation of any additional packages. It is slower than dlocate but has the advantage of being installed by default on all Debian and Ubuntu systems.

apt-file search <filename_search_pattern>
This command acts like dlocate and dpkg -S, but searches all available packages. It answers the question, "what package provides this file?".

apt-file needs to be updated regularly like apt-get. Use the command:

apt-file update
IconsPage/info.png In order to use this command, the package apt-file must be installed on your system.

apt-cache pkgnames
This command provides a listing of every package in the system
A general note on searching: If searching generates a list that is too long, you can filter your results by piping them through the command grep. Examples:

apt-cache search <filename> | grep -w <filename>
will show only the files that contain <filename> as a whole word

dpkg -L package | grep /usr/bin 
will list files located in the directory /usr/bin, useful if you're looking for a particular executable.
For more information on apt-get, apt-cache and dpkg consult their manual pages by using the man command. These manuals will provide a wider scope of information in addition to all of the options that you can use with each program.

IconsPage/example.png Example:

man apt-get
.
Typical usage example
I want to feel the wind in my hair, I want the adrenaline of speed. So let's install a racing game. But what racing games are available?

apt-cache search racing game
It gives me a lot of answers. I see a game named "torcs". Let's get some more information on this game.

apt-cache show torcs
Hmmm... it seems interesting. But is this game not already installed on my computer? And what is the available version? Which repository is it from (Universe or Main)?

apt-cache policy torcs
Ok, so now, let's install it!

apt-get install torcs
What is the command I must type in the console to launch this game? In this example, it's straightforward ("torcs"), but that's not always the case. One way of finding the name of the binary is to look at what files the package has installed in "/usr/bin". For games, the binary will be in "/usr/games". For administrative programs, it's in "/usr/sbin".

dpkg -L torcs | grep /usr/games/
The first part of the command display all files installed by the package "torcs" (try it). With the second part, we ask to only display lines containing "/usr/games/".

Hmmm, that game is cool. Maybe there are some extra tracks?

apt-cache search torcs
But I'm running out of space. I will delete the apt cache!

apt-get clean
Oh no, my mother asked me to remove all games from this computer. But I want to keep the configuration files so I can simply re-install it later.

apt-get remove torcs
If I want to also remove config files :

apt-get purge torcs
Setting up apt-get to use a http-proxy
These are three methods of using apt-get with a http-proxy.

Temporary proxy session
This is a temporary method that you can manually use each time you want to use apt-get through a http-proxy. This method is useful if you only want to temporarily use a http-proxy.

Enter this line in the terminal prior to using apt-get (substitute your details for yourproxyaddress and proxyport).


export http_proxy=http://yourproxyaddress:proxyport
If you normally use sudo to run apt-get you will need to login as root first for this to work unless you also add some explicit environment settings to /etc/sudoers, e.g.


Defaults env_keep = "http_proxy https_proxy ftp_proxy"
APT configuration file method
This method uses the apt.conf file which is found in your /etc/apt/ directory. This method is useful if you only want apt-get (and not other applications) to use a http-proxy permanently.

IconsPage/info.png On some installations there will be no apt-conf file set up. This procedure will either edit an existing apt-conf file or create a new apt-conf file.


gksudo gedit /etc/apt/apt.conf
Add this line to your /etc/apt/apt.conf file (substitute your details for yourproxyaddress and proxyport).


Acquire::http::Proxy "http://yourproxyaddress:proxyport";
Save the apt.conf file.

BASH rc method
This method adds a two lines to your .bashrc file in your $HOME directory. This method is useful if you would like apt-get and other applications for instance wget, to use a http-proxy.


gedit ~/.bashrc
Add these lines to the bottom of your ~/.bashrc file (substitute your details for yourproxyaddress and proxyport)


http_proxy=http://yourproxyaddress:proxyport
export http_proxy
Save the file. Close your terminal window and then open another terminal window or source the ~/.bashrc file:

source ~/.bashrc
Test your proxy with sudo apt-get update and whatever networking tool you desire. You can use firestarter or conky to see active connections.

If you make a mistake and go back to edit the file again, you can close the terminal and reopen it or you can source ~/.bashrc as shown above.

source ~/.bashrc
How to login a proxy user
If you need to login to the Proxy server this can be achieved in most cases by using the following layout in specifying the proxy address in http-proxy. (substitute your details for username, password, yourproxyaddress and proxyport)


http_proxy=http://username:password@yourproxyaddress:proxyport
