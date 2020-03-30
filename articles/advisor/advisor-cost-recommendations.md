---
title: Servicekosten verlagen met Azure Advisor
description: Gebruik Azure Advisor om de kosten van uw Azure-implementaties te optimaliseren.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 0237feab59551ecab87d78b0d4d66b9fc7b47e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259693"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Servicekosten verlagen met Azure Advisor

Advisor helpt u uw totale Azure-uitgaven te optimaliseren en te verminderen door inactieve en onderbenutte resources te identificeren.U kostenaanbevelingen krijgen via het tabblad **Kosten** op het dashboard van Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Uitgaven aan virtuele machines optimaliseren door de grootte ervan te wijzigen of door onderbenutte instanties af te sluiten 

Hoewel bepaalde toepassingsscenario's kunnen resulteren in een laag gebruik door ontwerp, u vaak geld besparen door het beheer van de grootte en het aantal van uw virtuele machines. Advisor geavanceerde evaluatie modellen overweegt virtuele machines voor shut-down wanneer P95e van de maximale waarde van cpu-gebruik is minder dan 3% en het gebruik van het netwerk is minder dan 2% over een periode van 7 dagen. Virtuele machines worden beschouwd voor de juiste grootte wanneer het mogelijk is om de huidige belasting in een kleinere SKU (binnen dezelfde SKU-familie) of een kleiner aantal # van bijvoorbeeld te passen dat de huidige belasting niet meer dan 80% gebruik gaat wanneer niet-gebruiker geconfronteerd workloads en niet meer dan 40% bij de gebruiker-geconfronteerd werkbelasting. Hier wordt het type werkbelasting bepaald door de CPU-gebruikskenmerken van de werkbelasting te analyseren.

De aanbevolen acties worden afgesloten of het formaat wijzigen, specifiek voor resource waarvoor wordt aanbevolen. Advisor toont u de geschatte kostenbesparingen voor aanbevolen acties - het formaat of de afsluiting wijzigen. Voor aanbevolen actie voor het wijzigen van het formaat biedt Advisor ook actuele en gerichte SKU-informatie. 

Als u agressiever wilt zijn bij het identificeren van onderbenutte virtuele machines, u de CPU-gebruiksregel per abonnement aanpassen.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Verlaag de kosten door ongesprodateerde ExpressRoute-circuits te elimineren

Advisor identificeert ExpressRoute-circuits die langer dan een maand in de providerstatus van *Not Provisioned* staan en raadt aan het circuit te verwijderen als u niet van plan bent het circuit in te richten bij uw connectiviteitsprovider.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Kosten verlagen door niet-actieve virtuele netwerkgateways te verwijderen of opnieuw te configureren

Advisor identificeert virtuele netwerkgateways die al meer dan 90 dagen niet actief zijn. Aangezien deze gateways elk uur worden gefactureerd, moet u overwegen ze opnieuw te configureren of te verwijderen als u niet van plan bent ze meer te gebruiken. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Gereserveerde instanties van virtuele machines kopen om kosten te besparen ten opzichte van betalen per gebruik

Advisor controleert uw gebruik van uw virtuele machine in de afgelopen 30 dagen en bepaalt of u geld besparen door een Azure-reservering aan te schaffen. Advisor toont u de regio's en maten waar u mogelijk de meeste besparingen heeft en toont u de geschatte besparingen van het kopen van reserveringen. Met Azure-reserveringen u de basiskosten voor uw virtuele machines vooraf aanschaffen. Kortingen zijn automatisch van toepassing op nieuwe of bestaande VM's die dezelfde grootte en regio hebben als uw reserveringen. [Meer informatie over Azure Reserved VM Instances.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor zal u ook op de hoogte stellen van gereserveerde exemplaren die u hebt die in de komende 30 dagen verlopen. Het zal u aanbevelen nieuwe gereserveerde exemplaren te kopen om te voorkomen dat betalen betalen-as-you-go prijzen.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Niet-gekoppelde openbare IP-adressen verwijderen om geld te besparen

Advisor identificeert openbare IP-adressen die momenteel niet zijn gekoppeld aan Azure-resources, zoals Load Balancers of VM's. Deze openbare IP-adressen worden geleverd met een nominale vergoeding. Als u niet van plan bent om ze te gebruiken, kan het verwijderen ervan leiden tot kostenbesparingen.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Azure Data Factory-pijplijnen verwijderen die niet werken

Azure Advisor detecteert Azure Data Factory-pijplijnen die herhaaldelijk mislukken en raden u aan de problemen op te lossen of de falende pijplijnen te verwijderen als ze niet meer nodig zijn. U wordt gefactureerd voor deze pijpleidingen, ook al zijn ze niet ten dienste van u, terwijl ze niet. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Standaardmomentopnamen gebruiken voor beheerde schijven
Sla uw momentopnamen op in Standard Storage, ongeacht het opslagtype van de bovenliggende schijf, om een kostenbesparing van 60% te realiseren. Deze optie is de standaardoptie voor momentopnamen van Beheerde schijven. Azure Advisor identificeert momentopnamen die zijn opgeslagen Premium Storage en raadt aan om uw momentopname te migreren van Premium naar Standard Storage. [Meer informatie over managed disk-prijzen](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="utilize-lifecycle-management"></a>Levenscyclusbeheer gebruiken
Azure Advisor gebruikt informatie over het aantal Azure blob-opslagobjecten, de totale grootte en transacties om te detecteren of een of meer van uw opslagaccounts het meest geschikt zijn om levenscyclusbeheer in te schakelen voor laaggegevens. Het zal u vragen om regels voor levenscyclusbeheer te maken om uw gegevens automatisch te vergelijken met Cool of Archiveren om uw opslagkosten te optimaliseren terwijl u uw gegevens in Azure blob-opslag behoudt voor toepassingscompatibiliteit.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Kostenaanbevelingen openen in Azure Advisor

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Zoek naar en selecteer [**Advisor**](https://aka.ms/azureadvisordashboard) op elke pagina.

1. Selecteer op het **dashboard Van Advisor** het tabblad **Kosten.**

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over aanbevelingen van Advisor:
* [Inleiding tot Adviseur](advisor-overview.md)
* [Slag](advisor-get-started.md)
* [Aanbevelingen voor prestaties van adviseur](advisor-performance-recommendations.md)
* [Aanbevelingen voor hoge beschikbaarheid](advisor-high-availability-recommendations.md)
* [Aanbevelingen voor beveiliging van adviseur](advisor-security-recommendations.md)
* [Aanbevelingen adviseur Operational Excellence](advisor-operational-excellence-recommendations.md)
