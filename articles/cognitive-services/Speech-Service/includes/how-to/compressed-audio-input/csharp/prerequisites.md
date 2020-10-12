---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 64eab53d24a14bb51633be3f979db20963677d93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81421891"
---
Het verwerken van gecomprimeerde audio wordt geïmplementeerd met behulp van [gstreamer](https://gstreamer.freedesktop.org). Om licentie redenen GStreamer binaire bestanden niet worden gecompileerd en gekoppeld aan de spraak-SDK. Ontwikkel aars moeten verschillende afhankelijkheden en invoeg toepassingen installeren, Zie [installeren op Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). Gstreamer binaire bestanden moeten zich in het systeempad bevinden, zodat de spraak-SDK gstreamer binaire bestanden kan laden tijdens runtime. Als spraak SDK libgstreamer-1.0-0.dll kan vinden tijdens runtime, betekent dit dat de binaire bestanden van gstreamer zich in het systeempad bevinden.

