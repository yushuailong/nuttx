=======================
USB Device-Side Drivers
=======================

-  ``include/nuttx/usb/usbdev.h``. All structures and APIs
   needed to work with USB device-side drivers are provided in
   this header file.

-  ``include/nuttx/usb/usbdev_trace.h``. Declarations needed
   to work with the NuttX USB device driver trace capability. That
   USB trace capability is detailed in :ref:`separate
   document <usbtrace>`.

-  ``struct usbdev_s``. Each USB device controller driver must
   implement an instance of ``struct usbdev_s``. This structure is
   defined in ``include/nuttx/usb/usbdev.h``.

   **Examples**: ``arch/arm/src/dm320/dm320_usbdev.c``,
   ``arch/arm/src/lpc17xx_40xx/lpc17_40_usbdev.c``,
   ``arch/arm/src/lpc214x/lpc214x_usbdev.c``,
   ``arch/arm/src/lpc313x/lpc313x_usbdev.c``, and
   ``arch/arm/src/common/stm32/stm32_usbdev_m3m4_v1.c``.

-  ``struct usbdevclass_driver_s``. Each USB device class
   driver must implement an instance of
   ``struct usbdevclass_driver_s``. This structure is also defined
   in ``include/nuttx/usb/usbdev.h``.

   **Examples**: ``drivers/usbdev/pl2303.c`` and
   ``drivers/usbdev/usbmsc.c``

-  **Binding USB Device-Side Drivers**. USB device-side controller
   drivers are not normally directly accessed by user code, but
   are usually bound to another, higher level USB device class
   driver. The class driver is then configured to export the USB
   device functionality. In general, the binding sequence is:

   #. Each USB device class driver includes an initialization
      entry point that is called from the application at
      initialization time.

      **Examples**: The function ``usbdev_serialinitialize()`` in
      the file ``drivers/usbdev/pl2303.c`` and the function
      in the file ``drivers/usbdev/usbmsc.c``

   #. These initialization functions called the driver API,
      ``usbdev_register()``. This driver function will *bind* the
      USB class driver to the USB device controller driver,
      completing the initialization.

Control OUT Data Buffering
==========================

``CONFIG_USBDEV_SETUP_MAXDATASIZE`` configures the buffer used by supporting
device controllers for the data stage of host-to-device control requests.
The default is 64 bytes.  Some controllers retain a larger minimum buffer
to preserve their existing behavior.

The STM32H7 OTG device driver assembles multiple EP0 OUT packets before
dispatching a control request to the class driver.  Set this option to at
least the largest control OUT payload expected by the class driver; for
example, use 2048 for a class driver accepting 2048-byte control writes.
The buffer size need not be a multiple of four or of the EP0 packet size.
A short packet, including a zero-length packet, ends the data stage early.
The class driver must check the received length against its protocol.

Increasing this option allocates more RAM in the controller driver.  It
does not change the EP0 maximum packet size or add multi-packet support to
controllers that do not implement it.

