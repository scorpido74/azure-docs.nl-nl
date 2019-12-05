---
title: Een extra Azure-abonnement maken | Microsoft Docs
description: Lees hier meer over het toevoegen van een nieuw Azure-abonnement in de Azure-portal.
services: billing
documentationcenter: ''
author: amberb
manager: amberb
editor: ''
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: banders
ms.openlocfilehash: bd561f616c9e120a38f1c6b125e349684f82942e
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665607"
---
# <a name="create-an-additional-azure-subscription"></a>Een extra Azure-abonnement maken:

U kunt een extra abonnement maken voor de factureringsrekening van uw [Enterprise Agreement (EA) ](https://azure.microsoft.com/pricing/enterprise-agreement/), [Microsoft-klantovereenkomst ](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) of [Microsoft Partner-overeenkomst ](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) in de Azure-portal. Mogelijk wilt u een extra abonnement om te voorkomen dat u abonnementslimieten overschrijdt, om afzonderlijke omgevingen in te richten voor beveiliging, of om gegevens te isoleren om redenen van naleving.

Als u een factureringsrekening voor een Microsoft Online-serviceprogramma (MOSP) hebt, kunt u extra abonnementen maken in de [Azure-registratieportal ](https://account.azure.com/signup?offer=ms-azr-0003p).

Zie[Uw factureringsrekeningen weergeven in de Azure-portal](billing-view-all-accounts.md) voor meer informatie over factureringsrekeningen en het bepalen van uw type factureringsrekening.

## <a name="permission-required-to-create-azure-subscriptions"></a>Vereiste machtiging voor het maken van Azure-abonnementen

U hebt de volgende machtigingen nodig voor het maken van abonnementen:

|Factureringsaccount  |Machtiging  |
|---------|---------|
|Enterprise Agreement (EA) |  De rol van accounteigenaar voor de inschrijving van de Enterprise Agreement. Zie [Inzicht in Azure Enterprise Overeenkomst-beheerdersrollen in Azure](billing-understand-ea-roles.md) voor meer informatie.    |
|Microsoft-klantovereenkomst (MCA) |  De rol van eigenaar of inzender voor de factuursectie, het factureringsprofiel of de factureringsrekening. Of de rol Maker van Azure-abonnement voor de factuursectie.  Zie [Rollen en taken voor abonnementsfacturering](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks) voor meer informatie.    |
|Microsoft Partner-overeenkomst (MPA) |   De rol Globale beheerder en Beheerderagent in de CSP-partnerorganisatie. Zie [Partner Center - Assign users roles and permissions](https://docs.microsoft.com/partner-center/permissions-overview) (Engelstalig) voor meer informatie.  De gebruiker moet zich aanmelden bij de partnertenant om Azure-abonnementen te maken.   |

## <a name="create-a-subscription-in-the-azure-portal"></a>Een abonnement maken in de Azure-portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek naar **Abonnementen**.

   ![Schermopname van zoekopdracht in portal naar abonnementen](./media/billing-create-subscription/billing-search-subscription-portal.png)

1. Selecteer **Toevoegen**.

   ![Schermopname van de knop Toevoegen in de weergave Abonnementen](./media/billing-create-subscription/subscription-add.png)

1. Als u toegang hebt tot meerdere factureringsrekeningen, selecteert u de factureringsrekening waarvoor u het abonnement wilt maken.

1. Vul het formulier in en klik op **Maken**. In de onderstaande tabellen worden de velden in het formulier weergegeven per type factureringsrekening.

**Enterprise Agreement**

|Veld  |Definitie  |
|---------|---------|
|Naam     | De weergavenaam helpt u om het abonnement in de Azure-portal gemakkelijk te identificeren.  |
|Aanbieding     | Selecteer EA Dev/Test als u van plan bent om dit abonnement te gebruiken voor ontwikkelings- of testworkloads. Gebruik anders Microsoft Azure Enterprise. De DevTest-aanbieding moet zijn ingeschakeld voor uw inschrijvingsaccount om EA Dev/Test-abonnementen te maken.|

**Microsoft-klantovereenkomst**

|Veld  |Definitie  |
|---------|---------|
|Factureringsprofiel     | De kosten voor uw abonnement worden gefactureerd op het factureringsprofiel dat u selecteert. Als u toegang hebt tot slechts één factureringsprofiel, wordt de selectie grijs weergegeven.     |
|Factuursectie     | De kosten voor uw abonnement worden weergegeven in deze factuursectie van het factureringsprofiel. Als u toegang hebt tot slechts één factuursectie, wordt de selectie grijs weergegeven.  |
|Plannen     | Selecteer Microsoft Azure Plan for DevTest als u van plan bent om dit abonnement te gebruiken voor ontwikkelings- of testworkloads. Gebruik anders Microsoft Azure Plan. Als er slechts één plan is ingeschakeld voor het factureringsprofiel, wordt de selectie grijs weergegeven.  |
|Naam     | De weergavenaam helpt u om het abonnement in de Azure-portal gemakkelijk te identificeren.  |

**Microsoft Partner-overeenkomst**

|Veld  |Definitie  |
|---------|---------|
|Klant    | Het abonnement wordt gemaakt voor de klant die u selecteert. Als u slechts één klant hebt, wordt de selectie grijs weergegeven.  |
|Reseller    | De reseller die services levert aan de klant. Dit is een optioneel veld dat alleen van toepassing is op indirecte providers in het CSP-model met twee lagen. |
|Naam     | De weergavenaam helpt u om het abonnement in de Azure-portal gemakkelijk te identificeren.  |

## <a name="create-an-additional-azure-subscription-programmatically"></a>Programmatisch een extra Azure-abonnement maken

U kunt ook programmatisch extra abonnementen maken. Zie [Programmatisch Azure-abonnementen maken](../azure-resource-manager/programmatically-create-subscription.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Azure-abonnementsbeheerders toevoegen of wijzigen](billing-add-change-azure-subscription-administrator.md)
- [Move resources to new resource group or subscription (Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement)](../azure-resource-manager/resource-group-move-resources.md?toc=/azure/billing/TOC.json)
- [Beheergroepen maken voor resource-organisatie en -beheer](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
- [Uw Azure-abonnement opzeggen](billing-how-to-cancel-azure-subscription.md)

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).
