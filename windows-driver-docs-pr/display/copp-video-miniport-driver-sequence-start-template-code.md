---
title: COPP Video Miniport Driver Sequence Start Template Code
description: COPP Video Miniport Driver Sequence Start Template Code
ms.assetid: f1fc0d03-43f6-44a0-b911-1ca473e4e701
keywords:
- sequence start WDK COPP
ms.author: windowsdriverdev
ms.date: 04/20/2017
ms.topic: article
ms.prod: windows-hardware
ms.technology: windows-devices
---

# COPP Video Miniport Driver Sequence Start Template Code


## <span id="ddk_copp_video_miniport_driver_sequence_start_template_code_gg"></span><span id="DDK_COPP_VIDEO_MINIPORT_DRIVER_SEQUENCE_START_TEMPLATE_CODE_GG"></span>


This section applies only to Windows Server 2003 SP1 and later, and Windows XP SP2 and later.

Use the following example code to set the current video session to protected mode for the COPP DirectX VA device object.

```
VP_STATUS
IoctlCOPPStartSeqence(
    PHW_DEVICE_EXTENSION pHwDeviceExtension,
    PVIDEO_REQUEST_PACKET pVideoRequestPacket
    )
{
    COPP_IO_InputBuffer* pInBuff = pVideoRequestPacket->InputBuffer;
    COPP_DeviceData* pThis = (COPP_DeviceData*)*pInBuff->ppThis;
    DXVA_COPPSignature* lpin = (DXVA_COPPSignature*)pInBuff->InputBuffer;
     *pInBuff->phr = COPPSequenceStart(pThis, lpin);
    return NO_ERROR;
}
```

 

 

[Send comments about this topic to Microsoft](mailto:wsddocfb@microsoft.com?subject=Documentation%20feedback%20[display\display]:%20COPP%20Video%20Miniport%20Driver%20Sequence%20Start%20Template%20Code%20%20RELEASE:%20%282/10/2017%29&body=%0A%0APRIVACY%20STATEMENT%0A%0AWe%20use%20your%20feedback%20to%20improve%20the%20documentation.%20We%20don't%20use%20your%20email%20address%20for%20any%20other%20purpose,%20and%20we'll%20remove%20your%20email%20address%20from%20our%20system%20after%20the%20issue%20that%20you're%20reporting%20is%20fixed.%20While%20we're%20working%20to%20fix%20this%20issue,%20we%20might%20send%20you%20an%20email%20message%20to%20ask%20for%20more%20info.%20Later,%20we%20might%20also%20send%20you%20an%20email%20message%20to%20let%20you%20know%20that%20we've%20addressed%20your%20feedback.%0A%0AFor%20more%20info%20about%20Microsoft's%20privacy%20policy,%20see%20http://privacy.microsoft.com/default.aspx. "Send comments about this topic to Microsoft")




