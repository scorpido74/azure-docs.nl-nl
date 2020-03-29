---
title: Een verhoging van de azure regionale vCPU-quotalimieten aanvragen
description: Een verhoging van de vCPU-quotumlimiet aanvragen voor een regio in de Azure-portal.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9236989f7bbb4695db24f86a2a049f5bb58f302a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843681"
---
# <a name="standard-quota-increase-limits-by-region"></a>Standaardquotum: Limieten per regio verhogen

Azure Resource Manager ondersteunt twee soorten vCPU-quota voor virtuele machines:

* *Pay-as-you-go VM's* en *gereserveerde VM-exemplaren* zijn onderworpen aan een *standaard vCPU-quotum.*
* *Voor spotVM's* geldt een *vCPU-quotum*voor spot .

Het standaard vCPU-quotum voor betalen per gebruik en gereserveerde virtuele machine-exemplaren wordt afgedwongen op twee lagen voor elk abonnement in elke regio:

* De eerste laag is de *totale regionale vCPU's limiet,* voor alle VM-series.
* De tweede laag is de *vCPU's limiet voor vCPU's per VM-reeks,* zoals de VCPU's uit de D-reeksen.

Wanneer u een nieuwe spotVM implementeert, mag het totale nieuwe en bestaande vCPU-gebruik voor die VM-serie het goedgekeurde vCPU-quotum voor die specifieke VM-reeks niet overschrijden. Bovendien mag het totale aantal nieuwe en bestaande vCPU's dat in alle VM-reeksen wordt geïmplementeerd, niet hoger zijn dan het totale goedgekeurde regionale vCPU-quotum voor het abonnement. Als een van deze quota wordt overschreden, is de VM-implementatie niet toegestaan.

U een verhoging van de vCPU-quotumlimiet voor de VM-serie aanvragen met behulp van de Azure-portal. Een verhoging van het VM-seriequotum verhoogt automatisch de totale regionale vCPU-limiet met hetzelfde bedrag.

Wanneer u een nieuw abonnement maakt, is het standaard totaalaantal regionale vCPU's mogelijk niet gelijk aan het totale standaard vCPU-quotum voor alle afzonderlijke VM-reeksen. Deze discrepantie kan resulteren in een abonnement met voldoende quota voor elke afzonderlijke VM-serie die u wilt implementeren. Maar er is misschien niet genoeg quotum om de totale regionale vCPU's voor alle implementaties tegemoet te komen. In dit geval moet u een verzoek indienen om de limiet van het totale aantal regionale vCPU's expliciet te verhogen. De totale regionale vCPU-limiet mag niet hoger zijn dan het totale goedgekeurde quotum voor alle VM-reeksen voor de regio.

Zie [VCPU-quota voor virtuele machines](../../virtual-machines/windows/quotas.md) en [Azure-abonnements- en servicelimieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over standaard vCPU-quota.

Zie [Spotquota: Limieten voor alle VM-series verhogen voor](low-priority-quota.md)meer informatie over het verhogen van vCPU-limieten voor spotvm's.

U op twee manieren een verhoging van uw vCPU-standaardquotumlimiet per regio aanvragen.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>Een quotumverhoging per regio aanvragen bij Help + ondersteuning

Een vCPU-quotumverhoging per regio aanvragen bij **Help + ondersteuning:**

1. Selecteer **Help + ondersteuning**in het menu Azure [portal.](https://portal.azure.com)

   ![De link 'Help + ondersteuning'](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Selecteer in **Help + ondersteuning**de optie Nieuw **ondersteuningsverzoek**.

    ![Nieuw ondersteuningsverzoek](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Selecteer **Service- en abonnementslimieten (quota)** **voor issuetype.**

   ![Een probleemtype selecteren](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Selecteer **bij Abonnement**het abonnement waarvan u het quotum wilt verhogen.

   ![Een abonnement selecteren](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Selecteer **Voor**het type Quotum de optie **Andere aanvragen**.

   ![Een quotumtype selecteren](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Selecteer **Volgende: Oplossingen** om PROBLEEMGEGEVENS te **openen.** Geef in **Beschrijving**de volgende gegevens:

    1. Geef **Resourcebeheer**op voor **implementatiemodel**.  
    1. Geef **voor Regio**uw vereiste regio op, bijvoorbeeld Oost US **2**.  
    1. Geef voor **Nieuwe limiet**een nieuwe vCPU-limiet voor de regio op. Deze waarde mag niet hoger zijn dan de som van de goedgekeurde quota voor afzonderlijke SKU-reeksen voor dit abonnement.

    ![Details voor de quotumaanvraag invoeren](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selecteer **Controleren + maken** om door te gaan met het maken van de ondersteuningsaanvraag.

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>Een quotumverhoging per regio aanvragen bij abonnementen

Een vCPU-quotumverhoging per regio aanvragen bij **Abonnementen:**

1. Zoek in de [Azure-portal](https://portal.azure.com)naar en selecteer **Abonnementen**.

   ![Naar Abonnementen in de Azure-portal gaan](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selecteer het abonnement waarvan u het quotum wilt verhogen.

   ![Selecteer een abonnement dat u wilt wijzigen](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Selecteer gebruik + **quota**in het linkerdeelvenster .

   ![Koppeling Gebruik en quota volgen](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Selecteer Rechtsboven **Aanvraag verhogen**.

   ![Selecteren om het quotum te verhogen](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Selecteer **andere aanvragen**in **quotatype**.

   ![Het quotumtype selecteren](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Selecteer **Volgende: Oplossingen** om PROBLEEMGEGEVENS te **openen.** Geef **in het** vak Beschrijving de volgende aanvullende informatie op:

    1. Geef **Resourcebeheer**op voor **implementatiemodel**.  
    1. Geef **voor Regio**uw vereiste regio op, bijvoorbeeld Oost US **2**.  
    1. Geef voor **Nieuwe limiet**een nieuwe vCPU-limiet voor de regio op. Deze waarde mag niet hoger zijn dan de som van de goedgekeurde quota voor afzonderlijke SKU-reeksen voor dit abonnement.

    ![Gegevens invoeren in details](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selecteer **Controleren + maken** om door te gaan met het maken van de ondersteuningsaanvraag.
