---
title: Streamingeindpunten beheren met de Azure-portal | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u streaming eindpunten beheert met de Azure-portal.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 57ec22cb02512577f8737718cae9175403fb5603
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900887"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Streaming-eindpunten beheren met de Azure-portal 

In dit artikel ziet u hoe u de Azure-portal gebruiken om streaming eindpunten te beheren. 

>[!NOTE]
>Bekijk het [overzichtsartikel.](media-services-streaming-endpoints-overview.md) 

Zie [dit](media-services-portal-scale-streaming-endpoints.md) artikel voor informatie over het schalen van het streaming-eindpunt.

## <a name="start-managing-streaming-endpoints"></a>Beginnen met het beheren van streaming eindpunten 

Ga als volgt te werk om streaming eindpunten voor je account te beheren.

1. Selecteer uw Azure Media Services-account in [Azure Portal](https://portal.azure.com/).
2. Selecteer **in** het blad Instellingen de optie **Streaming-eindpunten**.
   
    ![Streaming-eindpunt](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Er worden alleen kosten in rekening gebracht wanneer uw streamingeindpunt in de werktoestand is.

## <a name="adddelete-a-streaming-endpoint"></a>Een streamingeindpunt toevoegen/verwijderen

>[!NOTE]
>Het standaard streamingeindpunt kan niet worden verwijderd.

Ga als volgt te werk om streamingeindpunt toe te voegen/verwijderen met de Azure-portal:

1. Als u een streamingeindpunt wilt toevoegen, klikt u op het **+ eindpunt** boven aan de pagina. 

    U wilt mogelijk meerdere streaming eindpunten als u van plan bent om verschillende CDN's of een CDN en directe toegang hebben.

2. Als u een streamingeindpunt wilt verwijderen, drukt u op de knop **Verwijderen.**      
3. Klik op de knop **Start** om het streamingeindpunt te starten.
   
    ![Streaming-eindpunt](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a name="configuring-the-streaming-endpoint"></a><a id="configure_streaming_endpoints"></a>Het streaming-eindpunt configureren
Met Streaming Endpoint u de volgende eigenschappen configureren:

* Toegangsbeheer
* Cachebeheer
* Toegangsbeleid voor andere plaatsen

Zie [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)voor gedetailleerde informatie over deze eigenschappen.

>[!NOTE]
>Wanneer CDN is ingeschakeld, hebt u geen toegang tot IP-toegang. IP-toegang is alleen van toepassing als u geen CDN hebt.

U het eindpunt van streaming als volgt configureren:

1. Selecteer het streamingeindpunt dat u wilt configureren.
2. Klik op **Instellingen**.

Een korte beschrijving van de velden volgt.

![Streaming-eindpunt](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Maximaal cachebeleid: wordt gebruikt om de cachelevensduur te configureren voor assets die via dit streaming-eindpunt worden weergegeven. Als er geen waarde is ingesteld, wordt de standaardwaarde gebruikt. De standaardwaarden kunnen ook rechtstreeks worden gedefinieerd in Azure-opslag. Als Azure CDN is ingeschakeld voor het streaming-eindpunt, moet u de waarde van het cachebeleid niet instellen op minder dan 600 seconden.  
2. Toegestane IP-adressen: wordt gebruikt om IP-adressen op te geven die verbinding zouden kunnen maken met het gepubliceerde streaming-eindpunt. Als er geen IP-adressen zijn opgegeven, kan elk IP-adres verbinding maken. IP-adressen kunnen worden opgegeven als één IP-adres (bijvoorbeeld '10.0.0.1'), een IP-bereik met behulp van een IP-adres en een CIDR-subnetmasker (bijvoorbeeld '10.0.0.1/22'), of een IP-bereik met IP-adres en een gestippeld subnetmasker (bijvoorbeeld '10.0.0.1(255.255.255.0)).).
3. Configuratie voor Akamai signature header authentication: wordt gebruikt om op te geven hoe verificatieverzoek voor handtekeningkoppen van Akamai-servers is geconfigureerd. Expiratie is in UTC.

## <a name="scale-your-premium-streaming-endpoint"></a>Uw Premium-streamingeindpunt schalen

Zie voor meer informatie [dit](media-services-portal-scale-streaming-endpoints.md) artikel.

## <a name="enable-azure-cdn-integration"></a><a id="enable_cdn"></a>Azure CDN-integratie inschakelen

Wanneer u een nieuw account maakt, is standaard Streaming Endpoint Azure CDN-integratie standaard ingeschakeld.

Als u het CDN later wilt uitschakelen/inschakelen, moet uw streaming-eindpunt in de **gestopte** status staan. Het kan tot twee uur duren voordat de Azure CDN-integratie is ingeschakeld en dat de wijzigingen actief zijn voor alle CDN-POP's. Je je streaming-eindpunt echter starten zonder onderbrekingen van het streaming-eindpunt en zodra de integratie is voltooid, wordt de stream geleverd vanaf het CDN. Tijdens de inrichtingsperiode is uw streaming-eindpunt in **startstand** en u de verminderde prestaties waarnemen.

CDN-integratie is ingeschakeld in alle Azure-datacenters, behalve in china en de federale overheid.

Zodra deze is ingeschakeld, wordt de **verificatieconfiguratie Access Control**, **Custom hostname en **Akamai Signature** uitgeschakeld.
 
> [!IMPORTANT]
> Azure Media Services-integratie met Azure CDN wordt geïmplementeerd op **Azure CDN van Verizon** voor standaard streaming eindpunten. Premium streaming-eindpunten kunnen worden geconfigureerd met alle **Azure CDN-prijsniveaus en -providers.** Zie het [CDN-overzicht](../../cdn/cdn-overview.md)voor meer informatie over Azure CDN-functies.
 
### <a name="additional-considerations"></a>Aanvullende overwegingen

* Wanneer CDN is ingeschakeld voor een streaming eindpunt, kunnen clients geen inhoud rechtstreeks van de oorsprong aanvragen. Als u de mogelijkheid nodig hebt om uw inhoud met of zonder CDN te testen, u een ander streamingeindpunt maken dat niet is ingeschakeld voor CDN.
* De hostnaam voor streaming-eindpuntblijft hetzelfde nadat u CDN hebt ingesloten. U hoeft geen wijzigingen aan te brengen in de workflow van uw mediaservices nadat CDN is ingeschakeld. Als uw streaming endpointhostnaam bijvoorbeeld strasbourg.streaming.mediaservices.windows.net is, wordt na het inschakelen van CDN exact dezelfde hostnaam gebruikt.
* Voor nieuwe streaming eindpunten u CDN eenvoudig inschakelen door een nieuw eindpunt te maken; voor bestaande streaming eindpunten moet u eerst het eindpunt stoppen en vervolgens het CDN in- of uitschakelen.
* Standaard streaming eindpunt kan alleen worden geconfigureerd met behulp van **Verizon Standard CDN provider** met behulp van Azure klassieke portal. U echter andere Azure CDN-providers inschakelen met REST API's.

## <a name="configure-cdn-profile"></a>CDN-profiel configureren

U het CDN-profiel configureren door de knop **CDN beheren** bovenaan te selecteren.

![Streaming-eindpunt](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Volgende stappen
Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

