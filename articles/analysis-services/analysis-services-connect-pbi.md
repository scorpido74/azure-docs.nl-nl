---
title: Verbinding maken met Azure Analysis Services met Power BI | Microsoft Docs
description: Meer informatie over het maken van verbinding met een Azure Analysis Services-server met behulp van Power BI.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 181a63fda1feddfb654eb32f46632f0656febb82
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72295289"
---
# <a name="connect-with-power-bi"></a>Verbinden met Power BI

Zodra u een server in azure hebt gemaakt en een tabellair model hebt geïmplementeerd, zijn gebruikers in uw organisatie klaar om verbinding te maken en gegevens te verkennen. 

> [!TIP]
> Zorg ervoor dat u de nieuwste versie van [Power bi Desktop](https://powerbi.microsoft.com/desktop/)gebruikt.
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Verbinden maken in Power BI Desktop

1. Klik in Power BI Desktop op **Gegevens ophalen** > **Azure** > **Azure Analysis Services-database**.

2. Voer bij **Server**de naam van de server in. Zorg ervoor dat u de volledige URL opgeeft. bijvoorbeeld asazure://westcentralus.asazure.windows.net/advworks.

3. Als u in de **Data Base**de naam van de data base of het perspectief van de tabellaire model kent waarmee u verbinding wilt maken, plakt u deze hier. Als dat niet het geval is, kunt u dit veld leeg laten en later een Data Base of perspectief selecteren.

4. Selecteer een verbindings optie en druk vervolgens op **verbinding maken**. 

    Beide opties voor **verbinding met Live** en **importeren** worden ondersteund. We raden u echter aan om live-verbindingen te gebruiken omdat de import modus enige beperkingen heeft. Server prestaties kunnen vooral worden beïnvloed tijdens het importeren. Als het model moet worden vernieuwd in het Power BI-service, is de instelling **toegang vanaf Power bi toestaan** alleen van toepassing als u **verbinding maken met Live**selecteert.

5. Voer uw aanmeldings referenties in als u hierom wordt gevraagd. 

6. Vouw in **Navigator**de server uit en selecteer vervolgens het model of perspectief waarmee u verbinding wilt maken en klik vervolgens op **verbinding maken**. Klik op een model of perspectief om alle objecten voor die weer gave weer te geven.

    Het model wordt geopend in Power BI Desktop met een leeg rapport in de rapport weergave. In de lijst met velden worden alle niet-verborgen model objecten weer gegeven. De verbindingsstatus wordt rechtsonder weergegeven.

## <a name="connect-in-power-bi-service"></a>Verbinding maken in Power BI (Service)

1. Maak een Power BI Desktop-bestand dat een live verbinding heeft met uw model op uw server.
2. Klik in [Power bi](https://powerbi.microsoft.com)op **gegevens ophalen** > **bestanden**, en zoek en selecteer vervolgens uw pbix-bestand.

## <a name="see-also"></a>Zie ook
[Verbinding maken met Azure Analysis Services](analysis-services-connect.md)   
[Clientbibliotheken](analysis-services-data-providers.md)

