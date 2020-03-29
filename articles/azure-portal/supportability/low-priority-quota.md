---
title: Virtuele machinequota herkennen - Azure
description: Verhoog de quotumlimieten voor spotVM's, die een model van Azure-gebruik bieden waarmee u lagere kosten nemen in ruil voor het laten verwijderen van VM's door Azure als dat nodig is.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 3e6dddfc458f390f5648e23a8a2af8d714de4bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842765"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Spotquota: limieten voor alle VM-reeksen verhogen

Virtuele machines (Spot' s) bieden een ander model van Azure-gebruik. Hiermee u lagere kosten nemen in ruil voor het laten verwijderen van virtuele machines door Azure als dat nodig is voor pay-as-you-go of gereserveerde VM-instantieimplementaties. Zie [Azure spot VM's voor virtuele machineschaalsets voor](../../virtual-machine-scale-sets/use-spot.md)meer informatie over spotVM's.

Azure Resource Manager ondersteunt twee soorten vCPU-quota voor virtuele machines:

* *Pay-as-you-go VM's* en *gereserveerde VM-exemplaren* zijn onderworpen aan een *standaard vCPU-quotum.*
* *Voor spotVM's* geldt een *vCPU-quotum*voor spot .

Voor het vCPU-quotumtype ter plaatse worden VCPU-quota voor Resource Manager als één regionale limiet afgedwongen voor alle beschikbare virtuele machinereeksen.

Wanneer u een nieuwe spotVM implementeert, mag het totale nieuwe en bestaande vCPU-gebruik voor alle vm-exemplaren van spotniet hoger zijn dan de goedgekeurde vCPU-quotumlimiet voor spot. Als het steunquotum wordt overschreden, is de implementatie van de spot-VM niet toegestaan.

In dit artikel wordt besproken hoe u een verhoging van de vCPU-quotumlimiet voor vlekken aanvragen met behulp van de Azure-portal.

Zie [VCPU-quota voor virtuele machines](../../virtual-machines/windows/quotas.md) en [Azure-abonnements- en servicelimieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over standaard vCPU-quota.

Zie [Standaardquotum: Limieten per regio verhogen voor](regional-quota-requests.md)meer informatie over het verhogen van de vCPU-limiet per regio.

## <a name="request-a-quota-limit-increase-from-help--support"></a>Een verhoging van de quotumlimiet aanvragen bij Help + ondersteuning

Een verhoging van de spotquotalimiet aanvragen voor alle serie met virtuele machines met Behulp van **Help + ondersteuning:**

> [!NOTE]
> U ook een verhoging van de quotumlimiet aanvragen voor meerdere regio's via één ondersteuningsaanvraag. Zie stap 8 voor meer informatie.

1. Selecteer **Help + ondersteuning**in het menu Azure [portal.](https://portal.azure.com)

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

1. Ga **in quotadetails**de volgende stappen uit:

   1. Selecteer **voor implementatiemodel**het juiste model en selecteer voor **Locaties**een locatie.

      ![Aanvullende quotagegevens opgeven](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Kies Voor de geselecteerde locatie onder **Typen**in **Een type selecteren**de optie **Spot**.

      ![Steuntype selecteren](./media/resource-manager-core-quotas-request/select-spot-type.png)

       Onder **Typen**u zowel standaard- als steunquotumtypen aanvragen bij één ondersteuningsaanvraag via ondersteuning voor meerdere selecties.

       Zie [Standaardquotum: Limieten verhogen per VM-serie](per-vm-quota-requests.md)voor meer informatie.

   1. Voer de gewenste nieuwe quotumlimiet in voor dit abonnement.

      ![Een nieuw quotum voor spot-VM selecteren](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Als u een quotumverhoging wilt aanvragen voor meer dan één locatie, selecteert u een extra locatie in **Locaties**en selecteert u vervolgens een geschikt VM-type. U vervolgens een limiet invoeren die van toepassing is op de extra locatie.

   ![Extra locaties opgeven in quotumdetails](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecteer **Opslaan en doorgaan** met het maken van de ondersteuningsaanvraag.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Een verhoging van de quotumlimiet aanvragen in het deelvenster Abonnementen

Ga als u vanuit het deelvenster **Abonnementen** een verhoging van het steunquotum voor alle VM-reeksen aan:

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

1. Selecteer **Volgende: Oplossingen** om PROBLEEMGEGEVENS te **openen.** Selecteer **Details opgeven** om aanvullende informatie in te voeren. In **contingentgegevens**worden de volgende gegevens ingevoerd:

   1. Selecteer **voor implementatiemodel**het juiste model en selecteer voor **Locaties**een locatie.

      ![Quotagegevens opgeven](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Kies Voor de geselecteerde locatie onder **Typen**in **Een type selecteren**de optie **Spot**.

      ![Steuntype selecteren](./media/resource-manager-core-quotas-request/select-spot-type.png)

      Zie [Standaardquotum: Limieten verhogen per VM-serie](per-vm-quota-requests.md)voor meer informatie.

   1. Voer de gewenste nieuwe quotumlimiet in voor dit abonnement.

      ![Voer een nieuwe waarde in voor vCPU-limiet](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Als u een quotumverhoging wilt aanvragen voor meer dan één locatie, selecteert u een extra locatie in **Locaties**en selecteert u vervolgens een geschikt VM-type. U vervolgens een limiet invoeren die van toepassing is op de extra locatie.

   ![Extra locaties selecteren in quotumdetails](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecteer **Opslaan en doorgaan** met het maken van de ondersteuningsaanvraag.
