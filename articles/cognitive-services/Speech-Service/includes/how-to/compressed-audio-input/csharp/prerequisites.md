---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 07405ad0da9c9ba280810402a638395a7feb8554
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637295"
---
Het verwerken van gecomprimeerde audio wordt geïmplementeerd met [GStreamer](https://gstreamer.freedesktop.org). Om licentieredenen worden GStreamer-binaries niet gecompileerd en gekoppeld aan de Speech SDK. Ontwikkelaars moeten verschillende afhankelijkheden en plug-ins installeren, zie [Installeren op Windows.](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) Gstreamer binaries moeten zich in het systeempad bevinden, zodat de spraak-SDK gstreamer-binaire bestanden tijdens runtime kan laden. Als spraak SDK in staat is om libgstreamer-1.0-0.dll te vinden tijdens runtime betekent dit dat de gstreamer binaries zich in het systeempad bevinden.

