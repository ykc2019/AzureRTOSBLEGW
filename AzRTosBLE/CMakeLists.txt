#  Copyright (c) Microsoft Corporation. All rights reserved.
#  Licensed under the MIT License.

cmake_minimum_required (VERSION 3.11)

project (AzRTosBLE C ASM)
SET(CMAKE_ASM_FLAGS "-mcpu=cortex-m4")

azsphere_configure_tools(TOOLS_REVISION "21.01")
 add_compile_definitions(OSAI_AZURE_RTOS)

add_link_options(-specs=nano.specs -specs=nosys.specs)
set(MT3620_BASE_DIR mt3620_m4_software)
set(MT3620_SAMPLE_DIR ${MT3620_BASE_DIR}/MT3620_M4_Sample_Code)
set(MT3620_DRIVER_DIR ${MT3620_BASE_DIR}/MT3620_M4_DRIVER)
set(MT3620_M4_BSP_DIR ${MT3620_BASE_DIR}MT3620_M4_BSP)
set (APP_SRC
      ${MT3620_SAMPLE_DIR}/OS_HAL/src/os_hal_adc.c
        ${MT3620_SAMPLE_DIR}//OS_HAL/src/os_hal_dma.c
        ${MT3620_SAMPLE_DIR}//OS_HAL/src/os_hal_eint.c
    ${MT3620_SAMPLE_DIR}/OS_HAL/src/os_hal_gpio.c
    ${MT3620_SAMPLE_DIR}/OS_HAL/src/os_hal_gpioif.c
    ${MT3620_SAMPLE_DIR}/OS_HAL/src/os_hal_gpt.c
    ${MT3620_SAMPLE_DIR}/OS_HAL/src/os_hal_i2c.c
    ${MT3620_SAMPLE_DIR}/OS_HAL/src/os_hal_i2s.c
    ${MT3620_SAMPLE_DIR}/OS_HAL/src/os_hal_pwm.c
    ${MT3620_SAMPLE_DIR}/OS_HAL/src/os_hal_spim.c
    ${MT3620_SAMPLE_DIR}/OS_HAL/src/os_hal_uart.c
    ${MT3620_SAMPLE_DIR}/OS_HAL/src/os_hal_wdt.c
    app_threadx.c
    "rtcoremain.c"
    tx_initialize_low_level.S

    )

add_executable (${PROJECT_NAME} ${APP_SRC})

target_include_directories(${PROJECT_NAME} PUBLIC ${MT3620_SAMPLE_DIR}/OS_HAL/inc)
target_include_directories(${PROJECT_NAME} PUBLIC
                           ${MT3620_DRIVER_DIR}/MHAL/inc
                           ${MT3620_DRIVER_DIR}/HDL/inc
                          ${MT3620_M4_BSP_DIR}/MT3620_M4_BSP/CMSIS/include
                          ${MT3620_M4_BSP_DIR}/mt3620/inc
                           ${MT3620_M4_BSP_DIR}/printf)




add_subdirectory(${MT3620_DRIVER_DIR})

set(THREADX_ARCH "cortex_m4")
set(THREADX_TOOLCHAIN "gnu")
add_subdirectory(threadx)







target_link_libraries(${PROJECT_NAME} MT3620_M4_Driver azrtos::threadx)
set_target_properties (${PROJECT_NAME} PROPERTIES LINK_DEPENDS ${CMAKE_SOURCE_DIR}/linker.ld)

azsphere_target_add_image_package(${PROJECT_NAME})
