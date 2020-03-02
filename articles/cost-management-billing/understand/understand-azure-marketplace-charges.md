---
title: Meer informatie over uw kosten voor externe services in Azure | Microsoft Docs
description: Meer informatie over facturering van kosten voor externe services, voorheen bekend als Marketplace, in Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7955e9bc75b2a27ac42d381df1e686ec8a0ed04b
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587761"
---
# <a name="understand-your-azure-external-services-charges"></a>Inzicht in uw Azure-externe servicekosten
Externe services worden gepubliceerd door softwareleveranciers van derden in Azure Marketplace. SendGrid is bijvoorbeeld een externe service die u in Azure kunt kopen, maar die niet is gepubliceerd door Microsoft. Sommige Microsoft-producten worden ook verkocht via Azure Marketplace.

## <a name="how-external-services-are-billed"></a>Hoe externe services in rekening worden gebracht

- Als u een Microsoft-klantovereenkomst (MCA) of Microsoft Partner-overeenkomst (MPA) hebt, worden uw services van derden gefactureerd met de rest van uw Azure-services. [Controleer uw type factureringsrekening](#check-billing-account-type) om te zien of u toegang hebt tot een MCA of MPA.
- Als u geen MCA of MPA hebt, worden uw externe services apart van uw Azure-services gefactureerd. U ontvangt elke factureringsperiode twee facturen: een factuur voor Azure-services en een andere factuur voor Marketplace-aankopen.
- Elke externe service heeft een ander factureringsmodel. Sommige services worden gefactureerd op basis van betalen-naar-gebruik, terwijl andere vaste maandelijkse kosten hebben.
- U kunt geen maandelijks gratis tegoed gebruiken voor externe services. Als u een Azure-abonnement gebruikt dat [gratis tegoed](https://azure.microsoft.com/pricing/spending-limits/) bevat, kan dit niet worden toegepast op kosten van externe services. Wanneer u een nieuwe externe service of resource inricht, wordt een waarschuwing weergegeven:

    ![Waarschuwing Marketplace-aankoop](./media/understand-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/understand-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/understand-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/understand-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/understand-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="external-spending-for-ea-customers"></a>Externe uitgaven voor EA-klanten

EA-klanten kunnen de externe service-uitgaven bekijken en rapporten downloaden in het EA-Portal. Zie [Azure Marketplace voor EA-klanten](https://ea.azure.com/helpdocs/azureMarketplace) om aan de slag te gaan.

## <a name="view-and-download-invoices-for-external-services"></a>Facturen voor externe services weergeven en downloaden

Als u een MCA (Microsoft-klantovereenkomst) of MPA (Microsoft Partner-overeenkomst) hebt, worden services van derden gefactureerd met de rest van uw Azure-services, op één factuur. [Controleer uw type factureringsrekening](#check-billing-account-type) om te zien of u toegang hebt tot een MCA of MPA. Als dat het geval is, raadpleegt u [Facturen weergeven en downloaden in de Azure-portal](download-azure-invoice.md) om uw kosten van derden te bekijken.

Als u MCA of MPA hebt, hebt u aparte facturen voor de kosten van derden. 

Azure Marketplace-kosten worden weergegeven in uw lokale valuta.

U kunt uw Azure Marketplace-facturen bekijken en downloaden via de Azure-portal door de volgende stappen uit te voeren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar **Cost Management en facturering**.
1. Selecteer **Facturen** in het menu aan de linkerkant.
1. Selecteer in de vervolgkeuzelijst met abonnementen het abonnement dat is gekoppeld aan uw Marketplace-services.
1. Bekijk de kolom **Type** in de lijst met facturen. Als een factuur voor een Marketplace-service is, is het type **Azure Marketplace en reserveringen**. 

    ![schermopname van het type Azure Marketplace in het factuurraster](./media/understand-azure-marketplace-charges/marketplace-type-twd.png)

1. Selecteer het filter **Type** als u wilt filteren op type, zodat alleen facturen voor Azure Marketplace en reserveringen worden weergegeven. Selecteer in de vervolgkeuzelijst vervolgens **Azure Marketplace en reserveringen**.

    ![schermopname van het geselecteerde filter Type, met Azure Marketplace en reserveringen geselecteerd in de vervolgkeuzelijst](./media/understand-azure-marketplace-charges/type-filter.png)

1. Selecteer aan de rechterkant het downloadpictogram voor de factuur die u wilt downloaden.

    ![schermopname met het downloadpictogram voor een factuur geselecteerd](./media/understand-azure-marketplace-charges/download-icon-marketplace.png)

1. Selecteer onder **Factuur** de blauwe knop **Downloaden**.

    ![schermopname van de knop Downloaden voor een factuur in het deelvenster Context](./media/understand-azure-marketplace-charges/invoice-download-marketplace.png)

## <a name="pay-for-external-services-in-the-azure-portal"></a>Betalen voor externe services in de Azure-portal

Als u een Microsoft-klantovereenkomst (MCA) of Microsoft Partner-overeenkomst (MPA) hebt, worden uw services van derden gefactureerd met de rest van uw Azure-services. [Controleer uw type factureringsrekening](#check-billing-account-type) om te zien of u toegang hebt tot een MCA of MPA. Als u toegang hebt, kunt u uw hele factuur betalen in de Azure-portal door de stappen te volgen in [Uw factuur voor Microsoft Azure betalen](pay-bill.md).

Als u geen MCA of MPA hebt, kunt u uw Marketplace-facturen in de Azure-portal betalen door de volgende stappen uit te voeren:

1. Volg de stappen in de vorige sectie [Facturen voor externe services weergeven en downloaden](#view-and-download-invoices-for-external-services) om uw Marketplace-facturen te zoeken.
1. Selecteer de blauwe koppeling **Nu betalen** voor de factuur die u wilt betalen.

    ![schermopname met de koppeling Nu betalen in het factuurraster geselecteerd](./media/understand-azure-marketplace-charges/pay-now-twd.png)

    >[!NOTE]
    > U ziet de koppeling **Nu betalen** alleen als het type van uw factuur **Azure Marketplace en reserveringen** is, en als de vervaldatum voor de betaling van de factuur is bereikt of overschreden.

1. Klik op de nieuwe pagina op de blauwe koppeling **Betalingswijze selecteren**.

    ![schermopname met de koppeling Betalingswijze geselecteerd](./media/understand-azure-marketplace-charges/select-payment-method-pay-now-twd.png)

1. Nadat u een betalingswijze hebt geselecteerd, klikt u linksonder op de pagina op de blauwe knop **Nu betalen**.
    ![schermopname met de knop Nu betalen geselecteerd](./media/understand-azure-marketplace-charges/pay-now-button-twd.png)

## <a name="change-default-payment-for-external-services"></a>Standaardbetaling voor externe services wijzigen

Wanneer u een externe service aanschaft, kiest u een Azure-abonnement voor de resource. De betalingswijze van het geselecteerde Azure-abonnement wordt de betalingswijze voor de externe service. Als u de betalingswijze voor een externe service wilt wijzigen, moet u [de betalingswijze wijzigen van het Azure-abonnement](../manage/change-credit-card.md) dat is gekoppeld aan die externe service. U kunt nagaan aan welk abonnement uw externe service order is gekoppeld door de volgende stappen uit te voeren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Klik op **Alle resources** in het navigatiemenu links.
     ![schermopname van menu-item alle resources](./media/understand-azure-marketplace-charges/all-resources.png)
1. Zoek naar uw externe service.
1. Zoek naar de naam van het abonnement in de kolom **Abonnement**.
    ![schermopname van de naam van het abonnement voor de resource](./media/understand-azure-marketplace-charges/sub-selected.png)
1. Klik op de naam van het abonnement en [werk de actieve betalingsmethode bij](../manage/change-credit-card.md).

## <a name="cancel-an-external-service-order"></a>Een externe serviceorder annuleren

Als u een externe serviceorder wilt annuleren, verwijdert u de resource in de [Azure-portal](https://portal.azure.com).

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Klik op **Alle resources** in het navigatiemenu links.
    ![Schermopname van menu-item Alle resources](./media/understand-azure-marketplace-charges/all-resources.png)
1. Zoek naar uw externe service.
1. Schakel het selectievakje in naast de resource die u wilt verwijderen.
1. Selecteer **Verwijderen** in de opdrachtbalk.
    ![Schermopname van de knop Verwijderen](./media/understand-azure-marketplace-charges/delete-button.png)
1. Typ *‘Ja’* in de blade bevestiging.
    ![Resource verwijderen](./media/understand-azure-marketplace-charges/delete-resource.PNG)
1. Klik op **Verwijderen**.

## <a name="check-billing-account-type"></a>Type factureringsrekening controleren
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen
- [Beginnen met kosten analyseren](../costs/quick-acm-cost-analysis.md)
