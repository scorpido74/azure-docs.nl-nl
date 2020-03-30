---
title: Avere vFXT-vereisten - Azure
description: Voorwaarden voor Avere vFXT voor Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: a183989cc666f00da4be077c719c40d2524fd6e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252543"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Voorbereiden op het maken van de Avere vFXT

In dit artikel worden vereiste taken voor het maken van een Avere vFXT-cluster uitgelegd.

## <a name="create-a-new-subscription"></a>Een nieuw abonnement maken

Begin met het maken van een nieuw Azure-abonnement. Gebruik een apart abonnement voor elk Avere vFXT-project om het bijhouden en opschonen van kosten te vereenvoudigen en ervoor te zorgen dat andere projecten niet worden beïnvloed door quota of beperking van resources bij het inrichten van de clusterworkflow.

Ga als volgende voor een nieuw Azure-abonnement in de Azure-portal:

1. Navigeren naar het [blad Abonnementen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
1. Klik bovenaan op de knop **+ Toevoegen**
1. Aanmelden als u daarom wordt gevraagd
1. Selecteer een aanbieding en loop door de stappen om een nieuw abonnement te maken

## <a name="configure-subscription-owner-permissions"></a>Machtigingen voor abonnementseigenaren configureren

Een gebruiker met eigenaarmachtigingen voor het abonnement moet het vFXT-cluster maken. Voor het maken van clusteren moet een eigenaar de servicevoorwaarden voor software accepteren en wijzigingen in netwerk- en opslagbronnen autoriseren.

Er zijn enkele tijdelijke oplossingen waarmee een niet-eigenaar een Avere vFXT voor Azure-cluster kan maken. Deze scenario's omvatten het beperken van resources en het toewijzen van extra rbac-rollen (role-based access control) aan de maker. In al deze gevallen moet een eigenaar van een abonnement ook [de Avere vFXT-softwarevoorwaarden](#accept-software-terms) van tevoren accepteren.

| Scenario | Beperkingen | Toegang tot rollen die nodig zijn om het Avere vFXT-cluster te maken |
|----------|--------|-------|
| Resourcegroepbeheerder maakt de vFXT | De virtuele netwerk-, clustercontroller- en clusterknooppunten moeten worden gemaakt binnen de brongroep. | [User Access Administrator](../role-based-access-control/built-in-roles.md#user-access-administrator) en [Inzenderrollen,](../role-based-access-control/built-in-roles.md#contributor) beide scoped voor de doelgroep. |
| Een bestaand, extern virtueel netwerk gebruiken | De clustercontroller en clusterknooppunten worden gemaakt binnen de brongroep van de vFXT, maar gebruiken een bestaand virtueel netwerk in een andere brongroep. | (1) [User Access Administrator](../role-based-access-control/built-in-roles.md#user-access-administrator) en [Contributor](../role-based-access-control/built-in-roles.md#contributor) rollen die zijn bekleed met de vFXT-resourcegroep; en (2) [Virtuele machineinzender](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [user access administrator](../role-based-access-control/built-in-roles.md#user-access-administrator)en [Avere Contributor](../role-based-access-control/built-in-roles.md#avere-contributor) rollen die zijn verbonden aan de resourcegroep van het virtuele netwerk. |
| Aangepaste rol voor clustermakers | Geen beperkingen voor het plaatsen van resources. Deze methode geeft niet-eigenaren belangrijke privileges. | De eigenaar van een abonnement maakt een aangepaste RBAC-rol zoals uitgelegd in [dit artikel.](avere-vfxt-non-owner.md) |

## <a name="quota-for-the-vfxt-cluster"></a>Quotum voor het vFXT-cluster

Controleer of u voldoende quota hebt voor de volgende Azure-componenten. Vraag indien nodig [een verhoging van het quotum aan.](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)

> [!NOTE]
> De virtuele machines en SSD componenten die hier worden vermeld zijn voor de vFXT cluster zelf. Vergeet niet dat u ook quota nodig hebt voor de VM's en SSD's die u gebruikt voor uw compute farm.
>
> Controleer of het quotum is ingeschakeld voor de regio waar u de werkstroom wilt uitvoeren.

|Azure-onderdeel|Quota|
|----------|-----------|
|Virtuele machines|3 of meer E32s_v3 (één per clusterknooppunt) |
|Premium SSD-opslag|200 GB ruimte in het besturingssysteem plus 1 tot 4 TB ruimte in de cache per knooppunt |
|Opslagaccount (optioneel) |v2|
|Back-endopslag voor gegevens (optioneel) |Eén nieuwe LRS Blob-container |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>Softwarevoorwaarden accepteren

> [!TIP]
> Sla deze stap over als een abonnementhouder het Avere vFXT-cluster maakt.

Tijdens het maken van het cluster moet u de servicevoorwaarden voor de Avere vFXT-software accepteren. Als u geen eigenaar van een abonnement bent, moet u de voorwaarden van tevoren laten accepteren als u een abonnement hebt.

Deze stap hoeft slechts één keer per abonnement te worden uitgevoerd.

Ga als bedoeld als bedoeld om de softwarevoorwaarden van tevoren te accepteren:

1. Open een cloudshell in de Azure-portal of door te bladeren naar <https://shell.azure.com>. Meld u aan met uw abonnements-ID.

   ```azurecli
    az login
    az account set --subscription <subscription ID>
   ```

1. Geef deze opdracht uit om servicevoorwaarden te accepteren en programmatische toegang in te schakelen voor de Avere vFXT voor Azure-softwareafbeelding:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Een eindpunt van de opslagservice maken in uw virtuele netwerk (indien nodig)

Een [serviceeindpunt](../virtual-network/virtual-network-service-endpoints-overview.md) houdt Azure Blob-verkeer lokaal in plaats van het buiten het virtuele netwerk te routeren. Het wordt aanbevolen voor elk Avere vFXT voor Azure-cluster dat Azure Blob gebruikt voor back-end gegevensopslag.

Als u een nieuw virtueel netwerk maakt tijdens het maken van het cluster, wordt er automatisch een eindpunt gemaakt. Als u een bestaand virtueel netwerk opgeeft, moet het een eindpunt van de Microsoft-opslagservice hebben als u een nieuwe Blob-opslagcontainer wilt maken tijdens het maken van een cluster.<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* Sla deze stap over als u een nieuw virtueel netwerk maakt als onderdeel van het maken van een cluster.
>* Een eindpunt is optioneel als u geen Blob-opslag maakt tijdens het maken van een cluster. In dat geval u het eindpunt van de service later maken als u besluit Azure Blob te gebruiken.

Maak het eindpunt van de opslagservice vanuit de Azure-portal.

1. Open in de portal uw lijst met virtuele netwerken.
1. Selecteer het virtuele netwerk voor uw cluster.
1. Klik **op Serviceeindpunten** in het linkermenu.
1. Klik bovenaan **op Toevoegen.**
1. Kies de ``Microsoft.Storage``service .
1. Selecteer het subnet van het cluster.
1. Klik onderop Op **Toevoegen**.

   ![Schermafbeelding van Azure-portal met aantekeningen voor de stappen voor het maken van het serviceeindpunt](media/avere-vfxt-service-endpoint.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u deze vereisten hebt voltooid, u het cluster maken. Lees [Het vFXT-cluster implementeren](avere-vfxt-deploy.md) voor instructies.
