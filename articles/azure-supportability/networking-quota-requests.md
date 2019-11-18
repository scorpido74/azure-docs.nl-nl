---
title: Netwerk limiet verhogen | Microsoft Docs
description: Netwerklimiet verhoogd
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 5cadd0479a6ddc97cc7ee7d9b5aee1bbffba02bb
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74120963"
---
# <a name="networking-limit-increase"></a>Netwerklimiet verhoogd

Als u uw huidige netwerk gebruik en quotum wilt weer geven, gaat u naar de Blade gebruiks namen en **quotum** in de Azure Portal. U kunt ook gebruik van [cli](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages), [Power shell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0) of de [API voor netwerk gebruik](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) gebruiken om uw netwerk gebruik en-limieten te bekijken.

U kunt een verhoging aanvragen via de Blade **Help en ondersteuning** of de Blade **gebruik + quotum** in de portal.

> [!Note]
> Als u de standaard grootte van voor het voor voegsel openbaar IP-adres wilt wijzigen, selecteert u ' min openbaar IP-adres voor voegsel lengte ' in de vervolg keuzelijst.

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Netwerk quotum verhoging op abonnements niveau aanvragen met behulp van de Blade **Help en ondersteuning**

Volg de onderstaande instructies om een ondersteunings aanvraag te maken via de Blade Help en ondersteuning van Azure die beschikbaar zijn in de Azure Portal. 

1. Selecteer in https://portal.azure.com**Help + ondersteuning**.

    ![Help en ondersteuning](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selecteer **Nieuwe ondersteuningsaanvraag**. 

    ![Nieuw ondersteuningsverzoek](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Kies in de vervolg keuzelijst probleem type de optie **service-en abonnements limieten (quota's)** .

    ![Vervolg keuzelijst probleem type](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selecteer het abonnement waarvoor het quotum moet worden verhoogd.

    ![Abonnement-Nieuwsset selecteren](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selecteer **netwerken** in vervolg keuzelijst **quotum type** . 

    ![Quotum type selecteren](./media/networking-quota-request/select-quota-type-network.png)

6. Geef bij Details van het **probleem**aanvullende informatie op voor het verwerken van uw aanvraag door te klikken op **Details opgeven**.

    ![Details opgeven](./media/resource-manager-core-quotas-request/provide-details.png)

7. Selecteer in het deel venster **quotum Details** het implementatie model, een locatie en de resources waarvoor u een verhoging wilt aanvragen.

    ![Quotum Details DM](./media/networking-quota-request/quota-details-network.png)

8.  Voer de nieuwe limieten in die u wilt voor het abonnement. Als u een regel wilt verwijderen, schakelt u de resource uit in de vervolg keuzelijst resource of klikt u op het pictogram ' x ' negeren. Nadat u het gewenste quotum voor elke resource hebt ingevoerd, klikt u op **opslaan en door gaan** in het paneel quotum Details om door te gaan met het maken van de ondersteunings aanvraag.

    ![Nieuwe limieten](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Netwerk quotum verhoging op abonnements niveau aanvragen met **gebruik van + quotum** Blade

Volg de onderstaande instructies voor het maken van een ondersteunings aanvraag via de Blade gebruik en quotum van Azure die beschikbaar is in de Azure Portal. 

1. Selecteer in https://portal.azure.com**abonnementen**.

    ![Abonnementen](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selecteer het abonnement waarvoor het quotum moet worden verhoogd.

    ![Abonnement selecteren](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **Gebruik en quota's** selecteren

    ![Gebruik en quota's selecteren](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. Selecteer in de rechter bovenhoek **aanvraag verhoging**.

    ![Toename aanvragen](./media/resource-manager-core-quotas-request/request-increase.png)

5. Volg de stappen die beginnen met stap 3 van de sectie het *quotum voor het aantal netwerken voor de netwerk verbinding met het abonnement voor* het maken van de toegang tot het niveau van de **Help en ondersteuning** .

## <a name="about-networking-limits"></a>Over netwerk limieten

Zie de [sectie netwerken](../azure-subscription-service-limits.md#networking-limits) op de pagina limieten of onze veelgestelde vragen over netwerk limieten voor meer informatie over netwerk limieten
