---
title: Privéeindpunten gebruiken
titleSuffix: Azure Storage
description: Overzicht van privéeindpunten voor veilige toegang tot opslagaccounts van virtuele netwerken.
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: c51f2db698f30368c9d4090d3d571fa0c131178a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299053"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Privéeindpunten gebruiken voor Azure Storage

U [privéeindpunten](../../private-link/private-endpoint-overview.md) voor uw Azure Storage-accounts gebruiken om clients in een virtueel netwerk (VNet) veilig toegang te geven tot gegevens via een [privékoppeling.](../../private-link/private-link-overview.md) Het privéeindpunt gebruikt een IP-adres van de VNet-adresruimte voor uw opslagaccountservice. Netwerkverkeer tussen de clients op het VNet en het opslagaccount loopt via het VNet en een privékoppeling op het Microsoft-backbonenetwerk, waardoor blootstelling van het openbare internet wordt geëlimineerd.

Als u privéeindpunten voor uw opslagaccount gebruikt, u:

- Beveilig uw opslagaccount door de opslagfirewall te configureren om alle verbindingen op het openbare eindpunt voor de opslagservice te blokkeren.
- Verhoog de beveiliging van het virtuele netwerk (VNet), door exfiltratie van gegevens van het VNet te blokkeren.
- Maak veilig verbinding met opslagaccounts van on-premises netwerken die verbinding maken met het VNet via [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) of [ExpressRoutes](../../expressroute/expressroute-locations.md) met private-peering.

## <a name="conceptual-overview"></a>Conceptueel overzicht

![Overzicht van privéeindpunten voor Azure Storage](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Een privéeindpunt is een speciale netwerkinterface voor een Azure-service in uw [Virtual Network](../../virtual-network/virtual-networks-overview.md) (VNet). Wanneer u een privéeindpunt maakt voor uw opslagaccount, biedt het veilige connectiviteit tussen clients op uw VNet en uw opslag. Aan het privéeindpunt wordt een IP-adres toegewezen uit het IP-adresbereik van uw VNet. De verbinding tussen het privéeindpunt en de opslagservice maakt gebruik van een beveiligde privékoppeling.

Toepassingen in het VNet kunnen naadloos verbinding maken met de opslagservice via het privéeindpunt, **met dezelfde verbindingstekenreeksen en autorisatiemechanismen die ze anders zouden gebruiken.** Privéeindpunten kunnen worden gebruikt met alle protocollen die worden ondersteund door het opslagaccount, inclusief REST en SMB.

Privéeindpunten kunnen worden gemaakt in subnetten die [Service-eindpunten](../../virtual-network/virtual-network-service-endpoints-overview.md)gebruiken. Clients in een subnet kunnen dus verbinding maken met één opslagaccount met behulp van een privéeindpunt, terwijl serviceeindpunten worden gebruikt om toegang te krijgen tot anderen.

Wanneer u een privé-eindpunt voor een opslagservice in uw VNet maakt, wordt er een aanvraag voor goedkeuring verzonden naar de eigenaar van het opslagaccount. Als de gebruiker die het privéeindpunt aanvraagt, ook eigenaar is van het opslagaccount, wordt deze toestemmingsaanvraag automatisch goedgekeurd.

Eigenaren van opslagaccountkunnen toestemmingsaanvragen en de privéeindpunten beheren via het tabblad '*Privéeindpunten'* voor het opslagaccount in de [Azure-portal.](https://portal.azure.com)

> [!TIP]
> Als u de toegang tot uw opslagaccount alleen via het privéeindpunt wilt beperken, configureert u de opslagfirewall om de toegang via het openbare eindpunt te weigeren of te beheren.

U uw opslagaccount beveiligen om alleen verbindingen van uw VNet te accepteren, door [de opslagfirewall](storage-network-security.md#change-the-default-network-access-rule) te configureren om standaard toegang te weigeren via het openbare eindpunt. U hebt geen firewallregel nodig om verkeer toe te staan vanaf een VNet met een privéeindpunt, omdat de opslagfirewall alleen de toegang via het openbare eindpunt regelt. Privéeindpunten vertrouwen in plaats daarvan op de toestemmingsstroom voor het verlenen van subnetten toegang tot de opslagservice.

### <a name="private-endpoints-for-azure-storage"></a>Privéeindpunten voor Azure Storage

Wanneer u het privéeindpunt maakt, moet u het opslagaccount en de opslagservice opgeven waarmee het wordt verbonden. U hebt een apart privéeindpunt nodig voor elke opslagservice in een opslagaccount waartoe u toegang moet hebben, namelijk [Blobs,](../blobs/storage-blobs-overview.md) [Data Lake Storage Gen2,](../blobs/data-lake-storage-introduction.md) [Files](../files/storage-files-introduction.md), [Queues,](../queues/storage-queues-introduction.md) [Tables](../tables/table-storage-overview.md)of [Static Websites](../blobs/storage-blob-static-website.md).

> [!TIP]
> Maak een afzonderlijk privéeindpunt voor de secundaire instantie van de opslagservice voor betere leesprestaties op RA-GRS-accounts.

Voor leestoegang tot het secundaire gebied met een opslagaccount dat is geconfigureerd voor georedundante opslag, hebt u afzonderlijke privéeindpunten nodig voor zowel de primaire als secundaire exemplaren van de service. U hoeft geen privéeindpunt te maken voor de secundaire instantie voor **failover.** Het privéeindpunt maakt automatisch verbinding met de nieuwe primaire instantie na een failover. Zie [Redundantie van Azure Storage](storage-redundancy.md)voor meer informatie over redundantie van opslag.

Raadpleeg de volgende artikelen voor meer gedetailleerde informatie over het maken van een privéeindpunt voor uw opslagaccount:

- [Privé verbinding maken met een opslagaccount vanuit de ervaring van het opslagaccount in de Azure-portal](../../private-link/create-private-endpoint-storage-portal.md)
- [Een privéeindpunt maken met het Private Link Center in de Azure-portal](../../private-link/create-private-endpoint-portal.md)
- [Een privéeindpunt maken met Azure CLI](../../private-link/create-private-endpoint-cli.md)
- [Een privéeindpunt maken met Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Verbinding maken met privéeindpunten

Clients op een VNet die het privéeindpunt gebruiken, moeten dezelfde verbindingstekenreeks gebruiken voor het opslagaccount als clients die verbinding maken met het openbare eindpunt. We vertrouwen op DNS-resolutie om de verbindingen van het VNet automatisch over te leiden naar het opslagaccount via een privélink.

> [!IMPORTANT]
> Gebruik dezelfde verbindingstekenreeks om verbinding te maken met het opslagaccount met behulp van privéeindpunten, zoals u anders zou gebruiken. Maak geen verbinding met het opslagaccount via de URL van het *'privatelink'-subdomein.*

We maken standaard een [privé-DNS-zone](../../dns/private-dns-overview.md) die aan het VNet is gekoppeld met de nodige updates voor de privé-eindpunten. Als u echter uw eigen DNS-server gebruikt, moet u mogelijk aanvullende wijzigingen aanbrengen in uw DNS-configuratie. In de sectie [over DNS-wijzigingen](#dns-changes-for-private-endpoints) hieronder worden de updates beschreven die nodig zijn voor privéeindpunten.

## <a name="dns-changes-for-private-endpoints"></a>DNS-wijzigingen voor privéeindpunten

Wanneer u een privéeindpunt maakt, wordt de DNS CNAME-bronrecord voor het opslagaccount bijgewerkt naar een alias in een subdomein met het voorvoegsel '*privatelink'.* Standaard maken we ook een [privé-DNS-zone](../../dns/private-dns-overview.md), die overeenkomt met het subdomein *'privatelink',* met de DNS A-bronrecords voor de privéeindpunten.

Wanneer u de URL van het opslageindpunt van buiten het VNet oplost met het privéeindpunt, wordt deze opgelost tot het openbare eindpunt van de opslagservice. Wanneer de URL van het opslageindpunt wordt opgelost vanuit het VNet dat het privéeindpunt host, wordt deze naar het IP-adres van het privéeindpunt opgesteld.

Voor het geïllustreerde voorbeeld hierboven zijn de DNS-bronrecords voor het opslagaccount 'StorageAccountA', wanneer deze van buiten het VNet worden opgelost als host van het privéeindpunt:

| Name                                                  | Type  | Waarde                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<openbaar eindpunt van opslagdienst\>                   |
| \<openbaar eindpunt van opslagdienst\>                   | A     | \<openbare IP-adres van de opslagdienst\>                 |

Zoals eerder vermeld, u de toegang voor clients buiten het VNet weigeren of beheren via het openbare eindpunt met behulp van de opslagfirewall.

De DNS-bronrecords voor StorageAccountA, wanneer deze worden opgelost door een client in het VNet dat het privéeindpunt host, zijn:

| Name                                                  | Type  | Waarde                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Deze aanpak maakt toegang tot het opslagaccount **mogelijk met dezelfde verbindingstekenreeks** voor clients op de VNet die de privéeindpunten hosten, evenals clients buiten het VNet.

Als u een aangepaste DNS-server in uw netwerk gebruikt, moeten clients de FQDN voor het eindpunt van het opslagaccount kunnen oplossen naar het ip-adres voor privéeindpunt. U moet uw DNS-server configureren om uw subdomein voor privékoppelingen te delegeren aan de privé-DNS-zone voor de VNet of de A-records configureren voor '*StorageAccountA.privatelink.blob.core.windows.net*' met het privé-eindpunt-IP-adres.

> [!TIP]
> Wanneer u een aangepaste of on-premises DNS-server gebruikt, moet u uw DNS-server configureren om de naam van het opslagaccount in het subdomein 'privatelink' op te lossen naar het privé-eindpunt-IP-adres. U dit doen door het subdomein 'privatelink' te delegeren naar de private DNS-zone van het VNet, of door de DNS-zone op uw DNS-server te configureren en de DNS A-records toe te voegen.

De aanbevolen DNS-zonenamen voor privéeindpunten voor opslagservices zijn:

| Opslagservice        | Zonenaam                            |
| :--------------------- | :----------------------------------- |
| Blob-service           | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `privatelink.dfs.core.windows.net`   |
| Bestandsservice           | `privatelink.file.core.windows.net`  |
| Wachtrijservice          | `privatelink.queue.core.windows.net` |
| Tabelservice          | `privatelink.table.core.windows.net` |
| Statische websites        | `privatelink.web.core.windows.net`   |

Raadpleeg de volgende artikelen voor meer informatie over het configureren van uw eigen DNS-server ter ondersteuning van privéeindpunten:

- [Naamomzetting voor resources in virtuele Azure-netwerken](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [DNS-configuratie voor privéeindpunten](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="pricing"></a>Prijzen

Zie [Azure Private Link-prijzen](https://azure.microsoft.com/pricing/details/private-link)voor prijsdetails.

## <a name="known-issues"></a>Bekende problemen

Houd rekening met de volgende bekende problemen met privéeindpunten voor Azure Storage.

### <a name="copy-blob-support"></a>Ondersteuning voor Blob kopiëren

Als het opslagaccount wordt beschermd door een firewall en het account wordt geopend via privéeindpunten, kan dat account niet dienen als de bron van een [Copy Blob-bewerking.](/rest/api/storageservices/copy-blob)

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Beperkingen voor opslagtoegang voor clients in VNets met privéeindpunten

Clients in VNets met bestaande privéeindpunten worden geconfronteerd met beperkingen bij het openen van andere opslagaccounts met privéeindpunten. Stel dat een VNet N1 een privéeindpunt heeft voor een opslagaccount A1 voor Blob-opslag. Als opslagaccount A2 een privéeindpunt heeft in een VNet N2 voor Blob-opslag, moeten clients in VNet N1 ook toegang krijgen tot Blob-opslag in account A2 met behulp van een privéeindpunt. Als opslagaccount A2 geen privéeindpunten voor Blob-opslag heeft, hebben clients in VNet N1 toegang tot Blob-opslag in dat account zonder een privéeindpunt.

Deze beperking is het gevolg van de DNS-wijzigingen die zijn aangebracht wanneer account A2 een privéeindpunt maakt.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Regels voor netwerkbeveiligingsgroepen voor subnetten met privé-eindpunten

Momenteel u nsg-regels [(Network Security Group)](../../virtual-network/security-overview.md) en door de gebruiker gedefinieerde routes voor privéeindpunten niet configureren. NSG-regels die worden toegepast op het subnet dat het privéeindpunt host, worden toegepast op het privéeindpunt. Een beperkte oplossing voor dit probleem is het implementeren van uw toegangsregels voor privéeindpunten op de bronsubnetten, hoewel deze aanpak mogelijk een hogere beheeroverhead vereist.

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-firewalls en virtuele netwerken configureren](storage-network-security.md)
- [Beveiligingsaanbevelingen voor Blob-opslag](../blobs/security-recommendations.md)
