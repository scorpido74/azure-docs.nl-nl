---
title: Azure media Clipper gebruiken in de portal | Microsoft Docs
description: Clips maken met Azure media Clipper vanuit Azure Portal
services: media-services
keywords: clip; subclip; code ring; media
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: ec43fa469547dcd6481c0c6781c438f42ab4e2bd
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685000"
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Clips maken met Azure media Clipper in de portal  

U kunt Azure media Clipper in de portal gebruiken om clips te maken op basis van assets in uw Media Services-accounts. Om aan de slag te gaan, gaat u naar uw Media Services-account in de portal. Selecteer vervolgens het tabblad **subfragment** .

Op het tabblad **subfragment** kunt u beginnen met het opstellen van clips. In de portal laadt de Clipper Mp4's met één bitrate, multi-bitrate Mp4's en live-archieven die worden gepubliceerd met een geldige streaming-Locator. Niet-gepubliceerde assets zijn niet geladen.

De Clipper is momenteel beschikbaar als open bare preview. Ga naar deze [open bare voorbeeld pagina](https://portal.azure.com/?feature.subclipper=true)om de Clipper te openen in de Azure Portal.

De volgende afbeelding illustreert de landings pagina voor Clipper in uw Media Services-account: ![Azure media Clipper in Azure Portal](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>Clips produceren
Als u een clip wilt maken, sleept u een Asset naar de clip-interface en zet u deze neer. Als de markerings tijden bekend zijn, kunt u deze hand matig invoeren in de-interface. Als dat niet het geval is, kunt u het activum afspelen of de afspeelkop slepen om de gewenste markerings-en markerings tijd te vinden. Als er geen markerings-of markerings tijd is opgegeven, begint de clip vanaf het begin of gaat het naar het einde van de invoer Asset.

Als u wilt navigeren met frame-nauw keurigheid/GOP terug-nauw keurigheid, gebruikt u de knoppen frame-voorwaarts/GOP terug-forward of frame-backward/GOP terug-Backward. Voor knippen op meerdere assets kunt u meerdere assets slepen en neerzetten in de clip interface vanuit het deel venster activa selecteren. U kunt assets in de interface selecteren en opnieuw ordenen in de gewenste volg orde. In het deel venster activa selecteren worden de meta gegevens van de activa duur, het type en de oplossing voor elk activum verstrekt. Wanneer u meerdere assets samen voegen, moet u rekening houden met de bron resolutie van elk invoer bestand. Als de bron resoluties verschillen, wordt de invoer van een lagere resolutie geschaald om te voldoen aan de resolutie van het activum met de hoogste oplossing. Als u de uitvoer van de knip taak wilt bekijken, selecteert u de knop Preview en speelt de clip af vanaf de geselecteerde markerings tijden.

## <a name="producing-dynamic-manifest-filters"></a>Dynamische manifest filters produceren
[Dynamische manifest filters](https://azure.microsoft.com/blog/dynamic-manifest/) beschrijven een set regels op basis van manifest kenmerken en tijd lijn van activa. Deze regels bepalen hoe het streaming-eind punt de uitvoer afspeel lijst (manifest) bewerkt. Het filter kan worden gebruikt om te wijzigen welke segmenten worden gestreamd voor afspelen. De filters die worden geproduceerd door de Clipper zijn lokale filters en zijn specifiek voor de bron Asset. In tegens telling tot gerenderde clips zijn filters geen nieuwe assets en hoeven er geen coderings taak te worden geproduceerd. Ze kunnen snel worden gemaakt via de [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) of [rest API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest), maar ze zijn alleen GOP terug-nauw keurig. Activa gecodeerd voor streaming hebben doorgaans een GOP terug van twee seconden.

Als u een dynamisch manifest filter wilt maken, gaat u naar het tabblad **assets** en selecteert u de gewenste activa. Selecteer de knop **subfragment** in het bovenste menu. Selecteer dynamisch manifest filter als knip modus in het menu Geavanceerde instellingen. U kunt vervolgens hetzelfde proces volgen om een gerenderde clip te maken om het filter te creëren. Filters kunnen alleen worden geproduceerd vanuit één enkel activum.

In de volgende afbeelding ziet u de modus van dynamische manifest filter in de Azure Portal: ![Azure media Clipper in de modus voor dynamische manifest filtering in Azure Portal](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>Knip taken verzenden
Wanneer u klaar bent met het opstellen van de clip, selecteert u de knop taak verzenden om de bijbehorende knip opdracht of dynamische manifest aanroep te initiëren.

## <a name="next-steps"></a>Volgende stappen
Lees [het artikel aan de slag voor](media-services-azure-media-clipper-getting-started.md) meer informatie over het implementeren van de widget om aan de slag te gaan met Azure media Clipper.
