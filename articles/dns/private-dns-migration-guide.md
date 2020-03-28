---
title: Oudere Azure DNS Private Zones migreren naar nieuw resourcemodel
titleSuffix: Azure DNS
description: Deze handleiding geeft stapsgewijze instructies over het migreren van verouderde privé-DNS-zones naar het nieuwste resourcemodel
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: d29885104d6f39a17b5bdeb786cda8f56f58d987
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76939347"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Oudere Azure DNS-privézones migreren naar een nieuw resourcemodel

Tijdens openbare preview werden privé DNS-zones gemaakt met behulp van "dnszones" resource met "zoneType" eigenschap ingesteld op "Privé". Dergelijke zones worden niet ondersteund na 31 december 2019 en moeten worden gemigreerd naar GA-resourcemodel dat gebruik maakt van het resourcetype "privateDnsZones" in plaats van "dnszones". Het migratieproces is eenvoudig en we hebben een PowerShell-script geleverd om dit proces te automatiseren. Deze handleiding biedt stapsgewijze instructies om uw Azure DNS-privézones te migreren naar het nieuwe resourcemodel.

Om de bronnen van dnszones te achterhalen waarvoor migratie nodig is; de onderstaande opdracht in Azure CLI uit te voeren.
```azurecli
az account set --subscription <SubscriptionId>
az network dns zone list --query "[?zoneType=='Private']"
```

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u de nieuwste versie van Azure PowerShell hebt geïnstalleerd. Voor meer informatie over Azure PowerShell (Az) en hoe u deze installeren, u terechthttps://docs.microsoft.com/powershell/azure/new-azureps-module-az

Zorg ervoor dat de Az.PrivateDns-module voor de Azure PowerShell is geïnstalleerd. Als u deze module wilt installeren, opent u een verhoogd PowerShell-venster (beheermodus) en voert u de volgende opdracht in

```powershell
Install-Module -Name Az.PrivateDns
```

>[!IMPORTANT]
>Het migratieproces is volledig geautomatiseerd en zal naar verwachting geen downtime veroorzaken. Als u echter Azure DNS-privézones (preview) gebruikt in een kritieke productieomgeving, moet u het volgende migratieproces uitvoeren tijdens een gepland onderhoudstijdvenster. Zorg ervoor dat u de configuratie of recordsets van een privé-DNS-zone niet wijzigt terwijl u het migratiescript uitvoert.

## <a name="installing-the-script"></a>Het script installeren

Een verhoogd PowerShell-venster (beheermodus) openen en de volgende opdracht uitvoeren

```powershell
install-script PrivateDnsMigrationScript
```

Voer 'A' in wanneer u wordt gevraagd het script te installeren

![Het script installeren](./media/private-dns-migration-guide/install-migration-script.png)

U ook handmatig de nieuwste versie van PowerShell-scripthttps://www.powershellgallery.com/packages/PrivateDnsMigrationScript

>[!IMPORTANT]
>Het migratiescript mag niet worden uitgevoerd in azure cloudshell en moet worden uitgevoerd in een VM of lokale machine die is verbonden met internet.

## <a name="running-the-script"></a>Het script uitvoeren

Volgende opdracht uitvoeren om het script uit te voeren

```powershell
PrivateDnsMigrationScript.ps1
```

![Het script uitvoeren](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>De abonnements-id invoeren en aanmelden bij Azure

U wordt gevraagd een abonnements-ID in te voeren met de privé-DNS-zones die u wilt migreren. U wordt gevraagd zich aan te melden bij uw Azure-account. Vul de aanmelding in, zodat het script toegang heeft tot de privé-DNS-zonebronnen in het abonnement.

![Aanmelden bij Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Selecteer de DNS-zones die u wilt migreren

Het script met de lijst met alle privé-DNS-zones in het abonnement en u vragen om te bevestigen welke u wilt migreren. Voer 'A' in om alle privé-DNS-zones te migreren. Zodra u deze stap uitvoert, maakt het script nieuwe privé-DNS-zones met behulp van een nieuw resourcemodel en kopieert het de gegevens naar de nieuwe DSN-zone. Deze stap zal uw bestaande privé DNS-zones in ieder geval niet wijzigen.

![DNS-zones selecteren](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>DNS-resolutie overschakelen naar de nieuwe DNS-zones

Zodra de zones en records zijn gekopieerd naar het nieuwe resourcemodel, wordt u in het script gevraagd om de DNS-resolutie over te schakelen naar nieuwe DNS-zones. Met deze stap wordt de koppeling tussen verouderde privé-DNS-zones en uw virtuele netwerken verwijderd. Wanneer de verouderde zone is losgekoppeld van de virtuele netwerken, zouden de nieuwe DNS-zones die in bovenstaande stap zijn gemaakt, automatisch de DNS-resolutie voor die virtuele netwerken overnemen.

Selecteer 'A' om de DNS-resolutie voor alle virtuele netwerken te schakelen.

![Naamomzetting wijzigen](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>De DNS-resolutie verifiëren

Controleer voordat u verder gaat of de DNS-resolutie op uw DNS-zones werkt zoals verwacht. U zich aanmelden bij uw azure VM's en nslookupquery's uitvoeren tegen de gemigreerde zones om te controleren of dns-resolutie werkt.

![Naamomzetting verifiëren](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Als u merkt dat DNS-query's niet worden opgelost, wacht u enkele minuten en probeert u de query's opnieuw. Als DNS-query's werken zoals verwacht, voert u 'Y' in wanneer u in het script wordt gevraagd het virtuele netwerk uit de privé-DNS-zone te verwijderen.

![Naamomzetting bevestigen](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Als dns-resolutie om welke reden dan ook niet werkt zoals verwacht, voert u 'N' in bovenstaande stap in en schakelt het script de DNS-resolutie terug naar verouderde zones. Maak een ondersteuningsticket en wij kunnen u helpen met de migratie van uw DNS-zones.

## <a name="cleanup"></a>Opschonen

Met deze stap worden de verouderde DNS-zones verwijderd en moet deze pas worden uitgevoerd nadat u hebt geverifieerd dat de DNS-resolutie werkt zoals verwacht. U wordt gevraagd elke privé-DNS-zone te verwijderen. Voer 'Y' bij elke prompt in nadat u hebt geverifieerd dat de DNS-resolutie voor die zones naar behoren werkt.

![Opruimen](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Uw automatisering bijwerken

Als u automatisering gebruikt, waaronder sjablonen, PowerShell-scripts of aangepaste code die zijn ontwikkeld met SDK, moet u uw automatisering bijwerken om het nieuwe resourcemodel voor de privé-DNS-zones te gebruiken. Hieronder vindt u de links naar nieuwe private DNS CLI/PS/SDK documentatie.
* [REST-API voor Azure DNS-privézones](https://docs.microsoft.com/rest/api/dns/privatedns/privatezones)
* [Azure DNS-privézones CLI](https://docs.microsoft.com/cli/azure/ext/privatedns/network/private-dns?view=azure-cli-latest)
* [Azure DNS-privézones PowerShell](https://docs.microsoft.com/powershell/module/az.privatedns/?view=azps-2.3.2)
* [Azure DNS-privézones SDK](https://docs.microsoft.com/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>Verdere hulp nodig

Maak een ondersteuningsticket als u verdere hulp nodig hebt bij het migratieproces of om welke reden dan ook de bovenstaande stappen niet voor u werken. Voeg het transcriptbestand dat door het PowerShell-script wordt gegenereerd, op met uw ondersteuningsticket.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van een privézone in Azure DNS met [Azure PowerShell](./private-dns-getstarted-powershell.md) of [Azure CLI](./private-dns-getstarted-cli.md).

* Lees meer over enkele veelvoorkomende [privézonescenario's](./private-dns-scenarios.md) die kunnen worden gerealiseerd met privézones in Azure DNS.

* Zie [Veelgestelde vragen](./dns-faq-private.md)over privé-informatie over privézones in Azure DNS, inclusief specifiek gedrag dat u voor bepaalde soorten bewerkingen verwachten.

* Meer informatie over DNS-zones en -records door het overzicht van [DNS-zones en records te](dns-zones-records.md)bezoeken.

* Informatie over enkele van de andere belangrijke [netwerkmogelijkheden](../networking/networking-overview.md) van Azure.
