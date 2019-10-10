---
title: Azure Regional quota verhogen aanvragen | Microsoft Docs
description: Verzoeken om regionale quota verhogen
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e73f22b0e617ad8f20b98c3bb0fb1647bf5fe61d
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249080"
---
# <a name="total-regional-vcpu-limit-increase"></a>De algehele regionale vCPU-limiet verhogen 

Resource Manager-vCPU quota voor virtuele machines en virtuele-machine schaal sets worden afgedwongen op twee lagen voor elk abonnement, in elke regio. 

De eerste laag is de **totale regionale vcpu's limiet** (in alle VM-reeksen) en de tweede laag is de **limiet van de VM-reeks vcpu's** (zoals de vcpu's van de D-serie). Telkens wanneer een nieuwe VM moet worden geïmplementeerd, mag de som van het nieuwe en bestaande Vcpu's-gebruik voor die VM-reeks niet groter zijn dan het vCPU-quotum dat is goedgekeurd voor die bepaalde VM-reeks. Verder mag het totale nieuwe en bestaande vCPU aantal dat is geïmplementeerd in alle VM-reeksen, niet groter zijn dan het totale regionale Vcpu's-quotum dat is goedgekeurd voor het abonnement. Als een van deze quota wordt overschreden, is de implementatie van de VM niet toegestaan.
U kunt een toename van de Vcpu's-quotum limiet voor de VM-serie aanvragen van Azure Portal. Een toename in het quotum van de VM-reeks verhoogt automatisch de totale regionale Vcpu's limiet met hetzelfde bedrag. 

Wanneer een nieuw abonnement wordt gemaakt, is de standaard regionale Vcpu's mogelijk niet gelijk aan de som van de standaard vCPU-quota voor alle afzonderlijke VM-reeksen. Dit kan resulteren in een abonnement met voldoende quota voor elke afzonderlijke VM-reeks die u wilt implementeren, maar niet genoeg quotum voor het totale regionale Vcpu's voor alle implementaties. In dit geval moet u een aanvraag indienen om de limiet voor de totale regionale Vcpu's expliciet te verhogen. De limiet voor het totale regionale Vcpu's kan niet groter zijn dan de som van goedgekeurde quota voor alle VM-reeksen voor de regio.

Meer informatie over quota's vindt u op de pagina [vCPU quota van de virtuele machine](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) en de pagina [Azure-abonnement en service limieten](https://aka.ms/quotalimits) . 

U kunt nu een toename aanvragen via de Blade **Help en ondersteuning** of de Blade **gebruik + quotum** in de portal. 

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Het totale regionale Vcpu's quotum verhoging op abonnements niveau aanvragen met behulp van de Blade **Help en ondersteuning**

Volg de onderstaande instructies om een ondersteunings aanvraag te maken via de Blade Help en ondersteuning van Azure die beschikbaar zijn in de Azure Portal. 

1. Selecteer in https://portal.azure.com **Help + ondersteuning**.

![Help en ondersteuning](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selecteer **Nieuwe ondersteuningsaanvraag**. 

![Nieuw ondersteuningsverzoek](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Kies in de vervolg keuzelijst probleem type de optie **service-en abonnements limieten (quota's)** .

![Vervolg keuzelijst probleem type](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selecteer het abonnement waarvoor het quotum moet worden verhoogd.

![Abonnement-Nieuwsset selecteren](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selecteer **andere aanvragen** in vervolg keuzelijst **quotum type** .

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. Geef in het deel venster **Details** in het onderstaande voor beeld aanvullende informatie op om uw aanvraag te verwerken en door te gaan met het maken van de aanvraag. 
    1.  **Implementatie model** : Geef ' Resource Manager ' op
    2.  **Aangevraagde regio** : Geef uw vereiste regio op, bijvoorbeeld VS-Oost 2
    3.  **Nieuwe limiet waarde** : Geef een limiet op voor de nieuwe regio. Dit mag niet hoger zijn dan de som van de goedgekeurde quota voor afzonderlijke SKU-families voor dit abonnement

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>Het totale regionale Vcpu's quotum verhoging op abonnements niveau aanvragen met de Blade **gebruik + quotum**

Volg de onderstaande instructies voor het maken van een ondersteunings aanvraag via de Blade gebruik en quotum van Azure die beschikbaar is in de Azure Portal. 

1. Selecteer in https://portal.azure.com **abonnementen**.

![Abonnementen](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selecteer het abonnement waarvoor het quotum moet worden verhoogd.

![Abonnement selecteren](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **Gebruik en quota's** selecteren

![Gebruik en quota's selecteren](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. Selecteer in de rechter bovenhoek **aanvraag verhoging**.

![Toename aanvragen](./media/resource-manager-core-quotas-request/request-increase.png)

5. Selecteer **andere aanvragen** in vervolg keuzelijst **quotum type** .

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. Geef in het deel venster **Details** in het onderstaande voor beeld aanvullende informatie op om uw aanvraag te verwerken en door te gaan met het maken van de aanvraag. 
    1.  **Implementatie model** : Geef ' Resource Manager ' op
    2.  **Aangevraagde regio** : Geef uw vereiste regio op, bijvoorbeeld VS-Oost 2
    3.  **Nieuwe limiet waarde** : Geef een limiet op voor de nieuwe regio. Dit mag niet hoger zijn dan de som van de goedgekeurde quota voor afzonderlijke SKU-families voor dit abonnement

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)



