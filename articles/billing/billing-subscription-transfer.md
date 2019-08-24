---
title: Eigendom van de facturering van het Azure-abonnement overdragen aan een ander account | Microsoft Docs
description: Hierin wordt beschreven hoe u het eigendom van een Azure-abonnement overdraagt aan een ander account en enkele veelgestelde vragen over het proces
keywords: Azure-abonnement overdragen, abonnement voor Azure-overdracht, Azure-abonnement verplaatsen naar een ander account, eigenaar van het abonnement wijzigen, een Azure-abonnement naar een ander account overdragen, Azure-overdracht factureren
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1e4496e2b5d2b21fd878ef68665b8e5b06fa6cc5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012547"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Eigendom van een Azure-abonnement overdragen aan een ander account

Mogelijk wilt u het eigendom van uw Azure-abonnement overdragen als u uw organisatie verlaat of als u wilt dat uw abonnement wordt gefactureerd aan een ander account. Het overdragen van het eigendom van de facturering aan een ander account biedt de beheerders in het nieuwe account toestemming om facturerings taken uit te voeren, zoals het wijzigen van de betalings wijze, het weer geven van kosten en het annuleren van het abonnement.

Als u het eigendom van de facturering wilt houden, maar het type van uw abonnement wilt wijzigen, raadpleegt u [uw Azure-abonnement overschakelen naar een andere aanbieding](billing-how-to-switch-azure-offer.md). Zie [ingebouwde rollen voor Azure-resources](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)als u wilt bepalen wie de resources in het abonnement kan beheren.

Als u een debiteur van Enterprise Agreement (EA) bent, kunnen uw ondernemings Administrators het eigendom van uw abonnementen overdragen tussen accounts. Zie [facturerings eigendom van Enterprise Agreement (EA)-abonnementen overdragen](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions)voor meer informatie.

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Eigendom van een Azure-abonnement overdragen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als beheerder van het facturerings account met het abonnement dat u wilt overdragen. Zie [Veelgestelde vragen](#faq)als u wilt weten of u beheerder bent.

1. Zoeken op **kosten Management en facturering**.

   ![Schermafbeelding van zoeken in Azure portal](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Selecteer **abonnementen** in het linkerdeel venster. Afhankelijk van uw toegang moet u mogelijk een facturerings bereik selecteren en vervolgens **abonnementen** of **Azure-abonnementen**selecteren.

1. Selecteer **eigendom** van de facturering voor het abonnement dat u wilt overdragen. 

   ![Selecteer het abonnement dat u wilt overdragen](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Voer het e-mail adres in van een gebruiker die een facturerings beheerder is van het account dat wordt de nieuwe eigenaar van het abonnement.

1. Als u uw abonnement overbrengt naar een account in een andere Azure AD-Tenant, selecteert u of u het abonnement wilt verplaatsen naar de Tenant van het nieuwe account. Zie [het overdragen van een abonnement op een account in een andere Azure AD-Tenant](#transferring-subscription-to-an-account-in-another-azure-ad-tenant) voor meer informatie.

    > [!IMPORTANT]
    >
    > Als u ervoor kiest om het abonnement te verplaatsen naar de Azure AD-Tenant van het nieuwe account, worden alle [RBAC-toewijzingen (op rollen gebaseerd toegangs beheer)](../role-based-access-control/overview.md) voor het beheren van resources in het abonnement permanent verwijderd. Alleen de gebruiker in het nieuwe account die uw overdrachts aanvraag accepteert, heeft toegang tot het beheren van resources in het abonnement. Zie voor meer informatie [het overdragen van het abonnement naar een gebruiker in een andere Azure AD-Tenant](../active-directory/managed-identities-azure-resources/known-issues.md). U kunt ook het selectie vakje voor abonnementen van Azure AD-tenants uitschakelen om het eigendom van het abonnement over te dragen zonder het abonnement te verplaatsen naar de Tenant van het nieuwe account. Als u dit doet, blijven bestaande RBAC-machtigingen voor het beheer van Azure-resources behouden.
  
    ![Overdrachts pagina verzenden](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. Selecteer **overdrachts aanvraag verzenden**.

1. De gebruiker ontvangt een e-mail met instructies om uw overdrachts aanvraag te controleren.

   ![E-mail adres voor abonnements overdracht verzonden naar ontvanger](./media/billing-subscription-transfer/billing-receiver-email.png)

1. De gebruiker selecteert de koppeling in het e-mail bericht en volgt de instructies om de overdrachts aanvraag goed te keuren. De gebruiker moet een betalings methode selecteren die wordt gebruikt voor het betalen van het abonnement. Als de gebruiker niet beschikt over een Azure-account, zouden ze zich ook moeten registreren voor een nieuw account. 

   ![Eerste webpagina voor abonnements overdracht](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Tweede webpagina voor het overdragen van een abonnement](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Tweede webpagina voor het overdragen van een abonnement](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Gelukt! Het abonnement wordt nu overgedragen.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>Het abonnement wordt overgedragen naar een account in een andere Azure AD-Tenant

Er wordt een Azure Active Directory-Tenant (AD) voor u gemaakt wanneer u zich aanmeldt voor Azure. De Tenant vertegenwoordigt uw account. U gebruikt de Tenant om de toegang tot uw abonnementen en resources te beheren.

Wanneer u een nieuw abonnement maakt, wordt dit gehost in de Azure AD-Tenant van uw account. Als u anderen toegang wilt geven tot uw abonnement of de bijbehorende resources, moet u ze uitnodigen om lid te worden van uw Tenant. Zo kunt u de toegang tot uw abonnementen en resources beheren.

Wanneer u het eigendom van de facturering van uw abonnement overdraagt aan een account in een andere Azure AD-Tenant, kunt u het abonnement verplaatsen naar de Tenant van het nieuwe account. Als u dit doet, hebben alle gebruikers, groepen of service-principals die [op rollen gebaseerde toegang (RBAC)](../role-based-access-control/role-assignments-portal.md) hebben voor het beheren van abonnementen en de bijbehorende resources, hun toegang kwijt. Alleen de gebruiker in het nieuwe account die uw overdrachts aanvraag accepteert, heeft toegang tot de resources beheren. Om toegang te verlenen aan de gebruikers die oorspronkelijk toegang hebben, moet de nieuwe eigenaar [deze gebruikers hand matig toevoegen aan het abonnement](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).


## <a name="transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Ontwikkel-en test abonnementen van Visual Studio, MPN en betalen naar gebruik

Voor Visual Studio-en Microsoft Partner Network-abonnementen geldt een maandelijks terugkerend Azure-tegoed. Wanneer u deze abonnementen overdraagt, is uw tegoed niet beschikbaar in het doel facturerings account. Het abonnement gebruikt het tegoed in het doel facturerings account. Als Bob bijvoorbeeld een Visual Studio Enter prise-abonnement overdraagt aan het account van Jeanette op 9de Sept en Jeroen, wordt de overdracht geaccepteerd. Nadat de overdracht is voltooid, begint het abonnement met het tegoed in het account van Jeanette. Het tegoed wordt elke maand op 9de keer opnieuw ingesteld. 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Eigendom van de facturering van Enterprise Agreement (EA)-abonnementen overdragen

De Enter prise-beheerder kan het eigendom van abonnementen tussen accounts binnen een inschrijving overdragen. Zie [account eigendom overdragen](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) in de EA-portal voor meer informatie.

## <a name="next-steps-after-accepting-billing-ownership"></a>Volgende stappen na het accepteren van het eigendom van de facturering

Als u het eigendom van een Azure-abonnement hebt geaccepteerd, raden we u aan deze volgende stappen te controleren:

1. De service beheerder, co-beheerders en andere RBAC-rollen controleren en bijwerken. Zie [Azure-abonnements beheerders toevoegen of wijzigen](billing-add-change-azure-subscription-administrator.md) en [toegang beheren met RBAC en de Azure Portal](../role-based-access-control/role-assignments-portal.md)voor meer informatie.
1. Referenties bijwerken die zijn gekoppeld aan de services van dit abonnement, waaronder:
   1. Beheer certificaten waarmee de gebruikers beheerders rechten voor abonnements bronnen worden verleend. Zie [een beheer certificaat voor Azure maken en uploaden](../cloud-services/cloud-services-certs-create.md) voor meer informatie.
   1. Toegangs sleutels voor services zoals opslag. Zie [over Azure Storage-accounts](../storage/common/storage-create-storage-account.md) voor meer informatie.
   1. Externe toegangs referenties voor services zoals Azure Virtual Machines.
1. Als u met een partner werkt, kunt u overwegen om de partner-ID voor dit abonnement bij te werken. U kunt de partner-ID bijwerken in de [Azure Portal](https://portal.azure.com). Zie [een partner-id koppelen aan uw Azure-accounts](billing-partner-admin-link-started.md) voor meer informatie.

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Ondersteunde abonnements typen

De overdracht van abonnementen in de Azure Portal is beschikbaar voor de volgende typen abonnementen. Momenteel wordt de overdracht niet ondersteund voor [gratis proef](https://azure.microsoft.com/offers/ms-azr-0044p/) [-of Azure in open AIO-](https://azure.microsoft.com/offers/ms-azr-0111p/) abonnementen. Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../azure-resource-manager/resource-group-move-resources.md)voor een tijdelijke oplossing. [Neem contact op met de ondersteuning van Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)als u andere abonnementen, zoals [sponsor](https://azure.microsoft.com/offers/ms-azr-0036p/) -of ondersteunings abonnementen, wilt overdragen.

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [MPN-abonnees (Visual Studio Enter prise)](https://azure.microsoft.com/offers/ms-azr-0029p/) 
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enter prise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\*[Via de EA-Portal](#EA).

\*\*Alleen ondersteund voor accounts die worden gemaakt tijdens het aanmelden op de Azure-website. 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Veelgestelde vragen over afzenders

Deze veelgestelde vragen zijn van toepassing op gebruikers die het eigendom van een Azure-abonnement overdragen aan een ander account.

### <a name="whoisaa"></a>Wie is een facturerings beheerder van een account?

Een facturerings beheerder is een persoon die gemachtigd is voor het beheren van facturering voor een account. Ze zijn gemachtigd om toegang te krijgen tot de facturering op het [Azure Portal](https://portal.azure.com) en verschillende facturerings taken uit te voeren, zoals abonnementen maken, facturen weer geven en betalen, of betalings methoden bijwerken.

Als u accounts wilt identificeren waarvoor u een facturerings beheerder bent, gebruikt u de volgende stappen:

1. Ga naar de [pagina Cost Management + facturering in azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Selecteer **alle facturerings bereiken** in het linkerdeel venster. 
1. Op de pagina Abonnementen worden alle abonnementen vermeld waarvoor u een facturerings beheerder bent.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Gaat alles over. Inclusief resource groepen, Vm's, schijven en andere actieve services?

Al uw resources, zoals Vm's, schijven en websites, worden overgezet naar het nieuwe account. Als u echter een abonnement overbrengt naar een account in een andere Azure AD-Tenant, worden alle [beheerders rollen](billing-add-change-azure-subscription-administrator.md) en toewijzingen van op [rollen gebaseerde Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md) voor het abonnement [niet overgedragen](#transferring-subscription-to-an-account-in-another-azure-ad-tenant). Ook worden [app](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) -registraties en andere Tenant-specifieke services niet samen met het abonnement overgedragen.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>Kan ik het eigendom overdragen aan een account in een ander land?
Er kunnen echter geen cross-land overdrachten worden uitgevoerd in de Azure Portal. [Neem contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)om uw abonnement over te dragen over landen.

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Ik ben een beheerder van twee accounts. Kan ik een abonnement overdragen van een van mijn accounts naar een andere?
Ja, u kunt een abonnement overdragen tussen uw accounts. Uw accounts worden conceptueel gezien als accounts van twee verschillende gebruikers, zodat u de bovenstaande stappen kunt gebruiken om abonnementen tussen uw accounts over te dragen.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Resulteert een overdracht van een abonnement in een service downtime?

Als u een abonnement overbrengt naar een account in dezelfde Azure AD-Tenant, heeft dit geen invloed op de resources die in het abonnement worden uitgevoerd. Als u het abonnement echter overdraagt naar een account in een andere Tenant en besluit om het abonnement te verplaatsen naar de Tenant, hebben alle gebruikers, groepen en service-principals die op [rollen gebaseerde toegang hebben gehad (RBAC)](../role-based-access-control/overview.md) voor het beheren van resources in het abonnement, hun toegang kwijt . Dit kan leiden tot uitval tijd van de service.

### <a name="do-users-in-new-account-have-access-to-usage-and-billing-history"></a>Hebben gebruikers in het nieuwe account toegang tot de gebruiks-en facturerings geschiedenis?

De enige beschik bare informatie voor gebruikers in het nieuwe account is de kosten van de afgelopen maand voor uw abonnement. De rest van het gebruik en de facturerings geschiedenis worden niet overgedragen met het abonnement

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Hoe kan ik gegevens en services voor mijn Azure-abonnement naar een nieuw abonnement migreren?

Als u het eigendom van het abonnement niet kunt overdragen, hoeft u uw resources hand matig te migreren. Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../azure-resource-manager/resource-group-move-resources.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Als ik een Visual Studio-of Microsoft Partner Network-abonnement overdraagt, wordt mijn tegoed door gegeven aan het abonnement in het nieuwe account?

Nee, uw tegoed is niet beschikbaar in het nieuwe account. De gebruiker die de overdrachts aanvraag accepteert, moet een Visual Studio-licentie hebben om de overdrachts aanvraag te accepteren. Het abonnement maakt gebruik van het Visual Studio-tegoed dat beschikbaar is in het gebruikers account. Zie voor meer informatie over het [overdragen van Visual Studio, Microsoft Partner Network (MPN) en betalen naar gebruik ontwikkelen/testen-abonnementen](#transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions).


## <a name="frequently-asked-questions-faq-for-recipients"></a>Veelgestelde vragen (FAQ) voor ontvangers

Deze veelgestelde vragen zijn van toepassing op gebruikers die het eigendom van een Azure-abonnement accepteren van een ander account.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Als ik het eigendom van een abonnement van een ander account onderneemt, hebben gebruikers in dat account nog steeds toegang tot mijn resources?

Ja. Als uw account zich echter in een Azure AD-Tenant bevindt die afwijkt van de Tenant van het abonnement en de gebruiker die de overdrachts aanvraag heeft verzonden, wordt het abonnement verplaatst naar de Tenant van uw account, alle [beheerders rollen](billing-add-change-azure-subscription-administrator.md) en [Access Control op basis van rollen (RBAC) ](../role-based-access-control/role-assignments-portal.md)toewijzingen worden verwijderd. Gebruik de volgende stappen om gebruikers weer te geven die toegang hebben tot op [rollen gebaseerde toegang (RBAC)](../role-based-access-control/overview.md) voor het beheren van resources in het abonnement:

1. Ga naar de [pagina abonnement in de Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecteer het abonnement dat u wilt controleren en selecteer vervolgens **toegangs beheer (IAM)** in het linkerdeel venster.
1. Selecteer **roltoewijzingen** boven aan de pagina. De pagina roltoewijzingen bevat een lijst met alle gebruikers met RBAC-toegang voor het abonnement.

Zelfs als de toewijzingen voor [op rollen gebaseerde Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md) tijdens de overdracht worden verwijderd, hebben gebruikers in het oorspronkelijke eigenaars account mogelijk nog steeds toegang tot het abonnement via enkele beveiligings mechanismen, waaronder:

* Beheer certificaten waarmee de gebruikers beheerders rechten voor abonnements bronnen worden verleend. Zie [een beheer certificaat voor Azure maken en uploaden](../cloud-services/cloud-services-certs-create.md)voor meer informatie.
* Toegangs sleutels voor services zoals opslag. Zie [Over Azure-opslagaccounts](../storage/common/storage-create-storage-account.md) voor meer informatie.
* Externe toegangs referenties voor services zoals Azure Virtual Machines.

Als de ontvanger de toegang tot hun resources wil beperken, moet deze overwegen om de geheimen bij te werken die zijn gekoppeld aan de service. De meeste resources kunnen worden bijgewerkt met behulp van de volgende stappen:

  1. Meld u aan bij [Azure Portal](https://portal.azure.com).
  2. Selecteer **alle resources**in het menu hub.
  3. Selecteer de resource.
  4. Klik op de pagina resource op **instellingen**. Hier kunt u bestaande geheimen weer geven en bijwerken.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Als ik het eigendom van de facturering van een abonnement in het midden van de facturerings cyclus neem, moet ik dan betalen voor de volledige facturerings cyclus?

Uw account is verantwoordelijk voor de betaling van het gebruik dat wordt gerapporteerd vanaf het tijdstip van de overdracht. Er is mogelijk een deel van het gebruik dat plaatsvond voordat de overdracht is uitgevoerd. Het gebruik is opgenomen in de factuur van uw account.

### <a name="can-i-use-a-different-payment-method"></a>Kan ik een andere betalings wijze gebruiken?

Ja. Wanneer u de overdrachts aanvraag accepteert, kunt u een bestaande betalings methode selecteren die aan uw account is gekoppeld of een nieuwe betalings methode toevoegen.

## <a name="troubleshooting"></a>Problemen oplossen

### <a id="no-button"></a>Waarom zie ik de knop abonnement overdragen niet?

De overdracht van self-service abonnementen is niet beschikbaar voor uw facturerings account. Het is momenteel niet mogelijk om het eigendom van de facturering van abonnementen in Enterprise Agreement (EA)-accounts in de Azure Portal te verplaatsen. Daarnaast ondersteunen micro soft-gebruikers overeenkomst accounts die zijn gemaakt bij het werken met een micro soft-vertegenwoordiger geen ondersteuning voor het overdragen van de facturerings eigendom. 

### <a id="no-button"></a>Waarom ondersteunt mijn abonnements type geen overdracht? 

Niet alle soorten abonnementen bieden ondersteuning voor de overdracht van facturerings eigendom. Zie [ondersteunde abonnements typen](#supported-subscription-types) als u een lijst wilt weer geven met abonnements typen die ondersteuning bieden voor overdrachten.

### <a id="no-button"></a>Waarom ontvang ik een fout bericht over geweigerde toegang wanneer ik het eigendom van een abonnement wil overdragen? 

Deze fout wordt weer geven als u een abonnement van Microsoft Azure plan wilt overdragen en u niet over de benodigde machtigingen beschikt. U moet een eigenaar of bijdrager zijn voor het factuur gedeelte waarin het abonnement wordt gefactureerd. Microsoft Azure Zie [abonnementen voor factuur beheren](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section)voor meer informatie.


## <a name="need-help-contact-us"></a>Hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, kunt u [een ondersteunings aanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- De service beheerder, co-beheerders en andere RBAC-rollen controleren en bijwerken. Zie [Azure-abonnements beheerders toevoegen of wijzigen](billing-add-change-azure-subscription-administrator.md) en [toegang beheren met RBAC en de Azure Portal](../role-based-access-control/role-assignments-portal.md)voor meer informatie.
