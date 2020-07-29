---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: a715d0ece88cf2caf1cb3d20d703f550353094c5
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282726"
---
De verwerking van gecomprimeerde audio wordt geïmplementeerd met [`GStreamer`](https://gstreamer.freedesktop.org) . `GStreamer`De binaire bestanden voor licentie redenen worden niet gecompileerd en gekoppeld aan de spraak-SDK. Ontwikkel aars moeten verschillende afhankelijkheden en invoeg toepassingen installeren, Zie [installeren op Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). `GStreamer`binaire bestanden moeten zich in het systeempad bevinden, zodat de Speech SDK de binaire bestanden kan laden tijdens runtime. Als de spraak-SDK tijdens runtime kan worden gevonden `libgstreamer-1.0-0.dll` , betekent dit dat de binaire bestanden zich in het systeempad bevinden.

