---
title: Trans formaties en taken in Azure Media Services | Microsoft Docs
description: Wanneer u Media Services gebruikt, moet u een trans formatie maken om de regels of specificaties voor het verwerken van uw Video's te beschrijven. In dit artikel vindt u een overzicht van de trans formatie en hoe u deze kunt gebruiken.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: 466ab0737aa5af40bd1bc137b98ab57a48feafde
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637360"
---
# <a name="transforms-and-jobs"></a>Transformaties en taken

Dit onderwerp bevat gedetailleerde informatie over [trans formaties](https://docs.microsoft.com/rest/api/media/transforms) en [taken](https://docs.microsoft.com/rest/api/media/jobs) en een uitleg van de relatie tussen deze entiteiten. 

## <a name="overview"></a>Overzicht 

### <a name="transformsjobs-workflow"></a>Trans formatie/taken werk stroom

In het volgende diagram worden de werk stroom trans formaties/taken weer gegeven.

![Transformaties](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Standaardwerkstroom

1. Een transformatie maken 
2. Taken onder die trans formatie verzenden 
3. Trans formaties weer geven 
4. Verwijder een trans formatie als u deze niet in de toekomst wilt gebruiken. 

#### <a name="example"></a>Voorbeeld

Stel dat u het eerste frame van al uw Video's als een miniatuur afbeelding wilt extra heren. u kunt de volgende stappen uitvoeren: 

1. Definieer het recept of de regel voor het verwerken van uw Video's: "gebruik het eerste frame van de video als miniatuur". 
2. Voor elke video geeft u de volgende informatie: 
    1. Waar u deze video vindt,  
    2. Waar de afbeelding van de uitvoer miniatuur moet worden geschreven. 

Met een **trans formatie** kunt u het recept eenmaal maken (stap 1) en taken verzenden met behulp van dat recept (stap 2).

> [!NOTE]
> Eigenschappen van **trans formatie** en **taak** van het type datetime zijn altijd in UTC-indeling.

## <a name="transforms"></a>Transformaties

**Trans formaties** gebruiken om algemene taken te configureren voor het coderen of analyseren van Video's. Elke **trans formatie** beschrijft een recept of een werk stroom van taken voor het verwerken van uw video-of audio bestanden. Eén trans formatie kan meer dan één regel Toep assen. Een trans formatie kan bijvoorbeeld opgeven dat elke video moet worden gecodeerd in een MP4-bestand op een bepaalde bitrate en dat er een miniatuur afbeelding moet worden gegenereerd op basis van het eerste frame van de video. U voegt één TransformOutput-vermelding toe voor elke regel die u wilt gebruiken in uw trans formatie. U kunt voor instellingen gebruiken om de trans formatie te laten zien hoe de invoer media bestanden moeten worden verwerkt.

### <a name="viewing-schema"></a>Schema weer geven

In Media Services v3 zijn de voor instellingen sterk getypeerde entiteiten in de API zelf. U kunt de definitie ' schema ' voor deze objecten vinden in de [open API-specificatie (of Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). U kunt de vooraf ingestelde definities (zoals **StandardEncoderPreset**) ook weer geven in de [rest API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (of een andere Media Services v3 SDK-referentie documentatie).

### <a name="creating-transforms"></a>Trans formaties maken

U kunt trans formaties maken met behulp van REST, CLI of een van de gepubliceerde Sdk's gebruiken. De Media Services v3 API wordt door Azure Resource Manager aangestuurd, zodat u ook Resource Manager-sjablonen kunt gebruiken om trans formaties te maken en te implementeren in uw Media Services-account. Toegangs beheer op basis van rollen kan worden gebruikt om de toegang tot trans formaties te vergren delen.

### <a name="updating-transforms"></a>Trans formaties bijwerken

Als u uw [trans formatie](https://docs.microsoft.com/rest/api/media/transforms)wilt bijwerken, gebruikt u de bewerking **bijwerken** . Het is bedoeld voor het aanbrengen van wijzigingen in de beschrijving of de prioriteiten van de onderliggende TransformOutputs. Het wordt aanbevolen dat dergelijke updates worden uitgevoerd wanneer alle taken in uitvoering zijn voltooid. Als u van plan bent het recept opnieuw te schrijven, moet u een nieuwe trans formatie maken.

### <a name="transform-object-diagram"></a>Object diagram transformeren

Het volgende diagram toont het object **transform** en de objecten waarnaar wordt verwezen, inclusief de Afleidings relaties. De grijze pijlen geven een type weer waarnaar de taak verwijst en de groene pijlen geven een klasse-Afleidings relatie weer.<br/>Klik op de afbeelding om deze in volledig formaat weer te geven.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a> 

## <a name="jobs"></a>Taken

Een **taak** is de daad werkelijke aanvraag om Azure Media Services om de **trans formatie** toe te passen op een gegeven video-of audio-inhoud. Zodra de trans formatie is gemaakt, kunt u taken verzenden met behulp van Media Services Api's of een van de gepubliceerde Sdk's. De **taak** geeft informatie op, zoals de locatie van de invoer video en de locatie voor de uitvoer. U kunt de locatie van de invoer video opgeven met: HTTPS-Url's, SAS-Url's of [activa](https://docs.microsoft.com/rest/api/media/assets).  

### <a name="job-input-from-https"></a>Taak invoer van HTTPS

Gebruik [taak invoer van HTTPS](job-input-from-http-how-to.md) als uw inhoud al toegankelijk is via een URL en u het bron bestand niet hoeft op te slaan in azure (bijvoorbeeld importeren vanuit S3). Deze methode is ook geschikt als u de inhoud in Azure Blob-opslag hebt, maar u niet wilt dat het bestand zich in een Asset bevindt. Deze methode ondersteunt momenteel slechts één bestand voor invoer.

### <a name="asset-as-job-input"></a>Activum als taak invoer

Gebruik [activa als taak invoer](job-input-from-local-file-how-to.md) als de invoer inhoud al in een Asset is of als de inhoud in het lokale bestand wordt opgeslagen. Het is ook een goede optie als u van plan bent om het invoer activum te publiceren voor streaming of downloaden (Stel dat u de MP4 wilt publiceren om te downloaden, maar ook een spraak wilt maken op tekst-of gezichts herkenning). Deze methode ondersteunt multi-file assets (bijvoorbeeld MBR-streaming sets die lokaal zijn gecodeerd).

### <a name="checking-job-progress"></a>De voortgang van de taak controleren

De voortgang en de status van taken kunnen worden verkregen door gebeurtenissen met Event Grid te bewaken. Zie [gebeurtenissen bewaken met EventGrid](job-state-events-cli-how-to.md)voor meer informatie.

### <a name="updating-jobs"></a>Taken bijwerken

De update bewerking voor de [taak](https://docs.microsoft.com/rest/api/media/jobs) entiteit kan worden gebruikt om de *Beschrijving*te wijzigen en de *prioriteits* eigenschappen na het verzenden van de taak. Een wijziging in de *prioriteits* eigenschap is alleen effectief als de taak in de wachtrij staat. Als de verwerking van de taak is begonnen of is voltooid, heeft het wijzigen van de prioriteit geen effect.

### <a name="job-object-diagram"></a>Taak object diagram

Het volgende diagram toont het **taak** object en de objecten waarnaar wordt verwezen, inclusief de Afleidings relaties.<br/>Klik op de afbeelding om deze in volledig formaat weer te geven.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a> 

## <a name="configure-media-reserved-units"></a>Gereserveerde media-eenheden configureren

Voor de taken voor audio analyse en video analyse die worden geactiveerd door Media Services v3 of Video Indexer, wordt u ten zeerste aangeraden om uw account in te richten met 10 S3-media gereserveerde eenheden (MRUs). Als u meer dan 10 S3 groepsbeleidsinstelling nodig hebt, opent u een ondersteuning ticket met de [Azure-portal](https://portal.azure.com/).

Zie [Media verwerking schalen met CLI](media-reserved-units-cli-how-to.md)voor meer informatie.

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Bekijk het [Azure Media Services Community](media-services-community.md) -artikel voor verschillende manieren om vragen te stellen, feedback te geven en updates te ontvangen over Media Services.

## <a name="see-also"></a>Zie ook

* [Foutcodes](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Filteren, ordenen, paginering van Media Services entiteiten](entities-overview.md)

## <a name="next-steps"></a>Volgende stappen

- Controleer voordat u begint met het ontwikkelen [met behulp van Media Services v3 api's](media-services-apis-overview.md) (bevat informatie over het openen van api's, naam conventies, enzovoort).
- Bekijk deze zelf studies:

    - [Zelfstudie: Een extern bestand coderen op basis van URL en de video streamen](stream-files-tutorial-with-rest.md)
    - [Zelfstudie: Video's uploaden, coderen en streamen](stream-files-tutorial-with-api.md)
    - [Zelfstudie: Video's analyseren met Media Services v3](analyze-videos-tutorial-with-api.md)
