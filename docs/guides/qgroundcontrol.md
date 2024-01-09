
# QGroundControl

The unabridged guide can be found [here](https://docs.qgroundcontrol.com/master/en/qgc-dev-guide/getting_started/).

## Setup

* Install Qt using the [offline oss installer](https://www.qt.io/download-qt-installer-oss).
You will need to create your own account with Qt to do this. Next, install the
latest version of Qt6 *and* Qt5.11 as QGroundControl is migrating between versions 
at present and we may decide to stick with the LTS version.

* Install the following packages:
```sh
sudo apt-get install speech-dispatcher libudev-dev libsdl2-dev patchelf build-essential curl
```

* Install QtCreator.

## Get the Source

Clone the project from GitHub:
```sh
git clone --recursive git@github.com:SwinRoverTeam/qgroundcontrol.git
cd ardupilot
```

## Reference

To learn more about Qt or if you get stuck and need a reference, check the [Qt-QML book](https://www.qt.io/hubfs/_website/QML%20Book/qt6book-with-frontpage.pdf).
And if you need help navigating the editor and setting up shop, check the [QtCreator guide](https://www.qt.io/product/qt6/qml-book/ch03-qtcreator-qt-creator).

## Basic usage ##

It is good practice to only edit QGC through QtCreator as it keeps track of build systems, cross-compilation and Qt Kits for you.
