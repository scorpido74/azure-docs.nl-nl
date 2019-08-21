---
title: Aanbevolen procedures-Azure App Service
description: Lees de aanbevolen procedures en probleem oplossing voor Azure App Service.
services: app-service
documentationcenter: ''
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2016
ms.author: dariac
ms.custom: seodec18
ms.openlocfilehash: b4c7524415865f7db5d4514c14f8e45030620330
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636677"
---
# <a name="best-practices-for-azure-app-service"></a>Aanbevolen procedures voor Azure App Service
Dit artikel bevat een overzicht van de aanbevolen procedures voor het gebruik van [Azure app service](https://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Co
Als Azure-resources die een oplossing vormen, zoals een web-app en een Data Base zich in verschillende regio's bevinden, kan dit de volgende effecten hebben:

* Verhoogde latentie bij communicatie tussen bronnen
* De monetaire kosten voor uitgaande gegevens overdracht Kruis regio's zoals vermeld op de [pagina met prijzen voor Azure](https://azure.microsoft.com/pricing/details/data-transfers).

De co-locatie in dezelfde regio is het meest geschikt voor Azure-resources die een oplossing vormen, zoals een web-app en een Data Base of opslag account die wordt gebruikt om inhoud of gegevens te bevatten. Wanneer u resources maakt, moet u ervoor zorgen dat ze zich in dezelfde Azure-regio bevinden, tenzij u een specifieke zakelijke of ontwerp reden hebt. U kunt een App Service-app verplaatsen naar dezelfde regio als uw data base met behulp van de [functie voor app service klonen](app-service-web-app-cloning.md) die momenteel beschikbaar is voor Premium App service plan apps.   

## <a name="memoryresources"></a>Wanneer apps meer geheugen verbruiken dan verwacht
Wanneer u merkt dat een app meer geheugen verbruikt dan verwacht, zoals wordt aangegeven door middel van bewakings-of service aanbevelingen, moet u rekening houden met de [app service Automatische herstel functie](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Een van de opties voor de functie voor automatisch herstel is het nemen van aangepaste acties op basis van een drempel waarde voor geheugen. Acties omvatten het spectrum van e-mail meldingen tot onderzoek via de geheugen dump tot problemen door het werk proces te recyclen. Automatisch herstel kan worden geconfigureerd via web. config en via een beschrijvende gebruikers interface, zoals beschreven in dit blog bericht voor de [app service ondersteuning site-extensie](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Wanneer apps meer CPU verbruiken dan verwacht
Wanneer u merkt dat een app meer CPU verbruikt dan verwacht of een herhaalde CPU-pieken ondervindt, zoals wordt aangegeven door middel van bewakings-of service aanbevelingen, kunt u overwegen om het App Service plan omhoog of omhoog te schalen. Als uw toepassing stateful is, kunt u de optie alleen omhoog schalen selecteren. als uw toepassing stateless is, kunt u een grotere flexibiliteit en schaal baarheid bieden. 

Voor meer informatie over stateful en stateless toepassingen kunt u deze video bekijken: [Een schaal bare end-to-end toepassing met meerdere lagen plannen op Azure app service](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Zie [een web-app schalen in azure app service](manage-scale-up.md)voor meer informatie over opties voor app service schalen en automatisch schalen.  

## <a name="socketresources"></a>Wanneer socket bronnen worden uitgeput
Een veelvoorkomende reden voor het uitvallen van uitgaande TCP-verbindingen is het gebruik van client bibliotheken die niet zijn geïmplementeerd voor het opnieuw gebruiken van TCP-verbindingen, of wanneer een protocol op een hoger niveau, zoals HTTP-Keep-Alive, niet wordt gebruikt. Raadpleeg de documentatie voor elk van de bibliotheken waarnaar wordt verwezen door de apps in uw App Service-abonnement om te controleren of deze in uw code zijn geconfigureerd of geopend, zodat uitgaande verbindingen efficiënt kunnen worden hergebruikt. Volg ook de richt lijnen voor bibliotheek documentatie voor het maken en vrijgeven of opschonen om lekkende verbindingen te voor komen. Hoewel dergelijke client bibliotheken onderzoeken worden uitgevoerd, kan de impact worden verkleind door naar meerdere instanties te schalen.

### <a name="nodejs-and-outgoing-http-requests"></a>Node. js en uitgaande HTTP-aanvragen
Wanneer u werkt met node. js en veel uitgaande HTTP-aanvragen, is het belang rijk dat u de trans actie HTTP-Keep-Alive kunt gebruiken. U kunt het [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` -pakket gebruiken om uw code gemakkelijker te maken.

Het `http` antwoord altijd afhandelen, zelfs als u niets in de handler doet. Als u het antwoord niet op de juiste manier afhandelt, wordt uw toepassing uiteindelijk vastgelopen omdat er geen sockets meer beschikbaar zijn.

Als u bijvoorbeeld werkt met het pakket `http` of `https` , doet u het volgende:

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Als u op een machine met meerdere kernen gebruikmaakt van App Service op Linux best practice, is het gebruik van PM2 om meerdere node. js-processen te starten om uw toepassing uit te voeren. U kunt dit doen door een opstart opdracht op te geven in de container.

Als u bijvoorbeeld vier instanties wilt starten:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="appbackup"></a>Wanneer de back-up van de app niet kan worden gestart
De twee meest voorkomende oorzaken voor het mislukken van de app-back-up zijn: ongeldige opslag instellingen en ongeldige database configuratie. Deze fouten treden meestal op wanneer er wijzigingen zijn in de opslag-of database bronnen of wijzigingen in de toegang tot deze bronnen (bijvoorbeeld de referenties die zijn bijgewerkt voor de data base die is geselecteerd in de back-upinstellingen). Back-ups worden doorgaans volgens een planning uitgevoerd en vereisen toegang tot opslag (voor het uitvoeren van de back-upbestanden) en de data bases (voor het kopiëren en lezen van inhoud die u wilt opnemen in de back-up). Het resultaat van het mislukken van toegang tot een van deze resources is een consistente back-upfout. 

Wanneer er back-upfouten optreden, kunt u de meest recente resultaten bekijken om te begrijpen welk type fout er gebeurt. Bekijk de opslag instellingen die worden gebruikt in de back-upconfiguratie en werk deze bij voor toegangs fouten voor opslag. Voor toegang tot de Data Base kunt u verbindings reeksen controleren en bijwerken als onderdeel van de app-instellingen. Wijzig vervolgens de back-upconfiguratie om de vereiste data bases op de juiste wijze op te zetten. Zie [een back-up maken van een web-app in azure app service](manage-backup.md)voor meer informatie over app-back-ups.

## <a name="nodejs"></a>Als nieuwe node. js-apps worden geïmplementeerd in Azure App Service
Azure App Service standaard configuratie voor node. js-apps is bedoeld om het beste te voldoen aan de behoeften van de meest voorkomende apps. Als de configuratie voor uw node. js-app zou kunnen profiteren van persoonlijke afstemming om prestaties te verbeteren of het resource gebruik te optimaliseren voor CPU/geheugen/netwerk bronnen, raadpleegt u [Aanbevolen procedures en richt lijnen voor probleem oplossing voor knooppunt toepassingen op Azure app service](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). In dit artikel worden de iisnode-instellingen beschreven die u mogelijk moet configureren voor uw node. js-app, worden de verschillende scenario's of problemen beschreven die aan uw app zijn gericht en wordt uitgelegd hoe u deze problemen kunt oplossen.

