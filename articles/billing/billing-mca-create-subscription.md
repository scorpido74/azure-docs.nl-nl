---
title: Extra Azure-abonnement maken voor uw factureringsaccount
description: Meer informatie over het toevoegen van een nieuw Azure-abonnement in de Azure-portal.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 558410d980d261780f7287d1e27ed704b356fc2b
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490944"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>Een extra Azure-abonnement maken voor een Microsoft-klantovereenkomst

Maak extra abonnementen voor uw factureringsaccount om afzonderlijke omgevingen in te stellen voor ontwikkeling en testen, beveiliging of om gegevens te isoleren met het oog op naleving.

Dit artikel is van toepassing op een factureringsaccount voor een Microsoft-klantovereenkomst. [Controleer of u toegang hebt tot een Microsoft-klantovereenkomst](#check-access). Als u abonnementen voor andere soorten factureringsaccounts wilt maken, raadpleegt u [Extra abonnement maken in de Azure-portal](billing-create-subscription.md).

Als u een abonnement wilt maken, moet u een **factuursectie-eigenaar**, **factuursectie-inzender** of **maker van een Azure-abonnement** zijn. Voor meer informatie, zie [Rollen en taken voor abonnementsfacturering](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks). Als u anderen een machtiging wilt geven om Azure-abonnementen te maken voor uw factureringsaccount, raadpleegt u [Anderen een machtiging geven om Azure-abonnementen te maken](#give-others-permission).

## <a name="create-a-subscription"></a>Een abonnement maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Zoek naar **Abonnementen**.

   ![Schermopname van zoekopdracht in portal naar abonnementen](./media/billing-mca-create-subscription/billing-search-subscriptions.png)

3. Selecteer **Toevoegen**

4. Als u toegang hebt tot meerdere factureringsaccounts, selecteert u de factureringsaccount voor uw Microsoft-klantovereenkomst.

   ![Schermopname van de pagina voor het maken van abonnementen](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. Selecteer een factureringsprofiel. De kosten voor uw abonnement worden gefactureerd op het geselecteerde factureringsprofiel. Als u toegang hebt tot slechts één factureringsprofiel, wordt de selectie grijs weergegeven.

6. Selecteer een factuursectie. De kosten voor uw abonnement worden gefactureerd op deze factuursectie van het factureringsprofiel. Als u toegang hebt tot slechts één factuursectie, wordt de selectie grijs weergegeven.

7. Selecteer een plan voor het abonnement. Selecteer **Microsoft Azure Plan for DevTest** als u van plan bent om dit abonnement te gebruiken voor ontwikkelings- of testworkloads. Gebruik anders **Microsoft Azure Plan**. Als u toegang hebt tot slechts één plan, wordt de selectie grijs weergegeven.

8. Voer een naam in voor het abonnement. De naam helpt u om het abonnement in de Azure-portal gemakkelijk te identificeren.

9. Selecteer **Maken**.

## <a name="give-others-permission"></a>Anderen een machtiging geven

Voeg gebruikers toe als makers van Azure-abonnementen aan een factuursectie om hun een machtiging te geven om Azure-abonnementen te maken.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Zoek naar **Kostenbeheer en facturering**.

   ![Schermopname van zoekopdracht in portal naar abonnementen](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Ga naar de factureringssectie. Afhankelijk van uw toegang moet u mogelijk een factureringsaccount of factureringsprofiel selecteren. In het factureringsaccount of -profiel selecteert u **Factuursecties** en vervolgens een factuursectie uit de lijst. Alle abonnementen die door de gebruikers worden gemaakt, worden gefactureerd op deze factuursectie.
   
   ![Schermopname van het selecteren van factuursecties](./media/billing-mca-create-subscription/mca-select-invoice-sections.png)        

4. Selecteer **Toegangsbeheer (IAM)** linksboven op de pagina.

5. Selecteer **Toevoegen** bovenaan de pagina.

6. Selecteer **Maker van Azure-abonnementen** als rol.

7. Voer het e-mailadres in van de gebruiker die u toegang wilt verlenen.

8. Selecteer **Opslaan**.

## <a name="check-access"></a>Toegang controleren
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Hulp nodig? Contact opnemen met ondersteuning

Als u vragen hebt of hulp nodig hebt, kunt u [een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- [Anderen een machtiging geven om Azure-resources te maken met behulp van ingebouwde rollen](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Een virtuele Windows-machine maken](../virtual-machines/windows/quick-create-portal.md)
- [Een virtuele Linux-machine maken](../virtual-machines/linux/quick-create-portal.md)
- [Beheergroepen maken voor het organiseren en beheren van resources](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
