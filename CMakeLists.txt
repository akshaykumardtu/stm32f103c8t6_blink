cmake_minimum_required(VERSION 2.8)

set(TARGET firmware.elf)
set(CMAKE_SHARED_LIBRARY_LINK_C_FLAGS "")
set(CMAKE_SHARED_LIBRARY_LINK_CXX_FLAGS "")

include_directories("./" "plib" "core_lib")
link_directories("./")

file(GLOB Project_SOURCES plib/*.c core_lib/*.c core_lib/*.S *.c)
file(GLOB Project_HEADER core_lib/*.h plib/*.h *.h)

set (CMAKE_C_COMPILER "/usr/bin/arm-linux-gnueabi-gcc") 
set (CMAKE_C_FLAGS "-Os -fdata-sections -ffunction-sections -Wl,--gc-sections -std=c99 -mcpu=cortex-m3 -mthumb")

add_custom_target(install
	COMMAND arm-linux-gnueabi-objcopy -j .text ${TARGET} ${TARGET}.bin -O binary
	COMMAND sudo stm32flash/stm32flash -w ${TARGET}.bin -v -g 0x0 /dev/ttyUSB3
  DEPENDS ${TARGET}
)

# Executable
add_executable(${TARGET} ${Project_SOURCES} ${Project_HEADERS})

target_link_libraries(${TARGET})
