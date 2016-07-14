---
Description: Pin Factories
MS-HAID: 'audio.pin\_factories'
MSHAttr: 'PreferredLib:/library/windows/hardware'
title: Pin Factories
---

# Pin Factories


## <span id="pin_factories"></span><span id="PIN_FACTORIES"></span>


An audio filter's pin factories describe all of the pins that the filter can instantiate. As mentioned previously, an audio miniport driver stores pin information in an array of [**PCPIN\_DESCRIPTOR**](audio.pcpin_descriptor) structures. Each structure specifies a pin factory, and a pin factory is identified by its index in the array. This index is frequently referred to as the *pin ID*.

A PCPIN\_DESCRIPTOR structure contains an automation table and a [**KSPIN\_DESCRIPTOR**](stream.kspin_descriptor) structure.

The KSPIN\_DESCRIPTOR structure contains the following information about the pins in the pin factory:

-   Filter-relative direction of data flow

-   Filter-relative direction of communication flow (In all current Windows versions, KS filters use IRPs for communication.)

-   Pin category

-   Friendly name

-   Instance capabilities

-   Data-format capabilities

The structure's **Category** and **Name** members specify the pin factory's pin category and friendly name. For each pin factory in the filter, the miniport driver specifies a combination of **Category** and **Name** GUIDs that together uniquely identify the pin factory. If two or more pin factories share the same **Category** value, each pin factory has a **Name** value that distinguishes it from the others. If only a single pin factory has a particular **Category** value, that value is sufficient to identify the pin factory, and the **Name** value for that pin factory can be set to **NULL**. For a coding example, see [Exposing Filter Topology](exposing-filter-topology.md). For information about pin categories, see [Pin Category Property](pin-category-property.md).

A pin factory specifies the range of data formats that it supports as an array of extended [**KSDATARANGE**](stream.ksdatarange) structures:

-   A pin factory that supports a range of wave or DirectSound data formats for its input or output stream specifies an array of [**KSDATARANGE\_AUDIO**](audio.ksdatarange_audio) structures.

-   A pin factory that supports a range of MIDI or DirectMusic data formats for its input or output stream specifies an array of [**KSDATARANGE\_MUSIC**](audio.ksdatarange_music) structures.

KSDATARANGE\_AUDIO and KSDATARANGE\_MUSIC are extended versions of KSDATARANGE. For examples of both types of data ranges, see [Audio Data Formats and Data Ranges](audio-data-formats-and-data-ranges.md).

Before connecting a sink pin on one filter to a source pin on another filter, a graph builder (for example, the [SysAudio system driver](kernel-mode-wdm-audio-components.md#sysaudio-system-driver)) can search the data ranges for a compatible format. The graph builder typically calls the filter's [data-intersection handler](data-intersection-handlers.md), which allows the filter itself to choose a compatible format.

A filter can have multiple pin factories, and a pin factory can support multiple pin instances.

-   Having multiple pin factories on a filter is useful for distinguishing separate data paths for the different types of data that flow through the filter. For example, one pin factory might support PCM data streams, and another pin factory might support AC-3 streams.

-   A single filter can support rendering and capture streams simultaneously. The rendering and capture paths have separate sets of filter factories.

-   Having multiple pin instances on a sink-pin factory frequently implies mixing, in which case the filter contains a SUM node ([**KSNODETYPE\_SUM**](audio.ksnodetype_sum)).

Like filters, pins are kernel objects and are identified by kernel handles. The handle for a pin instance is created by calling [**KsCreatePin**](stream.kscreatepin). As a kernel object, a pin can be specified as the target of an IRP. A client of the driver specifies the pin handle when sending an IOCTL request to a pin.

When building an [audio filter graph](audio-filter-graphs.md), SysAudio links one filter to another by connecting their pins. A source pin from one filter can be connected to the sink pin of another filter. Data and IRPs from the source pin flow into the sink pin through this connection. To make the connection, a graph builder (typically SysAudio) creates the source pin first by calling [**KsCreatePin**](stream.kscreatepin) and then creates the sink pin by calling **KsCreatePin** again. In the second call, however, the client specifies that the new sink pin is to be connected to the source pin that was created in the first call.

 

 

[Send comments about this topic to Microsoft](mailto:wsddocfb@microsoft.com?subject=Documentation%20feedback%20[audio\audio]:%20Pin%20Factories%20%20RELEASE:%20%287/14/2016%29&body=%0A%0APRIVACY%20STATEMENT%0A%0AWe%20use%20your%20feedback%20to%20improve%20the%20documentation.%20We%20don't%20use%20your%20email%20address%20for%20any%20other%20purpose,%20and%20we'll%20remove%20your%20email%20address%20from%20our%20system%20after%20the%20issue%20that%20you're%20reporting%20is%20fixed.%20While%20we're%20working%20to%20fix%20this%20issue,%20we%20might%20send%20you%20an%20email%20message%20to%20ask%20for%20more%20info.%20Later,%20we%20might%20also%20send%20you%20an%20email%20message%20to%20let%20you%20know%20that%20we've%20addressed%20your%20feedback.%0A%0AFor%20more%20info%20about%20Microsoft's%20privacy%20policy,%20see%20http://privacy.microsoft.com/en-us/default.aspx. "Send comments about this topic to Microsoft")


