---
title: Azure VM's verplaatsen naar een andere regio met Azure Site Recovery
description: Azure Site Recovery gebruiken om Azure VM's van de ene Azure-regio naar de andere te verplaatsen.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 3f715af835df6783ae5d59dd073a042a553fba4d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75498042"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>Azure VM's verplaatsen naar een andere Azure-regio

In dit artikel vindt u een overzicht van de redenen en stappen die nodig zijn om Azure VM's naar een andere Azure-regio te verplaatsen met [Azure Site Recovery](site-recovery-overview.md). 


## <a name="reasons-to-move-azure-vms"></a>Redenen om Azure VM's te verplaatsen

U VM's verplaatsen om de volgende redenen:

- U bent al geïmplementeerd in één regio en er is een nieuwe regioondersteuning toegevoegd die dichter bij de eindgebruikers van uw toepassing of service staat. In dit scenario wilt u uw VM's verplaatsen naar de nieuwe regio om de latentie te verminderen. Gebruik dezelfde aanpak als u abonnementen wilt consolideren of als er governance- of organisatieregels zijn die vereisen dat u verplaatst.
- Uw VM is geïmplementeerd als vm met één instantie of als onderdeel van een beschikbaarheidsset. Als u de beschikbaarheids-SLA's wilt verhogen, u uw VM's verplaatsen naar een beschikbaarheidszone.

## <a name="steps-to-move-azure-vms"></a>Stappen om Azure VM's te verplaatsen

Het verplaatsen van virtuele machines omvat de volgende stappen:

1. Controleer de vereisten.
2. Bereid de bron-VM's voor.
3. Bereid de doelregio voor.
4. Gegevens kopiëren naar het doelgebied. Gebruik azure site recovery-replicatietechnologie om gegevens van de bron-VM naar het doelgebied te kopiëren.
5. Test de configuratie. Nadat de replicatie is voltooid, test u de configuratie door een testfailover uit te voeren naar een niet-productienetwerk.
6. Voer de beweging uit.
7. Verwijder de resources in het brongebied.

> [!NOTE]
> Details over deze stappen vindt u in de volgende secties.
> [!IMPORTANT]
> Azure Site Recovery ondersteunt momenteel het verplaatsen van VM's van de ene regio naar de andere, maar biedt geen ondersteuning voor verplaatsing binnen een regio.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Gangbare architecturen voor een implementatie met meerdere lagen

In deze sectie worden de meest voorkomende implementatiearchitecturen beschreven voor een toepassing met meerdere lagen in Azure. Het voorbeeld is een toepassing met drie niveaus met een openbaar IP-adres. Elk van de lagen (web, toepassing en database) heeft elk twee VM's en ze zijn verbonden door een Azure-load balancer met de andere lagen. De databaselaag heeft SQL Server Always On-replicatie tussen de VM's voor hoge beschikbaarheid.

* **VM's met**één instantie die in verschillende lagen zijn geïmplementeerd: elke VM in een laag is geconfigureerd als vm met één instantie en is door load balancers verbonden met de andere lagen. Deze configuratie is de eenvoudigste aan te nemen.

     ![VM-implementatie met één exemplaar voor verschillende lagen](media/move-vm-overview/regular-deployment.png)

* **VM's in elke laag die is geïmplementeerd in beschikbaarheidssets:** elke vm in een laag is geconfigureerd in een beschikbaarheidsset. [Beschikbaarheidssets](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) zorgen ervoor dat de VM's die u in Azure implementeert over meerdere geïsoleerde hardwareknooppunten in een cluster worden verdeeld. Dit zorgt ervoor dat als er een hardware- of softwarefout binnen Azure optreedt, alleen een subset van uw VM's wordt beïnvloed en uw algehele oplossing beschikbaar en operationeel blijft.

     ![VM-implementatie voor beschikbaarheidssets](media/move-vm-overview/avset.png)

* **VM's in elke laag die wordt geïmplementeerd in beschikbaarheidszones:** elke vm in een laag is geconfigureerd voor [beschikbaarheidszones.](https://docs.microsoft.com/azure/availability-zones/az-overview) Een beschikbaarheidszone in een Azure-regio is een combinatie van een foutdomein en een updatedomein. Als u bijvoorbeeld drie of meer virtuele machines in drie zones in een Azure-regio maakt, worden uw virtuele machines effectief over drie foutdomeinen en drie updatedomeinen verdeeld. Het Azure-platform herkent deze verdeling over updatedomeinen om ervoor te zorgen dat virtuele machines in verschillende zones niet op hetzelfde moment worden bijgewerkt.

     ![Implementatie van beschikbaarheidszone](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Virtuele machines ongewijzigd naar een doelregio verplaatsen

Op basis van de eerder genoemde [architecturen](#typical-architectures-for-a-multi-tier-deployment) ziet de implementatie eruit nadat u de verplaatsing uitvoert, zoals in de doelregio.

* **VM's met één instantie die in verschillende lagen worden geïmplementeerd**

     ![VM-implementatie met één exemplaar voor verschillende lagen](media/move-vm-overview/single-zone.png)

* **VM's in elke laag die is geïmplementeerd in beschikbaarheidssets**

     ![Beschikbaarheidssets voor verschillende regio's](media/move-vm-overview/crossregionaset.png)

* **VM's in elke laag die in beschikbaarheidszones wordt geïmplementeerd**

     ![VM-implementatie in beschikbaarheidszones](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>Virtuele machines verplaatsen om beschikbaarheid te vergroten

* **VM's met één instantie die in verschillende lagen worden geïmplementeerd**

     ![VM-implementatie met één exemplaar voor verschillende lagen](media/move-vm-overview/single-zone.png)

* **VM's in elke laag die is geïmplementeerd in beschikbaarheidssets:** u uw VM's configureren in een beschikbaarheidsset in afzonderlijke beschikbaarheidszones wanneer u replicatie voor uw vm inschakelt met Azure Site Recovery. De SLA voor beschikbaarheid bedraagt 99,99% nadat u de verplaatsingsbewerking hebt voltooid.

     ![VM-implementatie voor beschikbaarheidssets en beschikbaarheidszones](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> 
> * [Virtuele Azure-machines verplaatsen naar een andere regio](azure-to-azure-tutorial-migrate.md)
> 
> * [Virtuele Azure-machines verplaatsen naar beschikbaarheidszones](move-azure-vms-avset-azone.md)

