---
title: AVERE vFXT-vereisten-Azure
description: Vereisten voor avere vFXT voor Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 0dafef7cf262153ccdb3b490aa0c7bd039b4a89b
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75889172"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Voorbereiden op het maken van de Avere vFXT

In dit artikel worden de vereiste taken voor het maken van een avere vFXT-cluster beschreven.

## <a name="create-a-new-subscription"></a>Een nieuw abonnement maken

Begin met het maken van een nieuw Azure-abonnement. Gebruik een afzonderlijk abonnement voor elk avere vFXT-project, zodat u eenvoudig alle project resources en onkosten kunt bijhouden, andere projecten tegen mogelijke resource beperking tijdens de inrichting moet beveiligen en het opschonen kunt vereenvoudigen.

Een nieuw Azure-abonnement maken in de Azure Portal:

* Navigeer naar de [Blade abonnementen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
* Klik bovenaan op de knop **+ toevoegen**
* Meld u aan als u hierom wordt gevraagd
* Selecteer een aanbieding en door loop de stappen voor het maken van een nieuw abonnement

## <a name="configure-subscription-owner-permissions"></a>Machtigingen voor abonnements eigenaren configureren

Een gebruiker met eigenaars machtigingen voor het abonnement moet het vFXT-cluster maken. Er zijn machtigingen voor abonnements eigenaren nodig om de software voorwaarden te accepteren en andere acties uit te voeren.

Er zijn enkele oplossingen die een niet-eigenaar in staat stellen om een avere vFTX voor Azure-cluster te maken. Deze scenario's omvatten het beperken van resources en het toewijzen van extra rollen aan de maker. In beide gevallen moet een eigenaar van het abonnement ook [de avere vFXT-software voorwaarden](#accept-software-terms) van tevoren accepteren.

| Scenario | Beperkingen | Er zijn toegangs rollen vereist om het avere vFXT-cluster te maken |
|----------|--------|-------|
| Beheerder van resource groep | Het virtuele netwerk, de cluster controller en de cluster knooppunten moeten worden gemaakt in de resource groep | [Gebruikers toegang beheerder](../role-based-access-control/built-in-roles.md#user-access-administrator) en [Inzender](../role-based-access-control/built-in-roles.md#contributor) rollen, zowel binnen het bereik van de doel resource groep |
| Extern virtueel netwerk | De cluster controller en de cluster knooppunten worden gemaakt in de resource groep, maar er wordt een bestaand virtueel netwerk in een andere resource groep gebruikt. | (1) de beheerders-en [Inzender](../role-based-access-control/built-in-roles.md#contributor) rollen van de [gebruikers toegang](../role-based-access-control/built-in-roles.md#user-access-administrator) tot de resource groep vFXT. en (2) de Inzender van de [virtuele machine](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), de [gebruikers toegangs beheerder](../role-based-access-control/built-in-roles.md#user-access-administrator)en de [avere-Inzender](../role-based-access-control/built-in-roles.md#avere-contributor) rollen binnen het bereik van de resource groep van het virtuele netwerk. |

Een andere is het maken van een aangepaste functie op rollen gebaseerd toegangs beheer (RBAC) voor het eerst en het toewijzen van bevoegdheden aan de gebruiker, zoals wordt uitgelegd in [dit artikel](avere-vfxt-non-owner.md). Deze methode geeft belang rijke machtigingen voor deze gebruikers.

## <a name="quota-for-the-vfxt-cluster"></a>Quota voor het vFXT-cluster

U moet over voldoende quota beschikken voor de volgende onderdelen van Azure. Indien nodig kunt u [een quotum verhoging aanvragen](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

> [!NOTE]
> De virtuele machines en SSD-onderdelen die hier worden vermeld, gelden voor het vFXT-cluster zelf. U hebt extra quota nodig voor de Vm's en SSD die u wilt gebruiken voor uw Compute-farm.  Zorg ervoor dat het quotum is ingeschakeld voor de regio waar u de werk stroom wilt uitvoeren.

|Azure-onderdeel|Quota|
|----------|-----------|
|Virtuele machines|3 of meer E32s_v3|
|Premium SSD-opslag|200 GB ruimte in het besturingssysteem plus 1 tot 4 TB ruimte in de cache per knooppunt |
|Opslagaccount (optioneel) |v2|
|Back-endopslag van gegevens (optioneel) |Een nieuwe LRS-BLOB-container |

## <a name="accept-software-terms"></a>Software voorwaarden accepteren

> [!NOTE]
> Deze stap is niet vereist als de eigenaar van een abonnement het avere vFXT-cluster maakt.

Tijdens het maken van het cluster moet u de service voorwaarden accepteren voor de avere vFXT-software. Als u geen eigenaar van het abonnement bent, moet de eigenaar van het abonnement de voor waarden van tevoren accepteren. Deze stap hoeft slechts één keer per abonnement te worden uitgevoerd.

De software voorwaarden vooraf accepteren:

1. Open een Cloud shell in de Azure Portal of blader naar <https://shell.azure.com>. Meld u aan met uw abonnements-ID.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Geef de volgende opdracht om de service voorwaarden te accepteren en programmatische toegang in te scha kelen voor de avere vFXT voor Azure-software-installatie kopie:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Een opslag service-eind punt in uw virtuele netwerk maken (indien nodig)

Een [service-eind punt](../virtual-network/virtual-network-service-endpoints-overview.md) houdt Azure Blob-verkeer lokaal in plaats van dit buiten het virtuele netwerk te routeren. Het wordt aanbevolen voor alle avere vFXT voor Azure-clusters die gebruikmaken van Azure Blob voor back-end-gegevens opslag.

Als u een bestaand virtueel netwerk levert en een nieuwe Azure Blob-container voor uw back-end-opslag maakt als onderdeel van het maken van het cluster, moet u een service-eind punt in het netwerk hebben voor micro soft Storage. Dit eind punt moet bestaan voordat het cluster wordt gemaakt, anders mislukt het maken.

Een opslag service-eind punt wordt aanbevolen voor alle avere vFXT voor Azure-clusters die gebruikmaken van Azure Blob Storage, zelfs als u de opslag later toevoegt.

> [!TIP]
>
>* Sla deze stap over als u een nieuw virtueel netwerk maakt als onderdeel van het maken van het cluster.
>* Deze stap is optioneel als u geen Blob Storage maakt tijdens het maken van het cluster. In dat geval kunt u het service-eind punt later maken als u ervoor kiest om Azure Blob te gebruiken.

Maak het eind punt van de opslag service vanuit het Azure Portal.

1. Open de lijst met virtuele netwerken in de portal.
1. Selecteer het virtuele netwerk voor uw cluster.
1. Klik in het menu links op **service-eind punten** .
1. Klik bovenaan op **toevoegen** .
1. Kies de service ``Microsoft.Storage``.
1. Selecteer het subnet van het cluster.
1. Klik onderaan op **toevoegen**.

   ![Azure Portal scherm opname met annotaties voor de stappen voor het maken van het service-eind punt](media/avere-vfxt-service-endpoint.png)

## <a name="next-step-create-the-vfxt-cluster"></a>Volgende stap: het vFXT-cluster maken

Nadat u deze vereisten hebt voltooid, kunt u het cluster zelf maken. Lees [het vFXT-cluster implementeren](avere-vfxt-deploy.md) voor instructies.
