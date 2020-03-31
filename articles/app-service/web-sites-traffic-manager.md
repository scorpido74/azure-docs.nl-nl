---
title: Verkeer regelen met Traffic Manager
description: Best practices voor het configureren van Azure Traffic Manager wanneer u het integreert met Azure App Service.
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.topic: article
ms.date: 02/25/2016
ms.custom: seodec18
ms.openlocfilehash: 200effab70b369d69b4e89b1901578ecfe1a1b87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684102"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Azure App Service-verkeer beheren met Azure Traffic Manager
> [!NOTE]
> In dit artikel vindt u beknopte informatie voor Microsoft Azure Traffic Manager als het gaat om Azure App Service. Meer informatie over Azure Traffic Manager zelf vindt u door de links aan het einde van dit artikel te bezoeken.
> 
> 

## <a name="introduction"></a>Inleiding
U kunt Azure Traffic Manager gebruiken om te bepalen hoe aanvragen van web-clients worden gedistribueerd naar apps in Azure App Service. Wanneer App Service-eindpunten worden toegevoegd aan een Azure Traffic Manager-profiel, houdt Azure Traffic Manager de status van uw App Service-apps bij (actief, gestopt of verwijderd) zodat de service kan bepalen welke van deze eindpunten verkeer moeten ontvangen.

## <a name="routing-methods"></a>Routeringsmethoden
Azure Traffic Manager gebruikt vier verschillende routeringsmethoden. Deze methoden worden beschreven in de volgende lijst als ze betrekking hebben op Azure App Service.

* ** [Prioriteit:](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)** gebruik een primaire app voor al het verkeer en maak back-ups voor het geval de primaire of de back-up-apps niet beschikbaar zijn.
* ** [Gewogen](../traffic-manager/traffic-manager-routing-methods.md#weighted):** verkeer verdelen over een set apps, gelijkmatig of op basis van gewichten, die u definieert.
* ** [Prestaties:](../traffic-manager/traffic-manager-routing-methods.md#performance)** wanneer u apps op verschillende geografische locaties hebt, gebruikt u de 'dichtstbijzijnde' app in termen van de laagste netwerklatentie.
* ** [Geografisch:](../traffic-manager/traffic-manager-routing-methods.md#geographic)** gebruikers doorverwijzen naar specifieke apps op basis van welke geografische locatie hun DNS-query afkomstig is. 

Zie [Routeringsmethoden voor verkeersbeheer](../traffic-manager/traffic-manager-routing-methods.md)voor meer informatie .

## <a name="app-service-and-traffic-manager-profiles"></a>App Service- en Traffic Manager-profielen
Als u het beheer van app-appverkeer van App Service wilt configureren, maakt u een profiel in Azure Traffic Manager dat een van de vier eerder beschreven taakverdelingsmethoden gebruikt en voegt u vervolgens de eindpunten (in dit geval App Service) toe waarvoor u verkeer wilt beheren naar de Profiel. Uw app-status (uitgevoerd, gestopt of verwijderd) wordt regelmatig naar het profiel gecommuniceerd, zodat Azure Traffic Manager het verkeer dienovereenkomstig kan leiden.

Houd bij het gebruik van Azure-beheer rekening met de volgende punten:

* Voor app-implementaties binnen dezelfde regio biedt App Service al failover- en round-robin-functionaliteit zonder rekening te houden met de app-modus.
* Voor implementaties in dezelfde regio die App Service gebruiken in combinatie met een andere Azure-cloudservice, u beide typen eindpunten combineren om hybride scenario's in te schakelen.
* U slechts één eindpunt van de app-service per regio opgeven in een profiel. Wanneer u een app selecteert als eindpunt voor één regio, zijn de overige apps in die regio niet beschikbaar voor selectie voor dat profiel.
* De eindpunten van de App-service die u opgeeft in een Azure Traffic Manager-profiel, worden weergegeven onder de sectie **Domeinnamen** op de pagina Configureren voor de app in het profiel, maar kunnen daar niet worden geconfigureerd.
* Nadat u een app aan een profiel hebt toegevoegd, wordt op de URL van de **site** op het dashboard van de portalpagina van de app de aangepaste domein-URL van de app weergegeven als u er een hebt ingesteld. Anders wordt de URL van het Traffic `contoso.trafficmanager.net`Manager-profiel weergegeven (bijvoorbeeld ). Zowel de directe domeinnaam van de app als de URL van Traffic Manager zijn zichtbaar op de pagina Configureren van de app onder de sectie **Domeinnamen.**
* Uw aangepaste domeinnamen werken zoals verwacht, maar naast het toevoegen ervan aan uw apps, moet u ook uw DNS-kaart configureren om naar de URL van Traffic Manager te wijzen. Zie [Een bestaande aangepaste DNS-naam toewijzen aan Azure App Service](app-service-web-tutorial-custom-domain.md)voor informatie over het instellen van een aangepast domein voor een App Service-app.
* U alleen apps die zich in de standaard- of premiummodus bevinden toevoegen aan een Azure Traffic Manager-profiel.

## <a name="next-steps"></a>Volgende stappen
Zie [Traffic Manager Overview](../traffic-manager/traffic-manager-overview.md)voor een conceptueel en technisch overzicht van Azure Traffic Manager.


