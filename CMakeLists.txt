
cmake_minimum_required ( VERSION 3.0 )
set(CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS true)

include("cmake/HunterGate.cmake")
HunterGate(
    URL "https://github.com/ruslo/hunter/archive/v0.12.25.tar.gz"
    SHA1 "c8d7b703dd093ec1aa87333226ddf4617a8aa06c"
)

project ( JPEG VERSION "8.4.0" LANGUAGES C)

set(JPEGLIB_MAJOR ${PROJECT_VERSION_MAJOR})
set(JPEGLIB_MINOR ${PROJECT_VERSION_MINOR})
set(JPEGLIB_RELEASE ${PROJECT_VERSION_PATCH})
set(JPEGLIB_NAME libpng${PNGLIB_MAJOR}${PNGLIB_MINOR})
set(JPEGLIB_VERSION ${PNGLIB_MAJOR}.${PNGLIB_MINOR}.${PNGLIB_RELEASE})

include ( CheckIncludeFile )
check_include_file ( stddef.h HAVE_STDDEF_H )
check_include_file ( stdlib.h HAVE_STDLIB_H )
if ( WIN32 AND NOT CYGWIN )
  #improve? see jconfig.*
  set ( TWO_FILE_COMMANDLINE true )
  # jconfig.h
endif ( )
configure_file ( jconfig.h.cmake jconfig.h )

include_directories ( ${CMAKE_CURRENT_BINARY_DIR} )
# jconfig.h

set (JPEG_HEADERS jerror.h jmorecfg.h jpeglib.h ${CMAKE_CURRENT_BINARY_DIR}/jconfig.h )

set ( JPEG_SRC jmemnobs.c jaricom.c jcapimin.c jcapistd.c jcarith.c jccoefct.c jccolor.c 
  jcdctmgr.c jchuff.c jcinit.c jcmainct.c jcmarker.c jcmaster.c jcomapi.c jcparam.c 
  jcprepct.c jcsample.c jctrans.c jdapimin.c jdapistd.c jdarith.c jdatadst.c jdatasrc.c 
  jdcoefct.c jdcolor.c jddctmgr.c jdhuff.c jdinput.c jdmainct.c jdmarker.c jdmaster.c 
  jdmerge.c jdpostct.c jdsample.c jdtrans.c jerror.c jfdctflt.c jfdctfst.c jfdctint.c 
  jidctflt.c jidctfst.c jidctint.c jquant1.c jquant2.c jutils.c jmemmgr.c cderror.h 
  cdjpeg.h jdct.h jinclude.h jmemsys.h jpegint.h jversion.h transupp.h )

include_directories(${CMAKE_CURRENT_SOURCE_DIR})

add_library ( jpeg ${JPEG_SRC} ${JPEG_HEADERS} )

####
# Installation (https://github.com/forexample/package-example)

set(config_install_dir "lib/cmake/${PROJECT_NAME}")
set(include_install_dir "include")

set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")

set(version_config "${generated_dir}/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${generated_dir}/${PROJECT_NAME}Config.cmake")
set(targets_export_name "${PROJECT_NAME}Targets")
set(namespace "${PROJECT_NAME}::")

include(CMakePackageConfigHelpers)

# Note: PROJECT_VERSION is used as a VERSION
write_basic_package_version_file(
    "${version_config}" COMPATIBILITY SameMajorVersion
)

# Note: variable 'targets_export_name' used
configure_file("cmake/Config.cmake.in" "${project_config}" @ONLY)

install(
    TARGETS jpeg
    EXPORT "${targets_export_name}"
    LIBRARY DESTINATION "lib"
    ARCHIVE DESTINATION "lib"
    RUNTIME DESTINATION "bin"
    INCLUDES DESTINATION "${include_install_dir}"
)

install(
    FILES ${JPEG_HEADERS}
    DESTINATION "${include_install_dir}"
)

install(
    FILES "${project_config}" "${version_config}"
    DESTINATION "${config_install_dir}"
)

install(
    EXPORT "${targets_export_name}"
    NAMESPACE "${namespace}"
    DESTINATION "${config_install_dir}"
)
