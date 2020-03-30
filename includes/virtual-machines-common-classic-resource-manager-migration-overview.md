---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: tanmaygore
ms.service: virtual-machines
ms.topic: include
ms.date: 02/06/2020
ms.author: tagore
ms.custom: include file
ms.openlocfilehash: 4e07334e859f2c1401547cc3f88988830b71c5e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77193020"
---
In dit artikel wordt beschreven hoe u infrastructuur als een service (IaaS)-resources migreert van de implementatiemodellen Classic naar Resource Manager en hoe u resources verbinden van de twee implementatiemodellen die naast uw abonnement bestaan met behulp van een virtueel netwerk site-to-site gateways. U meer lezen over de functies en voordelen van [Azure Resource Manager.](../articles/azure-resource-manager/management/overview.md) 

## <a name="goal-for-migration"></a>Doel voor migratie
Resourcemanager maakt het implementeren van complexe toepassingen via sjablonen mogelijk, configureert virtuele machines met vm-extensies en bevat toegangsbeheer en -tags. Azure Resource Manager bevat schaalbare, parallelle implementatie voor virtuele machines in beschikbaarheidssets. Het nieuwe implementatiemodel biedt ook onafhankelijk van de levenscyclusbeheer van compute, netwerk en opslag. Ten slotte is er een focus op het inschakelen van beveiliging standaard met de handhaving van virtuele machines in een virtueel netwerk.

Bijna alle functies van het klassieke implementatiemodel worden ondersteund voor compute, netwerk en opslag onder Azure Resource Manager. Als u wilt profiteren van de nieuwe mogelijkheden in Azure Resource Manager, u bestaande implementaties migreren vanuit het Klassieke implementatiemodel.

## <a name="supported-resources-for-migration"></a>Ondersteunde bronnen voor migratie
Deze klassieke IaaS-bronnen worden ondersteund tijdens migratie

* Virtuele machines
* Beschikbaarheidssets
* Opslagaccounts
* Virtuele netwerken
* VPN-gateways
* Express Route Gateways _(alleen in hetzelfde abonnement als Virtueel netwerk)_
* Netwerkbeveiligingsgroepen
* Routetabellen
* Gereserveerde IP-adressen

## <a name="supported-scopes-of-migration"></a>Ondersteunde migratiescopes
Er zijn vier verschillende manieren om de migratie van reken-, netwerk- en opslagbronnen te voltooien:

* [Migratie van virtuele machines (NIET in een virtueel netwerk)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Migratie van virtuele machines (in een virtueel netwerk)](#migration-of-virtual-machines-in-a-virtual-network)
* [Migratie van opslagaccounts](#migration-of-storage-accounts)
* [Migratie van niet-gekoppelde middelen](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migratie van virtuele machines (NIET in een virtueel netwerk)
In het implementatiemodel ResourceManager wordt beveiliging standaard afgedwongen voor uw toepassingen. Alle VM's moeten zich in een virtueel netwerk bevinden in het Resource Manager-model. Het Azure-platform`Stop`start `Deallocate`( `Start`, en ) de VM's opnieuw als onderdeel van de migratie. U hebt twee opties voor de virtuele netwerken waarnaar de virtuele machines worden gemigreerd:

* U het platform vragen om een nieuw virtueel netwerk te maken en de virtuele machine te migreren naar het nieuwe virtuele netwerk.
* U de virtuele machine migreren naar een bestaand virtueel netwerk in Resource Manager.

> [!NOTE]
> In dit migratiebereik mogen zowel de beheer-vlakbewerkingen als de gegevensvlakbewerkingen gedurende een periode tijdens de migratie niet worden toegestaan.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migratie van virtuele machines (in een virtueel netwerk)
Voor de meeste VM-configuraties wordt alleen de metagegevens gemigreerd tussen de implementatiemodellen Classic en Resource Manager. De onderliggende VM's draaien op dezelfde hardware, in hetzelfde netwerk en met dezelfde opslag. De bewerkingen van het management-vliegtuig zijn mogelijk gedurende een bepaalde periode tijdens de migratie niet toegestaan. Het gegevensvlak blijft echter werken. Dat wil zeggen dat uw toepassingen die bovenop VM's (klassiek) worden uitgevoerd, geen downtime oplopen tijdens de migratie.

De volgende configuraties worden momenteel niet ondersteund. Als er in de toekomst ondersteuning wordt toegevoegd, kunnen sommige VM's in deze configuratie downtime veroorzaken (vm-bewerkingen stoppen, detoewijzen en opnieuw starten).

* U hebt meer dan één beschikbaarheid ingesteld in één cloudservice.
* U hebt een of meer beschikbaarheidssets en VM's die niet in één cloudservice zijn ingesteld.

> [!NOTE]
> In dit migratiebereik is het mogelijk dat het beheervlak gedurende een bepaalde periode tijdens de migratie niet is toegestaan. Voor bepaalde configuraties zoals eerder beschreven, vindt downtime tussen gegevens en vlak plaats.
>

### <a name="migration-of-storage-accounts"></a>Migratie van opslagaccounts
Als u naadloze migratie wilt toestaan, u VM's voor Resourcebeheer implementeren in een klassiek opslagaccount. Met deze mogelijkheid kunnen reken- en netwerkbronnen onafhankelijk van opslagaccounts worden gemigreerd. Zodra u bent gemigreerd over uw virtuele machines en virtuele netwerk, moet u migreren over uw opslagaccounts om het migratieproces te voltooien.

Als uw opslagaccount geen gekoppelde schijven of virtuele machines-gegevens heeft en alleen blobs, bestanden, tabellen en wachtrijen heeft, kan de migratie naar Azure Resource Manager worden uitgevoerd als een zelfstandige migratie zonder afhankelijkheden.

> [!NOTE]
> Het implementatiemodel resourcebeheer heeft niet het concept van klassieke afbeeldingen en schijven. Wanneer het opslagaccount is gemigreerd, zijn klassieke afbeeldingen en schijven niet zichtbaar in de stapel Resourcemanager, maar blijven de ondersteunende VHD's in het opslagaccount staan.

In de volgende schermafbeeldingen ziet u hoe u een Klassiek-opslagaccount upgraden naar een Azure Resource Manager-opslagaccount met Azure-portal:
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Ga naar uw opslagaccount.
3. Klik **in** de sectie Instellingen op **Migreren naar ARM**.
4. Klik op **Valideren** om de haalbaarheid van migratie te bepalen.
5. Als de validatie voorbij gaat, klikt u op **Voorbereiden** om een gemigreerd opslagaccount te maken.
6. Typ **ja** om migratie te bevestigen en klik op **Vastleggen** om de migratie te voltooien.

    ![Opslagaccount valideren](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Opslagaccount voorbereiden](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![Migratie van opslagaccount afronden](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>Migratie van niet-gekoppelde middelen
Opslagaccounts zonder gekoppelde schijven of virtuele machines gegevens kunnen onafhankelijk worden gemigreerd.

Netwerkbeveiligingsgroepen, routetabellen & gereserveerde IP's die niet zijn gekoppeld aan virtuele machines en virtuele netwerken, kunnen ook onafhankelijk van elkaar worden gemigreerd.

<br>

## <a name="unsupported-features-and-configurations"></a>Niet-ondersteunde functies en configuraties
Sommige functies en configuraties worden momenteel niet ondersteund; de volgende secties beschrijven onze aanbevelingen om hen heen.

### <a name="unsupported-features"></a>Niet-ondersteunde functies
De volgende functies worden momenteel niet ondersteund. U deze instellingen optioneel verwijderen, de VM's migreren en vervolgens de instellingen in het implementatiemodel resourcebeheer opnieuw inschakelen.

| Resourceprovider | Functie | Aanbeveling |
| --- | --- | --- |
| Compute | Niet-gekoppelde virtuele machineschijven. | De VHD-blobs achter deze schijven worden gemigreerd wanneer het opslagaccount wordt gemigreerd |
| Compute | Virtuele machine beelden. | De VHD-blobs achter deze schijven worden gemigreerd wanneer het opslagaccount wordt gemigreerd |
| Netwerk | Eindpunt-ACL's. | Eindpuntacl's verwijderen en migratie opnieuw proberen. |
| Netwerk | Application Gateway | Verwijder de toepassingsgateway voordat u met de migratie begint en maak de toepassingsgateway opnieuw zodra de migratie is voltooid. |
| Netwerk | Virtuele netwerken met VNet Peering. | Migreer virtueel netwerk naar Resourcebeheer en vervolgens peer. Meer informatie over [VNet Peering](../articles/virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Niet-ondersteunde configuraties
De volgende configuraties worden momenteel niet ondersteund.

| Service | Configuratie | Aanbeveling |
| --- | --- | --- |
| Resource Manager |RBAC (Role-Based Access Control) voor klassieke bronnen |Omdat de URI van de resources na migratie wordt gewijzigd, wordt aanbevolen dat u de RBAC-beleidsupdates plant die na migratie moeten worden uitgevoerd. |
| Compute |Meerdere subnetten die zijn gekoppeld aan een vm |Werk de subnetconfiguratie bij om naar slechts één subnet te verwijzen. Dit kan vereisen dat u een secundaire NIC (die verwijst naar een ander subnet) uit de VM verwijdert en deze opnieuw bevestigt nadat de migratie is voltooid. |
| Compute |Virtuele machines die deel uitmaken van een virtueel netwerk, maar geen expliciet subnet hebben toegewezen |U de VM optioneel verwijderen. |
| Compute |Virtuele machines met waarschuwingen, beleid voor autoschaal |De migratie gaat door en deze instellingen worden verwijderd. Het wordt ten zeerste aanbevolen om uw omgeving te evalueren voordat u de migratie doet. U ook de waarschuwingsinstellingen opnieuw configureren nadat de migratie is voltooid. |
| Compute |XML VM-extensies (BGInfo 1.*, Visual Studio Debugger, Web Deploy en Remote Debugging) |Dit wordt niet ondersteund. Het wordt aanbevolen deze extensies uit de virtuele machine te verwijderen om de migratie voort te zetten of ze worden automatisch verwijderd tijdens het migratieproces. |
| Compute |Opstartdiagnostiek met Premium-opslag |Schakel de functie Opstartdiagnose voor de VM's uit voordat u verdergaat met migratie. U opstartdiagnoses opnieuw inschakelen in de stack Resource Manager nadat de migratie is voltooid. Bovendien moeten blobs die worden gebruikt voor schermafbeeldingen en seriële logboeken worden verwijderd, zodat u niet langer in rekening wordt gebracht voor die blobs. |
| Compute | Cloudservices die web-/werknemersrollen bevatten | Dit wordt momenteel niet ondersteund. |
| Compute | Cloudservices die meer dan één beschikbaarheidsset of meerdere beschikbaarheidssets bevatten. |Dit wordt momenteel niet ondersteund. Verplaats de virtuele machines naar dezelfde beschikbaarheidsset voordat u migreert. |
| Compute | VM met Azure Security Center-extensie | Azure Security Center installeert automatisch extensies op uw virtuele machines om hun beveiliging te controleren en waarschuwingen te verhogen. Deze extensies worden meestal automatisch geïnstalleerd als het Azure Security Center-beleid is ingeschakeld voor het abonnement. Als u de virtuele machines wilt migreren, schakelt u het beveiligingscentrumbeleid voor het abonnement uit, waardoor de bewakingsextensie Beveiligingscentrum uit de virtuele machines wordt verwijderd. |
| Compute | VM met back-up- of momentopnameextensie | Deze extensies worden geïnstalleerd op een virtuele machine die is geconfigureerd met de Azure Backup-service. Hoewel de migratie van deze VM's niet wordt ondersteund, volgt u de richtlijnen [hier](/azure/virtual-machines/windows/migration-classic-resource-manager-faq#i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault) om back-ups te behouden die vóór de migratie zijn gemaakt.  |
| Compute | VM met Azure Site Recovery-extensie | Deze extensies worden geïnstalleerd op een virtuele machine die is geconfigureerd met de Azure Site Recovery-service. Hoewel de migratie van opslag die wordt gebruikt met Siteherstel werkt, wordt de huidige replicatie beïnvloed. U moet VM-replicatie uitschakelen en inschakelen na opslagmigratie. |
| Netwerk |Virtuele netwerken die virtuele machines en web-/werknemersrollen bevatten |Dit wordt momenteel niet ondersteund. Verplaats de web-/werkfunctie naar hun eigen virtuele netwerk voordat u migreert. Zodra het klassieke virtuele netwerk is gemigreerd, kan het gemigreerde Azure Resource Manager Virtual Network worden gekeken met het klassieke virtuele netwerk om vergelijkbare configuratie te bereiken als voorheen.|
| Netwerk | Classic Express Route circuits |Dit wordt momenteel niet ondersteund. Deze circuits moeten worden gemigreerd naar Azure Resource Manager voordat u begint met iaas-migratie. Zie [Schakelingen van De ExpressRoute van de klassieker naar het implementatiemodel Resource Manager](../articles/expressroute/expressroute-move.md)voor meer informatie.|
| Azure App Service |Virtuele netwerken die App Service-omgevingen bevatten |Dit wordt momenteel niet ondersteund. |
| Azure HDInsight |Virtuele netwerken die HDInsight-services bevatten |Dit wordt momenteel niet ondersteund. |
| Microsoft Dynamics Lifecycle Services |Virtuele netwerken die virtuele machines bevatten die worden beheerd door Dynamics Lifecycle Services |Dit wordt momenteel niet ondersteund. |
| Azure AD Domain Services |Virtuele netwerken die Azure AD-domeinservices bevatten |Dit wordt momenteel niet ondersteund. |
| Azure API Management |Virtuele netwerken die Azure API Management-implementaties bevatten |Dit wordt momenteel niet ondersteund. Als u de IaaS VNET wilt migreren, wijzigt u het VNET van de API-beheerimplementatie, een bewerking zonder downtime. |
