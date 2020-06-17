---
title: Opnamen van meerdere dagen afspelen - Azure
description: In deze zelfstudie leert u hoe u Azure Media Service-API's gebruikt om een continue video-opname van meerdere dagen af te spelen.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 52ef33e8c4380e9c21e99c4ba45b7f25f7c57780
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433654"
---
# <a name="tutorial-playback-of-multi-day-recordings"></a>Zelfstudie: Opnamen van meerdere dagen afspelen  

Deze zelfstudie is gebaseerd op de zelfstudie voor [continue video-opname](continuous-video-recording-concept.md) (CVR). In deze zelfstudie leert u hoe u Azure Media Service-API's kunt gebruiken voor het afspelen van een continue video-opname van meerdere dagen vanuit de cloud. 

Dit is handig voor scenario's zoals openbare veiligheid, waarbij het nodig is om de beelden van een camera meerdere dagen (of weken) op te nemen en zo nu en dan specifieke delen van die beelden te bekijken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Voer de voorbeeld-app uit om te laten zien hoe u door de inhoud van een opname van meerdere dagen kunt bladeren
> * Geselecteerde delen van de opname weergeven

## <a name="suggested-pre-reading"></a>Aanbevolen om te lezen  

U wordt aangeraden de volgende documentatie te lezen:

* [Overzicht van Live Video Analytics in IoT Edge](overview.md)
* [Terminologie van Live Video Analytics in IoT Edge](terminology.md)
* [Mediagrafiekconcepten](media-graph-concept.md)
* [Continue video-opname](continuous-video-recording-concept.md) 
* [Handleiding: Opnamen afspelen](playback-recordings-how-to.md).
* [Zelfstudie: Continue video-opname](continuous-video-recording-tutorial.md)

## <a name="prerequisites"></a>Vereisten

* Voltooi de [zelfstudie voor CVR](continuous-video-recording-tutorial.md). Hoewel deze zelfstudie en de relevante API's in de [Zelfstudie: Continue video-opname](continuous-video-recording-tutorial.md) gericht zijn op opnamen die vijf minuten of langer duren, wordt het aanbevolen om video-opnamen van vijf uur of langer te maken. De API's die worden gebruikt om door opnamen te bladeren, werken namelijk het beste met lange opnamen.
* We raden u aan deze zelfstudie uit te voeren terwijl de [Zelfstudie: Continue video-opname](continuous-video-recording-tutorial.md) nog actief is, dat wil zeggen dat u nog steeds video in de cloud opneemt.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren 

Als onderdeel van de [zelfstudie voor CVR](continuous-video-recording-tutorial.md)hebt u een Media Service-account gemaakt. Voor deze zelfstudie hebt u volledige API-toegang tot dat account nodig. U kunt de stappen in [Aanmeldingsgegevens ophalen voor toegang tot Media Services-API](../latest/access-api-howto.md#use-the-azure-portal) gebruiken om een service-principal te maken. U zou een JSON-blok van de Azure-portal moeten kunnen ophalen dat er als volgt uitziet:

```
{
    "AadClientId": "<<INSERT_AZURE_AD_APP_ID_HERE>>",
    "AadSecret": "<<INSERT_AZURE_AD_APP_SECRET_HERE>>",
    "AadTenantDomain": "<<YOUR_TENANT_DOMAIN>>",
    "AadTenantId": "<<YOUR_TENANT_ID>>",
    "AccountName": "<<YOUR_ACCOUNT_NAME>>",
    "Location": "<<YOUR_REGION_NAME>>",
    "ResourceGroup": "<<YOUR_RESOURCE_GROUP>>",
    "SubscriptionId": "<<YOUR_SUBSCRIPTION_ID>>",
    "ArmAadAudience": "https://management.core.windows.net",
    "ArmEndpoint": "https://management.azure.com"
}
```

Open vervolgens in Visual Studio Code de map src/ams-asset-player. Deze map bevat de bestanden die nodig zijn voor deze zelfstudie. Open het bestand appSettings.json en kopieer de inhoud naar een nieuw bestand, appsettings.development.json. Voer de volgende bewerkingen uit in het laatste bestand:

```
  "AMS" : {
    "subscriptionId" : "Use value of SubscriptionId above",
    "resourceGroup" : "Use value of ResourceGroup above",
    "accountId" : "Use value of AccountName above",
    "aadTenantId" : "Use value of AadTenantId above",
    "aadClientId" : "Use value of AadClientId above",
    "aadSecret" : "Use value of AadSecret above"
} 
```

In Visual Studio Code kunt u aan de linkerkant op het pictogram Uitvoeren (of Ctrl + Shift + D) klikken om de beschikbare toepassingen te tonen die kunnen worden uitgevoerd:

![Voer](./media/playback-multi-day-recordings-tutorial/run.png)
 
Selecteer de AMS Asset Player-toepassing in de vervolgkeuzelijst, zoals hieronder wordt weergegeven, en druk op F5 om de foutopsporing te starten.

![Fouten opsporen](./media/playback-multi-day-recordings-tutorial/debug.png)

De voorbeeldtoepassing bouwt en start uw standaard browser-app en opent de AMS Asset Player-pagina.

> [!NOTE]
> Afhankelijk van de beveiligingsinstellingen in uw browser, wordt er mogelijk een waarschuwing weergegeven. Omdat de webpagina lokaal wordt uitgevoerd, kunt u ervoor kiezen om de waarschuwing te negeren.

De AMS Asset Player vraagt u om de naam van een Media Service-Asset in te voeren. Gebruik de naam van de asset die u hebt gebruikt voor het opnemen van video in [Zelfstudie: Continue video-opname](continuous-video-recording-tutorial.md).

Nadat u de naam van de asset typt en op verzenden klikt, wordt de streaming-URL door de player-code geladen. Zie voor meer informatie [Handleiding: Opnamen afspelen](playback-recordings-how-to.md). Als u, zoals aanbevolen, nog steeds naar de asset opneemt, wordt dit door de speler gedetecteerd en zal deze proberen het afspelen naar het meest recente deel van de opgenomen video te sturen. U kunt de tijdstempel (in UTC) in de linkerbovenhoek van de speler zien. In de onderstaande schermopname ziet u hoe de knop 'live' wordt geselecteerd.

![Streamen](./media/playback-multi-day-recordings-tutorial/assetplayer1.png)
 
Aan de rechterkant van de speler ziet u de besturingselementen waarmee u in het archief kan zoeken. De jaren, maanden en datums in dit besturingselement worden ingevuld met behulp van de availableMedia-API die wordt beschreven in [Handleiding: Opnamen afspelen](playback-recordings-how-to.md).
Wanneer u de dag uitvouwt, nadat u de CVR-zelfstudie meerdere uren hebt laten lopen, ziet u een resultaat als dit:

![In archief zoeken](./media/playback-multi-day-recordings-tutorial/results.png)

De bron voor de videofeed in de zelfstudie is een MKV-bestand. Wanneer de RSTP-simulator (Zie [Live555 Media Server](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) het einde van het bestand bereikt, wordt de stream beëindigd. Het RTSP-bronknooppunt in de mediagrafiek detecteert dit en brengt de verbinding opnieuw tot stand om de video te hervatten. Tussen een dergelijk einde van een bestand en het opnieuw verbinden zit in het archief een onderbreking. Dit wordt als een nieuwe vermelding in de availableMedia-resultaten wordt weergegeven.

![Resultaten](./media/playback-multi-day-recordings-tutorial/assetplayer2.png)
 
Wanneer u op een vermelding in de lijst klikt, maakt de toepassing een streaming-URL met het juiste filter, zoals https://{hostname}/{locatorId}/content.ism/manifest(format=mpd-time-csf,startTime=YYYY-MM-DDTHH:MM:SS). De speler laadt deze URL en het afspelen begint op de gewenste starttijd.

U kunt ook specifieke start- en eindtijden opgeven via de besturingselementen aan de onderkant van de pagina. U kunt de resultaten van de availableMedia-aanroep, zoals vermeld aan de rechterkant, als richtlijn gebruiken voor de toegestane waarden van start- en eindtijden. De gedetailleerde beperkingen van de filters voor de start- en eindtijd worden beschreven in [Handleiding: Opnamen afspelen](playback-recordings-how-to.md). Nadat u de tijdwaarden hebt geselecteerd en op verzenden hebt geklikt, wordt de speler geladen met een streaming-URL zoals: https://{hostname}/{locatorId}/content.ism/manifest(format=mpd-time-csf,startTime=YYYY-MM-DDTHH:MM:SS,endTime= YYYY-MM-DDTHH:MM:SS) Het afspelen begint op de gewenste starttijd.

## <a name="next-steps"></a>Volgende stappen

[Video-opname op basis van gebeurtenissen in de cloud en afspelen vanuit de cloud](event-based-video-recording-tutorial.md)
