---
title: 'Fout oplossen: Azure Functions Runtime is onbereikbaar'
description: Meer informatie over het oplossen van een ongeldig opslagaccount.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 8fcd0661e2c7cab505121cf0d4d7b4c1d29017f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063778"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Fout oplossen: "Azure Functions Runtime is onbereikbaar"

Met dit artikel u de volgende fouttekenreeks oplossen die in de Azure-portal wordt weergegeven:

> "Fout: Azure Functions Runtime is onbereikbaar. Klik hier voor meer informatie over de opslagconfiguratie."

Dit probleem treedt op wanneer de Runtime azure-functies niet kan worden gestart. De meest voorkomende reden voor het probleem is dat de functie-app geen toegang meer heeft tot zijn opslagaccount. Zie [Vereisten voor opslagaccount voor](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)meer informatie .

De rest van dit artikel helpt u bij het oplossen van de volgende oorzaken van deze fout, waaronder het identificeren en oplossen van elk geval.

## <a name="storage-account-was-deleted"></a>Opslagaccount is verwijderd

Elke functie-app vereist een opslagaccount om te werken. Als dat account wordt verwijderd, werkt uw functie niet.

Begin met het opzoeken van de naam van uw opslagaccount in de toepassingsinstellingen. Of `AzureWebJobsStorage` `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` bevat de naam van uw opslagaccount verpakt in een verbindingstekenreeks. Zie [Verwijzing naar app-instellingen voor Azure-functies voor](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)meer informatie.

Zoek naar uw opslagaccount in de Azure-portal om te zien of het nog bestaat. Als het is verwijderd, maakt u het opslagaccount opnieuw en vervangt u de tekenreeksen van uw opslagverbinding. Uw functiecode gaat verloren en u moet deze opnieuw implementeren.

## <a name="storage-account-application-settings-were-deleted"></a>Instellingen voor opslagaccounttoepassingen zijn verwijderd

Als u in de vorige stap geen tekenreeks voor de verbinding met het opslagaccount vinden, is deze waarschijnlijk verwijderd of overschreven. Toepassingsinstellingen verwijderen gebeurt meestal wanneer u implementatiesleuven of Azure Resource Manager-scripts gebruikt om toepassingsinstellingen in te stellen.

### <a name="required-application-settings"></a>Vereiste toepassingsinstellingen

* Vereist:
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Vereist voor functies van het verbruiksplan:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

Zie [Verwijzing naar app-instellingen voor Azure-functies voor](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)meer informatie.

### <a name="guidance"></a>Richtlijnen

* Controleer niet op 'slotinstelling' voor een van deze instellingen. Als u implementatieslots wisselt, wordt de functie-app afgebroken.
* Wijzig deze instellingen niet als onderdeel van geautomatiseerde implementaties.
* Deze instellingen moeten worden opgegeven en geldig zijn bij het maken van de tijd. Een geautomatiseerde implementatie die deze instellingen niet bevat, resulteert in een functie-app die niet wordt uitgevoerd, zelfs als de instellingen later worden toegevoegd.

## <a name="storage-account-credentials-are-invalid"></a>Opslagaccountreferenties zijn ongeldig

De eerder besproken opslagaccountverbindingstekenreeksen moeten worden bijgewerkt als u opslagsleutels regenereert. Zie [Een Azure Storage-account maken](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)voor meer informatie over beheer van opslagsleutels.

## <a name="storage-account-is-inaccessible"></a>Opslagaccount is niet toegankelijk

Uw functie-app moet toegang hebben tot het opslagaccount. Veelvoorkomende problemen die de toegang van een functie-app tot een opslagaccount blokkeren, zijn:

* De functie-app wordt geïmplementeerd in uw App Service-omgeving zonder de juiste netwerkregels om verkeer van en naar het opslagaccount toe te staan.

* De firewall van het opslagaccount is ingeschakeld en niet geconfigureerd om verkeer van en naar functies toe te staan. Raadpleeg [Firewalls en virtuele netwerken voor Azure Storage configureren](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor meer informatie.

## <a name="daily-execution-quota-is-full"></a>Daguitvoeringsquotum is vol

Als u een dagelijks uitvoeringsquotum hebt geconfigureerd, is uw functie-app tijdelijk uitgeschakeld, waardoor veel van de portalbesturingselementen niet meer beschikbaar zijn. 

Als u het quotum in de [Azure-portal wilt](https://portal.azure.com)verifiëren, selecteert u**App-instellingen** voor functies **platformonderdelen** > in uw functie-app. Als u het **quotum voor dagelijks gebruik** dat u hebt ingesteld, overschrijdt, wordt het volgende bericht weergegeven:

  > "De functie-app heeft het quotum voor dagelijks gebruik bereikt en is gestopt tot de volgende 24 uur."

Als u dit probleem wilt oplossen, verwijdert of verhoogt u het dagelijkse quotum en start u de app opnieuw. Anders wordt de uitvoering van uw app geblokkeerd tot de volgende dag.

## <a name="app-is-behind-a-firewall"></a>App zit achter een firewall

Uw taakruntime is mogelijk onbereikbaar om een van de volgende redenen:

* Uw functie-app wordt gehost in een [intern geladen gebalanceerde App Service-omgeving](../app-service/environment/create-ilb-ase.md) en is geconfigureerd om binnenkomend internetverkeer te blokkeren.

* Uw functie-app heeft [binnenkomende IP-beperkingen](functions-networking-options.md#inbound-ip-restrictions) die zijn geconfigureerd om internettoegang te blokkeren. 

De Azure-portal voert rechtstreeks naar de lopende app om de lijst met functies op te halen en voert HTTP-oproepen naar het Kudu-eindpunt. Instellingen op platformniveau onder het tabblad **Platformfuncties** zijn nog steeds beschikbaar.

Ga als volgt te werk om de configuratie van uw app-serviceomgeving te verifiëren:
1. Ga naar de netwerkbeveiligingsgroep (NSG) van het subnet waar de App Service Omgeving zich bevindt.
1. Valideer de binnenkomende regels om verkeer toe te staan dat afkomstig is van het openbare IP van de computer waar u toegang hebt tot de toepassing. 
   
U de portal ook gebruiken vanaf een computer die is verbonden met het virtuele netwerk waarop uw app wordt uitgevoerd of met een virtuele machine die in uw virtuele netwerk wordt uitgevoerd. 

Zie het gedeelte Netwerkbeveiligingsgroepen [voor netwerkoverwegingen voor een app-serviceomgeving voor](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups)meer informatie over de configuratie van inkomende regels.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het bewaken van uw functie-apps:

> [!div class="nextstepaction"]
> [Azure Functions controleren](functions-monitoring.md)
