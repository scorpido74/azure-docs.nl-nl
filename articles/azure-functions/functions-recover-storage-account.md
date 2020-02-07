---
title: 'Fout oplossen: de Azure Functions-runtime is onbereikbaar'
description: Meer informatie over het oplossen van problemen met een ongeldig opslag account.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 8fcd0661e2c7cab505121cf0d4d7b4c1d29017f8
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063778"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Fout oplossen: ' Azure Functions-runtime is onbereikbaar '

Dit artikel helpt u bij het oplossen van de volgende fout teken reeks die wordt weer gegeven in de Azure Portal:

> "Fout: Azure Functions-runtime is onbereikbaar. Klik hier voor meer informatie over de opslag configuratie.

Dit probleem treedt op wanneer de Azure Functions-runtime niet kan worden gestart. De meest voorkomende reden voor het probleem is dat de functie-app de toegang tot het opslag account heeft verloren. Zie [vereisten voor opslag accounts](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)voor meer informatie.

De rest van dit artikel helpt u bij het oplossen van de volgende oorzaken van deze fout, inclusief het identificeren en oplossen van elke case.

## <a name="storage-account-was-deleted"></a>Het opslag account is verwijderd

Voor elke functie-app moet een opslag account worden gebruikt. Als dat account wordt verwijderd, werkt de functie niet.

Zoek eerst de naam van uw opslag account op in de instellingen van uw toepassing. `AzureWebJobsStorage` of `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` bevat de naam van uw opslag account dat in een connection string moet worden ingepakt. Zie voor meer informatie [app-instellingen naslag informatie voor Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage).

Zoek uw opslag account in de Azure Portal om te zien of het nog bestaat. Als deze is verwijderd, maakt u het opslag account opnieuw en vervangt u de verbindings reeksen voor de opslag. De functie code gaat verloren en u moet deze opnieuw implementeren.

## <a name="storage-account-application-settings-were-deleted"></a>De toepassings instellingen voor het opslag account zijn verwijderd

Als u in de vorige stap geen opslag account connection string kunt vinden, is deze waarschijnlijk verwijderd of overschreven. Het verwijderen van toepassings instellingen gebeurt meestal wanneer u implementatie sleuven of Azure Resource Manager scripts gebruikt om toepassings instellingen in te stellen.

### <a name="required-application-settings"></a>Vereiste toepassings instellingen

* Vereist:
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Vereist voor de functies van het verbruiks abonnement:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

Zie voor meer informatie [app-instellingen naslag informatie voor Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

### <a name="guidance"></a>Richtlijnen

* Schakel de optie ' sleuf instelling ' niet in voor een van deze instellingen. Als u implementatie sleuven verwisselt, wordt de functie-app onderbroken.
* Wijzig deze instellingen niet als onderdeel van automatische implementaties.
* Deze instellingen moeten worden ingevoerd en geldig zijn tijdens het maken. Een geautomatiseerde implementatie die deze instellingen niet bevat, resulteert in een functie-app die niet wordt uitgevoerd, zelfs niet als de instellingen later worden toegevoegd.

## <a name="storage-account-credentials-are-invalid"></a>De referenties van het opslag account zijn ongeldig

De eerder beschreven verbindings reeksen voor opslag accounts moeten worden bijgewerkt als u opslag sleutels opnieuw genereert. Zie [een Azure Storage-account maken](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)voor meer informatie over opslag sleutel beheer.

## <a name="storage-account-is-inaccessible"></a>Het opslag account is niet toegankelijk

De functie-app moet toegang hebben tot het opslag account. Veelvoorkomende problemen met het blok keren van toegang tot een opslag account met een functie-app:

* De functie-app wordt op uw App Service Environment geïmplementeerd zonder de juiste netwerk regels om verkeer toe te staan van en naar het opslag account.

* De firewall voor het opslag account is ingeschakeld en is niet geconfigureerd om verkeer van en naar functies toe te staan. Raadpleeg [Firewalls en virtuele netwerken voor Azure Storage configureren](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor meer informatie.

## <a name="daily-execution-quota-is-full"></a>Het dagelijkse uitvoerings quotum is vol

Als u een dagelijks uitvoerings quotum hebt geconfigureerd, is de functie-app tijdelijk uitgeschakeld, waardoor veel van de besturings elementen van de portal niet meer beschikbaar zijn. 

Als u het quotum wilt controleren in de [Azure Portal](https://portal.azure.com), selecteert u **Platform functies** > **functie-app instellingen** in uw functie-app. Als u de **dagelijkse gebruiks quota** hebt ingesteld, wordt het volgende bericht weer gegeven:

  > "De functie-app heeft het quotum voor dagelijks gebruik bereikt en is gestopt tot de volgende periode van 24 uur."

U kunt dit probleem oplossen door het dagelijkse quotum te verwijderen of te verhogen en vervolgens uw app opnieuw te starten. Anders wordt de uitvoering van uw app geblokkeerd tot de volgende dag.

## <a name="app-is-behind-a-firewall"></a>App bevindt zich achter een firewall

De functie-runtime kan om een van de volgende redenen onbereikbaar zijn:

* Uw functie-app wordt gehost in een [app service Environment met interne taak verdeling](../app-service/environment/create-ilb-ase.md) en is geconfigureerd om inkomend Internet verkeer te blok keren.

* Uw functie-app heeft [binnenkomende IP-beperkingen](functions-networking-options.md#inbound-ip-restrictions) die zijn geconfigureerd om Internet toegang te blok keren. 

De Azure Portal maakt rechtstreeks aan de actieve app aanroepen om de lijst met functies op te halen en maakt HTTP-aanroepen naar het kudu-eind punt. Instellingen op platform niveau onder het tabblad **platform functies** zijn nog steeds beschikbaar.

Controleren of uw App Service Environment configuratie:
1. Ga naar de netwerk beveiligings groep (NSG) van het subnet waar de App Service Environment zich bevindt.
1. Valideer de regels voor binnenkomende verbindingen om verkeer toe te staan dat afkomstig is van het open bare IP-adres van de computer waarop u toegang tot de toepassing hebt. 
   
U kunt de portal ook gebruiken vanaf een computer die is verbonden met het virtuele netwerk waarop uw app wordt uitgevoerd of op een virtuele machine die wordt uitgevoerd in uw virtuele netwerk. 

Zie de sectie netwerk beveiligings groepen van [netwerk overwegingen voor een app service Environment](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups)voor meer informatie over de configuratie van de binnenkomende regel.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het bewaken van uw functie-apps:

> [!div class="nextstepaction"]
> [Azure Functions bewaken](functions-monitoring.md)
