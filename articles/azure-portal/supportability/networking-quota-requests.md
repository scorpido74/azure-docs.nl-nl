---
title: Verhoging van de netwerklimiet | Microsoft Documenten
description: Netwerklimiet verhoogd
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9b5c7043b06172c2d4931ca1c3fd3ac5d0e80883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547740"
---
# <a name="networking-limit-increase"></a>Netwerklimiet verhoogd

Gebruik de [Azure-portal](https://portal.azure.com) om uw netwerkquotum te verhogen.

Als u uw huidige netwerkgebruik en -quotum wilt weergeven in de Azure-portal, opent u uw abonnement en selecteert u **Gebruik + quota**. U ook de volgende opties gebruiken om uw netwerkgebruik en -limieten te bekijken.

* [Gebruik CLI](/cli/azure/network#az-network-list-usages)
* [Powershell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [De API voor netwerkgebruik](/rest/api/virtualnetwork/virtualnetworks/listusage)

U een verhoging aanvragen door **Help + ondersteuning of** in Gebruik + **quota** in de portal te gebruiken.

> [!Note]
> Als u de standaardgrootte van **openbare IP-voorvoegsels**wilt wijzigen, selecteert u **Min Public IP InterNetwork Prefix Length** in de vervolgkeuzelijst.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Netwerkquotumverhogen aanvragen op abonnementsniveau met Behulp van Help + ondersteuning

Volg de onderstaande instructies om een ondersteuningsaanvraag te maken met Behulp van **Help + ondersteuning** in de Azure-portal.

1. Meld u aan bij [azure portal](https://portal.azure.com)en selecteer vervolgens Help **+ ondersteuning** in het azure portal-menu of zoek naar Help + ondersteuning en selecteer help **+ ondersteuning**.

    ![Help en ondersteuning](./media/networking-quota-request/help-plus-support.png)

1. Selecteer **Nieuw ondersteuningsverzoek**.

    ![Nieuw ondersteuningsverzoek](./media/networking-quota-request/new-support-request.png)

1. Kies **Service- en abonnementslimieten (quota)** voor **Issue-type**.

    ![Selecteer abonnementslimieten in de vervolgkeuzelijst van het type probleem](./media/networking-quota-request/select-quota-issue-type.png)

1. Selecteer het abonnement waarvoor het quotum moet worden verhoogd.

    ![Abonnement newSR selecteren](./media/networking-quota-request/select-subscription-support-request.png)

1. Selecteer **Netwerken onder Quotumtype** **.** Selecteer **Volgende: Oplossingen**.

    ![Quotumtype selecteren](./media/networking-quota-request/select-quota-type-network.png)

1. Selecteer **in PROBLEEMGEGEVENS**de optie **Details opgeven** en aanvullende informatie invullen om uw aanvraag te verwerken.

    ![Details geven](./media/networking-quota-request/provide-details-link.png)

1. Selecteer in het deelvenster **Quotadetails** een implementatiemodel, een locatie en de resources die u in uw aanvraag wilt opnemen.

    ![Quotumdetails DM](./media/networking-quota-request/quota-details-network.png)

1. Voer de nieuwe limieten in die u op het abonnement wilt hebben. Als u een regel wilt verwijderen, schakelt u de selectie uit het menu **Resources** uit of selecteert u het pictogram 'x' verwijderen. Nadat u het quotum voor elke resource hebt ingevoerd, selecteert u **Opslaan en doorgaan** met het maken van ondersteuningsaanvragen.

    ![Nieuwe limieten](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Netwerkquotumverhoging aanvragen op abonnementsniveau met gebruiksgebruik + quota

Volg deze instructies om een ondersteuningsaanvraag te maken met **gebruikgebruik + quotum** in de Azure-portal.

1. Zoeken https://portal.azure.comnaar en selecteer **Abonnementen**.

    ![Abonnementen](./media/networking-quota-request/search-for-suscriptions.png)

1. Selecteer het abonnement waarvoor het quotum moet worden verhoogd.

    ![Abonnement selecteren](./media/networking-quota-request/select-subscription-change-quota.png)

1. Selecteer **Gebruik + quota**

    ![Gebruiken en quota selecteren](./media/networking-quota-request/select-usage-plus-quotas.png)

1. Selecteer In de rechterbovenhoek de optie **Aanvraag verhogen**.

    ![Aanvraagverhoging](./media/networking-quota-request/request-increase-from-subscription.png)

1. Volg de stappen die beginnen met stap 3 in [Het quotum verhogen van het quotum voor netwerken aanvragen op abonnementsniveau](#request-networking-quota-increase-at-subscription-level-using-help--support).

## <a name="about-networking-limits"></a>Over netwerklimieten

Zie het [gedeelte Netwerken](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) van de limietenpagina of onze veelgestelde vragen over netwerklimieten voor meer informatie over netwerklimieten.
