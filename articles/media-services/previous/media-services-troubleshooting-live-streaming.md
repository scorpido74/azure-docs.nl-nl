---
title: Handleiding voor probleemoplossing voor live streaming | Microsoft Documenten
description: In dit artikel vindt u suggesties voor het oplossen van problemen met live streaming van Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: b00df5e8176aaad86be5cf3ae4e04c736f36cf5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74885599"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Handleiding voor het oplossen van problemen met live streaming  

Dit artikel geeft suggesties over hoe u een aantal problemen met live streaming oplossen.

## <a name="issues-related-to-on-premises-encoders"></a>Problemen met betrekking tot on-premises encoders
In dit gedeelte vindt u suggesties voor het oplossen van problemen met betrekking tot on-premises encoders die zijn geconfigureerd om één bitratestream naar AMS-kanalen te verzenden die zijn ingeschakeld voor live codering.

### <a name="problem-would-like-to-see-logs"></a>Probleem: Wilt u logboeken zien
* **Mogelijk probleem:** Kan geen encoderlogboeken vinden die kunnen helpen bij het opsporen van problemen.
  
  * **Telestream Wirecast**: U meestal logboeken vinden onder C:\Gebruikers\{gebruikersnaam}\AppData\Roaming\Wirecast\ 
  * **Elemental Live:** U vinden heeft links naar logs op het beheer portal. Klik op **Statistieken,** dan **Logs**. Op de pagina **Logbestanden** ziet u een lijst met logboeken voor alle LiveEvent-items; selecteer de sessie die overeenkomt met uw huidige sessie. 
  * **Flash Media Live Encoder**: U de **logboekmap vinden...** door naar het tabblad **Logboek codering** te navigeren.

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Probleem: Er is geen optie voor het outputting van een progressieve stream
* **Mogelijk probleem**: De encoder die wordt gebruikt, deinterlaceniet automatisch. 
  
    **Stappen voor het oplossen van problemen:** zoek naar een de-interlacing-optie binnen de encoder-interface. Zodra de-interlacing is ingeschakeld, controleert u opnieuw op progressieve uitvoerinstellingen. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Probleem: Probeerde verschillende encoder uitvoerinstellingen en kan nog steeds geen verbinding maken.
* **Mogelijk probleem:** Azure-coderingskanaal is niet goed gereset. 
  
    **Stappen voor het oplossen van problemen:** Zorg ervoor dat de encoder niet langer naar AMS duwt, stopt en reset het kanaal. Probeer nogmaals uw encoder aan te sluiten op de nieuwe instellingen. Als dit het probleem nog steeds niet wordt verholpen, probeert u een nieuw kanaal volledig te maken, soms kunnen kanalen beschadigd raken na verschillende mislukte pogingen.  
* **Mogelijk probleem**: De GOP-grootte- of hoofdframe-instellingen zijn niet optimaal. 
  
    **Stappen voor het oplossen van problemen**: Aanbevolen GOP-grootte of hoofdframeinterval is twee seconden. Sommige encoders berekenen deze instelling in aantal frames, terwijl andere seconden gebruiken. Bijvoorbeeld: Bij het outputting 30 fps, de GOP grootte zou 60 frames, dat is gelijk aan 2 seconden.  
* **Mogelijk probleem**: Gesloten poorten blokkeren de stream. 
  
    **Stappen voor het oplossen van problemen**: Controleer bij het streamen via RTMP firewall- en/of proxy-instellingen om te controleren of uitgaande poorten 1935 en 1936 open staan. 

> [!NOTE]
> Als u na het volgen van de stappen voor het oplossen van problemen nog steeds niet streamen, dient u een ondersteuningsticket in via de Azure-portal.
> 
> 

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

