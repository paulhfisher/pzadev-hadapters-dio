# cmake_minimum_required(VERSION 3.16)
cmake_minimum_required(VERSION 3.16)

#Import pico-sdk librairie
set(FETCHCONTENT_QUIET FALSE)
set(PICO_SDK_FETCH_FROM_GIT ON)
set(PICO_SDK_FETCH_FROM_GIT_PATH ${CMAKE_CURRENT_LIST_DIR}/pico-sdk)

# Import liblightmodbus librairie
set (LIBLIGHTMODBUS_FETCH_FROM_GIT ON)
set(LIBLIGHTMODBUS_FROM_GIT_PATH ${CMAKE_CURRENT_LIST_DIR}/liblightmodbus)

include(pico_sdk_import.cmake)
include(liblightmodbus_import.cmake)
include(${LIBLIGHTMODBUS_PATH}/lightmodbusConfig.cmake)
project(firmware C CXX ASM)

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)
set(PICO_EXAMPLES_PATH ${PROJECT_SOURCE_DIR})

# to use the sdk
pico_sdk_init()

set(PROJECT_SOURCES
    ${CMAKE_CURRENT_LIST_DIR}/src/firmware.c
)
# if (TARGET tinyusb_device)
add_executable(${CMAKE_PROJECT_NAME} ${PROJECT_SOURCES})

target_include_directories(${CMAKE_PROJECT_NAME} PUBLIC
    ${liblightmodbus_SOURCE_DIR}/include/lightmodbus
    ${CMAKE_CURRENT_LIST_DIR}/inc/
)

# pull in common dependencies
target_link_libraries(firmware pico_stdlib lightmodbus::lightmodbus)

# enable usb output, disable uart output
pico_enable_stdio_usb(${CMAKE_PROJECT_NAME}  1)
pico_enable_stdio_uart(${CMAKE_PROJECT_NAME}  1)

# create binaries
pico_add_extra_outputs(${CMAKE_PROJECT_NAME} )

# add url via pico_set_program_url
# example_auto_set_url(${CMAKE_PROJECT_NAME} )

# elseif(PICO_ON_DEVICE)
#     message(WARNING "not building ${CMAKE_PROJECT_NAME}  because TinyUSB submodule is not initialized in the SDK")
# endif()

# USB definition
add_definitions(-DUSBD_VID=0x16C0) # ID_VENDOR_ID
add_definitions(-DUSBD_PID=0x05E1) # ID_MODEL_ID
add_definitions(-DUSBD_MANUFACTURER="panduza.io") # ID_VENDOR
add_definitions(-DUSBD_PRODUCT="dio-modbus") # ID_MODEL