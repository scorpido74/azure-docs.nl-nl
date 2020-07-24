---
title: Overzicht van rendering
description: Inleiding tot het gebruik van Azure voor rendering en een overzicht van de mogelijkheden van Azure Batch-Rendering
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 9fac5d3efabc5d9f796c91d688f35e01aeefdca3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092759"
---
# <a name="rendering-using-azure"></a>Weergeven met Azure

Rendering is het proces van het maken van 3D-modellen en het converteren ervan naar 2D-afbeeldingen. 3D-scène bestanden worden in toepassingen geschreven, zoals auto Desk 3ds Max, Autodesk Maya en blender.  Het renderen van toepassingen zoals Autodesk Maya, Autodesk Arnold, chaos groep V-Ray en overvloei cycli produceren 2D-installatie kopieën.  Soms worden afzonderlijke installatie kopieën uit de scène bestanden gemaakt. Het is echter gebruikelijk om meerdere installatie kopieën te model leren en weer te geven en deze vervolgens te combi neren in een animatie.

De werk belasting voor rendering wordt intensief gebruikt voor speciale effecten (VFX) in de media en entertainment industrie. Rendering wordt ook gebruikt in veel andere branches, zoals reclame, detailhandel, olie- en gasindustrie, en productie.

Het proces voor rendering is reken intensief; Er kunnen veel frames/afbeeldingen worden gemaakt en elke afbeelding kan veel uur duren om weer te geven.  Rendering is daarom een perfecte werk belasting voor batch verwerking die gebruikmaakt van Azure en Azure Batch om een groot aantal weer Parallelen te kunnen uitvoeren.

## <a name="why-use-azure-for-rendering"></a>Waarom Azure voor Rendering gebruiken?

Rendering is om verschillende redenen een werk belasting die perfect geschikt is voor Azure en Azure Batch:

* Rendering-taken kunnen worden gesplitst in veel onderdelen die parallel kunnen worden uitgevoerd met meerdere Vm's:
  * Animaties bestaan uit een groot aantal frames en elk frame kan parallel worden weer gegeven.  Hoe meer Vm's er beschikbaar zijn voor het verwerken van elk frame, hoe sneller alle frames en de animatie kunnen worden geproduceerd.
  * Met sommige beeldrenderings software kunnen afzonderlijke frames worden opgedeeld in meerdere delen, zoals tegels of segmenten.  Elk stuk kan afzonderlijk worden gerenderd en vervolgens in de uiteindelijke afbeelding worden gecombineerd wanneer alle onderdelen zijn voltooid.  Hoe meer Vm's er beschikbaar zijn, hoe sneller een frame kan worden weer gegeven.
* Voor het renderen van projecten kunnen zeer grote schalen worden vereist:
  * Afzonderlijke frames kunnen ingewikkeld zijn en veel uur duren om weer te geven, zelfs op een geavanceerde hardware. animaties kunnen bestaan uit honderd duizenden frames.  Een enorme hoeveelheid reken kracht is vereist voor het renderen van animaties van hoge kwaliteit in een redelijke hoeveelheid tijd.  In sommige gevallen zijn er meer dan 100.000 kern geheugens gebruikt om duizenden frames parallel weer te geven.
* Rendering van projecten is gebaseerd op een project en vereisen verschillende hoeveel heden reken kracht:
  * Wijs reken-en opslag capaciteit toe, indien nodig, schaal deze naar boven of beneden op basis van de belasting tijdens een project en verwijder deze wanneer een project is voltooid.
  * U betaalt voor de capaciteit wanneer deze is toegewezen, maar niet voor deze betaling wanneer er geen belasting is, zoals tussen projecten.
  * Keuken voor bursts vanwege onverwachte wijzigingen; schaal hoger als er onverwachte wijzigingen in een project aanwezig zijn en die wijzigingen moeten worden verwerkt op een strak schema.
* Kies uit een breed scala aan hardware op basis van de toepassing, workload en tijds bestek:
  * Er is een brede selectie van hardware beschikbaar in azure die kan worden toegewezen en beheerd met batch.
  * Afhankelijk van het project is de vereiste mogelijk voor de beste prijs-prestatie verhouding of de beste algehele prestaties.  Verschillende scènes en/of rendering-toepassingen hebben andere geheugen vereisten.  Sommige rendering-toepassingen kunnen gebruikmaken van Gpu's voor de beste prestaties of bepaalde functies. 
* Vm's met een lage prioriteit verlagen de kosten:
  * Vm's met lage prioriteit zijn beschikbaar voor een grote korting vergeleken met de normale Vm's op aanvraag en zijn geschikt voor sommige taak typen.
  * Virtuele machines met lage prioriteit kunnen worden toegewezen door Azure Batch, waarbij batch flexibiliteit biedt voor de manier waarop ze worden gebruikt voor een groot aantal vereisten.  Batch-Pools kunnen bestaan uit een of meer Vm's met een lage prioriteit, omdat het mogelijk is om de combi natie van VM-typen op elk gewenst moment te wijzigen.

## <a name="options-for-rendering-on-azure"></a>Opties voor rendering op Azure

Er zijn tal van Azure-functies die kunnen worden gebruikt voor het renderen van werk belastingen.  Welke mogelijkheden u moet gebruiken, is afhankelijk van eventuele bestaande omgevingen en vereisten.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>Bestaande on-premises rendering-omgeving met behulp van een toepassing voor render beheer

Het meest voorkomende geval is dat er een bestaande on-premises weergave farm wordt beheerd door een beeldrenderings toepassing, zoals PipelineFX Qube, Royal rendering of Thinkbox deadline.  De vereiste is om de on-premises weer gave-Farm capaciteit uit te breiden met behulp van virtuele Azure-machines.

De software voor het renderen van apparaten heeft ingebouwde ondersteuning voor Azure of we maken beschik bare invoeg toepassingen die ondersteuning voor Azure toevoegen. Zie het artikel over het [gebruik van render-beheerders](./batch-rendering-render-managers.md)voor meer informatie over de ondersteunde weergave beheerders en de ingeschakelde functionaliteit.

### <a name="custom-rendering-workflow"></a>Aangepaste werk stroom voor Rendering

De vereiste is voor virtuele machines om een bestaande render-farm uit te breiden.  Azure Batch groepen kunnen grote aantallen virtuele machines toewijzen, virtuele machines met lage prioriteit kunnen worden gebruikt en dynamisch automatisch worden geschaald op basis van volle geprijsde Vm's, en licenties voor betalen voor gebruik bieden voor populaire rendering-toepassingen.

### <a name="no-existing-render-farm"></a>Geen bestaande render-farm

Client werkstations kunnen rendering uitvoeren, maar de rendering van de werk belasting neemt toe en het duurt te lang om alleen de capaciteit van werk stations te gebruiken.  Azure Batch kan worden gebruikt voor het toewijzen van een werk belasting op aanvraag voor het genereren van een farm en het plannen van de weergave taken voor de Azure-weergave farm.

## <a name="azure-batch-rendering-capabilities"></a>Mogelijkheden voor Azure Batch Rendering

Azure Batch kunnen parallelle workloads worden uitgevoerd in Azure.  Hiermee maakt u het maken en beheren van een groot aantal virtuele machines waarop toepassingen worden geïnstalleerd en worden uitgevoerd.  Het biedt ook uitgebreide functies voor taak planning voor het uitvoeren van exemplaren van deze toepassingen, waarbij de toewijzing van taken aan Vm's, Queuing, toepassings bewaking, enzovoort.

Azure Batch wordt gebruikt voor veel werk belastingen, maar de volgende mogelijkheden zijn beschikbaar om het samen stellen van werk belastingen gemakkelijker en sneller uit te voeren.

* VM-installatie kopieën met vooraf geïnstalleerde afbeeldingen en rendering-toepassingen:
  * Er zijn Azure Marketplace-VM-installatie kopieën beschikbaar die populaire grafische toepassingen bevatten, zodat u niet zelf de toepassingen hoeft te installeren of uw eigen aangepaste installatie kopieën wilt maken met de toepassingen die zijn geïnstalleerd. 
* Betalen per gebruik-licentie voor het weer geven van toepassingen:
  * U kunt ervoor kiezen om per minuut te betalen voor de toepassingen, maar niet alleen voor de berekenings-Vm's, waardoor het niet mogelijk is om licenties te kopen en een licentie server voor de toepassingen te configureren.  Als u betaalt voor gebruik, betekent dit ook dat het mogelijk is om de belasting te variëren en onverwacht te laden omdat er geen vast aantal licenties is.
  * Het is ook mogelijk om de vooraf geïnstalleerde toepassingen met uw eigen licenties te gebruiken en de licentie voor betalen per gebruik niet te gebruiken. Hiervoor installeert u doorgaans een on-premises of op Azure gebaseerde licentie server en gebruikt u een virtueel Azure-netwerk om de opbouw groep te verbinden met de licentie server.
* Invoeg toepassingen voor client ontwerp-en modellerings toepassingen:
  * Met invoeg toepassingen kunnen eind gebruikers Azure Batch rechtstreeks vanuit een client toepassing gebruiken, zoals Autodesk Maya, zodat ze groepen kunnen maken, projecten kunnen verzenden en gebruik kunnen maken van meer reken capaciteit om sneller weer te geven.
* Integratie van render Manager:
  * Azure Batch is geïntegreerd in toepassingen voor weergave beheer of invoeg toepassingen die de Azure Batch-integratie kunnen bieden.

Er zijn verschillende manieren om Azure Batch te gebruiken, die allemaal ook van toepassing zijn op Azure Batch rendering.

* API's:
  * Code schrijven met behulp van de [rest](/rest/api/batchservice)-, [.net](/dotnet/api/overview/azure/batch)-, [python](/python/api/overview/azure/batch)-, [Java](/java/api/overview/azure/batch)-of andere ondersteunde api's.  Ontwikkel aars kunnen Azure Batch-mogelijkheden integreren in hun bestaande toepassingen of werk stroom, of deze in de Cloud of op basis van on-premises.  De [Autodesk Maya-invoeg toepassing](https://github.com/Azure/azure-batch-maya) maakt bijvoorbeeld gebruik van de batch PYTHON-API voor het aanroepen van batch, het maken en beheren van groepen, het verzenden van taken en taken en het controleren van de status.
* Opdracht regel Programma's:
  * De [Azure-opdracht regel](/cli/azure/) of- [Azure PowerShell](/powershell/azure/) kan worden gebruikt voor het uitvoeren van scripts voor batch gebruik.
  * Met name de batch-CLI-sjabloon ondersteuning maakt het veel eenvoudiger om Pools te maken en taken te verzenden.
* UIs:
  * [Batch Explorer](https://github.com/Azure/BatchExplorer) is een platform voor meerdere platforms waarmee batch-accounts ook kunnen worden beheerd en bewaakt, maar biedt een aantal rijkere mogelijkheden in vergelijking met de Azure Portal-gebruikers interface.  Er zijn een set groeps-en taak sjablonen beschikbaar die zijn afgestemd op elke ondersteunde toepassing en kunnen worden gebruikt om eenvoudig Pools te maken en taken te verzenden.
  * De Azure Portal kan worden gebruikt om Azure Batch te beheren en te controleren.
* Invoeg toepassing voor client toepassing:
  * Invoeg toepassingen zijn beschikbaar waarmee batch rendering rechtstreeks kan worden gebruikt in de client ontwerp-en modelleer toepassingen. De invoeg toepassingen roepen voornamelijk de Batch Explorer-toepassing met contextuele informatie over het huidige 3D-model.
  * De volgende invoeg toepassingen zijn beschikbaar:
    * [Azure Batch voor Maya](https://github.com/Azure/azure-batch-maya)
    * [3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Aan de slag met het weer geven van Azure Batch

Raadpleeg de volgende inleidende zelf studies om Azure Batch rendering te proberen:

* [Batch Explorer gebruiken om een overvloei scène weer te geven](./tutorial-rendering-batchexplorer-blender.md)
* [De batch-CLI gebruiken voor het weer geven van een auto Desk 3ds Max scène](./tutorial-rendering-cli.md)

## <a name="next-steps"></a>Volgende stappen

Bepaal de lijst met rendering-toepassingen en versies die zijn opgenomen in de VM-installatie kopieën van Azure Marketplace in [dit artikel](./batch-rendering-applications.md).
