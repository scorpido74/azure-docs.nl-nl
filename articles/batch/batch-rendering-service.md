---
title: Renderingoverzicht - Azure Batch
description: Introductie van het gebruik van Azure voor rendering en een overzicht van azure batch rendering mogelijkheden
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: d4423b22c4c8afea5afa9c7040e081665b17ba87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60774026"
---
# <a name="rendering-using-azure"></a>Weergeven met Azure

Rendering is het proces van het nemen van 3D-modellen en het omzetten ervan in 2D-beelden. 3D-scènebestanden zijn geschreven in toepassingen zoals Autodesk 3ds Max, Autodesk Maya en Blender.  Rendering toepassingen zoals Autodesk Maya, Autodesk Arnold, Chaos Group V-Ray, en Blender Cycles produceren 2D-beelden.  Soms worden afzonderlijke afbeeldingen gemaakt van de scènebestanden. Het is echter gebruikelijk om meerdere afbeeldingen te modelleren en weer te geven en deze vervolgens te combineren in een animatie.

De rendering workload wordt veel gebruikt voor special effects (VFX) in de Media en Entertainment industrie. Rendering wordt ook gebruikt in veel andere branches, zoals reclame, detailhandel, olie- en gasindustrie, en productie.

Het proces van rendering is rekenintensief; er kunnen veel frames / afbeeldingen te produceren en elke afbeelding kan vele uren duren om te renderen.  Rendering is daarom een perfecte batchprocessingwerkdie Azure en Azure Batch kan gebruiken om veel renders parallel uit te voeren.

## <a name="why-use-azure-for-rendering"></a>Waarom Azure gebruiken voor rendering?

Om vele redenen is rendering een workload die perfect geschikt is voor Azure en Azure Batch:

* Rendering taken kunnen worden opgesplitst in vele stukken die parallel kunnen worden uitgevoerd met behulp van meerdere VM's:
  * Animaties bestaan uit vele frames en elk frame kan parallel worden weergegeven.  Hoe meer VM's beschikbaar zijn om elk frame te verwerken, hoe sneller alle frames en de animatie kunnen worden geproduceerd.
  * In sommige renderingsoftware kunnen afzonderlijke frames in meerdere stukken worden opgesplitst, zoals tegels of segmenten.  Elk stuk kan afzonderlijk worden weergegeven, vervolgens gecombineerd in de uiteindelijke afbeelding wanneer alle stukken zijn voltooid.  Hoe meer VM's beschikbaar zijn, hoe sneller een frame kan worden weergegeven.
* Rendering projecten kunnen vereisen enorme schaal:
  * Individuele frames kunnen complex zijn en vele uren nodig hebben om te renderen, zelfs op high-end hardware; animaties kunnen bestaan uit honderdduizenden frames.  Een enorme hoeveelheid rekenkracht is nodig om animaties van hoge kwaliteit te renderen in een redelijke hoeveelheid tijd.  In sommige gevallen zijn meer dan 100.000 cores gebruikt om duizenden frames parallel weer te geven.
* Renderingprojecten zijn projectmatig en vereisen verschillende hoeveelheden rekenkracht:
  * Wijs de reken- en opslagcapaciteit toe wanneer dat nodig is, schaal deze op of neer op basis van de belasting tijdens een project en verwijder deze wanneer een project is voltooid.
  * Betaal voor capaciteit wanneer toegewezen, maar betaal er niet voor wanneer er geen belasting is, zoals tussen projecten.
  * Tegemoet te komen aan uitbarstingen als gevolg van onverwachte veranderingen; schaal hoger als er onverwachte wijzigingen te laat in een project en deze wijzigingen moeten worden verwerkt op een strak schema.
* Kies uit een brede selectie hardware op basis van toepassing, werkbelasting en tijdsbestek:
  * Er is een brede selectie van hardware beschikbaar in Azure die kan worden toegewezen en beheerd met Batch.
  * Afhankelijk van het project kan de eis zijn voor de beste prijs/prestatie of de beste algemene prestaties.  Verschillende scènes en/of renderingtoepassingen hebben verschillende geheugenvereisten.  Sommige rendering-toepassingen kunnen GPU's gebruiken voor de beste prestaties of bepaalde functies. 
* VM's met lage prioriteit verlagen de kosten:
  * VM's met lage prioriteit zijn beschikbaar voor een grote korting in vergelijking met reguliere on-demand VM's en zijn geschikt voor sommige taaktypen.
  * Vm's met lage prioriteit kunnen worden toegewezen door Azure Batch, waarbij Batch flexibiliteit biedt over hoe ze worden gebruikt om te voldoen aan een breed scala aan vereisten.  Batchpools kunnen bestaan uit zowel speciale als vm's met lage prioriteit, waarbij het mogelijk is om de mix van VM-typen op elk gewenst moment te wijzigen.

## <a name="options-for-rendering-on-azure"></a>Opties voor rendering op Azure

Er zijn een aantal Azure-mogelijkheden die kunnen worden gebruikt voor het renderen van workloads.  Welke mogelijkheden u wilt gebruiken, is afhankelijk van elke bestaande omgeving en vereisten.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>Bestaande on-premises renderingomgeving met behulp van een renderbeheertoepassing

Het meest voorkomende geval is dat er een bestaande on-premises renderfarm is die wordt beheerd door een rendermanagementtoepassing zoals PipelineFX Qube, Royal Render of Thinkbox Deadline.  De vereiste is om de on-premises renderfarmcapaciteit uit te breiden met Azure VM's.

De renderbeheersoftware heeft azure-ondersteuning ingebouwd of we stellen plug-ins beschikbaar die Azure-ondersteuning toevoegen. Zie het artikel over [het gebruik van rendermanagers](https://docs.microsoft.com/azure/batch/batch-rendering-render-managers)voor meer informatie over de ingeschakelde ondersteunde rendermanagers en functionaliteit.

### <a name="custom-rendering-workflow"></a>Aangepaste renderingwerkstroom

De eis is dat VM's een bestaande renderfarm uitbreiden.  Azure Batch-groepen kunnen grote aantallen VM's toewijzen, vm's met lage prioriteit kunnen gebruiken en dynamisch automatisch worden geschaald met volledig geprijsde VM's en licenties bieden voor het gebruik van populaire renderingtoepassingen.

### <a name="no-existing-render-farm"></a>Geen bestaande renderfarm

Clientwerkstations kunnen rendering uitvoeren, maar de renderingworkload neemt toe en het duurt te lang om alleen werkstationcapaciteit te gebruiken.  Azure Batch kan worden gebruikt om zowel render farm compute on-demand toe te wijzen als om de rendertaken te plannen naar de Azure renderfarm.

## <a name="azure-batch-rendering-capabilities"></a>Azure Batch-renderingmogelijkheden

Azure Batch maakt het mogelijk parallelle workloads uit te voeren in Azure.  Het maakt het mogelijk om grote aantallen VM's te maken en te beheren waarop toepassingen worden geïnstalleerd en uitgevoerd.  Het biedt ook uitgebreide mogelijkheden voor taakplanning om exemplaren van deze toepassingen uit te voeren, waarbij taken worden toegewezen aan VM's, wachtrijen, toepassingsbewaking, enzovoort.

Azure Batch wordt gebruikt voor veel workloads, maar de volgende mogelijkheden zijn beschikbaar om het eenvoudiger en sneller uit te voeren om renderingworkloads uit te voeren.

* VM-afbeeldingen met vooraf geïnstalleerde grafische en renderingtoepassingen:
  * Azure Marketplace VM-afbeeldingen zijn beschikbaar die populaire afbeeldingen en renderingtoepassingen bevatten, waardoor de toepassingen niet zelf hoeven te worden geïnstalleerd of uw eigen aangepaste afbeeldingen moeten worden gemaakt met de geïnstalleerde toepassingen. 
* Licenties voor betalen per gebruik voor het renderen van toepassingen:
  * U ervoor kiezen om te betalen voor de toepassingen per minuut, in aanvulling op het betalen voor de compute VM's, die voorkomt dat licenties te kopen en mogelijk configureren van een licentieserver voor de toepassingen.  Betalen voor gebruik betekent ook dat het mogelijk is om tegemoet te komen aan wisselende en onverwachte belasting, omdat er geen vast aantal licenties is.
  * Het is ook mogelijk om de vooraf geïnstalleerde applicaties te gebruiken met uw eigen licenties en geen gebruik te maken van de pay-per-use licenties. Om dit te doen, installeert u doorgaans een on-premises of Azure-gebaseerde licentieserver en gebruikt u een virtueel Azure-netwerk om de renderingpool aan te sluiten op de licentieserver.
* Plug-ins voor clientontwerp- en modelleringstoepassingen:
  * Met plug-ins kunnen eindgebruikers Azure Batch rechtstreeks vanuit clienttoepassing gebruiken, zoals Autodesk Maya, zodat ze pools kunnen maken, taken kunnen indienen en meer rekencapaciteit kunnen gebruiken om snellere renders uit te voeren.
* Render manager integratie:
  * Azure Batch is geïntegreerd in renderbeheertoepassingen of er zijn plug-ins beschikbaar om de Azure Batch-integratie te bieden.

Er zijn verschillende manieren om Azure Batch te gebruiken, die allemaal ook van toepassing zijn op Azure Batch-rendering.

* API's:
  * Schrijf code met behulp van de [REST](https://docs.microsoft.com/rest/api/batchservice), [.NET,](https://docs.microsoft.com/dotnet/api/overview/azure/batch) [Python,](https://docs.microsoft.com/python/api/overview/azure/batch) [Java](https://docs.microsoft.com/java/api/overview/azure/batch)of andere ondersteunde API's.  Ontwikkelaars kunnen Azure Batch-mogelijkheden integreren in hun bestaande toepassingen of workflow, zowel in de cloud als op basis van on-premises.  De [Autodesk Maya-plug-in](https://github.com/Azure/azure-batch-maya) maakt bijvoorbeeld gebruik van de Batch Python API om batch aan te roepen, pools te maken en te beheren, taken en taken in te dienen en de status van controle te controleren.
* Opdrachtregelgereedschappen:
  * De [Azure-opdrachtregel](https://docs.microsoft.com/cli/azure/) of [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) kan worden gebruikt voor het gebruik van batch.
  * Met name de batch-CLI-sjabloonondersteuning maakt het veel gemakkelijker om pools te maken en taken in te dienen.
* Uis:
  * [Batch Explorer](https://github.com/Azure/BatchExplorer) is een cross-platform clienttool waarmee batchaccounts ook kunnen worden beheerd en gecontroleerd, maar die een aantal uitgebreidere mogelijkheden biedt in vergelijking met de Gebruikersinterface van de Azure-portal.  Er wordt een set pool- en taaksjablonen verstrekt die zijn afgestemd op elke ondersteunde toepassing en die kunnen worden gebruikt om eenvoudig pools te maken en taken in te dienen.
  * De Azure-portal kan worden gebruikt voor het beheren en bewaken van Azure Batch.
* Client applicatie plug-in's:
  * Er zijn plug-ins beschikbaar waarmee batchrendering rechtstreeks binnen de clientontwerp- en modelleringstoepassingen kan worden gebruikt. De plug-ins roepen voornamelijk de Batch Explorer-toepassing op met contextuele informatie over het huidige 3D-model.
  * De volgende plug-ins zijn beschikbaar:
    * [Azure-batch voor Maya](https://github.com/Azure/azure-batch-maya)
    * [3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Aan de slag met Azure Batch-rendering

Bekijk de volgende inleidende zelfstudies om Azure Batch-rendering uit te proberen:

* [Batch Explorer gebruiken om een Blender-scène weer te geven](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
* [De Batch CLI gebruiken om een Autodesk 3ds Max-scène weer te geven](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)

## <a name="next-steps"></a>Volgende stappen

Bepaal de lijst met rendertoepassingen en -versies die zijn opgenomen in de Azure Marketplace VM-afbeeldingen in [dit artikel.](https://docs.microsoft.com/azure/batch/batch-rendering-applications)