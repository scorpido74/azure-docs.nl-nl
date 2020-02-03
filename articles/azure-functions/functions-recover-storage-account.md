---
title: Het oplossen van problemen met Azure Functions-runtime is onbereikbaar.
description: Meer informatie over het oplossen van problemen met een ongeldig opslag account.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 910b582cb40b9f8aff6a553621b4677d6b019826
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963883"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Problemen oplossen met functions runtime is onbereikbaar

Dit artikel is bedoeld voor het oplossen van problemen met het fout bericht ' functions runtime is onbereikbaar ' wanneer het wordt weer gegeven in de Azure Portal. Als deze fout optreedt, ziet u de volgende fout teken reeks die wordt weer gegeven in de portal.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

Dit gebeurt wanneer de Azure Functions-runtime niet kan worden gestart. De meest voorkomende reden voor deze fout is dat de functie-app de toegang tot het opslag account kwijtraakt. Zie [vereisten voor opslag accounts](storage-considerations.md#storage-account-requirements)voor meer informatie.

De rest van dit artikel helpt u bij het oplossen van de volgende oorzaken van deze fout, inclusief het identificeren en oplossen van elke case.

+ [Opslagaccount verwijderd](#storage-account-deleted)
+ [De toepassingsinstellingen van het opslagaccount zijn verwijderd](#storage-account-application-settings-deleted)
+ [Opslagaccountreferenties ongeldig](#storage-account-credentials-invalid)
+ [Het opslagaccount is niet toegankelijk](#storage-account-inaccessible)
+ [Het dagelijkse uitvoerings quotum is overschreden](#daily-execution-quota-full)
+ [Uw app bevindt zich achter een firewall](#app-is-behind-a-firewall)


## <a name="storage-account-deleted"></a>Opslag account verwijderd

Voor elke functie-app moet een opslag account worden gebruikt. Als dat account wordt verwijderd, werkt de functie niet.

### <a name="how-to-find-your-storage-account"></a>Uw opslag account zoeken

Zoek eerst de naam van uw opslag account op in de instellingen van uw toepassing. `AzureWebJobsStorage` of `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` bevat de naam van uw opslag account in een connection string. Lees hier meer details over het [instellen van de toepassings instelling](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage).

Zoek uw opslag account in de Azure Portal om te zien of het nog bestaat. Als deze is verwijderd, moet u een opslag account opnieuw maken en de verbindings reeksen voor de opslag vervangen. De functie code gaat verloren en moet opnieuw worden geïmplementeerd.

## <a name="storage-account-application-settings-deleted"></a>De toepassings instellingen voor het opslag account zijn verwijderd

Als u in de vorige stap geen opslag account hebt connection string is het waarschijnlijk verwijderd of overschreven. Het verwijderen van app-instellingen wordt meestal uitgevoerd wanneer u implementatie sleuven of Azure Resource Manager scripts gebruikt om toepassings instellingen in te stellen.

### <a name="required-application-settings"></a>Vereiste toepassings instellingen

* Verplicht
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Vereist voor de functies van het verbruiks abonnement
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Lees hier meer over deze toepassings instellingen](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

### <a name="guidance"></a>Hulp

* Schakel de optie ' sleuf instelling ' niet in voor een van deze instellingen. Wanneer u implementatie sleuven verwisselt, wordt de functie-app onderbroken.
* Wijzig deze instellingen niet als onderdeel van automatische implementaties.
* Deze instellingen moeten worden ingevoerd en geldig zijn tijdens het maken. Een geautomatiseerde implementatie die deze instellingen niet bevat, resulteert in een functie-app die niet wordt uitgevoerd, zelfs niet als de instellingen later worden toegevoegd.

## <a name="storage-account-credentials-invalid"></a>De referenties van het opslag account zijn ongeldig

De bovenstaande verbindings reeksen voor het opslag account moeten worden bijgewerkt als u opslag sleutels opnieuw genereert. [Lees hier meer over opslag sleutel beheer](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

## <a name="storage-account-inaccessible"></a>Het opslag account is niet toegankelijk

De functie-app moet toegang hebben tot het opslag account. Veelvoorkomende problemen met het blok keren van een functie toegang tot een opslag account zijn:

+ functie-apps die zijn geïmplementeerd op App Service omgevingen (ASE) zonder de juiste netwerk regels voor het toestaan van verkeer naar en van het opslag account.

+ De firewall voor het opslag account is ingeschakeld en is niet geconfigureerd om verkeer van en naar functies toe te staan. Zie [Firewalls en virtuele netwerken voor Azure Storage configureren](../storage/common/storage-network-security.md) voor meer informatie.

## <a name="daily-execution-quota-full"></a>Het dagelijkse uitvoerings quotum is vol

Als u een dagelijks uitvoerings quotum hebt geconfigureerd, is de functie-app tijdelijk uitgeschakeld, waardoor veel van de besturings elementen van de portal niet meer beschikbaar zijn. 

+ Als u wilt controleren in de [Azure Portal](https://portal.azure.com), opent u **Platform functies** > **functie-app instellingen** in uw functie-app. Wanneer u het quotum voor **dagelijks gebruik** dat u hebt ingesteld overschrijdt, wordt het volgende bericht weer gegeven:

    `The function app has reached daily usage quota and has been stopped until the next 24 hours time frame.`

+ U kunt dit probleem oplossen door het dagelijkse quotum te verwijderen of te verhogen en de app opnieuw te starten. Anders wordt de uitvoering van uw app geblokkeerd tot de volgende dag.

## <a name="app-is-behind-a-firewall"></a>App bevindt zich achter een firewall

De runtime van de functie is onbereikbaar als uw functie-app wordt gehost in een [app service Environment met interne taak verdeling](../app-service/environment/create-ilb-ase.md) en is geconfigureerd om inkomend Internet verkeer te blok keren of waarvoor [binnenkomende IP-beperkingen](functions-networking-options.md#inbound-ip-restrictions) zijn geconfigureerd om Internet toegang te blok keren. De Azure Portal maakt rechtstreeks aan de actieve app aanroepen om de lijst met functies op te halen en maakt ook HTTP-aanroepen naar het KUDU-eind punt. Instellingen op platform niveau onder het tabblad `Platform Features` zijn nog steeds beschikbaar.

Als u de ASE-configuratie wilt controleren, gaat u naar de NSG van het subnet waar ASE zich bevindt en valideert u regels voor binnenkomende verbindingen zodat verkeer afkomstig is van het open bare IP-adres van de computer waarop u de toepassing opent. U kunt de portal ook gebruiken vanaf een computer die is verbonden met het virtuele netwerk waarop uw app wordt uitgevoerd of een virtuele machine die wordt uitgevoerd in uw virtuele netwerk. [Lees hier meer over de configuratie van de inkomende regel](../app-service/environment/network-info.md#network-security-groups)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het bewaken van uw functie-apps:

> [!div class="nextstepaction"]
> [Azure Functions bewaken](functions-monitoring.md)
