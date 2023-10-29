# stm32-ssd1306_lib

* forked: https://github.com/afiskon/stm32-ssd1306

## Example

1. создать папку ${CMAKE_CURRENT_SOURCE_DIR}/libs
2. создать в ${CMAKE_CURRENT_SOURCE_DIR}/libs файл CMakeLists.txt

~~~cmake
add_subdirectory(ssd1306)
~~~

3. создать директорию ssd1306 и скопировать файлы библиотеки в ssd1306
4. Добавить главный сценарий CMake в CMakeLists.txt и CMakeLists_template.txt

~~~cmake
#THIS FILE IS AUTO GENERATED FROM THE TEMPLATE! DO NOT CHANGE!
set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_SYSTEM_VERSION 1)
cmake_minimum_required(VERSION 3.23)

# specify cross-compilers and tools
set(CMAKE_C_COMPILER arm-none-eabi-gcc)
set(CMAKE_CXX_COMPILER arm-none-eabi-g++)
set(CMAKE_ASM_COMPILER arm-none-eabi-gcc)
set(CMAKE_AR arm-none-eabi-ar)
set(CMAKE_OBJCOPY arm-none-eabi-objcopy)
set(CMAKE_OBJDUMP arm-none-eabi-objdump)
set(SIZE arm-none-eabi-size)
set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)

# project settings
project(test C CXX ASM)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_C_STANDARD 11)

################################
SET(LIBS_DIR ${CMAKE_CURRENT_SOURCE_DIR}/libs)
message(STATUS "Build PROJECT_NAME: ${PROJECT_NAME}")
################################

#Uncomment for hardware floating point
#add_compile_definitions(ARM_MATH_CM4;ARM_MATH_MATRIX_CHECK;ARM_MATH_ROUNDING)
#add_compile_options(-mfloat-abi=hard -mfpu=fpv4-sp-d16)
#add_link_options(-mfloat-abi=hard -mfpu=fpv4-sp-d16)

#Uncomment for software floating point
#add_compile_options(-mfloat-abi=soft)

add_compile_options(-mcpu=cortex-m3 -mthumb -mthumb-interwork)
add_compile_options(-ffunction-sections -fdata-sections -fno-common -fmessage-length=0)

# uncomment to mitigate c++17 absolute addresses warnings
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-register")

# Enable assembler files preprocessing
add_compile_options($<$<COMPILE_LANGUAGE:ASM>:-x$<SEMICOLON>assembler-with-cpp>)

if ("${CMAKE_BUILD_TYPE}" STREQUAL "Release")
    message(STATUS "Maximum optimization for speed")
    add_compile_options(-Ofast)
elseif ("${CMAKE_BUILD_TYPE}" STREQUAL "RelWithDebInfo")
    message(STATUS "Maximum optimization for speed, debug info included")
    add_compile_options(-Ofast -g)
elseif ("${CMAKE_BUILD_TYPE}" STREQUAL "MinSizeRel")
    message(STATUS "Maximum optimization for size")
    add_compile_options(-Os)
else ()
    message(STATUS "Minimal optimization, debug info included")
    add_compile_options(-Og -g)
endif ()

################################
include_directories("${LIBS_DIR}/ssd1306/src/inc" "${LIBS_DIR}/ssd1306/src/sample/inc")
################################

include_directories(Core/Inc Drivers/STM32F1xx_HAL_Driver/Inc Drivers/STM32F1xx_HAL_Driver/Inc/Legacy Drivers/CMSIS/Device/ST/STM32F1xx/Include Drivers/CMSIS/Include)

add_definitions(-DDEBUG -DUSE_HAL_DRIVER -DSTM32F103xB)

file(GLOB_RECURSE SOURCES "Core/*.*" "Drivers/*.*")

set(MODBUS ${LIBS_DIR}/modbus/src)
file(GLOB_RECURSE MODBUS_SOURCES "${MODBUS}/*.c")
include_directories("${MODBUS}/ascii" "${MODBUS}/include" "${MODBUS}/port" "${MODBUS}/rtu" "${MODBUS}/tcp")

set(LINKER_SCRIPT ${CMAKE_SOURCE_DIR}/STM32F103VBTX_FLASH.ld)

add_link_options(-Wl,-gc-sections,--print-memory-usage,-Map=${PROJECT_BINARY_DIR}/${PROJECT_NAME}.map)
add_link_options(-mcpu=cortex-m3 -mthumb -mthumb-interwork)
add_link_options(-T ${LINKER_SCRIPT})

################################
add_subdirectory(${LIBS_DIR})
################################

add_executable(${PROJECT_NAME}.elf ${SOURCES} ${MODBUS_SOURCES} ${LINKER_SCRIPT})

################################
TARGET_LINK_LIBRARIES(${PROJECT_NAME}.elf
        ssd1306_lib
        )
################################

set(HEX_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.hex)
set(BIN_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.bin)

add_custom_command(TARGET ${PROJECT_NAME}.elf POST_BUILD
        COMMAND ${CMAKE_OBJCOPY} -Oihex $<TARGET_FILE:${PROJECT_NAME}.elf> ${HEX_FILE}
        COMMAND ${CMAKE_OBJCOPY} -Obinary $<TARGET_FILE:${PROJECT_NAME}.elf> ${BIN_FILE}
        COMMENT "Building ${HEX_FILE}
Building ${BIN_FILE}")

################################
#TARGET_LINK_LIBRARIES(${PROJECT_NAME}.elf
#        )
################################
~~~

5. зоздать Core/Inc/ssd1306_conf.h

~~~c
/**
 * Private configuration file for the SSD1306 library.
 * This example is configured for STM32F0, I2C and including all fonts.
 */

#ifndef __SSD1306_CONF_H__
#define __SSD1306_CONF_H__

// Choose a microcontroller family
//#define STM32F0
#define STM32F1
//#define STM32F4
//#define STM32L0
//#define STM32L1
//#define STM32L4
//#define STM32F3
//#define STM32H7
//#define STM32F7
//#define STM32G0

// Choose a bus
#define SSD1306_USE_I2C
//#define SSD1306_USE_SPI

// I2C Configuration
#define SSD1306_I2C_PORT        hi2c1
#define SSD1306_I2C_ADDR        (0x3C << 1)

// SPI Configuration
//#define SSD1306_SPI_PORT        hspi1
//#define SSD1306_CS_Port         OLED_CS_GPIO_Port
//#define SSD1306_CS_Pin          OLED_CS_Pin
//#define SSD1306_DC_Port         OLED_DC_GPIO_Port
//#define SSD1306_DC_Pin          OLED_DC_Pin
//#define SSD1306_Reset_Port      OLED_Res_GPIO_Port
//#define SSD1306_Reset_Pin       OLED_Res_Pin

// Mirror the screen if needed
// #define SSD1306_MIRROR_VERT
// #define SSD1306_MIRROR_HORIZ

// Set inverse color if needed
// # define SSD1306_INVERSE_COLOR

// Include only needed fonts
#define SSD1306_INCLUDE_FONT_6x8
#define SSD1306_INCLUDE_FONT_7x10
#define SSD1306_INCLUDE_FONT_11x18
#define SSD1306_INCLUDE_FONT_16x26

#define SSD1306_INCLUDE_FONT_16x24

// The width of the screen can be set using this
// define. The default value is 128.
// #define SSD1306_WIDTH           64

// If your screen horizontal axis does not start
// in column 0 you can use this define to
// adjust the horizontal offset
// #define SSD1306_X_OFFSET

// The height can be changed as well if necessary.
// It can be 32, 64 or 128. The default value is 64.
// #define SSD1306_HEIGHT          64

#endif /* __SSD1306_CONF_H__ */
~~~

6. main.c

~~~c
/* USER CODE BEGIN Includes */
#include "ssd1306.h"
#include "ssd1306_images.h"
// ...

int main(void) {
// ...

    /* USER CODE BEGIN 2 */
    ssd1306_Init();
    ssd1306_Fill(Black);
    ssd1306_DrawBitmap(32, 0, github_logo_64x64, 64, 64, White);
    ssd1306_UpdateScreen();
    HAL_Delay(3000);

    // ...
    while (1) {
        ssd1306_Fill(Black);
        ssd1306_SetCursor(2, 0);
        ssd1306_WriteString("Hello world", Font_7x10, White);
        ssd1306_UpdateScreen();
        // ...
    }
// ..
}
~~~
