---
title: Overdracht van Azure-abonnementen tussen abonnees en CSP's
description: Lees hoe u Azure-abonnementen kunt overdragen tussen abonnees en CSP's.
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/19/2020
ms.author: banders
ms.openlocfilehash: 7db0a5bd4697c99c071d685903dfc394c2b49f25
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835796"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>Overdracht van Azure-abonnementen tussen abonnees en CSP's

Dit artikel bevat de algemene stappen voor het overdragen van Azure-abonnementen van en naar CSP-partners (Cloud Solution Provider) en hun klanten.

## <a name="transfer-ea-subscriptions-to-a-csp-partner"></a>EA-abonnementen overdragen naar een CSP-partner

CSP-partners voor directe facturering die zijn gecertificeerd als een [Azure Expert MSP (Managed Services provider)](https://partner.microsoft.com/membership/azure-expert-msp) kunnen een aanvraag indienen voor de overdracht van Azure-abonnementen voor hun klanten die een directe EA (Enterprise Agreement) hebben. Abonnementsoverdrachten zijn alleen toegestaan voor klanten die een Microsoft-klantovereenkomst (MCA) hebben geaccepteerd en een Azure-abonnement hebben gekocht.

Wanneer de aanvraag is goedgekeurd, kan de CSP een gecombineerde factuur aanbieden aan klanten. Zie [Eigendom van facturering van Azure-abonnementen krijgen voor uw MPA-account](mpa-request-ownership.md) voor meer informatie over CSP's die abonnementen willen overdragen.

## <a name="other-subscription-transfers-to-a-csp-partner"></a>Andere abonnementsoverdrachten naar een CSP-partner

Als u andere Azure-abonnementen wilt overdragen naar een CSP-partner, moet de abonnee resources van bronabonnementen verplaatsen naar CSP-abonnementen. Gebruik de volgende richtlijnen voor het verplaatsen van resources tussen abonnementen.

1. Werk samen met uw CSP-partner om Azure CSP-abonnementen te maken op de doellocatie.
1. Zorg ervoor dat de bron- en doelabonnementen van de CSP zich in dezelfde Azure Active Directory-tenant (Azure AD) bevinden.  
    U kunt de Azure AD-tenant voor een Azure CSP-abonnement niet wijzigen. In plaats daarvan moet u het bronabonnement toevoegen of koppelen aan de CSP Azure AD-tenant. Zie [Een Azure-abonnement aan uw Azure Active Directory-tenant toevoegen of koppelen](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) voor meer informatie.
    > [!IMPORTANT]
    > - Wanneer u een abonnement aan een andere Azure Active Directory koppelt, gaat de toegang verloren voor gebruikers aan wie rollen zijn toegewezen met [Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC](../../role-based-access-control/role-assignments-portal.md)). Klassieke abonnementsbeheerders, waaronder de servicebeheerder en co-beheerders, verliezen ook hun toegang.
    > - Beleidstoewijzingen worden verwijderd uit een abonnement wanneer het abonnement wordt gekoppeld aan een andere Active Directory.
1. Het gebruikersaccount dat u gebruikt om de overdracht uit te voeren, moet in beide abonnementen beschikken over eigenaarstoegang ingesteld via [RBAC](add-change-subscription-administrator.md).
1. Voordat u begint, moet u [controleren](/rest/api/resources/resources/validatemoveresources) of alle Azure-resources kunnen worden verplaatst van het bronabonnement naar het doelabonnement.  
    Sommige Azure-resources kunnen namelijk niet worden verplaatst tussen abonnementen. Als u de volledige lijst met Azure-resources wilt weergeven die wel kunnen worden verplaatst, raadpleegt u [Ondersteuning voor het verplaatsen van resources](../../azure-resource-manager/management/move-support-resources.md).
    > [!IMPORTANT]
    >  - Azure CSP ondersteunt alleen resources van Azure Resource Manager. Als er Azure-resources in het bronabonnement zijn gemaakt met behulp van het klassieke Azure-implementatiemodel, moet u deze vóór de migratie naar [Azure Resource Manager](https://docs.microsoft.com/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm) migreren. U moet een partner zijn om de webpagina te kunnen weergeven.

1. Controleer of alle services van het bronabonnement gebruikmaken van het Azure Resource Manager-model. Vervolgens brengt u resources van het bronabonnement over naar het doelabonnement met behulp van de instructies in [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - Het verplaatsen van Azure-resources tussen abonnementen kan leiden tot downtime van services, op basis van resources in de abonnementen.

## <a name="transfer-csp-subscription-to-other-offer"></a>CSP-abonnement overdragen naar een andere aanbieding

Als u andere Azure-abonnementen wilt overdragen van een CSP-partner naar een andere Azure-aanbieding, moet de abonnee resources verplaatsen tussen bron- en doelabonnementen van CSP.

1. Maak doelabonnementen voor Azure.
1. Zorg ervoor dat de bron- en doelabonnementen zich in dezelfde Azure Active Directory-tenant (Azure AD) bevinden. Zie [Een Azure-abonnement aan uw Azure Active Directory-tenant toevoegen of koppelen](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) voor meer informatie over het wijzigen van een Azure AD-tenant.
    Houd er rekening mee dat de directory voor de wijziging niet het CSP-abonnement is. U kunt bijvoorbeeld overgaan van een CSP-abonnement naar een abonnement met betalen per gebruik. U moet de directory van het abonnement met betalen per gebruik wijzigen, zodat deze overeenkomt met de directory.

    > [!IMPORTANT]
    >  - Wanneer u een abonnement aan een andere Active Directory koppelt, gaat de toegang verloren van gebruikers aan wie rollen zijn toegewezen met [RBAC](../../role-based-access-control/role-assignments-portal.md). Klassieke abonnementsbeheerders, waaronder de servicebeheerder en co-beheerders, verliezen ook hun toegang.
    >  - Beleidstoewijzingen worden verwijderd uit een abonnement wanneer het abonnement wordt gekoppeld aan een andere Active Directory.

1. Het gebruikersaccount dat u gebruikt om de overdracht uit te voeren, moet in beide abonnementen beschikken over eigenaarstoegang ingesteld via [RBAC](add-change-subscription-administrator.md).
1. Voordat u begint, moet u [controleren](/rest/api/resources/resources/validatemoveresources) of alle Azure-resources kunnen worden verplaatst van het bronabonnement naar het doelabonnement.
    > [!IMPORTANT]
    >  - Sommige Azure-resources kunnen namelijk niet worden verplaatst tussen abonnementen. Als u de volledige lijst met Azure-resources wilt weergeven die wel kunnen worden verplaatst, raadpleegt u [Ondersteuning voor het verplaatsen van resources](../../azure-resource-manager/management/move-support-resources.md).

1. Gebruik de instructies in [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md) om resources van het bronabonnement over te brengen naar het doelabonnement.
    > [!IMPORTANT]
    >  - Het verplaatsen van Azure-resources tussen abonnementen kan leiden tot downtime van services, op basis van resources in de abonnementen.

## <a name="next-steps"></a>Volgende stappen
- [Vraag het eigendom van facturering van Azure-abonnementen aan voor uw MPA-account](mpa-request-ownership.md).
- Lees meer over het [beheren van accounts en abonnementen met Azure -facturering](../index.yml).
