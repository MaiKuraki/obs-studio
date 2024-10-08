cmake_minimum_required(VERSION 3.28...3.30)

add_library(obs-dal-plugin MODULE)
add_library(OBS::dal-plugin ALIAS obs-dal-plugin)

target_sources(
  obs-dal-plugin
  PRIVATE
    CMSampleBufferUtils.h
    CMSampleBufferUtils.mm
    Defines.h
    Logging.h
    OBSDALDevice.h
    OBSDALDevice.mm
    OBSDALMachClient.h
    OBSDALMachClient.mm
    OBSDALObjectStore.h
    OBSDALObjectStore.mm
    OBSDALPlugIn.h
    OBSDALPlugIn.mm
    OBSDALPlugInInterface.h
    OBSDALPlugInInterface.mm
    OBSDALPlugInMain.mm
    OBSDALStream.h
    OBSDALStream.mm
)

set(_placeholder_location "${CMAKE_CURRENT_SOURCE_DIR}/../common/data/placeholder.png")

set_property(SOURCE "${_placeholder_location}" PROPERTY MACOSX_PACKAGE_LOCATION "Resources")
source_group("Resources" FILES "${_placeholder_location}")

target_link_libraries(
  obs-dal-plugin
  PRIVATE
    OBS::mach-protocol
    "$<LINK_LIBRARY:FRAMEWORK,Cocoa.framework>"
    "$<LINK_LIBRARY:FRAMEWORK,CoreMedia.framework>"
    "$<LINK_LIBRARY:FRAMEWORK,CoreMediaIO.framework>"
    "$<LINK_LIBRARY:FRAMEWORK,CoreVideo.framework>"
    "$<LINK_LIBRARY:FRAMEWORK,IOSurface.framework>"
)

set_target_properties_obs(
  obs-dal-plugin
  PROPERTIES OUTPUT_NAME obs-mac-virtualcam
             FOLDER plugins
             PREFIX ""
             BUNDLE TRUE BUNDLE_EXTENSION plugin MACOSX_BUNDLE_INFO_PLIST
                         "${CMAKE_CURRENT_SOURCE_DIR}/cmake/macos/Info.plist.in"
             LIBRARY_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/../../"
)

set_property(TARGET obs-dal-plugin PROPERTY OSX_ARCHITECTURES x86_64 arm64)

string(TIMESTAMP CURRENT_YEAR "%Y")
set_target_xcode_properties(
  obs-dal-plugin
  PROPERTIES CLANG_ENABLE_OBJC_ARC YES
             CLANG_WARN_SUSPICIOUS_IMPLICIT_CONVERSION YES
             GCC_WARN_SHADOW YES
             PRODUCT_NAME obs-mac-virtualcam
             PRODUCT_BUNDLE_IDENTIFIER com.obsproject.obs-mac-virtualcam
             CURRENT_PROJECT_VERSION ${OBS_BUILD_NUMBER}
             MARKETING_VERSION ${OBS_VERSION_CANONICAL}
             GENERATE_INFOPLIST_FILE YES
             INFOPLIST_KEY_CFBundleDisplayName obs-mac-virtualcam
             INFOPLIST_KEY_NSHumanReadableCopyright "(c) 2020-${CURRENT_YEAR} John Boiles, Patrick Heyer, Sebastian Beckmann"
)
