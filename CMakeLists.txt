cmake_minimum_required(VERSION 3.22)

#
# This file is generated only once,
# and is not re-generated if converter is called multiple times.
#
# User is free to modify the file as much as necessary
#

# Setup compiler settings
set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_C_EXTENSIONS ON)


# Define the build type
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Debug")
endif()

# Set the project name
set(CMAKE_PROJECT_NAME STM32F401CCU6_W5500)

# Include toolchain file
include("cmake/gcc-arm-none-eabi.cmake")

# Enable compile command to ease indexing with e.g. clangd
set(CMAKE_EXPORT_COMPILE_COMMANDS TRUE)

# Enable CMake support for ASM and C languages
enable_language(C ASM)

# Core project settings
project(${CMAKE_PROJECT_NAME})
message("Build type: " ${CMAKE_BUILD_TYPE})

# Create an executable object type
add_executable(${CMAKE_PROJECT_NAME})

# Add STM32CubeMX generated sources
add_subdirectory(cmake/stm32cubemx)

# Link directories setup
target_link_directories(${CMAKE_PROJECT_NAME} PRIVATE
    # Add user defined library search paths
)

# Add sources to executable
target_sources(${CMAKE_PROJECT_NAME} PRIVATE
    # Add user sources here
)

# Add include paths
target_include_directories(${CMAKE_PROJECT_NAME} PRIVATE
    # Add user defined include paths
)

# Add project symbols (macros)
target_compile_definitions(${CMAKE_PROJECT_NAME} PRIVATE
    # Add user defined symbols
)

# Add linked libraries
target_link_libraries(${CMAKE_PROJECT_NAME}
    stm32cubemx

    # Add user defined libraries
)

# Пути для ELF, BIN и HEX
set(ELF_FILE ${CMAKE_BINARY_DIR}/${CMAKE_PROJECT_NAME}.elf)
set(BIN_FILE ${CMAKE_BINARY_DIR}/${CMAKE_PROJECT_NAME}.bin)
set(HEX_FILE ${CMAKE_BINARY_DIR}/${CMAKE_PROJECT_NAME}.hex)

# Добавление команды для генерации .bin и .hex
add_custom_command(TARGET ${CMAKE_PROJECT_NAME} POST_BUILD
    COMMAND ${CMAKE_OBJCOPY} -O binary ${ELF_FILE} ${BIN_FILE}
    COMMAND ${CMAKE_OBJCOPY} -O ihex ${ELF_FILE} ${HEX_FILE}
    COMMENT "Generating binary and hex files"
)

# Определение пути для flash.jlink в корневой папке
set(JLINK_FILE ${CMAKE_SOURCE_DIR}/flash.jlink)

# Создание содержимого файла flash.jlink
file(WRITE ${JLINK_FILE}
"r\n\
loadbin ${BIN_FILE}, 0x08000000\n\
r\n\
g\n\
q\n"
)

# Добавление команды для генерации flash.jlink
add_custom_command(TARGET ${CMAKE_PROJECT_NAME} POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E echo "Generated ${JLINK_FILE}"
    DEPENDS ${BIN_FILE}
    COMMENT "Generating J-Link script file: flash.jlink"
)