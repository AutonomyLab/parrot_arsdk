^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Changelog for package parrot_arsdk
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

3.9.1 (2016-07-25)
------------------
* Parrot ARSDK 3.9 Changes
    * New commands:
        * Bebop and Bebop 2:
        * BankedTurn
        * BankedTurnChanged
        * MaxPitchRollRotationSpeed
        * MaxPitchRollRotationSpeedChanged
        * wifiSecurity
        * VideoStabilizationMode
        * VideoStabilizationModeChanged
        * New commands to build the SDK:
    * For iOS: ./build.sh -p arsdk-ios -t build-sdk -j
    * For iOS Simulator: ./build.sh -p arsdk-ios_sim -t build-sdk -j
    * For Android: ./build.sh -p arsdk-android -t build-sdk -j
    * For Unix: ./build.sh -p arsdk-native -t build-sdk -j
    * New outputs directory:
        * For iOS: <SDK>/arsdk-ios
        * For iOS Simulator: <SDK>/arsdk-ios_sim
        * For Android: <SDK>/arsdk-android
        * For Unix: <SDK>/arsdk-native But old output are kept (with simlinks to avoid breaking compatibility).
        * libARController Android bug fix (a big thanks to Synman :+1: )
    * Video decoding bug fix in the iOS Sample (a big thanks to ar.tommy and ka010 :+1: )
    * Other bug fixes
* parrot_arsdk Changes
    * Add travis config file
    * Minor fix for package.xml
    * Initial catkin wrapper for Parrot ARDSDK
        * Include a stripped tarball of the SDK
        * SDK Version: 3.9.1
        * Populate both devel and install spaces
    * Add a patch to disable non-x86 compilation flags for arm platform from
    * Add a script to determine the host arch robustly. This script is used
      by CMake to pass appropriate flags to build_sdk.py
    * Contributors: Mani Monajjemi
