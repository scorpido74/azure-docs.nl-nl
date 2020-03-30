---
title: Beste praktijken
description: Lees aanbevolen procedures en de veelvoorkomende probleemoplossingsscenario's voor uw app die worden uitgevoerd in Azure App Service.
author: dariagrigoriu
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.topic: article
ms.date: 07/01/2016
ms.author: dariac
ms.custom: seodec18
ms.openlocfilehash: ded812d5d7a0440466e7284b56c90965ea00406e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768483"
---
# <a name="best-practices-for-azure-app-service"></a>Aanbevolen procedures voor Azure App Service
In dit artikel worden aanbevolen procedures voor het gebruik van [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)samengevat. 

## <a name="colocation"></a><a name="colocation"></a>Colocatie
Wanneer Azure-resources die een oplossing samenstellen, zoals een web-app en een database, zich in verschillende regio's bevinden, kan dit de volgende effecten hebben:

* Verhoogde latentie in communicatie tussen resources
* Monetaire kosten voor uitgaande gegevensoverdracht tussen regio's zoals vermeld op de [prijspagina van Azure](https://azure.microsoft.com/pricing/details/data-transfers).

Colocatie in dezelfde regio is het beste voor Azure-resources die een oplossing samenstellen, zoals een web-app en een database- of opslagaccount dat wordt gebruikt om inhoud of gegevens vast te houden. Controleer bij het maken van resources of ze zich in dezelfde Azure-regio bevinden, tenzij u specifieke bedrijfs- of ontwerpreden hebt om dat niet te doen. U een App Service-app verplaatsen naar dezelfde regio als uw database met behulp van de [app-service-kloonfunctie](app-service-web-app-cloning.md) die momenteel beschikbaar is voor apps van het Premium App-serviceplan.   

## <a name="when-apps-consume-more-memory-than-expected"></a><a name="memoryresources"></a>Wanneer apps meer geheugen verbruiken dan verwacht
Wanneer u merkt dat een app meer geheugen verbruikt dan verwacht, zoals aangegeven via monitoring- of serviceaanbevelingen, u rekening houden met de [functie Auto-Healing van app-service.](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites) Een van de opties voor de functie Automatisch herstellen is het uitvoeren van aangepaste acties op basis van een geheugendrempel. Acties omvatten het spectrum van e-mailmeldingen tot onderzoek via geheugendump tot beperking ter plaatse door het werkproces te recyclen. Auto-healing kan worden geconfigureerd via web.config en via een vriendelijke gebruikersinterface zoals beschreven in deze blogpost voor de [App Service Support Site Extension.](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps)   

## <a name="when-apps-consume-more-cpu-than-expected"></a><a name="CPUresources"></a>Wanneer apps meer CPU verbruiken dan verwacht
Wanneer u merkt dat een app meer CPU verbruikt dan verwacht of herhaalde CPU-pieken ervaart, zoals aangegeven via monitoring- of serviceaanbevelingen, u overwegen het App Service-abonnement op te schalen of uit te schalen. Als uw toepassing stateful is, is opschalen de enige optie, terwijl als uw toepassing stateloos is, het uitschalen u meer flexibiliteit en een groter schaalpotentieel biedt. 

Voor meer informatie over 'stateful' versus 'stateless' toepassingen u deze video bekijken: [Een schaalbare end-to-end multi-tier-toepassing plannen op Azure App Service.](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid) Zie [Een webapp schalen in Azure App-service](manage-scale-up.md)voor meer informatie over opties voor schalen en automatisch schalen van app-service.  

## <a name="when-socket-resources-are-exhausted"></a><a name="socketresources"></a>Wanneer socketresources zijn uitgeput
Een veelvoorkomende reden voor het uitputten van uitgaande TCP-verbindingen is het gebruik van clientbibliotheken, die niet worden geïmplementeerd om TCP-verbindingen opnieuw te gebruiken of wanneer een protocol op een hoger niveau zoals HTTP - Keep-Alive niet wordt gebruikt. Bekijk de documentatie voor elk van de bibliotheken waarnaar wordt verwezen door de apps in uw App Service Plan om te controleren of ze zijn geconfigureerd of geopend in uw code voor efficiënt hergebruik van uitgaande verbindingen. Volg ook de documentatierichtlijnen voor de bibliotheek voor het juiste maken en vrijgeven of opruimen om lekkende verbindingen te voorkomen. Terwijl dergelijke onderzoeken naar clientbibliotheken worden uitgevoerd, kunnen de gevolgen worden beperkt door uit te schalen naar meerdere exemplaren.

### <a name="nodejs-and-outgoing-http-requests"></a>Node.js en uitgaande http-aanvragen
Bij het werken met Node.js en vele uitgaande http-verzoeken is het belangrijk om met HTTP - Keep-Alive om te gaan. U het [agentkeepalive-pakket](https://www.npmjs.com/package/agentkeepalive) `npm` gebruiken om het gemakkelijker te maken in uw code.

Behandel altijd `http` de reactie, zelfs als u niets doet in de handler. Als u de reactie niet goed verwerkt, komt uw toepassing uiteindelijk vast te zitten omdat er geen sockets meer beschikbaar zijn.

Bijvoorbeeld bij het werken `http` `https` met de of pakket:

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Als u op App Service op Linux draait op een machine met meerdere cores, is een andere best practice om PM2 te gebruiken om meerdere Node.js-processen te starten om uw toepassing uit te voeren. U dit doen door een opstartopdracht op te geven aan uw container.

Bijvoorbeeld om vier instanties te starten:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="when-your-app-backup-starts-failing"></a><a name="appbackup"></a>Wanneer de back-up van uw app mislukt
De twee meest voorkomende redenen waarom app-back-up mislukt zijn: ongeldige opslaginstellingen en ongeldige databaseconfiguratie. Deze fouten worden meestal weergegeven wanneer er wijzigingen zijn in opslag- of databasebronnen of wijzigingen voor het openen van deze bronnen (bijvoorbeeld referenties die zijn bijgewerkt voor de database die is geselecteerd in de back-upinstellingen). Back-ups worden doorgaans volgens een planning uitgevoerd en vereisen toegang tot opslag (voor het uitbesteden van de back-upbestanden) en databases (voor het kopiëren en lezen van inhoud die in de back-up moet worden opgenomen). Het resultaat van het niet toegang tot een van deze bronnen zou consistent back-up fout. 

Wanneer er back-upfouten optreden, bekijkt u de meest recente resultaten om te begrijpen welk type fout er optreedt. Controleer en werk de opslaginstellingen die in de back-upconfiguratie worden gebruikt voor opslagtoegangsfouten. Voor fouten in databasetoegang controleert en werkt u de tekenreeksen van uw verbindingen als onderdeel van app-instellingen; ga vervolgens verder met het bijwerken van uw back-upconfiguratie om de vereiste databases correct op te nemen. Zie [Back-ups van een web-app maken in Azure App Service](manage-backup.md)voor meer informatie over app-back-ups.

## <a name="when-new-nodejs-apps-are-deployed-to-azure-app-service"></a><a name="nodejs"></a>Wanneer nieuwe Node.js-apps worden geïmplementeerd in Azure App Service
De standaardconfiguratie van Azure App Service voor Node.js-apps is bedoeld om het beste aan de behoeften van de meest voorkomende apps te voldoen. Als configuratie voor uw Node.js-app baat zou hebben bij gepersonaliseerde afstemming om de prestaties te verbeteren of het resourcegebruik voor CPU/geheugen/netwerkbronnen te optimaliseren, raadpleegt u [Aanbevolen procedures en handleiding voor probleemoplossing voor knooppunttoepassingen op Azure App Service.](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md) In dit artikel worden de iisnode-instellingen beschreven die u mogelijk moet configureren voor uw Node.js-app, worden de verschillende scenario's of problemen beschreven waarmee uw app te maken kan krijgen en wordt uitgelegd hoe u deze problemen oplossen.


## <a name="next-steps"></a>Volgende stappen
Ga voor meer informatie over aanbevolen procedures naar [App Service Diagnostics](https://docs.microsoft.com/azure/app-service/overview-diagnostics) voor meer informatie over bruikbare aanbevolen procedures die specifiek zijn voor uw resource.

- Navigeer naar uw web-app in de [Azure-portal.](https://portal.azure.com)
- Klik op **Diagnosticeren en los problemen op** in de linkernavigatie, waarmee App Service Diagnostics wordt geopend.
- Kies de startpaginategel **Aanbevolen procedures.**
- Klik **op Aanbevolen procedures voor beschikbaarheid & Prestaties** of Aanbevolen procedures voor optimale **configuratie** om de huidige status van uw app met betrekking tot deze aanbevolen procedures weer te geven.

U deze koppeling ook gebruiken om App `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`Service Diagnostics direct te openen voor uw resource:.