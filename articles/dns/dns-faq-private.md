---
title: Veelgestelde vragen over privé-DNS in Azure
description: Veelgestelde vragen over Azure Privé-DNS
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: fca7359f9fa54899bb72be3b939e1a1839dbfbd1
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155708"
---
# <a name="azure-private-dns-faq"></a>Veelgestelde vragen over privé-DNS in Azure

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="does-azure-dns-support-private-domains"></a>Ondersteunt Azure DNS persoonlijke domeinen?

Ondersteuning voor privé domeinen wordt ondersteund met behulp van de functie Azure Privé-DNS-zones. Privé-DNS zones worden beheerd met dezelfde hulpprogram ma's als zones met Internet gerichte Azure DNS. Ze kunnen alleen worden omgezet in de opgegeven virtuele netwerken. Zie het [overzicht](private-dns-overview.md)voor meer informatie.

Zie [naam omzetting voor vm's en rolinstanties](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)voor informatie over andere interne DNS-opties in Azure.

## <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Werkt Azure DNS Private Zones over Azure-regio's?

Ja. Persoonlijke zones worden ondersteund voor DNS-omzetting tussen virtuele netwerken in azure-regio's. Persoonlijke zones werken zelfs zonder expliciet peering van de virtuele netwerken. Alle virtuele netwerken moeten worden opgegeven als omzetting virtuele netwerken voor de privé zone. Mogelijk moet u de virtuele netwerken moeten worden gekoppeld voor TCP/HTTP-verkeer om van de ene regio naar de andere te stromen.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Is er verbinding met Internet via virtuele netwerken die vereist zijn voor persoonlijke zones?

Nee. Persoonlijke zones werken samen met virtuele netwerken. U kunt ze gebruiken om domeinen te beheren voor virtuele machines of andere resources binnen en tussen virtuele netwerken. Internet connectiviteit is niet vereist voor naam omzetting.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Kan dezelfde privé zone worden gebruikt voor verschillende virtuele netwerken voor oplossing?

Ja. U kunt Maxi maal 1000 virtuele netwerken koppelen aan één privé zone.

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-linked-virtual-network-to-a-private-zone"></a>Kan een virtueel netwerk dat tot een ander abonnement behoort, worden toegevoegd als een gekoppeld virtueel netwerk aan een privé zone?

Ja. U moet beschikken over de machtiging schrijf bewerking voor de virtuele netwerken en de particuliere DNS-zone. De schrijf machtiging kan worden toegekend aan verschillende RBAC-rollen. De rol klassieke netwerkinzender RBAC heeft bijvoorbeeld schrijf machtigingen voor virtuele netwerken. Zie op [rollen gebaseerd toegangs beheer](../role-based-access-control/overview.md)voor meer informatie over RBAC-rollen.

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Worden de automatisch geregistreerde DNS-records van virtuele machines in een privé zone automatisch verwijderd wanneer u de virtuele machine verwijdert?

Ja. Als u een virtuele machine in een gekoppeld virtueel netwerk verwijdert waarvoor automatische registratie is ingeschakeld, worden de geregistreerde records automatisch verwijderd.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Kan een automatisch geregistreerde virtuele-machine record in een privé zone van een gekoppeld virtueel netwerk hand matig worden verwijderd?

Ja. U kunt de automatisch geregistreerde DNS-records overschrijven met een hand matig gemaakte DNS-record in de zone. In dit onderwerp worden de volgende vraag en dit antwoord adres beschreven.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Wat gebeurt er wanneer ik hand matig een nieuwe DNS-record wil maken in een persoonlijke zone die dezelfde hostnaam heeft als een automatisch geregistreerde bestaande virtuele machine in een gekoppeld virtueel netwerk?

U probeert hand matig een nieuwe DNS-record te maken in een persoonlijke zone die dezelfde hostnaam heeft als een bestaande, automatisch geregistreerde virtuele machine in een gekoppeld virtueel netwerk. Wanneer u dit doet, overschrijft de nieuwe DNS-record de automatisch geregistreerde virtuele-machine record. Als u probeert deze hand matig gemaakte DNS-record opnieuw uit de zone te verwijderen, slaagt de verwijdering. De automatische registratie gebeurt opnieuw zolang de virtuele machine nog bestaat en waaraan een persoonlijk IP-adres is gekoppeld. De DNS-record wordt automatisch opnieuw gemaakt in de zone.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Wat gebeurt er wanneer we een gekoppeld virtueel netwerk ontkoppelen van een privé zone? Worden de automatisch geregistreerde virtuele-machine records uit het virtuele netwerk ook uit de zone verwijderd?

Ja. Als u een gekoppeld virtueel netwerk wilt ontkoppelen van een privé zone, werkt u de DNS-zone bij om de gekoppelde virtuele netwerk koppeling te verwijderen. In dit proces worden de records van virtuele machines die automatisch zijn geregistreerd, uit de zone verwijderd.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Wat gebeurt er wanneer we een gekoppeld virtueel netwerk verwijderen dat is gekoppeld aan een privé zone? Moeten we de privé zone hand matig bijwerken om het virtuele netwerk als een gekoppeld virtueel netwerk van de zone te ontkoppelen?

Ja. Wanneer u een gekoppeld virtueel netwerk verwijdert zonder het eerst te ontkoppelen van een particuliere zone, wordt uw verwijderings bewerking voltooid. Het virtuele netwerk wordt echter niet automatisch ontkoppeld van uw privé zone, indien van toepassing. U moet het virtuele netwerk hand matig ontkoppelen van de privé zone. Koppel het virtuele netwerk daarom losgekoppeld van uw privé zone voordat u het verwijdert.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Werkt de DNS-omzetting met behulp van de standaard-FQDN (internal.cloudapp.net) nog steeds, zelfs wanneer een privé zone (bijvoorbeeld private.contoso.com) is gekoppeld aan een virtueel netwerk?

Ja. Particuliere zones vervangen de standaard DNS-resoluties niet met behulp van de door Azure meegeleverde internal.cloudapp.net-zone. Het wordt aangeboden als een extra functie of uitbrei ding. Of u nu afhankelijk bent van de door Azure verschafte internal.cloudapp.net of uw eigen privé zone, gebruikt u de FQDN van de zone die u wilt oplossen.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Wordt het DNS-achtervoegsel op virtuele machines in een gekoppeld virtueel netwerk gewijzigd in dat van de privé zone?

Nee. Het DNS-achtervoegsel op de virtuele machines in het gekoppelde virtuele netwerk blijft als standaard achtervoegsel van Azure (' *. internal.cloudapp.net '). U kunt dit DNS-achtervoegsel hand matig wijzigen op uw virtuele machines naar dat van de privé zone.

## <a name="what-are-the-usage-limits-for-azure-private-dns"></a>Wat zijn de gebruiks limieten voor Azure Privé-DNS?

De volgende standaard limieten zijn van toepassing wanneer u Azure Privé-DNS gebruikt.

| Resource | Standaardlimiet |
| --- | --- |
|Privé-DNS zones per abonnement|1000|
|Record sets per Privé-DNS zone|25,000|
|Records per Recordset|20|
|Virtual Network koppelingen per privé-DNS-zone|1000|
|Virtuele netwerken koppelingen per privé-DNS-zone met automatische registratie ingeschakeld|100|
|Aantal privé-DNS-zones waaraan een virtueel netwerk kan worden gekoppeld met automatische registratie ingeschakeld|1|
|Aantal privé-DNS-zones waaraan een virtueel netwerk kan worden gekoppeld|1000|

## <a name="is-there-portal-support-for-private-zones"></a>Zijn er Portal-ondersteuning voor privé zones?

Ja, en privé zones die al zijn gemaakt via Api's, Power shell, de CLI en Sdk's, worden weer gegeven op de Azure Portal.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Waarom worden mijn bestaande privé-DNS-zones niet weer gegeven in de nieuwe portal?

Als onderdeel van de preview-versie voor vernieuwing hebben we een nieuw resource model voor privé-DNS-zones verzonden. Uw bestaande privé-DNS-zones moeten worden gemigreerd naar een nieuw resource model voordat deze in de nieuwe portal-ervaring kunnen worden weer gegeven. Hieronder vindt u instructies voor het migreren naar een nieuw resource model.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Hoe kan ik mijn bestaande privé-DNS-zones naar het nieuwe model migreren?
We raden u ten zeerste aan om zo snel mogelijk naar het nieuwe resource model te migreren. Verouderd resource model wordt ondersteund, maar er worden echter geen verdere functies op dit model ontwikkeld. In de toekomst nemen we de bedoeling af om het nieuwe resource model te vervangen. Zie[migratie handleiding voor Azure DNS particuliere zones](private-dns-migration-guide.md)voor meer informatie over het migreren van uw bestaande privé-DNS-zones naar een nieuw resource model.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over Azure Privé-DNS](private-dns-overview.md)