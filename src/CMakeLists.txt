cmake_minimum_required(VERSION 3.15)

project(SCodes LANGUAGES CXX)
option(SCODES_DEBUG OFF)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(ZXING_READERS ON)
message("SCodes Build Type " ${CMAKE_BUILD_TYPE})
if (NOT DEFINED BUILD_SHARED_LIBS)
    set(BUILD_SHARED_LIBS ON)
endif()

set(BUILD_UNIT_TESTS OFF)
set(BUILD_BLACKBOX_TESTS OFF)
set(ZXING_EXAMPLES OFF)
add_subdirectory(zxing-cpp)

find_package(Qt6 REQUIRED COMPONENTS Core Multimedia Concurrent Quick)

file(GLOB SOURCE_CPP
    "*.cpp"
)


add_library(${PROJECT_NAME} STATIC ${SOURCE_CPP})
target_sources(${PROJECT_NAME} PRIVATE private/debug.h)
set_target_properties(${PROJECT_NAME} PROPERTIES
    QT_QML_MODULE_VERSION 1.0
    QT_QML_MODULE_URI com.scythestudio.scodes

)
if(SCODES_DEBUG)
    message("${PROJECT_NAME} debug messages enabled: ${SS_SCODES_DEBUG}")
    target_compile_definitions(${PROJECT_NAME} PUBLIC SCODES_DEBUG)
endif()


target_link_libraries(${PROJECT_NAME} PUBLIC
    ZXing
    Qt::Core
    Qt::Multimedia
    Qt::Concurrent
    Qt::Quick
)
target_include_directories(${PROJECT_NAME} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_include_directories(${PROJECT_NAME} PRIVATE "${CMAKE_CURRENT_SOURCE_DIR}/private")

if (ANDROID)
    if(QT_VERSION_MAJOR EQUAL 5)
        find_package(Qt${QT_VERSION_MAJOR} COMPONENTS AndroidExtras REQUIRED)
        target_link_libraries(${PROJECT_NAME} PUBLIC Qt${QT_VERSION_MAJOR}::AndroidExtras)
    else()
        target_link_libraries(${PROJECT_NAME} PUBLIC Qt::CorePrivate)
    endif()

    #target_link_libraries(${PROJECT_NAME} PUBLIC EGL GLESv2)
endif()
