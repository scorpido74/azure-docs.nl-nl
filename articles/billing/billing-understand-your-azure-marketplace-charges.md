---
title: Meer informatie over uw kosten voor externe services in Azure | Microsoft Docs
description: Meer informatie over facturering van kosten voor externe services, voorheen bekend als Marketplace, in Azure.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 43986ce57b8d320beeae748d3b848cf9ef38744d
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718726"
---
# <a name="understand-your-azure-external-services-charges"></a>Inzicht in uw Azure-externe servicekosten
Externe services worden gepubliceerd door softwareleveranciers van derden in Azure Marketplace. SendGrid is bijvoorbeeld een externe service die u in Azure kunt kopen, maar die niet is gepubliceerd door Microsoft. Sommige Microsoft-producten worden ook verkocht via Azure Marketplace.

## <a name="how-external-services-are-billed"></a>Hoe externe services in rekening worden gebracht

- Als u een [Microsoft-klantovereenkomst](#check-access) hebt, worden uw services van derden gefactureerd met de rest van uw Azure-services.
- Als u geen Microsoft-klantovereenkomst hebt, worden uw externe services apart van de rest van uw Azure-services gefactureerd.
- Elke externe service heeft een ander factureringsmodel. Sommige services worden gefactureerd op basis van betalen-naar-gebruik, terwijl andere vaste maandelijkse kosten hebben.
- U kunt geen maandelijks gratis tegoed gebruiken voor externe services. Als u een Azure-abonnement gebruikt dat [gratis tegoed](https://azure.microsoft.com/pricing/spending-limits/) bevat, kan dit niet worden toegepast op kosten van externe services. Wanneer u een nieuwe externe service of resource inricht, wordt een waarschuwing weergegeven:

    ![Waarschuwing Marketplace-aankoop](./media/billing-understand-your-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/billing-understand-your-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="view-and-download-invoices"></a>Facturen weergeven en downloaden

Als u een [Microsoft-klantovereenkomst](#check-access) hebt, staan uw kosten van derden op dezelfde factuur als uw Azure-kosten. Meer informatie over [Hoe u uw Azure-factuur kunt bekijken en downloaden](billing-download-azure-invoice.md) via de Azure-portal om uw kosten van derden te bekijken.

Als u geen Microsoft-klantovereenkomst hebt, hebt u aparte facturen voor de kosten van derden. U kunt uw Azure Marketplace-facturen bekijken en downloaden via de Azure-portal door de volgende stappen uit te voeren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek naar **Cost Management en facturering**.
1. Selecteer **Facturen** in het menu aan de linkerkant.
1. Klik op het tabblad **Azure Marketplace en reserveringen**.  ![Afbeelding van het tabblad Azure Marketplace en reserveringen](./media/billing-understand-your-azure-marketplace-charges/invoice-tabs.png)
1. Selecteer in de vervolgkeuzelijst Abonnementen het abonnement met de externe services waarvoor u facturen wilt bekijken.

## <a name="external-spending-for-ea-customers"></a>Externe uitgaven voor EA-klanten

EA-klanten kunnen de externe service-uitgaven bekijken en rapporten downloaden in het EA-Portal. Zie [Azure Marketplace voor EA-klanten](https://ea.azure.com/helpdocs/azureMarketplace) om aan de slag te gaan.

## <a name="manage-payment-for-external-services"></a>Betaling voor externe services beheren

Wanneer u een externe service aanschaft, kiest u een Azure-abonnement voor de resource. De betalingswijze van het geselecteerde Azure-abonnement wordt de betalingswijze voor de externe service. Als u de betalingswijze voor een externe service wilt wijzigen, moet u [de betalingswijze wijzigen van het Azure-abonnement](billing-how-to-change-credit-card.md) dat is gekoppeld aan die externe service. U kunt nagaan aan welk abonnement uw externe service order is gekoppeld door de volgende stappen uit te voeren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik op **Alle resources** in het navigatiemenu links.
     ![schermopname van menu-item alle resources](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Zoek naar uw externe service.
1. Zoek naar de naam van het abonnement in de kolom **Abonnement**.
    ![schermopname van de naam van het abonnement voor de resource](./media/billing-understand-your-azure-marketplace-charges/sub-selected.png)
1. Klik op de naam van het abonnement en [werk de actieve betalingsmethode bij](billing-how-to-change-credit-card.md).

<!-- Update your payment methods for external service orders from the [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> If you purchased your subscription with a Work or School account, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to make changes to your payment method.

1. Sign in to the [Account Center](https://account.windowsazure.com/) and [navigate to the **marketplace** tab](https://account.windowsazure.com/Store)

    ![Select marketplace in the account center](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Select the external service you want to manage

    ![Select the external service you want to manage](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Click **Change payment method** on the right side of the page. This link brings you to a different portal to manage your payment method.

    ![Order summary](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Click **Edit info** and follow instructions to update your payment information.

    ![Select edit info](./media/billing-understand-your-azure-marketplace-charges/edit-info.png) -->

## <a name="cancel-an-external-service-order"></a>Een externe serviceorder annuleren
Als u een externe serviceorder wilt annuleren, verwijdert u de resource in de [Azure-portal](https://portal.azure.com).

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik op **Alle resources** in het navigatiemenu links.
    ![Schermopname van menu-item Alle resources](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Zoek naar uw externe service.
1. Schakel het selectievakje in naast de resource die u wilt verwijderen.
1. Selecteer **Verwijderen** in de opdrachtbalk.
    ![Schermopname van de knop Verwijderen](./media/billing-understand-your-azure-marketplace-charges/delete-button.png)
1. Typ *‘Ja’* in de blade bevestiging.
    ![Resource verwijderen](./media/billing-understand-your-azure-marketplace-charges/delete-resource.PNG)
1. Klik op **Verwijderen**.

## <a name="check-access"></a>Toegang controleren
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen
- [Beginnen met kosten analyseren](../cost-management/quick-acm-cost-analysis.md)
