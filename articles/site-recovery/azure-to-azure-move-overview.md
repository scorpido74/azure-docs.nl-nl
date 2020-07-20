---
title: Azure-VM’s verplaatsen naar een andere regio met behulp van Azure Site Recovery
description: Azure Site Recovery gebruiken om Azure-VM’s te verplaatsen van de ene Azure-regio naar een andere.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 0c7efc94bcde18e7b6ff43726602fa87641f3e76
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86130616"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>Azure-VM’s verplaatsen naar een andere Azure-regio

Dit artikel biedt een overzicht van de redenen en stappen voor het verplaatsen van Azure VM’s naar een andere Azure-regio met behulp van [Azure Site Recovery](site-recovery-overview.md). 


## <a name="reasons-to-move-azure-vms"></a>Redenen om Azure-VM’s te verplaatsen

U verplaatst VM’s wellicht om de volgende redenen:

- U hebt al in één regio geïmplementeerd en er wordt een nieuwe ondersteunde regio toegevoegd die zich dichter bij de eindgebruikers van uw toepassing of service bevindt. In dit scenario wilt u uw VM’s ongewijzigd naar de nieuwe regio verplaatsen om latentie te verminderen. Gebruik diezelfde aanpak als u abonnementen wilt samenvoegen of als er bestuurlijke of organisatieregels zijn op basis waarvan u verplicht bent te verplaatsen.
- Uw VM is geïmplementeerd als een VM met één exemplaar of als onderdeel van een beschikbaarheidsset. Als u de beschikbaarheids-SLA’s wilt vergroten, kunt u uw VM’s verplaatsen naar een beschikbaarheidszone.

## <a name="steps-to-move-azure-vms"></a>Stappen om Azure-VM’s te verplaatsen

Het verplaatsen van virtuele machines omvat de volgende stappen:

1. Controleer de vereisten.
2. Bereid de bron-VM’s voor.
3. Bereid de doelregio voor.
4. Kopieer gegevens naar de doelregio. Maak gebruik van de replicatietechnologie van Azure Site Recovery om gegevens van de bron-VM naar de doelregio te kopiëren.
5. Test de configuratie. Nadat de replicatie is voltooid, test u de configuratie door een testfailover via een niet-productie-netwerk uit te voeren.
6. Voer de verplaatsing uit.
7. Verwijder de resources in de bronregio.

> [!NOTE]
> Meer details over deze stappen kunt u vinden in de volgende secties.
> [!IMPORTANT]
> Momenteel ondersteunt Azure Site Recovery de verplaatsing van VM’s van de ene regio naar een andere regio, maar biedt het geen ondersteuning voor het verplaatsen binnen een regio.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Gangbare architecturen voor een implementatie met meerdere lagen

In deze sectie worden de meest voorkomende implementatie-architecturen beschreven voor een toepassing met meerdere lagen in Azure. Het voorbeeld is een toepassing met drie lagen met een openbaar IP-adres. Elk van de lagen (web, toepassing en database) bevat elk twee VM's en is via een Azure-loadbalancer met de andere lagen verbonden. De databaselaag heeft SQL Server Always On-replicatie tussen de VM's voor hoge beschikbaarheid.

* **VM’s met één exemplaar die zijn geïmplementeerd in verschillende lagen:** Elke VM in een laag is geconfigureerd als een VM met één exemplaar, en is via load balancers met de andere lagen verbonden. Deze configuratie is de eenvoudigste om te gebruiken.

     ![Implementatie van VM’s met één exemplaar in verschillende lagen](media/move-vm-overview/regular-deployment.png)

* **VM’s in elke laag die zijn geïmplementeerd in beschikbaarheidssets**: Elke VM in een laag wordt geconfigureerd in een beschikbaarheidsset. [Beschikbaarheidssets](../virtual-machines/windows/tutorial-availability-sets.md) zorgen ervoor dat de VM's die u in Azure implementeert over meerdere geïsoleerde hardwareknooppunten in een cluster worden verdeeld. Dit zorgt ervoor dat als er zich binnen Azure een hardware- of softwarestoring voordoet, er slechts een subset van uw VM's wordt beïnvloed en uw totale oplossing beschikbaar en operationeel blijft.

     ![Implementatie van VM’s in beschikbaarheidssets](media/move-vm-overview/avset.png)

* **VM’s in elke laag die zijn geïmplementeerd in beschikbaarheidszones**: Elke VM in een laag wordt geconfigureerd in [beschikbaarheidszones](../availability-zones/az-overview.md). Een beschikbaarheidszone in een Azure-regio is een combinatie van een foutdomein en een updatedomein. Als u bijvoorbeeld drie of meer virtuele machines in drie zones in een Azure-regio maakt, worden uw virtuele machines effectief over drie foutdomeinen en drie updatedomeinen verdeeld. Het Azure-platform herkent deze verdeling over updatedomeinen om ervoor te zorgen dat virtuele machines in verschillende zones niet op hetzelfde moment worden bijgewerkt.

     ![Implementatie in beschikbaarheidszones](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Virtuele machines ongewijzigd naar een doelregio verplaatsen

Op basis van de eerder genoemde [architecturen](#typical-architectures-for-a-multi-tier-deployment) ziet u hier hoe de implementaties eruitzien nadat u de VM’s ongewijzigd naar de doelregio hebt verplaatst.

* **VM’s met één exemplaar die zijn geïmplementeerd in verschillende lagen**

     ![Implementatie van VM’s met één exemplaar in verschillende lagen](media/move-vm-overview/single-zone.png)

* **VM’s in elke laag die zijn geïmplementeerd in beschikbaarheidssets**

     ![Beschikbaarheidssets voor meerdere regio’s](media/move-vm-overview/crossregionaset.png)

* **VM’s in elke laag die zijn geïmplementeerd in beschikbaarheidszones**

     ![Implementatie van VM’s in beschikbaarheidszones](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>Virtuele machines verplaatsen om beschikbaarheid te vergroten

* **VM’s met één exemplaar die zijn geïmplementeerd in verschillende lagen**

     ![Implementatie van VM’s met één exemplaar in verschillende lagen](media/move-vm-overview/single-zone.png)

* **VM’s in elke laag die zijn geïmplementeerd in beschikbaarheidssets**: U kunt uw VM’s in een beschikbaarheidsset configureren in afzonderlijke beschikbaarheidszones wanneer u replicatie inschakelt voor uw VM met behulp van Azure Site Recovery. Nadat u de verplaatsing hebt voltooid, is de SLA voor beschikbaarheid 99,99%.

     ![Implementatie van VM’s in beschikbaarheidssets en beschikbaarheidszones](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> 
> * [Virtuele Azure-machines verplaatsen naar een andere regio](azure-to-azure-tutorial-migrate.md)
> 
> * [Virtuele Azure-machines verplaatsen naar beschikbaarheidszones](move-azure-vms-avset-azone.md)

