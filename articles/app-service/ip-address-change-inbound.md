---
title: Voorbereiden op inkomende IP-adreswijziging
description: Als uw binnenkomende IP-adres wordt gewijzigd, leest u wat u moet doen zodat uw app na de wijziging blijft werken.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 999511c4759ee761f028a61fbfaa0310ac93d9b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672420"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Voorbereiden op een inkomende IP-adreswijziging

Als u een melding hebt ontvangen dat het binnenkomende IP-adres van uw Azure App Service-app wordt gewijzigd, volgt u de instructies in dit artikel.

## <a name="determine-if-you-have-to-do-anything"></a>Bepaal of je iets moet doen

* Optie 1: Als uw App Service-app geen aangepast domein heeft, is er geen actie vereist.

* Optie 2: Als alleen een CNAME-record (DNS-record die naar een URI wijst) is geconfigureerd in uw Domeinregistratieportal (DNS-provider van derden of Azure DNS), is er geen actie vereist.

* Optie 3: Als een A-record (DNS-record dat rechtstreeks naar uw IP-adres wijst) is geconfigureerd in uw Domeinregistratieportal (DNS-provider van derden of Azure DNS), vervangt u het bestaande IP-adres door het nieuwe. U het nieuwe IP-adres vinden door de instructies in de volgende sectie te volgen.

* Optie 4: Als uw toepassing zich achter een load balancer, IP-filter of een ander IP-mechanisme bevindt waarvoor het IP-adres van uw app vereist is, vervangt u het bestaande IP-adres door het nieuwe. U het nieuwe IP-adres vinden door de instructies in de volgende sectie te volgen.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Het nieuwe binnenkomende IP-adres zoeken in de Azure-portal

Het nieuwe binnenkomende IP-adres dat aan uw app wordt gegeven, bevindt zich in de portal in het veld **Virtueel IP-adres.** Zowel dit nieuwe IP-adres als het oude zijn nu verbonden met uw app, en later wordt de oude losgekoppeld.

1.  Open de [Azure Portal](https://portal.azure.com).

2.  Selecteer **App Services**in het navigatiemenu aan de linkerkant .

3.  Selecteer uw App Service-app in de lijst.

1.  Zie [Inkomende IP-adres functie functie](../azure-functions/ip-addresses.md#function-app-inbound-ip-address)als de app een functie-app is.

4.  Klik **onder** de kop Instellingen op **Eigenschappen** in de linkernavigatie en zoek de sectie met het label **Virtueel IP-adres**.

5. Kopieer het IP-adres en configureer uw domeinrecord of IP-mechanisme opnieuw.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u zich voorbereiden op een IP-adreswijziging die is geïnitieerd door Azure. Zie [Binnenkomende en uitgaande IP-adressen in Azure App Service](overview-inbound-outbound-ips.md)voor meer informatie over IP-adressen in Azure App Service.
