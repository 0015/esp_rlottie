# IDF component for using rLottie in ESP32 projects

## Disclaimer
**This project is a forked version of the Samsung/rlottie project. Therefore, the source code used in this project also follows the original license. For more details, check this out. (https://github.com/Samsung/rlottie/)**

### What is Lottie?
Lottie is a JSON-based animation file. Lottie allows designers to move animations as easily as images. It is small in size, works on any device, and can be freely resized without losing resolution.

### Why should we use Lottie?
Lottie has several advantages over other animation file formats:

1. Compact File Size
 * Lottie animations offer a significantly smaller file size compared to other formats such as GIF or MP4 while preserving high-quality visuals.
2. Scalable Vector Graphics
* Being vector-based, Lottie animations can be resized freely without losing resolution, allowing them to scale up or down effortlessly.
3. Cross-Platform Compatibility
* Lottie animations are easily integrated across multiple platforms and libraries, making them straightforward for developers to implement. They work seamlessly on iOS, Android, Web, and React Native without the need for any modifications.

### Then what is the rLottie? 

* Developed by: Samsung
* Features: rLottie (Rapid Lottie) is an optimized version of Lottie, primarily focused on performance improvements.
Designed to be lightweight and faster, particularly for use in environments with limited resources.
It also supports the JSON format for animations exported from Adobe After Effects.
While it maintains compatibility with Lottie animations, it emphasizes speed and efficiency, making it suitable for embedded systems or applications where performance is critical.

### rLottie Component for ESP32 Project
This rLottie component for an ESP32 project, which is designed to be integrated within the ESP-IDF (Espressif IoT Development Framework) environment. This component allows the rLottie animations to be played on the ESP32 by leveraging the LVGL (Light and Versatile Graphics Library).(https://docs.lvgl.io/master/index.html)

### Comprehensive Setup Guide for Using rLottie with LVGL on ESP32
This guide walks you through the process of setting up an ESP32 project with the rLottie component using LVGL, including preparing the display driver.

1. Setting Up ESP-IDF Environment:
* Ensure that the ESP-IDF is installed and set up correctly on your development machine.
* Follow the official ESP-IDF installation guide(https://idf.espressif.com/) to get started if you haven't already.

2. Preparing the Display Driver
* Each hardware device is different, so you need to prepare and ensure your display driver is ready to run LVGL in the IDF environment.
* Recommended Components:
  - espressif/esp_lvgl_port 
  - espressif/esp_lcd

3. Add LVGL component to your project
* This rLottie is a component for using LVGL's lottie player, so you must configure the LVGL environment.
If you add it through ESP Component Registry, it will be registered in managed_components folder, so cmake modification is not possible, so please put it directly in the component folder through Git. 
```
mkdir components
cd components
git clone https://github.com/lvgl/lvgl.git
```

4. Including rLottie Component:
* Clone the `esp_rlottie` component in the components directory of your ESP-IDF project. 
```
git clone https://github.com/0015/esp_rlottie.git
```
* The directory structure might look like this:
```
your_project/
├── components/
│   ├── esp_rlottie/
│   │   ├── rlottie/
│   │   │   ├── inc/
│   │   │   ├── src/
│   │   │   ├── CMakeLists.txt
│   │   │   └── ...
│   │   └── CMakeLists.txt
│   ├── lvgl/
│   │   ├── lvgl.h
│   │   ├── env_support/
│   │   │   ├── cmake/
│   │   │   │   ├── esp.cmake
│   │   └── CMakeLists.txt
├── main/
│   ├── main.c
│   └── CMakeLists.txt
├── CMakeLists.txt
└── sdkconfig
```

5. Configuration Files
   * LVGL `esp.cmake`
    Ensure that `esp_rlottie` is included in the idf_component_register REQUIRES list in `components/lvgl/env_support/cmake/esp.cmake`:
    ```
    idf_component_register(SRCS ${SOURCES} ${EXAMPLE_SOURCES} ${DEMO_SOURCES}
        INCLUDE_DIRS ${LVGL_ROOT_DIR} ${LVGL_ROOT_DIR}/src ${LVGL_ROOT_DIR}/../
                    ${LVGL_ROOT_DIR}/examples ${LVGL_ROOT_DIR}/demos
        REQUIRES esp_timer esp_rlottie)
    ```

   * To enable the Lottie library among the LVGL configuration 3rd Party Libraries in `sdkconfig` or `menuconfig` tool, you'll need to ensure that your project includes the necessary configuration options.
   ```
   # Enable LVGL
   CONFIG_LVGL_ENABLE=y

   # Enable 3rd Party Libraries in LVGL
   CONFIG_LVGL_USE_LOTTIE=y
   ```

6. Main Application Code
In your `main/main.c`, you need to register `lvgl` since `esp_rlottie` depends on it:
```
idf_component_register(
    SRCS "main.c"
    INCLUDE_DIRS "."
    REQUIRES lvgl
)
```

Here's a simplified `main.c`:

```
#include "lvgl.h"
...
const char battery[] = "Escape string in JSON";
...
void rlottie_display()
{
    lv_obj_t *scr = lv_screen_active();
    lv_obj_t *lottie = lv_rlottie_create_from_raw(scr, 100, 100, (const char *)battery);
    lv_obj_center(lottie);
}
```

## Memory Issues
When working with animations and graphics on the ESP32, memory management is critical due to the limited resources available on the microcontroller. The error in `multi_heap_internal_lock` typically indicates a memory allocation issue.
* Large memory allocations can lead to fragmentation, especially if there are many small allocations followed by large ones. The heap might have enough total free space, but not in contiguous blocks.
* The ESP32 has limited memory, and large objects (like high-resolution animations) can exhaust the available RAM.
* If the stack size for a task is too small, large allocations or deep recursion might cause a stack overflow.
* High-priority tasks or inappropriate use of locks can lead to deadlocks or priority inversion issues.

