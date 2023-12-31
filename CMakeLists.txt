cmake_minimum_required(VERSION 3.5)


project(AirportInspector VERSION 1.0 LANGUAGES CXX)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS   Widgets
                                                    Concurrent
                                                    Sql
                                                    Charts)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS  Widgets
                                                        Concurrent
                                                        Sql
                                                        Charts)
set(PROJECT_SOURCES
        main.cpp
        mainwindow.cpp
        mainwindow.h
        mainwindow.ui
        database.h
        database.cpp
        setup.h
        setup.cpp
        graphic.h
        graphic.cpp
        graphic.ui
)

if(${QT_VERSION_MAJOR} GREATER_EQUAL 6)
    qt_add_executable(${PROJECT_NAME}
        MANUAL_FINALIZATION
        ${PROJECT_SOURCES}
    )

# Define target properties for Android with Qt 6 as:
#    set_property(TARGET testQt APPEND PROPERTY QT_ANDROID_PACKAGE_SOURCE_DIR
#                 ${CMAKE_CURRENT_SOURCE_DIR}/android)
# For more information, see https://doc.qt.io/qt-6/qt-add-executable.html#target-creation
else()
    if(ANDROID)
        add_library(${PROJECT_NAME} SHARED
            ${PROJECT_SOURCES}
        )
# Define properties for Android with Qt 5 after find_package() calls as:
#    set(ANDROID_PACKAGE_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/android")
    else()
        add_executable(${PROJECT_NAME}
            ${PROJECT_SOURCES}
        )
    endif()
endif()

if(WIN32)    # Для Windows
    target_link_directories(${PROJECT_NAME} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/PG_Libs)
    target_link_libraries(${PROJECT_NAME} PRIVATE   Qt${QT_VERSION_MAJOR}::Widgets
                                                    Qt${QT_VERSION_MAJOR}::Concurrent
                                                    Qt${QT_VERSION_MAJOR}::Sql
                                                    Qt${QT_VERSION_MAJOR}::Charts
                                                    libcrypto-3-x64
                                                    libiconv-2
                                                    libintl-9
                                                    libpq
                                                    libssl-3-x64)
#    MESSAGE( STATUS "File copy: " "${CMAKE_CURRENT_SOURCE_DIR}/config.ini" )
#    MESSAGE( STATUS "copy to...: " "${CMAKE_CURRENT_BINARY_DIR}" )

#    file(COPY ${CMAKE_CURRENT_SOURCE_DIR}/config.ini
#        DESTINATION ${CMAKE_CURRENT_BINARY_DIR})

#    add_custom_command(
#        TARGET ${PROJECT_NAME} POST_BUILD                   # PRE_BUILD | PRE_LINK | POST_BUILD
#        COMMAND ${CMAKE_COMMAND} -E copy_if_different       # which executes "cmake - E copy_if_different..."
#                "${CMAKE_CURRENT_SOURCE_DIR}/config.ini"    # <--this is in-file
#                "${CMAKE_CURRENT_BINARY_DIR}"               # <--this is out-file path
#        COMMAND_EXPAND_LISTS
#        COMMENT "-= Copying config.ini file to work folder =-")
elseif(UNIX)
    target_link_libraries(${PROJECT_NAME} PRIVATE   Qt${QT_VERSION_MAJOR}::Widgets
                                                    Qt${QT_VERSION_MAJOR}::Concurrent
                                                    Qt${QT_VERSION_MAJOR}::Sql
                                                    Qt${QT_VERSION_MAJOR}::Charts)
else()
    message(FATAL_ERROR "Unknow OS")
endif()


set_target_properties(${PROJECT_NAME} PROPERTIES
    OUTPUT_NAME "airpins"
    MACOSX_BUNDLE_GUI_IDENTIFIER my.example.com
    MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION}
    MACOSX_BUNDLE_SHORT_VERSION_STRING ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}
    MACOSX_BUNDLE TRUE
    WIN32_EXECUTABLE TRUE
)

if(QT_VERSION_MAJOR EQUAL 6)
    qt_finalize_executable(${PROJECT_NAME})
endif()