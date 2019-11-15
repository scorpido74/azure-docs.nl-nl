---
title: Virtuele Azure-machines verplaatsen naar een andere regio met Azure Site Recovery
description: Azure Site Recovery gebruiken om virtuele Azure-machines te verplaatsen van de ene Azure-regio naar een andere.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 05d0fcb9e689b8954bffaff402475762344c023d
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091940"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>Virtuele Azure-machines verplaatsen naar een andere Azure-regio

In dit artikel vindt u een overzicht van de redenen en stappen voor het verplaatsen van virtuele Azure-machines naar een andere Azure-regio met behulp van [Azure site Recovery](site-recovery-overview.md). 


## <a name="reasons-to-move-azure-vms"></a>Redenen voor het verplaatsen van virtuele Azure-machines

U kunt Vm's om de volgende redenen verplaatsen:

- U hebt al geïmplementeerd in één regio en er is een nieuwe regio ondersteuning toegevoegd die dichter bij de eind gebruikers van uw toepassing of service valt. In dit scenario wilt u uw virtuele machines verplaatsen naar de nieuwe regio om de latentie te verminderen. Gebruik dezelfde methode als u abonnementen wilt consolideren of als er governance-of organisatie regels zijn die u moet verplaatsen.
- Uw virtuele machine is geïmplementeerd als een virtuele machine met één exemplaar of als onderdeel van een beschikbaarheidsset. Als u de beschikbaarheids periode wilt verhogen, kunt u uw Vm's verplaatsen naar een beschikbaarheids zone.

## <a name="steps-to-move-azure-vms"></a>Stappen voor het verplaatsen van virtuele Azure-machines

Het verplaatsen van virtuele machines omvat de volgende stappen:

1. Controleer de vereisten.
2. Bereid de bron-Vm's voor.
3. Bereid de doel regio voor.
4. Gegevens kopiëren naar de doel regio. Gebruik Azure Site Recovery-replicatie technologie om gegevens te kopiëren van de bron-VM naar de doel regio.
5. De configuratie testen. Nadat de replicatie is voltooid, test u de configuratie door een testfailover uit te voeren op een niet-productie netwerk.
6. Voer de verplaatsing uit.
7. Verwijder de resources in de bron regio.

> [!NOTE]
> Meer informatie over deze stappen vindt u in de volgende secties.
> [!IMPORTANT]
> Momenteel biedt Azure Site Recovery ondersteuning voor het verplaatsen van Vm's van de ene regio naar een andere, maar biedt geen ondersteuning voor verplaatsen binnen een regio.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Gangbare architecturen voor een implementatie met meerdere lagen

In deze sectie worden de meest voorkomende implementatie architecturen voor een toepassing met meerdere lagen in azure beschreven. Het voor beeld is een toepassing met drie lagen met een openbaar IP-adres. Elk van de lagen (Web, toepassing en data base) heeft twee virtuele machines en ze zijn verbonden door een Azure-load balancer naar de andere lagen. De database laag heeft SQL Server altijd op replicatie tussen de Vm's voor hoge Beschik baarheid.

* **Vm's met één exemplaar die zijn geïmplementeerd in verschillende lagen**: elke VM in een laag wordt geconfigureerd als een virtuele machine met één exemplaar en is verbonden door load balancers naar de andere lagen. Deze configuratie is het eenvoudigst te nemen.

     ![VM-implementatie met één exemplaar in meerdere lagen](media/move-vm-overview/regular-deployment.png)

* **Vm's in elke laag die worden geïmplementeerd in verschillende beschikbaarheids sets**: elke VM in een laag wordt geconfigureerd in een beschikbaarheidsset. [Beschikbaarheidssets](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) zorgen ervoor dat de VM's die u in Azure implementeert over meerdere geïsoleerde hardwareknooppunten in een cluster worden verdeeld. Dit zorgt ervoor dat als er een hardware-of software fout optreedt in azure, alleen een subset van uw Vm's wordt beïnvloed en uw algemene oplossing beschikbaar en operationeel blijft.

     ![VM-implementatie in verschillende beschikbaarheids sets](media/move-vm-overview/avset.png)

* **Virtuele machines in elke laag die over Beschikbaarheidszones zijn geïmplementeerd**: elke VM in een laag wordt geconfigureerd op [Beschikbaarheidszones](https://docs.microsoft.com/azure/availability-zones/az-overview). Een beschikbaarheidszone in een Azure-regio is een combinatie van een foutdomein en een updatedomein. Als u bijvoorbeeld drie of meer virtuele machines in drie zones in een Azure-regio maakt, worden uw virtuele machines effectief over drie foutdomeinen en drie updatedomeinen verdeeld. Het Azure-platform herkent deze verdeling over updatedomeinen om ervoor te zorgen dat virtuele machines in verschillende zones niet op hetzelfde moment worden bijgewerkt.

     ![Implementatie van beschikbaarheids zone](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Virtuele machines ongewijzigd naar een doelregio verplaatsen

Op basis van de eerder genoemde [architecturen](#typical-architectures-for-a-multi-tier-deployment) ziet u hoe de implementaties eruitzien nadat u de verplaatsing naar de doel regio hebt uitgevoerd.

* **Vm's met één exemplaar die worden geïmplementeerd in verschillende lagen**

     ![VM-implementatie met één exemplaar in meerdere lagen](media/move-vm-overview/single-zone.png)

* **Vm's in elke laag die worden geïmplementeerd in verschillende beschikbaarheids sets**

     ![Beschikbaarheids sets voor meerdere regio's](media/move-vm-overview/crossregionaset.png)

* **Vm's in elke laag die over Beschikbaarheidszones zijn geïmplementeerd**

     ![VM-implementatie over Beschikbaarheidszones](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>Virtuele machines verplaatsen om beschikbaarheid te vergroten

* **Vm's met één exemplaar die worden geïmplementeerd in verschillende lagen**

     ![VM-implementatie met één exemplaar in meerdere lagen](media/move-vm-overview/single-zone.png)

* **Vm's in elke laag die zijn geïmplementeerd in verschillende beschikbaarheids sets**: u kunt uw vm's in een beschikbaarheidsset configureren in afzonderlijke Beschikbaarheidszones wanneer u replicatie voor uw virtuele machine inschakelt met behulp van Azure site Recovery. De SLA voor Beschik baarheid is 99,9% nadat u de verplaatsings bewerking hebt voltooid.

     ![VM-implementatie in verschillende beschikbaarheids sets en Beschikbaarheidszones](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> 
> * [Virtuele Azure-machines verplaatsen naar een andere regio](azure-to-azure-tutorial-migrate.md)
> 
> * [Virtuele Azure-machines verplaatsen naar beschikbaarheidszones](move-azure-vms-avset-azone.md)

