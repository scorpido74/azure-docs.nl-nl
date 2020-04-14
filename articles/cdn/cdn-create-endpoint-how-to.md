---
title: Een Azure CDN-eindpunt maken | Microsoft Documenten
description: In dit artikel ziet u hoe u een nieuw CDN-eindpunt (Azure Content Delivery Network) maakt, inclusief geavanceerde instellingen.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 0a130a433c68d0d5cc8c26eae4b81ff264eb0ca2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254012"
---
# <a name="create-an-azure-cdn-endpoint"></a>Een Azure CDN-eindpunt maken
In dit artikel worden alle instellingen beschreven voor het maken van een [CDN-eindpunt (Azure Content Delivery Network)](cdn-overview.md) in een bestaand CDN-profiel. Nadat u een profiel en een eindpunt hebt gemaakt, u beginnen met het leveren van inhoud aan uw klanten. Zie [Snelstart: Een Azure CDN-profiel en eindpunt maken voor](cdn-create-new-endpoint.md)een snelle start bij het maken van een profiel en eindpunt.

## <a name="prerequisites"></a>Vereisten
Voordat u een CDN-eindpunt maken, moet u ten minste één CDN-profiel hebben gemaakt, dat een of meer CDN-eindpunten kan bevatten. U kunt meerdere profielen gebruiken om de CDN-eindpunten te ordenen op basis van het internetdomein, de webtoepassing of andere criteria. Omdat CDN-prijzen worden toegepast op CDN-profielniveau, moet u meerdere CDN-profielen maken als u een mix van Azure CDN-prijslagen wilt gebruiken. Zie [Een nieuw CDN-profiel maken](cdn-create-new-endpoint.md#create-a-new-cdn-profile)als u een CDN-profiel wilt maken.

## <a name="log-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal
Meld u met uw Azure-account aan bij het [Azure Portal](https://portal.azure.com).

## <a name="create-a-new-cdn-endpoint"></a>Nieuwe CDN-eindpunten maken

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar uw CDN-profiel. Mogelijk hebt u het profiel in de vorige stap vastgemaakt aan het dashboard. Zo niet, dan kunt u het vinden door **Alle services** en vervolgens **CDN-profielen** te selecteren. Selecteer in het deelvenster **CDN-profielen** het profiel waaraan u uw eindpunt wilt toevoegen. 
   
    Het deelvenster CDN-profiel wordt weergegeven.

2. Selecteer **Eindpunt**.
   
    ![CDN-selectieeindpunt](./media/cdn-create-endpoint-how-to/cdn-select-endpoint.png)
   
    De pagina **Een eindpunt toevoegen** wordt weergegeven.
   
    ![Eindpuntpagina toevoegen](./media/cdn-create-endpoint-how-to/cdn-add-endpoint-page.png)

3. Voer voor **Naam** een unieke naam voor het nieuwe CDN-eindpunt in. Deze naam wordt gebruikt om toegang te krijgen tot uw resources in de cache op het _ \<eindpunt van _het domein>.azureedge.net.

4. Kies **voor oorsprongstype**een van de volgende oorsprongstypen: 
   - **Opslag** voor Azure Storage
   - **Cloudservice** voor Azure Cloud Services
   - **Web App** voor Azure Web Apps
   - **Aangepaste oorsprong** voor elke andere openbaar toegankelijke origin-webserver (gehost in Azure of elders)

5. Selecteer of voer uw origin-serverdomein in voor **Origin-hostnaam.** In de vervolgkeuzelijst worden alle beschikbare origin-servers weergegeven van het type dat u in stap 4 hebt opgegeven. Als u **Aangepaste oorsprong** als oorsprongstype hebt geselecteerd, voert u het domein van uw aangepaste oorsprongsserver in.
    
6. Voer voor **Origin-pad**het pad in naar de resources die u in de cache wilt plaatsen. Als u het plaatsen van een resource in het domein dat u in stap 5 hebt opgegeven, wilt toestaan, laat u deze instelling leeg.
    
7. Geef bij **Host-header van oorsprong** de host-header op die voor elke aanvraag door Azure CDN moet worden verzonden of laat de standaardinstelling staan.
   
   > [!NOTE]
   > Bepaalde oorsprongtypen, zoals Azure Storage en Web Apps, vereisen dat de hostheader overeenkomt met het domein van de oorsprong. Tenzij u een oorsprong hebt waarvoor een andere host-header is vereist dan die van het domein, laat u de standaardwaarde ongewijzigd.
   > 
    
8. Geef **voor de protocol-** en **oorsprongspoort**de protocollen en poorten op die u wilt gebruiken om toegang te krijgen tot uw bronnen op de oorsprongsserver. Er moet minimaal één protocol (HTTP of HTTPS) worden geselecteerd. Gebruik het door CDN geleverde domein_\<(eindpuntnaam>_ azureedge.net) om toegang te krijgen tot HTTPS-inhoud. 
   
   > [!NOTE]
   > De waarde van de **oorsprongspoort** bepaalt alleen de poort die het eindpunt gebruikt om informatie op te halen van de oorsprongsserver. Het eindpunt zelf is alleen beschikbaar voor eindclients op de standaard-HTTP- en -HTTPS-poorten (80 en 443), ongeacht de waarde voor **Poort van oorsprong**.  
   > 
   > Eindpunten in **Azure CDN van Akamai**-profielen staan niet het volledige TCP-poortbereik voor oorsprongpoorten toe. Zie [Azure CDN from Akamai Allowed Origin Ports](/previous-versions/azure/mt757337(v=azure.100)) (Door Azure CDN van Akamai toegestane poorten van oorsprong) voor een lijst met poorten van oorsprong die niet zijn toegestaan.  
   > 
   > HTTPS-ondersteuning voor aangepaste Azure CDN-domeinen wordt niet ondersteund op **Azure CDN van Akamai-producten.** Zie [HTTPS op een aangepast Azure CDN-domein configureren](cdn-custom-ssl.md) voor meer informatie.
    
9. Selecteer **voor Geoptimaliseerd voor**een optimalisatietype dat het beste overeenkomt met het scenario en het type inhoud dat het eindpunt moet leveren. Zie [Azure CDN optimaliseren voor het type inhoudslevering voor](cdn-optimization-overview.md)meer informatie.

    De volgende optimalisatietype-instellingen worden ondersteund, afhankelijk van het profieltype:
    - **Azure CDN-standaard uit** Microsoft-profielen:
       - [**Algemene weblevering**](cdn-optimization-overview.md#general-web-delivery)

    - **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon-profielen:**
       - [**Algemene weblevering**](cdn-optimization-overview.md#general-web-delivery)
       - [**Dynamische siteversnelling**](cdn-optimization-overview.md#dynamic-site-acceleration)

    - **Azure CDN-standaard van Akamai-profielen:**
       - [**Algemene weblevering**](cdn-optimization-overview.md#general-web-delivery)
       - [**Algemene mediastreaming**](cdn-optimization-overview.md#general-media-streaming)
       - [**Video on demand media streaming**](cdn-optimization-overview.md#video-on-demand-media-streaming)
       - [**Groot bestand downloaden**](cdn-optimization-overview.md#large-file-download)
       - [**Dynamische siteversnelling**](cdn-optimization-overview.md#dynamic-site-acceleration)

10. Selecteer **Toevoegen** om het nieuwe eindpunt te maken.
   
    Zodra het eindpunt is gemaakt, wordt deze weergegeven in de lijst met eindpunten voor het profiel.
    
    ![CDN-eindpunt](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
    Het eindpunt is niet onmiddellijk beschikbaar voor gebruik, aangezien het enige tijd vergt om de registratie door te geven: 
    - Voor profielen van **Azure CDN Standard van Microsoft** is het doorgeven gewoonlijk binnen 10 minuten voltooid. 
    - Profielen van **Azure CDN Standard van Akamai** worden doorgaans binnen één minuut doorgegeven. 
    - Profielen van **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon** worden doorgaans binnen 90 minuten doorgegeven, maar in sommige gevallen kan dit langer duren. 
   
    Als u de CDN-domeinnaam probeert te gebruiken voordat de eindpuntconfiguratie is doorgegeven aan de POP-servers (point-of-presence), ontvangt u mogelijk een HTTP 404-antwoordstatus. Zie [Azure CDN-eindpunten oplossen die een status van 404-status retourneren](cdn-troubleshoot-endpoint.md)als u uw eindpunt hebt gemaakt en u nog steeds een antwoordstatus van 404 ontvangt.

## <a name="clean-up-resources"></a>Resources opschonen
Als u een eindpunt wilt verwijderen wanneer dit niet meer nodig is, selecteert u het punt en selecteert u **Verwijderen**. 

## <a name="next-steps"></a>Volgende stappen
Ga voor meer informatie over aangepaste domeinen verder naar de zelfstudie voor het toevoegen van een aangepast domein aan uw CDN-eindpunt.

> [!div class="nextstepaction"]
> [Een aangepast domein toevoegen](cdn-map-content-to-custom-domain.md)


