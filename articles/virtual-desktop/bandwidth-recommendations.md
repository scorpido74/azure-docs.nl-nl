---
title: Aanbevelingen voor de band breedte voor externe sessies-Azure
description: Beschik bare bandbreedte aanbevelingen voor externe sessies.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: helohr
ms.openlocfilehash: 8352858b9fd6059257b5ba7637822f85c56eb070
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802612"
---
# <a name="bandwidth-recommendations-for-remote-sessions"></a>Aanbevelingen voor bandbreedte voor externe sessies

Wanneer u een externe Windows-sessie gebruikt, heeft de beschik bare band breedte van uw netwerk een grote invloed op de kwaliteit van uw ervaring. Voor verschillende toepassingen en weergave resoluties zijn verschillende netwerk configuraties vereist. het is dus belang rijk om ervoor te zorgen dat uw netwerk zo is geconfigureerd dat het voldoet aan uw behoeften.

>[!NOTE]
>De volgende aanbevelingen zijn van toepassing op netwerken met minder dan 0,1% verlies.

## <a name="applications"></a>Toepassingen

De volgende tabel bevat de minimale aanbevolen band breedten voor een soepele gebruikers ervaring. 

|Workload        |Voorbeeldtoepassingen                                                                                           |Aanbevolen band breedte|
|----------------|--------------------------------------------------------------------------------------------------------------|---------------------|
|Taakuitvoering     |Micro soft Word, Outlook, Excel, Adobe Reader                                                                  |1.5 @ no__t-0Mbps        |
|Office-werk nemer   |Micro soft Word, Outlook, Excel, Adobe Reader, Power Point, Photo Viewer                                        |3 @ no__t-0Mbps          |
|Kennis medewerker|Micro soft Word, Outlook, Excel, Adobe Reader, Power Point, Photo Viewer, java                                  |5 @ no__t-0Mbps          |
|Power worker    |Micro soft Word, Outlook, Excel, Adobe Reader, Power Point, Photo Viewer, Java, CAD/CAM, illustratie/publicatie|15 @ no__t-0Mbps         |

>[!NOTE]
>Deze aanbevelingen zijn van toepassing, ongeacht het aantal gebruikers in de sessie.

Houd er wel voor dat de stress die in uw netwerk wordt geplaatst, afhankelijk is van het uitvoer frame en de weergave resolutie van uw app-werk belasting. Als de frame frequentie of de weergave resolutie toeneemt, neemt de bandbreedte vereiste ook toe. Een lichte workload met een weer gave met een hoge resolutie vereist bijvoorbeeld meer beschik bare band breedte dan een lichte workload met een normale of lage resolutie.

Andere scenario's kunnen de bandbreedte vereisten wijzigen, afhankelijk van hoe u ze gebruikt, zoals:

- Telefonische of video vergaderingen
- Real-time communicatie
- Video van 4.000 kB streamen

Zorg ervoor dat u deze scenario's in uw implementatie laadt met behulp van simulatie hulpprogramma's als aanmeldings VSI. U kunt de belasting grootte variëren, stress testen uitvoeren en algemene gebruikers scenario's testen in externe sessies om beter inzicht te krijgen in de vereisten van uw netwerk. 

## <a name="display-resolutions"></a>Beeldscherm resoluties

Voor verschillende weergave resoluties zijn verschillende beschik bare band breedten vereist. De volgende tabel geeft een lijst van de band breedten die wij u aanraden voor een soepele gebruikers ervaring met typische weergave resoluties met een frame snelheid van 30 frames per seconde (fps). Deze aanbevelingen zijn van toepassing op scenario's met één en meerdere gebruikers. Denk eraan dat scenario's met een frame frequentie onder 30 fps, zoals het lezen van statische tekst, minder beschik bare band breedte vereisen. 

|Typische weergave resoluties met 30 fps    |Aanbevolen band breedte|
|-----------------------------------------|---------------------|
|Ongeveer 1024 x 768 PX                      |1,5 Mbps             |
|Ongeveer 1280 x 720 PX                      |3 Mbps               |
|Ongeveer 1920 x 1080 PX                     |5 Mbps               |
|Ongeveer 3840 x 2160 px (4.000)                |15 Mbps              |

>[!NOTE]
>Deze aanbevelingen zijn van toepassing, ongeacht het aantal gebruikers in de sessie.

## <a name="additional-resources"></a>Aanvullende resources

De Azure-regio die u aanmeldt, is van invloed op de gebruikers ervaring, net als bij netwerk omstandigheden. Bekijk de [Estimator van Windows virtueel bureau blad](https://azure.microsoft.com/services/virtual-desktop/assessment/) voor meer informatie.
