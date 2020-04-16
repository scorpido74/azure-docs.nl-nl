---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 64eab53d24a14bb51633be3f979db20963677d93
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421891"
---
Het verwerken van gecomprimeerde audio wordt geïmplementeerd met [GStreamer](https://gstreamer.freedesktop.org). Om licentieredenen worden GStreamer-binaries niet gecompileerd en gekoppeld aan de Speech SDK. Ontwikkelaars moeten verschillende afhankelijkheden en plug-ins installeren, zie [Installeren op Windows.](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) Gstreamer binaries moeten zich in het systeempad bevinden, zodat de spraak-SDK gstreamer-binaire bestanden tijdens runtime kan laden. Als spraak SDK in staat is om libgstreamer-1.0-0.dll te vinden tijdens runtime betekent dit dat de gstreamer binaries zich in het systeempad bevinden.

