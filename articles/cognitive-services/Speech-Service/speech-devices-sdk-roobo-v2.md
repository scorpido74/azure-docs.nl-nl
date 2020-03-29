---
title: Spraakapparaten SDK Roobo Smart Audio Dev Kit v2 - Spraakservice
titleSuffix: Azure Cognitive Services
description: Voorwaarden en instructies om aan de slag te gaan met de Speech Devices SDK, Roobo Smart Audio Dev Kit v2.
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 2f325c80877068c53d690bd7ff74f768dab3a174
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371577"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>Apparaat: Roobo Smart Audio Dev Kit v2

Dit artikel bevat apparaatspecifieke informatie voor de Roobo Smart Audio Dev Kit2.

## <a name="set-up-the-development-kit"></a>De ontwikkelkit instellen

1. De development kit heeft twee micro USB-aansluitingen. De linker connector is om de macht van de ontwikkeling kit en wordt gemarkeerd als Macht in de afbeelding hieronder. De juiste is om het te controleren, en is gemarkeerd Foutopsporing in de afbeelding. 
    ![De dev-kit aansluiten](media/speech-devices-sdk/roobo-v2-connections.png)
1. Power the development kit by using a micro USB cable to connect the power port to a PC or power adapter. Een groene stroomindicator zal onder het bovenbord oplichten.
1. Als u de ontwikkelingskit wilt bedienen, sluit u de foutopsporingspoort aan op een computer met behulp van een tweede micro-USB-kabel. Het is essentieel om een hoogwaardige kabel te gebruiken om betrouwbare communicatie te garanderen.
1. Oriënteer uw ontwikkelingskit circulair - Rechtop, met microfoons naar het plafond zoals hierboven weergegeven


## <a name="development-information"></a>Ontwikkelingsinformatie

Zie voor meer informatie over ontwikkeling de [Roobo-ontwikkelingsgids.](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)

## <a name="audio-recordplay"></a>Audiorecord/afspelen

DDK2-audiobewerkingen kunnen op de volgende manieren worden uitgevoerd:
* Gebruik ALSA Open-source bibliotheken en hun toepassingen.
* Gebruik de appmainprog-interface om de ontwikkeling van toepassingen uit te doen. DDK2 audio - gerelateerde software framework maakt gebruik van standaard ALSA framework, u gebruik maken van libasound. Dus om software direct te ontwikkelen. Zo u ALSA's arecord en aplay direct gebruiken om audio op te nemen en af te spelen.
