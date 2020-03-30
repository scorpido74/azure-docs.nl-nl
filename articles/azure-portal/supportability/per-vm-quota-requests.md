---
title: Een verhoging van de vCPU-quotalimieten per Azure VM-reeks aanvragen
description: Hoe u een verhoging van de vCPU-quotumlimiet voor een VM-reeks in de Azure-portal aanvragen, waardoor de totale regionale vCPU-limiet met hetzelfde bedrag wordt verhoogd.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: de06375dad5999a29691435317e62585a2ea7f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843714"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Standaardquotum: limieten per VM-serie verhogen

Azure Resource Manager ondersteunt twee soorten vCPU-quota voor virtuele machines:

* *Pay-as-you-go VM's* en *gereserveerde VM-exemplaren* zijn onderworpen aan een *standaard vCPU-quotum.*
* *Voor spotVM's* geldt een *vCPU-quotum*voor spot .

Het standaard vCPU-quotum voor betalen per gebruik en gereserveerde virtuele machine-exemplaren wordt afgedwongen op twee lagen voor elk abonnement in elke regio:

* De eerste laag is de *totale regionale vCPU's limiet,* voor alle VM-series.
* De tweede laag is de *vCPU's limiet voor de VCPU's in de VCPU's*uit de Dv3-serie.

Wanneer u een nieuwe spotVM implementeert, mag het totale nieuwe en bestaande vCPU-gebruik voor die VM-serie het goedgekeurde vCPU-quotum voor die specifieke VM-reeks niet overschrijden. Bovendien mag het totale aantal nieuwe en bestaande vCPU's dat in alle VM-reeksen wordt geïmplementeerd, niet hoger zijn dan het totale goedgekeurde regionale vCPU-quotum voor het abonnement. Als een van deze quota wordt overschreden, is de VM-implementatie niet toegestaan.

U een verhoging van de vCPU-quotumlimiet voor de VM-serie aanvragen met behulp van de Azure-portal. Een verhoging van het VM-seriequotum verhoogt automatisch de totale regionale vCPU-limiet met hetzelfde bedrag.

Zie [VCPU-quota voor virtuele machines](../../virtual-machines/windows/quotas.md) en [Azure-abonnements- en servicelimieten](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests)voor meer informatie over standaard vCPU-quota.

Zie [Standaardquotum verhogen per regio](regional-quota-requests.md)voor meer informatie over het verhogen van de vCPU-limiet per regio.

Zie [Spotquota: Limieten voor alle VM-series verhogen voor](low-priority-quota.md)meer informatie over het verhogen van vCPU-limieten voor spotvm's.

U op twee manieren een verhoging van de standaardvCPU-quotalimieten per VM-serie aanvragen, zoals beschreven in de volgende secties.

## <a name="request-a-standard-quota-increase-from-help--support"></a>Een standaardquotumverhoging aanvragen bij Help + ondersteuning

Een standaard vCPU-quotumverhoging per VM-serie aanvragen bij **Help + ondersteuning:**

> [!NOTE]
> U ook een verhoging van de quotumlimiet aanvragen voor meerdere regio's via één ondersteuningsaanvraag. Zie stap 8 voor meer informatie.

1. Selecteer **help + ondersteuning**in het menu Azure [portal](https://portal.azure.com) .

   ![De koppeling Help + ondersteuning](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Selecteer in **Help + ondersteuning**de optie Nieuw **ondersteuningsverzoek**.

    ![Een nieuw ondersteuningsverzoek maken](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Selecteer **Service- en abonnementslimieten (quota)** **voor issuetype.**

   ![Een probleemtype selecteren](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Selecteer **bij Abonnement**het abonnement waarvan u het quotum wilt verhogen.

   ![Een abonnement selecteren voor een verhoogd quotum](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Selecteer **voor quotumtype** **de maximumsnelheid voor Compute-VM (cores-vCPU's).**

   ![Een quotumtype selecteren](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selecteer **Volgende: Oplossingen** om PROBLEEMGEGEVENS te **openen.** Selecteer **Details opgeven** om aanvullende informatie in te voeren.

   ![De link 'Details geven'](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Ga in **de details van het quotum**de volgende stappen uit:

   ![TAanvullende quotagegevens opgeven](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Selecteer **voor implementatiemodel**het juiste model.

   1. Selecteer **voor locaties**een locatie. Kies Voor de geselecteerde locatie onder **Typen**in **Een type selecteren**de optie **Standaard**.

      ![Contingentgegevens - quotatypen](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      Onder **Typen**u zowel standaard- als steunquotumtypen aanvragen bij één ondersteuningsaanvraag via ondersteuning voor meerdere selecties.

      Zie [Azure spot VM's voor virtuele machineschaalsets voor](../../virtual-machine-scale-sets/use-spot.md)meer informatie over het verhogen van de limiet voor spotquota.

   1. Selecteer **in Standaard**de SKU-reeks voor verhoogde quota.

      ![Quotadetails - SKU-reeks](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Voer de nieuwe quotalimieten in die u voor dit abonnement wilt. Als u een SKU uit uw lijst wilt verwijderen, schakelt u het selectievakje naast de SKU uit of schakelt u het pictogram 'X' verwijderen in.

      ![Een nieuwe vCPU-limiet selecteren](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Als u een quotumverhoging wilt aanvragen voor meer dan één locatie, selecteert u een extra locatie in **Locaties**en selecteert u vervolgens een geschikt VM-type. U vervolgens een limiet invoeren die van toepassing is op de extra locatie.

   ![Extra locaties opgeven in quotumdetails](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecteer **Opslaan en doorgaan** met het maken van de ondersteuningsaanvraag.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>Een standaardquotumverhoging aanvragen bij Abonnementen

Een standaard vCPU-quotumverhoging per VM-serie aanvragen bij **Abonnementen:**

> [!NOTE]
> U ook een verhoging van de quotumlimiet aanvragen voor meerdere regio's via één ondersteuningsaanvraag. Zie stap 7 voor meer informatie.

1. Zoek in de [Azure-portal](https://portal.azure.com)naar en selecteer **Abonnementen**.

   ![Abonnementen in de Azure-portalzoeken](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selecteer het abonnement waarvan u het quotum wilt verhogen.

   ![Abonnementen om te selecteren voor wijzigingen](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Selecteer gebruik + **quota**in het linkerdeelvenster .

   ![De link 'Gebruik + quota'](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Selecteer Rechtsboven **Aanvraag verhogen**.

   ![Selecteren om het quotum te verhogen](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Selecteer **voor quotumtype** **de maximumsnelheid voor Compute-VM (cores-vCPU's).**

   ![Een quotumtype selecteren](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Ga in **de details van het quotum**de volgende stappen uit:

   1. Selecteer **voor implementatiemodel**het juiste model en selecteer voor **Locaties**een locatie.

      ![Quotagegevens opgeven](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Selecteer voor de geselecteerde locatie onder **Typen**de optie **Een type selecteren**en selecteer Vervolgens **Standaard**.

      ![Standaardtype selecteren](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      Onder **Typen**u zowel standaard- als steunquotumtypen aanvragen bij één ondersteuningsaanvraag via ondersteuning voor meerdere selecties.

      Zie [Azure spot VM's voor virtuele machineschaalsets voor](../../virtual-machine-scale-sets/use-spot.md)meer informatie over het verhogen van de limiet voor spotquota.

   1. Selecteer **bij Standaard**de SKU-reeks waarvan u de quota wilt verhogen.

      ![Quotadetails - SKU-reeks](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Voer de nieuwe quotalimieten in die u voor dit abonnement wilt. Als u een SKU uit uw lijst wilt verwijderen, schakelt u het selectievakje naast de SKU uit of schakelt u het pictogram 'X' verwijderen in.

      ![Een nieuwe vCPU-limiet selecteren](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Als u een quotumverhoging wilt aanvragen voor meer dan één locatie, selecteert u een extra locatie in **Locaties**en selecteert u vervolgens een geschikt VM-type.

   Deze stap laadt de SKU-serie die u hebt geselecteerd voor eerdere locaties vooraf. Voer de quotalimieten in die u wilt toepassen op de extra reeksen.

   ![Extra locaties selecteren in quotumdetails](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecteer **Opslaan en doorgaan** met het maken van de ondersteuningsaanvraag.
