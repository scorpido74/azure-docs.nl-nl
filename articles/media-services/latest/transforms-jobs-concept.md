---
title: Transformaties en taken in mediaservices
titleSuffix: Azure Media Services
description: Meer informatie over het maken van een transformatie om de regels voor het verwerken van uw video's in Azure Media Services te beschrijven.
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
ms.openlocfilehash: ab99b974aed6f8cd5e1da2ee9b427f593b405889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73571241"
---
# <a name="transforms-and-jobs-in-media-services"></a>Transformaties en taken in mediaservices

In dit onderwerp vindt u details over [transformaties](https://docs.microsoft.com/rest/api/media/transforms) en [taken](https://docs.microsoft.com/rest/api/media/jobs) en wordt de relatie tussen deze entiteiten uitgelegd.

## <a name="overview"></a>Overzicht

### <a name="transformsjobs-workflow"></a>Werkstroom transformaties/taken

In het volgende diagram ziet u de werkstroom voor transformaties/taken:

![Werkstroom voor transformaties en taken in Azure Media Services](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Standaardwerkstroom

1. Maak een transformatie.
2. Vacatures indienen onder die transformatie.
3. Lijst transformaties.
4. Verwijder een transformatie als u niet van plan bent deze in de toekomst te gebruiken.

#### <a name="example"></a>Voorbeeld

Stel dat je het eerste frame van al je video's als miniatuurafbeelding wilt extraheren, de stappen die je zou nemen zijn:

1. Definieer het recept of de regel voor het verwerken van je video's: "gebruik het eerste frame van de video als miniatuur".
2. Voor elke video zou je de service vertellen:
    1. Waar vind je die video.
    2. Waar moet u de miniatuurafbeelding van de uitvoer schrijven.

Met een **transformatie** u het recept één keer maken (stap 1) en Vacatures indienen met dat recept (stap 2).

> [!NOTE]
> Eigenschappen van **Transformeren** en **taak** van het type Datetime zijn altijd in UTC-indeling.

## <a name="transforms"></a>Transformaties

Gebruik **Transformaties** om veelvoorkomende taken te configureren voor het coderen of analyseren van video's. Elke **transformatie** beschrijft een recept of een werkstroom van taken voor het verwerken van uw video- of audiobestanden. Eén transformatie kan meer dan één regel toepassen. Een transformatie kan bijvoorbeeld opgeven dat elke video wordt gecodeerd in een MP4-bestand met een bepaalde bitrate en dat een miniatuurafbeelding wordt gegenereerd vanaf het eerste frame van de video. U voegt één TransformOutput-vermelding toe voor elke regel die u in uw transformatie wilt opnemen. U gebruikt voorinstellingen om de transformatie te vertellen hoe de invoermediabestanden moeten worden verwerkt.

### <a name="viewing-schema"></a>Schema weergeven

In Media Services v3 zijn voorinstellingen sterk getypte entiteiten in de API zelf. U de definitie 'schema' voor deze objecten vinden in [Open API Specification (of Swagger).](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) U ook de vooraf ingestelde definities (zoals **StandardEncoderPreset)** bekijken in de [REST API,](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)of andere Media Services v3 SDK-referentiedocumentatie.

### <a name="creating-transforms"></a>Transformaties maken

U Transformaties maken met REST, CLI of een van de gepubliceerde SDK's. De Media Services v3 API wordt aangestuurd door Azure Resource Manager, zodat u ook Resource Manager-sjablonen gebruiken om transformaties te maken en te implementeren in uw Media Services-account. Op rollen gebaseerd toegangscontrolekan worden gebruikt om de toegang tot Transformaties te vergrendelen.

### <a name="updating-transforms"></a>Transformaties bijwerken

Als u uw [transformatie](https://docs.microsoft.com/rest/api/media/transforms)wilt bijwerken, gebruikt u de **bewerking Bijwerken.** Het is bedoeld voor het aanbrengen van wijzigingen in de beschrijving of de prioriteiten van de onderliggende TransformOutputs. Het wordt aanbevolen dergelijke updates te doen wanneer alle lopende taken zijn voltooid. Als u van plan bent het recept te herschrijven, moet u een nieuwe Transformatie maken.

### <a name="transform-object-diagram"></a>Objectdiagram transformeren

In het volgende diagram ziet u het object **Transformeren** en de objecten waarnaar wordt verwezen, inclusief de afleidingsrelaties. De grijze pijlen tonen een type dat in de verwijzingen naar taak en de groene pijlen klasseafleidingsrelaties weergeven.

Selecteer de afbeelding om deze op ware grootte weer te geven.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a>

## <a name="jobs"></a>Taken

Een **taak** is het feitelijke verzoek aan Media Services om de **transformatie** toe te passen op een bepaalde invoervideo of audio-inhoud. Zodra de transformatie is gemaakt, u vacatures indienen met behulp van Media Services API's of een van de gepubliceerde SDK's. De **taak** geeft informatie op, zoals de locatie van de invoervideo en de locatie voor de uitvoer. U de locatie van uw invoervideo opgeven met: HTTPS-URL's, SAS-URL's of [assets](https://docs.microsoft.com/rest/api/media/assets).  

### <a name="job-input-from-https"></a>Taakinvoer van HTTPS

Gebruik [taakinvoer van HTTPS](job-input-from-http-how-to.md) als uw inhoud al toegankelijk is via een URL en u het bronbestand niet hoeft op te slaan in Azure (bijvoorbeeld importeren uit S3). Deze methode is ook geschikt als u de inhoud in Azure Blob-opslag hebt, maar het bestand niet in een asset hoeft te hebben. Momenteel ondersteunt deze methode slechts één bestand voor invoer.

### <a name="asset-as-job-input"></a>Asset als jobinvoer

Gebruik [Asset als taakinvoer](job-input-from-local-file-how-to.md) als de invoerinhoud al in een asset staat of als de inhoud is opgeslagen in lokaal bestand. Het is ook een goede optie als u van plan bent om de invoerasset voor streaming of download te publiceren (stel dat u de mp4 wilt publiceren om te downloaden, maar ook spraak naar tekst of gezichtsherkenning wilt doen). Deze methode ondersteunt assets met meerdere bestanden (bijvoorbeeld MBR-streamingsets die lokaal zijn gecodeerd).

### <a name="checking-job-progress"></a>Voortgang van de taak controleren

De voortgang en status van taken kunnen worden verkregen door gebeurtenissen te monitoren met Event Grid. Zie [Gebeurtenissen controleren met EventGrid](job-state-events-cli-how-to.md)voor meer informatie.

### <a name="updating-jobs"></a>Taken bijwerken

De bewerking Bijwerken op de entiteit [Taak](https://docs.microsoft.com/rest/api/media/jobs) kan worden gebruikt om de *beschrijving* en de *prioriteitseigenschappen* te wijzigen nadat de taak is ingediend. Een wijziging in de *eigenschap prioriteit* is alleen effectief als de taak nog steeds in de wachtrijstaat staat. Als de taak is begonnen met de verwerking of is voltooid, heeft het wijzigen van prioriteit geen effect.

### <a name="job-object-diagram"></a>Diagram voor taakobject

In het volgende diagram ziet u het object **Job** en de objecten waarnaar wordt verwezen, inclusief de afleidingsrelaties.

Klik op de afbeelding om deze in volledig formaat weer te geven.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a>

## <a name="configure-media-reserved-units"></a>Gereserveerde media configureren

Voor de functies audioanalyse en videoanalyse die worden geactiveerd door Media Services v3 of Video Indexer, is het ten zeerste aan te raden om uw account in te richten bij 10 S3 Media Reserved Units (MRU's). Als u meer dan 10 S3-MRU's nodig hebt, opent u een ondersteuningsticket via de [Azure-portal.](https://portal.azure.com/)

Zie [Mediaverwerking schalen met CLI](media-reserved-units-cli-how-to.md)voor meer informatie.

## <a name="ask-questions-give-feedback-get-updates"></a>Stel vragen, geef feedback, ontvang updates

Bekijk het communityartikel [van Azure Media Services](media-services-community.md) om verschillende manieren te zien waarop u vragen stellen, feedback geven en updates ontvangen over Media Services.

## <a name="see-also"></a>Zie ook

* [Foutcodes](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Filteren, bestellen, paging van Media Services entiteiten](entities-overview.md)

## <a name="next-steps"></a>Volgende stappen

- Voordat u begint met het ontwikkelen, bekijkt [u Ontwikkelen met Media Services v3 API's](media-services-apis-overview.md) (inclusief informatie over toegang tot API's, naamgevingsconventies, enz.)
- Bekijk deze tutorials:

    - [Zelfstudie: Een extern bestand coderen op basis van URL en de video streamen](stream-files-tutorial-with-rest.md)
    - [Zelfstudie: Video's uploaden, coderen en streamen](stream-files-tutorial-with-api.md)
    - [Zelfstudie: Video's analyseren met Media Services v3](analyze-videos-tutorial-with-api.md)
