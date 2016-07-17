Substantial changes from USB Developers Package
-----------------------------------------------

  - Includes for driverlib changed from `#include "driverlib.h"` 
    to `#include <msp430-driverlib/MSP430F5xx_6xx/driverlib.h>`. This
    is done to make the library compatible a cmake based build system 
    acting on pre-compiled, statically linked libraries. Note that this
    approach has many other downsides, not the least of which is that 
    link-time optimization is far less effective than compile-time 
    optimization.
        
  - `hal.h` includes eliminated. The content of that layer is left to 
    the application, which must ensure that the functions performed by 
    that layer (IO port configuration and Clock systtem initialization)
    are done by the application code.
    
  - There is a strange `__no_init` preprocessor #define in
    usb.h and usb.c. This has been replaced with `__no_init_usb` in 
    order to avoid the compiler warnings it produces. Whether or not
    this is a valid change is not definitively known.
    
  - The USB_API code seems to be arranged under the assumption that all 
    code is compiled in one go. Many function calls would result by 
    having multiple libraries linked together after compilation. In 
    order to accomodate this structure (or minimize the changes 
    necessary to accomodate it in the future), the cmake-compatible
    library defined includes all USB_API and associated source files, 
    including application specific USB_config. Related functions in 
    the driverlib which used in critical places and are small enough 
    to be inlined without increasing their footprint relative to the 
    cost of the function call have been moved from their C files to 
    the corresponding header files to be inlined.
    
  - The content of USB_app is discarded in favor of a custom implementation
    in peripherals/usb_impl.{c/h} and peripherals/usb_handlers.{c/h}, 
    possibly to be based on the content of USB_app/usbConstructs.{c/h} 
    and USB_app/usbEventHandling.c. Similarly, USB_config/UsbIsr.c is 
    moved into peripherals/usb_handlers.{c/h}, in-line with the pattern
    for interrupt handler functions. 
    
    