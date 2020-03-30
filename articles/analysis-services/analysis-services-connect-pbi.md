---
title: Verbinding maken met Azure Analysis Services met Power BI | Microsoft Documenten
description: Meer informatie over hoe u verbinding maken met een Azure Analysis Services-server met Power BI. Eenmaal verbonden, kunnen gebruikers modelgegevens verkennen.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: db0c00268c343cd99e439bb49460523cf0563c3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573105"
---
# <a name="connect-with-power-bi"></a>Verbinden met Power BI

Zodra u een server in Azure hebt gemaakt en er een tabelmodel op hebt geïmplementeerd, zijn gebruikers in uw organisatie klaar om verbinding te maken en gegevens te verkennen. 

> [!TIP]
> Zorg ervoor dat u de nieuwste versie van [Power BI Desktop gebruikt.](https://powerbi.microsoft.com/desktop/)
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Verbinden maken in Power BI Desktop

1. Klik in Power BI Desktop op Gegevens**azure** >  > **Azure Analysis Services-database** **opvragen**.

2. Voer **in Server**de servernaam in. Zorg ervoor dat u de volledige URL opneemt; asazure://westcentralus.asazure.windows.net/advworks bijvoorbeeld.

3. Als u in **Database**de naam weet van de tabelmodeldatabase of het perspectief waarmee u verbinding wilt maken, plakt u deze hier. Anders u dit veld leeg laten en later een database of perspectief selecteren.

4. Selecteer een verbindingsoptie en druk op **Verbinding maken**. 

    Zowel **connect live-** als **importopties** worden ondersteund. We raden je echter aan om live verbindingen te gebruiken, omdat de importmodus wel een aantal beperkingen heeft; met name de serverprestaties kunnen tijdens de import worden beïnvloed. Als het model moet worden vernieuwd in de Power BI-service, is de instelling **Toegang toestaan vanuit Power BI** alleen van toepassing wanneer u Live **verbinding**maakt.

5. Voer desgevraagd uw inloggegevens in. 

6. Vouw **in Navigator**de server uit, selecteer vervolgens het model of perspectief waarmee u verbinding wilt maken en klik vervolgens op Verbinding **maken**. Klik op een model of perspectief om alle objecten voor die weergave weer te geven.

    Het model wordt geopend in Power BI Desktop met een leeg rapport in de rapportweergave. De lijst Velden bevat alle niet-verborgen modelobjecten. De verbindingsstatus wordt rechtsonder weergegeven.

## <a name="connect-in-power-bi-service"></a>Verbinding maken in Power BI (service)

1. Maak een Power BI-bureaubladbestand met een liveverbinding met uw model op uw server.
2. Klik in [Power BI](https://powerbi.microsoft.com)op **Gegevensbestanden** > **opvragen**en zoek en selecteer het .pbix-bestand.

## <a name="see-also"></a>Zie ook
[Verbinding maken met Azure Analysis Services](analysis-services-connect.md)   
[Clientbibliotheken](analysis-services-data-providers.md)

