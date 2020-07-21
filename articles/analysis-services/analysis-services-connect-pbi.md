---
title: Verbinding maken met Azure Analysis Services met Power BI | Microsoft Docs
description: Meer informatie over het maken van verbinding met een Azure Analysis Services-server met behulp van Power BI. Nadat de verbinding tot stand is gebracht, kunnen gebruikers model gegevens verkennen.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f79ff9af1272ca908c7a5ba35fbaa1e8bb87fc50
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506705"
---
# <a name="connect-with-power-bi"></a>Verbinden met Power BI

Zodra u een server in azure hebt gemaakt en een tabellair model hebt geïmplementeerd, zijn gebruikers in uw organisatie klaar om verbinding te maken en gegevens te verkennen. 

> [!TIP]
> Zorg ervoor dat u de nieuwste versie van [Power bi Desktop](https://powerbi.microsoft.com/desktop/)gebruikt.
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Verbinden maken in Power BI Desktop

1. Klik in Power bi Desktop op **gegevens ophalen**  >  **Azure**  >  **Azure Analysis Services Data Base**.

2. Voer bij **Server**de naam van de server in. Zorg ervoor dat u de volledige URL opgeeft. bijvoorbeeld asazure://westcentralus.asazure.windows.net/advworks.

3. Als u in de **Data Base**de naam van de data base of het perspectief van de tabellaire model kent waarmee u verbinding wilt maken, plakt u deze hier. Als dat niet het geval is, kunt u dit veld leeg laten en later een Data Base of perspectief selecteren.

4. Selecteer een verbindings optie en druk vervolgens op **verbinding maken**. 

    Beide opties voor **verbinding met Live** en **importeren** worden ondersteund. We raden u echter aan om live-verbindingen te gebruiken omdat de import modus enige beperkingen heeft. Server prestaties kunnen vooral worden beïnvloed tijdens het importeren. Als het model moet worden vernieuwd in het Power BI-service, is de instelling **toegang vanaf Power bi toestaan** alleen van toepassing als u **verbinding maken met Live**selecteert.

5. Voer uw aanmeldings referenties in als u hierom wordt gevraagd. 

   > [!NOTE]
   > OTP-accounts (one-time wachtwoord code) worden niet ondersteund. 

6. Vouw in **Navigator**de server uit en selecteer vervolgens het model of perspectief waarmee u verbinding wilt maken en klik vervolgens op **verbinding maken**. Klik op een model of perspectief om alle objecten voor die weer gave weer te geven.

    Het model wordt geopend in Power BI Desktop met een leeg rapport in de rapport weergave. De lijst Velden bevat alle niet-verborgen modelobjecten. De verbindingsstatus wordt rechtsonder weergegeven.

## <a name="connect-in-power-bi-service"></a>Verbinding maken in Power BI (Service)

1. Maak een Power BI Desktop-bestand dat een live verbinding heeft met uw model op uw server.
2. Klik in [Power bi](https://powerbi.microsoft.com)op **gegevens**  >  **bestanden**ophalen, zoek en selecteer uw pbix-bestand.

## <a name="see-also"></a>Zie tevens
[Verbinding maken met Azure Analysis Services](analysis-services-connect.md)   
[Clientbibliotheken](https://docs.microsoft.com/analysis-services/client-libraries?view=azure-analysis-services-current)

