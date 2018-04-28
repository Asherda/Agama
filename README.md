# Agama Desktop App
Desktop App for SuperNET DAPPs

#### For Developers
You must have `node.js` and `npm` installed on your machine.
node 9.x:
curl -sL https://deb.nodesource.com/setup_9.x | sudo -E bash -
sudo apt-get install -y nodejs

Check versions:
nodejs --version
v8.9.4

Note that Easy-DEX-GUI is quite sensitive to the node version. 9.x fails for me, and older versions do as well. Make sure you're on 8.9.x. Installing nvm is probably a good idea, especially if you already have ndoe installed but it is not a compatible version.

Other essential dependencies:
sudo apt-get install -y build-essential

1) Clone Agama Desktop App with EasyDEX-GUI submodule
```shell
git clone https://github.com/miketout/agama --recursive --branch pkg_automation_electrum --single-branch
```
with this command you git clone agama - but explicitly just the pkg_automation_electrum branch (therefore --single-branch) which we also use for the release packages.
2) Get the binary artfacts into place (linux version)
```shell 
cd agama
./binary_artifacts.sh
```
3) install the electron packager and prebuilt - note the ugly unsafe-perm and allow-root stuff for the prebuilt electron.
```shell
sudo npm install electron-packager -g
sudo npm install electron-prebuilt -g --unsafe-perm=true --allow-root
```
4) Get Dave's fork for the new coin support
```shell
cd gui/EasyDEX-GUI/
git remote add dave git@github.com:DavidLDawes/EasyDEX-GUI.git
git checkout -b prep
git pull dave prep
```
5) get webpack dependencies into place for the react stuff
```shell
npm install && npm install webpack webpack-dashboard
```
6) Now get the react stuff installed and running
```shell
cd react
npm install
cd src
npm start
```
Brings up the dashboard and loads the react app using localhost:3000

7) start a new shell and go back to the react/src dir and build things
```shell
cd agama/gui/EasyDEX-GUI/react/src
npm run build
```
8) Get the top level stuff set, after the cd you shold be in the root agama dir
```shell
cd ../../..
npm install
```
9) At this point you can test things by running the wallet directly from electron:
```shell
npm start
```
This is a pretty wrapper around electron. I set the environment to production too.

10) Check that things work. Choose the native Komodo, or once it works better still our Verus coin. Loading will take 10+ hours the first time to get the chain.

11) toggle dev and debug options in settings, note the view menu that mentions denugging, that brings up the browser console which is quite useful, allowing variable examination and break points. Code has been squashed so more work is needed to get breakpoints completely useful.

12) sync komodod and/or asset chains - now that the wallet is running if you choose Komodo native (or eventually Verus) it will load the assect chain. It's taking me 16 hours on a local VM to get it loaded the first time. It only takes 10 or 30 minutes to catch up on startup after that if things are going well.
You are ready to dev
### Important dev notes

#### Sockets.io
In dev mode backend is configured to send/receive messages from/to http://127.0.0.1:3000 address. If you open it as http://localhost:3000 sockets server will reject any messages.

#### **Build the Wallet-App**
Refer to the original [electron-packager](https://github.com/electron-userland/electron-packager) repository for more detailed information.
Fairly useless advice

##### Linux
Change directory to agama and execute the following command to build the Linux app
```shell
cd agama
electron-packager . --platform=linux --arch=x64 --icon=assets/icons/agama_icons/128x128.png --out=build/ --buildVersion=0.11 --ignore=assets/bin/win64 --ignore=assets/bin/osx --overwrite
```

##### OSX
Change directory to agama and execute the following command to build the OSX app
```shell
cd agama
electron-packager . --platform=darwin --arch=x64 --icon=assets/icons/agama_icons/agama_app_icon.icns --out=build/ --buildVersion=0.2 --ignore=assets/bin/win64 --ignore=assets/bin/linux64 --overwrite
```

##### Windows
Change directory to agama and execute the following command to build the Windows app
```shell
cd agama
electron-packager . --platform=win32 --arch=x64 --icon=assets/icons/agama_icons/agama_app_icon.ico --out=build/ --buildVersion=0.2 --ignore=assets/bin/osx --ignore=assets/bin/linux64 --overwrite

# If generating 32bit desktop package
electron-packager . --platform=win32 --arch=ia32 --icon=assets/icons/agama_icons/agama_app_icon.ico --out=build/ --buildVersion=0.3 --ignore=assets/bin/osx --ignore=assets/bin/linux64 --overwrite

# To build both x64 and x86 desktop package
electron-packager . --platform=win32 --arch=all --icon=assets/icons/agama_icons/agama_app_icon.ico --out=build/ --buildVersion=0.3 --ignore=assets/bin/osx --ignore=assets/bin/linux64 --overwrite
```
change architecture build parameter to ```--arch=x64``` for 64 bit build


## Troubleshooting Instructions

### Windows DLL issues
On Windows it's noticed agama.exe complains about `VCRUNTIME140D.DLL` and `ucrtbased.dll` file.

Please see **windeps** directory and README file for instructions to install the required DLL files on Windows, and then try again running Agama App.

## Optional packages to make rpm and deb distros

electron-installer-debian

electron-installer-redhat

refer to ./make-deb.js and ./make-rpm.js
