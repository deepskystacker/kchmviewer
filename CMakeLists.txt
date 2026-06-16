cmake_minimum_required(VERSION 3.16)

# Project name
PROJECT( kchmviewer )

# Init cmake
set( CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/")

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTORCC ON)

# Necessary packages
find_package(Qt6 REQUIRED COMPONENTS Core Widgets Network PrintSupport DBus WebEngineWidgets Xml)
find_package(libzip REQUIRED)
FIND_PACKAGE( Libchm REQUIRED)

# KDE Frameworks (optional; enables USE_KDE build)
option(USE_KDE "Build with KDE Frameworks integration" ON)

if(USE_KDE)
        find_package(KF6 QUIET COMPONENTS I18n WidgetsAddons)
        if(NOT KF6_FOUND)
                find_package(KF5 QUIET COMPONENTS I18n WidgetsAddons)
        endif()
        if(KF6_FOUND OR KF5_FOUND)
                add_definitions(-DUSE_KDE)
                if(KF6_FOUND)
                        MESSAGE(STATUS "Building with KDE Frameworks 6")
                else()
                        MESSAGE(STATUS "Building with KDE Frameworks 5")
                endif()
        else()
                MESSAGE(STATUS "KDE Frameworks not found; building without KDE support")
        endif()
endif()

# libzip
FIND_PATH(LIBZIP_INCLUDE_DIR zip.h)
FIND_LIBRARY(LIBZIP_LIBRARY NAMES zip)

IF (LIBZIP_INCLUDE_DIR AND LIBZIP_LIBRARY)
	MESSAGE(STATUS "Found libzip: ${LIBZIP_LIBRARY}")
	MESSAGE(STATUS "Found zip.h: ${LIBZIP_INCLUDE_DIR}")
ELSE ()
	MESSAGE(FATAL_ERROR "Could not find libzip. Please install libzip and libzip-devel packages")
ENDIF ()

# Set compilation params
INCLUDE_DIRECTORIES(
		${CMAKE_SOURCE_DIR}/src
		${CMAKE_SOURCE_DIR}/lib/libebook )

target_include_directories(kchmviewer AFTER SYSTEM
  PRIVATE ${LIBZIP_INCLUDE_DIR} ${LIBCHM_INCLUDE_DIR}
  )
if(NOT DEFINED LOCALE_INSTALL_DIR)
	set(LOCALE_INSTALL_DIR share/locale)
endif()

# Run in those subdirectories
ADD_SUBDIRECTORY( lib/libebook )
ADD_SUBDIRECTORY( src )
ADD_SUBDIRECTORY( po )
ADD_SUBDIRECTORY( packages )
