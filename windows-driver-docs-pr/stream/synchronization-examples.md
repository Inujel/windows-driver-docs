---
title: Synchronization Examples
author: windows-driver-content
description: Synchronization Examples
MS-HAID:
- 'strmini-design\_fe251cde-15c5-4607-8a49-c050ae9d6bd5.xml'
- 'stream.synchronization\_examples'
MSHAttr:
- 'PreferredSiteName:MSDN'
- 'PreferredLib:/library/windows/hardware'
ms.assetid: b9290fab-8213-4083-bda5-0e6c2af737a6
keywords: ["Stream.sys class driver WDK Windows 2000 Kernel , synchronization", "streaming minidrivers WDK Windows 2000 Kernel , synchronization", "minidrivers WDK Windows 2000 Kernel Streaming , synchronization", "synchronization WDK streaming minidriver"]
---

# Synchronization Examples


## <a href="" id="ddk-synchronization-examples-ksg"></a>


The following examples illustrate what a minidriver needs to do regarding synchronization and includes examples of when synchronization should not be used:

-   **Example One: Minidriver With A Functioning ISR**

    If stream class synchronization is turned on, all minidriver entry points are called at raised IRQL, using [**KeSynchronizeExecution**](https://msdn.microsoft.com/library/windows/hardware/ff553302), which means that the IRQ level of the adapter and all lower IRQ levels are masked when the minidriver is executing its code. Therefore, it is imperative that the minidriver does only a small amount of work in this mode.

    The minidriver should not run code that typically takes more than 10 to 20 microseconds at raised IRQL. If you use the debug build of *stream.sys*, the stream class logs the amount of time spent at raised IRQL and asserts if the driver is spending too much time there. If the minidriver simply needs to program hardware DMA registers for a request, or just needs to read ports in its ISR, it is usually acceptable to do all of its processing at raised IRQL.

    If the minidriver needs to do processing that takes more than a few microseconds, such as a minidriver that transfers data through PIO, the minidriver should use [**StreamClassCallAtNewPriority**](https://msdn.microsoft.com/library/windows/hardware/ff568230) to schedule a DISPATCH\_LEVEL callback. In the callback, the minidriver can take up to around 1/2 to 1 millisecond to do its processing. One thing to remember when in this mode is that the DISPATCH\_LEVEL callback is *not* synchronized with the ISR.

    This lack of synchronization is not a problem if the hardware remains stable when the minidriver accesses resources (for example, ports or a queue) during the callback as well as in the ISR. But if instability could be a problem, the minidriver must use **StreamClassCallAtNewPriority** to schedule a HIGH priority callback where the DISPATCH\_LEVEL callback touches resources that are shared with the resources used by the ISR.

    Note that a HIGH priority callback is equivalent to calling **KeSynchronizeExecution**. **KeSynchronizeExecution** requires the minidriver to reference several parameters that [**StreamClassCallAtNewPriority**](https://msdn.microsoft.com/library/windows/hardware/ff568230) does not, but in general the two result in the same behavior.

    If the minidriver only occasionally needs to run code that takes more than 1/2 to 1 millisecond, or occasionally needs to call services at PASSIVE\_LEVEL (such as at initialization time), then setting **StreamClassCallAtNewPriority** to LOW priority can be used to acquire a PASSIVE\_LEVEL worker thread. Note that a low-priority callback is not synchronized with anything and that the minidriver could receive new requests (assuming **ReadyForNextRequest** NotificationType parameter is pending) or an ISR call when running a low priority callback.

-   **Example Two: Minidriver Without An ISR**

    If stream class synchronization is turned on, the minidriver's entry points are all called at DISPATCH\_LEVEL. The minidriver can do processing of up to around 1/2 to 1 millisecond duration without needing to adjust priority. If the minidriver only occasionally needs to run code that takes more than 1/2 millisecond, or occasionally needs to call services at PASSIVE\_LEVEL (such as at initialization time), then [**StreamClassCallAtNewPriority**](https://msdn.microsoft.com/library/windows/hardware/ff568230) with LOW priority can be used to acquire a PASSIVE\_LEVEL worker thread. Note that a low-priority callback is not synchronized with anything and the minidriver could receive new requests (assuming **ReadyForNextRequest** NotificationType parameter is pending) when running a low-priority callback.

-   **When Stream Class Synchronization Should** ***Not*** **Be Used**

    The following are examples of situations where stream class synchronization should not be used. These include:

    -   When drivers frequently (more than about 20 percent of the requests the minidriver receives) need to do processing that takes more than 1 millisecond, or need to frequently call PASSIVE\_LEVEL services, such as Microsoft DirectDraw services. When using the debug version of *stream.sys*, stream class will assert both of these cases and halt if they are detected with synchronization turned on.
    -   When the minidriver is a filter with no associated hardware. Such a minidriver should be running at PASSIVE\_LEVEL since there is no underlying hardware to synchronize with and the minidriver typically does a lot of processing. It is easier to do your own synchronization in this case than to waste overhead using stream class synchronization. If necessary, use mutexes to protect your queues.

        Bugs in synchronization code can often be difficult to find, and in certain environments (such as NT-based operating systems running on multiprocessor systems) bugs may show up only after many hours of stress. Based on experience with vendors, these are not the kinds of things that most vendors have the capability or desire to debug. Only driver writers familiar with writing fully asynchronous WDM device drivers should attempt to do their own synchronization.

    -   When the minidriver is a bus-on-bus type driver (for example, a USB or 1394 peripheral driver) that does not really worry about synchronization of the actual hardware, but just calls requests down to the next layer at PASSIVE\_LEVEL and receives callbacks typically at DISPATCH\_LEVEL.

 

 


--------------------
[Send comments about this topic to Microsoft](mailto:wsddocfb@microsoft.com?subject=Documentation%20feedback%20%5Bstream\stream%5D:%20Synchronization%20Examples%20%20RELEASE:%20%288/23/2016%29&body=%0A%0APRIVACY%20STATEMENT%0A%0AWe%20use%20your%20feedback%20to%20improve%20the%20documentation.%20We%20don't%20use%20your%20email%20address%20for%20any%20other%20purpose,%20and%20we'll%20remove%20your%20email%20address%20from%20our%20system%20after%20the%20issue%20that%20you're%20reporting%20is%20fixed.%20While%20we're%20working%20to%20fix%20this%20issue,%20we%20might%20send%20you%20an%20email%20message%20to%20ask%20for%20more%20info.%20Later,%20we%20might%20also%20send%20you%20an%20email%20message%20to%20let%20you%20know%20that%20we've%20addressed%20your%20feedback.%0A%0AFor%20more%20info%20about%20Microsoft's%20privacy%20policy,%20see%20http://privacy.microsoft.com/default.aspx. "Send comments about this topic to Microsoft")

