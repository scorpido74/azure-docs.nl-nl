---
title: Een toename van Azure Regional vCPU-quotum limieten aanvragen | Microsoft Docs
description: Verzoeken om regionale quota verhogen
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e262651a6e040c40dbe240ad3437eff1914aa3e5
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896738"
---
# <a name="standard-quota-increase-limits-by-region"></a>Standaard quotum: limieten per regio verhogen 

Azure Resource Manager ondersteunt twee typen vCPU-quota's voor virtuele machines:
* Voor *vm's met betalen per gebruik* en *gereserveerde VM-instanties* gelden een *standaard vCPU-quotum*.
* Voor de *Spot-vm's* geldt een *vCPU-quotum*. 

Het standaard vCPU-quotum voor betalen per gebruik en gereserveerde VM-instanties wordt afgedwongen op twee lagen voor elk abonnement in elke regio:
* De eerste laag is de *totale regionale vcpu's limiet* (in alle VM-reeksen).
* De tweede laag is de *limiet voor de serie vcpu's per VM* (zoals de vcpu's van de D-serie).
 
Wanneer u een nieuwe spot-VM implementeert, mag het totale nieuwe en bestaande vCPU-gebruik voor die VM-reeks niet groter zijn dan het goedgekeurde vCPU-quotum voor die bepaalde VM-reeks. Daarnaast mag het totale aantal nieuwe en bestaande Vcpu's dat in alle VM-reeksen worden geïmplementeerd, niet groter zijn dan het totale goedgekeurde regionale vCPU-quotum voor het abonnement. Als een van deze quota wordt overschreden, is de implementatie van de VM niet toegestaan. 

U kunt een verhoging van de vCPU-quotum limiet voor de VM-serie aanvragen met behulp van de Azure Portal. Een toename in het quotum van de VM-reeks verhoogt automatisch de totale regionale vCPU limiet met hetzelfde bedrag.

Wanneer u een nieuw abonnement maakt, is het standaard totaal aantal regionale Vcpu'ss mogelijk niet gelijk aan het totale standaard quotum voor vCPU voor alle afzonderlijke VM-reeksen. Dit verschil kan resulteren in een abonnement met voldoende quota voor elke afzonderlijke VM-reeks die u wilt implementeren. Het is echter mogelijk dat er onvoldoende quota zijn voor de totale regionale Vcpu's van alle implementaties. In dit geval moet u een aanvraag indienen om de limiet van het totale aantal regionale Vcpu's expliciet te verhogen. De limiet voor het totale regionale vCPU kan niet groter zijn dan het totale goedgekeurde quotum voor alle VM-reeksen voor de regio.

Zie vCPU voor [virtuele machines](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) en Azure-abonnementen en- [service limieten](https://aka.ms/quotalimits)voor meer informatie over standaard-quota quota's.

Zie [Spot quota: limieten voor alle VM-reeksen verhogen voor](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)meer informatie over het verhogen van de vCPU limieten voor de VM.

U kunt op twee manieren een verhoging van uw vCPU-standaard quotum limiet per regio aanvragen, zoals beschreven in de volgende secties.

## <a name="request-a-quota-increase-by-region-from-the-help--support-pane"></a>Een quotum toename per regio aanvragen in het deel venster "Help + ondersteuning"

Ga als volgt te werk als u een vCPU-quotum toename per regio wilt aanvragen in het deel venster **Help en ondersteuning** : 

1. Selecteer **Help en ondersteuning**in het linkerdeel venster van de [Azure Portal](https://portal.azure.com).

   ![De koppeling Help + ondersteuning](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. Selecteer in het deel venster **Help + ondersteuning** de optie **nieuwe ondersteunings aanvraag**. 

    ![Nieuw ondersteuningsverzoek](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. Selecteer in de vervolg keuzelijst **probleem type** de optie **service-en abonnements limieten (quota's)** .

   ![De vervolg keuzelijst probleem type](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. Selecteer in de vervolg keuzelijst **abonnement** het abonnement waarvan u het quotum wilt verhogen.

   ![De vervolg keuzelijst abonnement](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. In de vervolg keuzelijst **quotum type** selecteert u **andere aanvragen**.

   ![De vervolg keuzelijst quotum type](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Geef in het deel venster **Details van probleem** in het vak **Beschrijving** de volgende aanvullende informatie op: 

    a. Geef voor **implementatie model** **Resource Manager**op.  
    b. Geef voor **regio**uw vereiste regio op (bijvoorbeeld VS- **Oost 2**).  
    c. Geef voor **nieuwe limiet**een nieuwe vCPU-limiet op voor de regio. Deze waarde mag niet groter zijn dan de som van de goedgekeurde quota's voor de afzonderlijke SKU-serie voor dit abonnement.

    ![Het deel venster probleem Details](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selecteer **opslaan en door gaan** om door te gaan met het maken van het ondersteunings verzoek.

## <a name="request-a-quota-increase-by-region-from-the-subscriptions-pane"></a>Een quotum toename per regio aanvragen in het deel venster abonnementen

Ga als volgt te werk om een vCPU-quotum toename per regio aan te vragen vanuit het deel venster **abonnementen** : 

1. Selecteer in het linkerdeel venster van de [Azure Portal](https://portal.azure.com) **abonnementen**.

   ![De koppeling abonnementen](./media/resource-manager-core-quotas-request/subscriptions.png)

1. Selecteer het abonnement waarvan u het quotum wilt verhogen.

   ![Het deel venster abonnementen](./media/resource-manager-core-quotas-request/select-subscription.png)

1. Selecteer in het linkerdeel venster van de naam van uw **\<-abonnement >** pagina de optie **gebruik en quota's**.

   ![De koppeling gebruik en quota's](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. Selecteer in de rechter bovenhoek de optie **aanvraag verg Roten**.

   ![Toename aanvragen](./media/resource-manager-core-quotas-request/request-increase.png)

1. In de vervolg keuzelijst **quotum type** selecteert u **andere aanvragen**.

   ![De vervolg keuzelijst quotum type](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Geef in het deel venster **Details van probleem** in het vak **Beschrijving** de volgende aanvullende informatie op: 

    a. Geef voor **implementatie model** **Resource Manager**op.  
    b. Geef voor **regio**uw vereiste regio op (bijvoorbeeld VS- **Oost 2**).  
    c. Geef voor **nieuwe limiet**een nieuwe vCPU-limiet op voor de regio. Deze waarde mag niet groter zijn dan de som van de goedgekeurde quota's voor de afzonderlijke SKU-serie voor dit abonnement.

    ![Het deel venster probleem Details](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selecteer **opslaan en door gaan** om door te gaan met het maken van het ondersteunings verzoek.

