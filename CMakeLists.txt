cmake_minimum_required(VERSION 2.8.3)
project(parrot_arsdk)

find_package(catkin REQUIRED)

catkin_package(
  CFG_EXTRAS parrot_arsdk.cmake
)

###########
## Build ##
###########

# use the build space for downloading/building the package
# we populate the results to devel/install spaces after the build
set(ARDRONESDK3_DEVEL_PATH ${CMAKE_CURRENT_BINARY_DIR}/arsdk)
set(ARDRONESDK3_PATH ${ARDRONESDK3_DEVEL_PATH}/out/arsdk-native/staging/usr/)

# 3.9.1
set(ARSDK_MANIFEST_HASH 40d5d08ff37a43b0b2deb641cc899d5356163db7)

add_custom_target(ARSDK_MKDIR
  COMMAND ${CMAKE_COMMAND} -E make_directory ${ARDRONESDK3_DEVEL_PATH})

add_custom_target(ARSDKBuildUtils ALL
  COMMENT "Building Parrot ARDroneSDK3 @ ${ARSDK_MANIFEST_HASH}"
  WORKING_DIRECTORY ${ARDRONESDK3_DEVEL_PATH}
  COMMAND curl -s https://storage.googleapis.com/git-repo-downloads/repo > ./repo
  COMMAND chmod a+x ./repo
  COMMAND echo "y" | ./repo init -u https://github.com/Parrot-Developers/arsdk_manifests.git -b ${ARSDK_MANIFEST_HASH} -m release.xml
  COMMAND ./repo sync
  # Patch dragon_build to enable build as root
  COMMAND cd ${ARDRONESDK3_DEVEL_PATH}/build/dragon_build && git reset --hard && git apply ${PROJECT_SOURCE_DIR}/patch/dragon_disable_root_check.patch
  COMMAND ./build.sh -p arsdk-native -t build-sdk -j
  # Populate the build results into the devel space
  COMMAND ${CMAKE_COMMAND} -E make_directory ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  COMMAND cp -rp ./out/Unix-base/staging/usr/include/* ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  COMMAND ${CMAKE_COMMAND} -E make_directory ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_LIB_DESTINATION}/${PROJECT_NAME}
  COMMAND cp -rp ./out/Unix-base/staging/usr/lib/* ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_LIB_DESTINATION}/${PROJECT_NAME}
  COMMAND ${CMAKE_COMMAND} -E make_directory ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_SHARE_DESTINATION}/${PROJECT_NAME}
  COMMAND cp -rp ./out/Unix-base/staging/usr/share/* ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_SHARE_DESTINATION}/${PROJECT_NAME}
  #COMMAND ${CMAKE_COMMAND} -E make_directory ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_BIN_DESTINATION}/${PROJECT_NAME}
  #COMMAND cp -rp ./out/Unix-base/staging/usr/bin/* ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_BIN_DESTINATION}/${PROJECT_NAME}
)
add_dependencies(ARSDKBuildUtils ARSDK_MKDIR)

#############
## Install ##
#############

#install(DIRECTORY ${ARDRONESDK3_PATH}/bin/
#  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
#  USE_SOURCE_PERMISSIONS
#)

install(DIRECTORY ${ARDRONESDK3_PATH}/include/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)

install(DIRECTORY ${ARDRONESDK3_PATH}/lib/
  DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}/parrot_arsdk
)

install(DIRECTORY ${ARDRONESDK3_PATH}/share/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

install(DIRECTORY ${ARDRONESDK3_PATH}/../etc/
  DESTINATION ${CATKIN_PACKAGE_ETC_DESTINATION}
)
