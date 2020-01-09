---
title: Het oplossen van problemen met Azure Functions-runtime is onbereikbaar.
description: Meer informatie over het oplossen van problemen met een ongeldig opslag account.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 358f26af8d990d29f226978387fdf8093d2b8644
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75612969"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Problemen oplossen met functions runtime is onbereikbaar


## <a name="error-text"></a>Fout tekst
Dit document is bedoeld om de volgende fout op te lossen wanneer het wordt weer gegeven in de functions-Portal.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>Samenvatting
Dit probleem treedt op wanneer de Azure Functions-runtime niet kan worden gestart. De meest voorkomende reden voor deze fout is dat de functie-app de toegang tot het opslag account kwijtraakt. [Lees hier meer over de vereisten voor het opslag account](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>Problemen oplossen
We behandelen de vier meest voorkomende fout gevallen, het identificeren en het oplossen van elke case.

1. Opslag account verwijderd
1. De toepassings instellingen voor het opslag account zijn verwijderd
1. De referenties van het opslag account zijn ongeldig
1. Het opslag account is niet toegankelijk
1. Het dagelijkse uitvoerings quotum is vol
1. App bevindt zich achter een firewall


## <a name="storage-account-deleted"></a>Opslag account verwijderd

Voor elke functie-app moet een opslag account worden gebruikt. Als dat account wordt verwijderd, werkt de functie niet.

### <a name="how-to-find-your-storage-account"></a>Uw opslag account zoeken

Zoek eerst de naam van uw opslag account op in de instellingen van uw toepassing. `AzureWebJobsStorage` of `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` bevat de naam van uw opslag account in een connection string. Lees hier meer details over het [instellen van de toepassings instelling](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)

Zoek uw opslag account in de Azure Portal om te zien of het nog bestaat. Als deze is verwijderd, moet u een opslag account opnieuw maken en de verbindings reeksen voor de opslag vervangen. De functie code gaat verloren en moet opnieuw worden geïmplementeerd.

## <a name="storage-account-application-settings-deleted"></a>De toepassings instellingen voor het opslag account zijn verwijderd

Als u in de vorige stap geen opslag account hebt connection string, zijn deze waarschijnlijk verwijderd of overschreven. Het verwijderen van app-instellingen wordt meestal uitgevoerd wanneer u implementatie sleuven of Azure Resource Manager scripts gebruikt om toepassings instellingen in te stellen.

### <a name="required-application-settings"></a>Vereiste toepassings instellingen

* Verplicht
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Vereist voor de functies van het verbruiks abonnement
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Lees hier meer over deze toepassings instellingen](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

### <a name="guidance"></a>Hulp

* Schakel ' sleuf instelling ' niet in voor een van deze instellingen. Wanneer u implementatie sleuven verwisselt, wordt de functie onderbroken.
* Wijzig deze instellingen niet als onderdeel van automatische implementaties.
* Deze instellingen moeten worden ingevoerd en geldig zijn tijdens het maken. Een geautomatiseerde implementatie die deze instellingen niet bevat, resulteert in een niet-functionele app, zelfs als de instellingen worden toegevoegd na het feit.

## <a name="storage-account-credentials-invalid"></a>De referenties van het opslag account zijn ongeldig

De bovenstaande verbindings reeksen voor het opslag account moeten worden bijgewerkt als u opslag sleutels opnieuw genereert. [Lees hier meer over opslag sleutel beheer](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)

## <a name="storage-account-inaccessible"></a>Het opslag account is niet toegankelijk

Uw functie-app moet toegang hebben tot het opslag account. Veelvoorkomende problemen met het blok keren van een functie toegang tot een opslag account zijn:

* Functie-apps die zijn geïmplementeerd in App Service omgevingen zonder de juiste netwerk regels voor het toestaan van verkeer naar en van het opslag account
* De firewall voor het opslag account is ingeschakeld en is niet geconfigureerd om verkeer van en naar functies toe te staan. [Lees hier meer over Firewall configuratie voor opslag accounts](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>Het dagelijkse uitvoerings quotum is vol

Als u een dagelijks uitvoerings quotum hebt geconfigureerd, wordt uw functie-app tijdelijk uitgeschakeld en zijn veel van de besturings elementen van de portal niet meer beschikbaar. 

* Als u dit wilt controleren, controleert u de open platform functies > functie-app instellingen in de portal. Het volgende bericht wordt weer gegeven als u het quotum overschrijdt
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* Verwijder het quotum en start de app opnieuw om het probleem op te lossen.

## <a name="app-is-behind-a-firewall"></a>App bevindt zich achter een firewall

De runtime van de functie is onbereikbaar als uw functie-app wordt gehost in een [app service Environment met interne taak verdeling](../app-service/environment/create-ilb-ase.md) en is geconfigureerd om inkomend Internet verkeer te blok keren of waarvoor [binnenkomende IP-beperkingen](functions-networking-options.md#inbound-ip-restrictions) zijn geconfigureerd om Internet toegang te blok keren. De Azure Portal maakt rechtstreeks aan de actieve app aanroepen om de lijst met functies op te halen en maakt ook http-aanroepen naar KUDU-eind punt. Instellingen op platform niveau onder het tabblad `Platform Features` zijn nog steeds beschikbaar.

* Als u de ASE-configuratie wilt controleren, gaat u naar NSG van het subnet waar ASE zich bevindt en valideert u de regels voor binnenkomende verbindingen zodat verkeer afkomstig is van het open bare IP-adres van de computer waarop u de toepassing opent. U kunt de portal ook gebruiken vanaf een computer die is verbonden met het virtuele netwerk waarop uw app wordt uitgevoerd of een virtuele machine die wordt uitgevoerd in uw virtuele netwerk. [Lees hier meer over de configuratie van de inkomende regel](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups)

## <a name="next-steps"></a>Volgende stappen

Nu uw functie-app terug en operationeel is, Bekijk onze Quick starts en naslag informatie voor ontwikkel aars om weer aan de slag te gaan.

* [Uw eerste Azure-functie maken](functions-create-first-azure-function.md)  
  Ga meteen aan de slag en maak uw eerste functie met de Azure Functions-snelstartgids. 
* [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)  
  Biedt meer technische informatie over de Azure Functions-runtime en bevat gedetailleerde informatie over het coderen van functies en het definiëren van triggers en bindingen.
* [Azure Functions testen](functions-test-a-function.md)  
  Beschrijft de verschillende hulpprogramma’s en technieken voor het testen van uw functies.
* [Azure Functions schalen](functions-scale.md)  
  Beschrijft de serviceabonnementen die beschikbaar zijn voor Azure Functions, zoals het hostingabonnement Consumption, en helpt u bij het kiezen van het juiste abonnement. 
* [Meer informatie over Azure App Service](../app-service/overview.md)  
  Azure Functions maakt gebruik van Azure App Service voor kernfunctionaliteit zoals implementaties, omgevingsvariabelen en diagnostische procedures. 
