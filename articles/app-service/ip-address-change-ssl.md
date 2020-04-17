---
title: Voorbereiden op wijziging SSL-IP-adres
description: Als uw SSL-IP-adres wordt gewijzigd, leest u wat u moet doen zodat uw app na de wijziging blijft werken.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: dcfe11bcab25f6267a557de5faf7befab467bc29
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535720"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>Voorbereiden op een WIJZIGING van een SSL-IP-adres

Als u een melding hebt ontvangen dat het SSL-IP-adres van uw Azure App Service-app wordt gewijzigd, volgt u de instructies in dit artikel om bestaand SSL-IP-adres vrij te geven en een nieuw IP-adres toe te wijzen.

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>SSL-IP-adressen vrijgeven en nieuwe toewijzen

1.  Open de [Azure Portal](https://portal.azure.com).

2.  Selecteer **App Services**in het navigatiemenu aan de linkerkant .

3.  Selecteer uw App Service-app in de lijst.

4.  Klik **onder** de kop Instellingen op **SSL-instellingen** in de linkernavigatie.

1. Selecteer in de sectie TLS/SSL-bindingen de hostnaamrecord. Kies in de editor die wordt geopend de optie **SNI SSL** in het vervolgkeuzemenu **SSL-type** en klik op **Binding toevoegen**. Wanneer u het succesbericht van de bewerking ziet, is het bestaande IP-adres vrijgegeven.

6.  Selecteer in de sectie **SSL-bindingen** opnieuw dezelfde hostnaamrecord met het certificaat. Kies in de editor die wordt geopend **ip-gebaseerde SSL** in het vervolgkeuzemenu **SSL-type** en klik op **Binding toevoegen**. Wanneer u het succesbericht van de bewerking ziet, hebt u een nieuw IP-adres verworven.

7.  Als een A-record (DNS-record dat rechtstreeks naar uw IP-adres wijst) is geconfigureerd in uw Domeinregistratieportal (DNS-provider van derden of Azure DNS), vervangt u het bestaande IP-adres door het nieuw gegenereerde adres. U het nieuwe IP-adres vinden door de instructies in de volgende sectie te volgen.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Het nieuwe SSL-IP-adres zoeken in de Azure Portal

1.  Wacht een paar minuten en open de [Azure-portal.](https://portal.azure.com)

2.  Selecteer **App Services**in het navigatiemenu aan de linkerkant .

3.  Selecteer uw App Service-app in de lijst.

4.  Klik **onder** de kop Instellingen op **Eigenschappen** in de linkernavigatie en zoek de sectie met het label **Virtueel IP-adres**.

5. Kopieer het IP-adres en configureer uw domeinrecord of IP-mechanisme opnieuw.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u zich voorbereiden op een IP-adreswijziging die is geïnitieerd door Azure. Zie [Binnenkomende en uitgaande IP-adressen in Azure App Service](overview-inbound-outbound-ips.md)voor meer informatie over IP-adressen in Azure App Service.
