---
title: Veelgestelde vragen over netwerken in Azure-functies
description: Antwoorden op enkele van de meest voorkomende vragen en scenario's voor netwerken met Azure-functies.
author: alexkarcher-msft
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: acb1e942c1f342ce6fee7d8aeacafcc1d7b6fd91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75409530"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Veelgestelde vragen over netwerken in Azure-functies

In dit artikel worden veelgestelde vragen over netwerken in Azure-functies weergegeven. Zie [Netwerkopties functies](functions-networking-options.md)voor een uitgebreider overzicht.

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Hoe stel ik een statisch IP in functies in?

Het implementeren van een functie in een App-serviceomgeving is momenteel de enige manier om een statisch in- en uitgaand IP voor uw functie te hebben. Voor meer informatie over het gebruik van een App Service-omgeving, begint u met het artikel [Een interne load balancer maken en gebruiken met een App Service-omgeving.](../app-service/environment/create-ilb-ase.md)

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Hoe beperk ik de toegang tot internet tot mijn functie?

U de toegang tot internet op een aantal manieren beperken:

* [IP-beperkingen](../app-service/app-service-ip-restrictions.md): Beperk binnenkomend verkeer naar uw functie-app op IP-bereik.
    * Onder IP-beperkingen u ook [Service-eindpunten](../virtual-network/virtual-network-service-endpoints-overview.md)configureren, waardoor uw functie alleen binnenkomend verkeer van een bepaald virtueel netwerk wordt geaccepteerd.
* Verwijdering van alle HTTP-triggers. Voor sommige toepassingen is het voldoende om http-triggers te vermijden en elke andere gebeurtenisbron te gebruiken om uw functie te activeren.

Houd er rekening mee dat de Azure-portaleditor directe toegang tot uw hardloopfunctie vereist. Voor wijzigingen in de code via de Azure-portal moet het apparaat dat u gebruikt door de portal bladeren om het IP-adres op de witte lijst te krijgen. Maar je nog steeds alles gebruiken onder het tabblad platformfuncties met netwerkbeperkingen.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Hoe beperk ik mijn functie-app tot een virtueel netwerk?

U **binnenkomend** verkeer voor een functie-app beperken tot een virtueel netwerk met [serviceeindpunten.](./functions-networking-options.md#private-site-access) Met deze configuratie kan de functie-app nog steeds uitgaande gesprekken voeren naar het internet.

De enige manier om een functie zodanig volledig te beperken dat al het verkeer door een virtueel netwerk stroomt, is door een intern belaste App Service-omgeving te gebruiken. Met deze optie wordt uw site geïmplementeerd op een speciale infrastructuur binnen een virtueel netwerk en worden alle triggers en verkeer door het virtuele netwerk verzenden. 

Voor meer informatie over het gebruik van een App Service-omgeving, begint u met het artikel [Een interne load balancer maken en gebruiken met een App Service-omgeving.](../app-service/environment/create-ilb-ase.md)

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Hoe kan ik toegang krijgen tot bronnen in een virtueel netwerk vanuit een functie-app?

U hebt toegang tot bronnen in een virtueel netwerk vanuit een bedrijfsfunctie met behulp van virtuele netwerkintegratie. Zie [Virtuele netwerkintegratie](functions-networking-options.md#virtual-network-integration)voor meer informatie.

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Hoe krijg ik toegang tot bronnen die worden beschermd door serviceeindpunten?

Door gebruik te maken van virtuele netwerkintegratie hebt u toegang tot service-endpoint-beveiligde bronnen vanuit een bedrijfsfunctie. Zie voor meer informatie [de integratie van virtuele netwerken](functions-networking-options.md#virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Hoe kan ik een functie activeren vanuit een bron in een virtueel netwerk?

U toestaan dat HTTP-triggers vanuit een virtueel netwerk worden aangeroepen met [Service-eindpunten.](./functions-networking-options.md#private-site-access) 

U ook een functie activeren van alle andere bronnen in een virtueel netwerk door uw functie-app te implementeren in een Premium-abonnement, App Service-abonnement of App Service-omgeving. Zie [niet-HTTP virtuele netwerktriggers](./functions-networking-options.md#virtual-network-triggers-non-http) voor meer informatie

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Hoe kan ik mijn functie-app implementeren in een virtueel netwerk?

Implementeren in een App-serviceomgeving is de enige manier om een functie-app te maken die volledig binnen een virtueel netwerk zit. Voor meer informatie over het gebruik van een interne load balancer met een App Service-omgeving, begint u met het artikel [Een interne load balancer maken en gebruiken met een App Service-omgeving.](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase)

Zie het [netwerkoverzicht Functies](functions-networking-options.md)voor scenario's waarbij u slechts één richting toegang tot virtuele netwerkbronnen of minder uitgebreide netwerkisolatie nodig hebt.

## <a name="next-steps"></a>Volgende stappen

Ga als voor meer informatie over netwerken en functies: 

* [Volg de zelfstudie over aan de slag gaan met virtuele netwerkintegratie](./functions-create-vnet.md)
* [Meer informatie over de netwerkopties in Azure-functies](./functions-networking-options.md)
* [Meer informatie over virtuele netwerkintegratie met App-service en -functies](../app-service/web-sites-integrate-with-vnet.md)
* [Meer informatie over virtuele netwerken in Azure](../virtual-network/virtual-networks-overview.md)
* [Meer netwerkfuncties en -bediening inschakelen met App-serviceomgevingen](../app-service/environment/intro.md)
