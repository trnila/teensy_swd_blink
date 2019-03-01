SET(CMAKE_SYSTEM_NAME Generic)
SET(CMAKE_SYSTEM_VERSION 1)
cmake_minimum_required(VERSION 3.7)

# specify the cross compiler
SET(CMAKE_C_COMPILER_WORKS 1)
SET(CMAKE_C_COMPILER arm-none-eabi-gcc)
SET(CMAKE_CXX_COMPILER arm-none-eabi-g++)
set(AS arm-none-eabi-as)
set(AR arm-none-eabi-ar)
set(OBJCOPY arm-none-eabi-objcopy)
set(OBJDUMP arm-none-eabi-objdump)
set(SIZE arm-none-eabi-size)


SET(LINKER_SCRIPT ${CMAKE_SOURCE_DIR}/k64f.ld)

SET(FPU_FLAGS "-mcpu=cortex-m4 -mfpu=fpv4-sp-d16 -mfloat-abi=hard")
SET(ANOTHER "-DCPU_MK64FN1M0VLL12 -DCPU_MK64FN1M0VLL12_cm4 -DFRDM_K64F -DFREEDOM -DSDK_DEBUGCONSOLE=0 -DCR_INTEGER_PRINTF -DPRINTF_FLOAT_ENABLE=0 -D__MCUXPRESSO")
SET(COMMON_FLAGS "-mcpu=cortex-m3 ${FPU_FLAGS} ${ANOTHER} -mthumb -mthumb-interwork -ffunction-sections -fdata-sections -g -fno-common -fmessage-length=0 -specs=nosys.specs -specs=nano.specs")


SET(CMAKE_CXX_FLAGS_INIT "${COMMON_FLAGS} -std=c++11")
SET(CMAKE_C_FLAGS_INIT "${COMMON_FLAGS} -std=gnu99")
SET(CMAKE_EXE_LINKER_FLAGS_INIT "-Wl,-gc-sections,--print-memory-usage -T ${LINKER_SCRIPT}")

PROJECT(teensy_blink C CXX ASM)
set(CMAKE_CXX_STANDARD 11)

add_executable(${PROJECT_NAME}.elf ${LINKER_SCRIPT}
        board/
        board/peripherals.c
        board/peripherals.h
        board/src
        board/src/board.h
        board/src/clock_config.h
        board/src/board.c
        board/src/clock_config.c
        board/pin_mux.h
        board/pin_mux.c

        CMSIS/
        CMSIS/arm_common_tables.h
        CMSIS/arm_const_structs.h
        CMSIS/arm_math.h
        CMSIS/cmsis_armcc.h
        CMSIS/cmsis_armclang.h
        CMSIS/cmsis_compiler.h
        CMSIS/cmsis_gcc.h
        CMSIS/cmsis_iccarm.h
        CMSIS/cmsis_version.h
        CMSIS/core_armv8mbl.h
        CMSIS/core_armv8mml.h
        CMSIS/mpu_armv7.h
        CMSIS/mpu_armv8.h
        CMSIS/core_cm4.h
        CMSIS/system_MK64F12.c
        CMSIS/fsl_device_registers.h
        CMSIS/MK64F12.h
        CMSIS/MK64F12_features.h
        CMSIS/system_MK64F12.h
        drivers/fsl_ftfx_cache.c
        drivers/fsl_ftfx_controller.c
        drivers/fsl_ftfx_flash.c
        drivers/fsl_ftfx_flexnvm.c
        drivers/fsl_flash.h
        drivers/fsl_ftfx_adapter.h
        drivers/fsl_ftfx_cache.h
        drivers/fsl_ftfx_controller.h
        drivers/fsl_ftfx_features.h
        drivers/fsl_ftfx_flash.h
        drivers/fsl_ftfx_flexnvm.h
        drivers/fsl_ftfx_utilities.h
        drivers/fsl_gpio.c
        drivers/fsl_gpio.h
        drivers/fsl_clock.c
        drivers/fsl_clock.h
        drivers/fsl_common.c
        drivers/fsl_common.h
        drivers/fsl_uart.c
        drivers/fsl_uart.h
        drivers/fsl_port.h
        drivers/fsl_smc.c
        drivers/fsl_smc.h
        startup/startup_mk64f12.c

        utilities/fsl_io.c
        utilities/fsl_io.h
        utilities/fsl_log.c
        utilities/fsl_log.h
        utilities/fsl_str.c
        utilities/fsl_str.h
        utilities/fsl_debug_console.c
        utilities/fsl_debug_console.h
        utilities/fsl_debug_console_conf.h
        utilities/fsl_assert.c

        source/semihost_hardfault.c
        source/led_blinky.c
)

include_directories(board board/src drivers CMSIS utilities source)

set(HEX_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.hex)
set(BIN_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.bin)

add_custom_command(TARGET ${PROJECT_NAME}.elf POST_BUILD
        COMMAND ${CMAKE_OBJCOPY} -Oihex $<TARGET_FILE:${PROJECT_NAME}.elf> ${HEX_FILE}
        COMMAND ${CMAKE_OBJCOPY} -Obinary $<TARGET_FILE:${PROJECT_NAME}.elf> ${BIN_FILE}
        COMMENT "Building ${HEX_FILE}
Building ${BIN_FILE}")
