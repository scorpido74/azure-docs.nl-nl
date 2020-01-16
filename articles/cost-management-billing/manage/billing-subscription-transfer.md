---
title: Eigendom van facturering voor een Azure-abonnement overdragen
description: Dit artikel bevat informatie over hoe u het eigendom van een Azure-abonnement overdraagt aan een ander account en enkele veelgestelde vragen over het proces
keywords: transfer azure subscription, azure transfer subscription, move azure subscription to another account,azure change subscription owner, transfer azure subscription to another account, azure transfer billing
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6f43f7424cddae8659301be0ddc4842412a62952
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75993542"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Eigendom van de facturering van een Azure-abonnement overdragen aan een ander account

Mogelijk wilt u het eigendom van de facturering van uw Azure-abonnement overdragen als u uw organisatie verlaat of wilt u dat het abonnement wordt gefactureerd aan een ander account. Het overdragen van het eigendom van de facturering naar een ander account geeft de beheerders in het nieuwe account de mogelijkheid om factureringstaken uit te voeren, zoals het wijzigen van de betalingswijze, het weergeven van kosten en het annuleren van het abonnement.

Zie [Uw Azure-abonnement overzetten naar een andere aanbieding](switch-azure-offer.md) als het abonnement uw eigendom moet blijven maar u het type abonnement wilt wijzigen. Zie [Ingebouwde rollen voor Azure-resources](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) als u wilt bepalen wie resources in het abonnement kan beheren.

Als u een EA-klant (Enterprise Agreement) bent, kan uw ondernemingsbeheerder het eigendom van de facturering overdragen naar andere accounts. Zie [Eigendom van facturering overdragen voor EA-abonnementen (Enterprise Agreement)](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions) voor meer informatie.

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Eigendom van facturering voor een Azure-abonnement overdragen

1. Meld u aan bij [Azure Portal](https://portal.azure.com) als beheerder van het factureringsaccount met het abonnement dat u wilt overdragen. Zie [Veelgestelde vragen](#faq)als u wilt weten of u een beheerder bent.

1. Zoek naar **kostenbeheer en facturering**.

   ![Schermopname van zoekopdracht in de Azure-portal](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Selecteer in het linkerdeelvenster **Abonnementen**. Afhankelijk van de toegangsmogelijkheden moet u mogelijk een factureringsbereik en vervolgens **Abonnementen** of **Azure-abonnementen** selecteren.

1. Selecteer **Eigendom van facturering overdragen** voor het abonnement dat u wilt overdragen.

   ![Het abonnement selecteren dat u wilt overdragen](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Voer het e-mailadres in van een gebruiker die een factureringsbeheerder is voor het account dat de nieuwe eigenaar van het abonnement wordt.

1. Als u uw abonnement overdraagt naar een account in een andere Azure AD-tenant, selecteert u of u het abonnement wilt verplaatsen naar de tenant van het nieuwe account. Zie [Abonnementen overdragen naar een account in een andere Azure AD-tenant](#transferring-subscription-to-an-account-in-another-azure-ad-tenant) voor meer informatie

    > [!IMPORTANT]
    >
    > Als u ervoor kiest om het abonnement te verplaatsen naar de Azure AD-tenant van het nieuwe account, worden alle [RBAC-toewijzingen](../../role-based-access-control/overview.md) (op rollen gebaseerd toegangsbeheer) voor het beheren van de resources in het abonnement definitief verwijderd. Alleen de gebruiker in het nieuwe account die uw overdrachtsaanvraag accepteert, heeft toegang om de resources in het abonnement te beheren. Zie [Abonnementen overdragen naar een gebruiker in een andere Azure AD-tenant](../../cognitive-services/acoustics/known-issues.md) voor meer informatie. U kunt eventueel het vinkje voor Subscription Azure AD-tenant verwijderen om het eigendom van de facturering over te dragen zonder dat het abonnement naar de tenant van het nieuwe account wordt verplaatst. Als u dit doet, blijven de bestaande RBAC-machtigingen voor het beheer van Azure-resources behouden.

    ![Overdrachtspagina verzenden](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. Selecteer **Overdrachtsaanvraag verzenden**.

1. De gebruiker ontvangt een e-mail met instructies voor het beoordelen van uw overdrachtsaanvraag.

   ![E-mail voor de abonnementsoverdracht verzonden naar de ontvanger](./media/billing-subscription-transfer/billing-receiver-email.png)

1. De gebruiker kan de overdrachtsaanvraag goedkeuren door de koppeling in de e-mail te selecteren en de instructies te volgen. De gebruiker moet een betalingswijze selecteren die wordt gebruikt om voor het abonnement te betalen. Als de gebruiker niet beschikt over een Azure-account, moet deze zich registreren voor een nieuw account.

   ![Eerste webpagina voor abonnementsoverdracht](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Tweede webpagina voor abonnementsoverdracht](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Tweede webpagina voor abonnementsoverdracht](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Gelukt! Het abonnement wordt nu overgedragen.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>Abonnement wordt overgedragen naar een account in een andere Azure AD-tenant

Er wordt een Azure Active Directory-tenant (AD) voor u gemaakt wanneer u zich aanmeldt voor Azure. De tenant vertegenwoordigt uw account. U gebruikt de tenant om de toegang tot uw abonnementen en resources te beheren.

Wanneer u een nieuw abonnement maakt, wordt dit gehost in de Azure AD-tenant van uw account. Als u anderen toegang wilt geven tot uw abonnement of de bijbehorende resources, moet u ze uitnodigen om lid te worden van uw tenant. Zo kunt u de toegang tot uw abonnementen en resources beheren.

Wanneer u het eigendom van de facturering van uw abonnement overdraagt naar een account in een andere Azure AD-tenant, kunt u het abonnement verplaatsen naar de tenant van het nieuwe account. Als u dit doet, raken alle gebruikers, groepen of service-principals die [op rollen gebaseerde toegang (RBAC)](../../role-based-access-control/role-assignments-portal.md) hadden om abonnementen en de bijbehorende resources te beheren, hun toegang kwijt. Alleen de gebruiker in het nieuwe account die uw overdrachtsaanvraag accepteert, heeft toegang om de resources te beheren. Als u de oorspronkelijke gebruikers opnieuw toegang wilt verlenen, moet de nieuwe eigenaar [deze gebruikers handmatig toevoegen aan het abonnement](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).


## <a name="transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Abonnementen voor Visual Studio, MPN en Betalen per gebruik Dev/Test overdragen

Voor Visual Studio- en Microsoft Partner Network-abonnementen geldt een maandelijks terugkerend Azure-tegoed. Wanneer u deze abonnementen overdraagt, is uw tegoed niet beschikbaar in het doelfactureringsaccount. Het tegoed in het doelfactureringsaccount wordt gebruikt voor het abonnement. Als Bob bijvoorbeeld op 9 september een Visual Studio Enterprise-abonnement overdraagt naar het account van Jeanette en Jeanette de overdracht accepteert. Zodra de overdracht is voltooid, wordt het tegoed in het account van Jeanette gebruikt voor het abonnement. Het tegoed wordt elke maand op 9de opnieuw ingesteld.


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Eigendom van facturering overdragen voor EA-abonnementen (Enterprise Agreement)

De ondernemingsbeheerder kan het eigendom van abonnementen overdragen tussen accounts in een inschrijving. Zie [Accounteigendom overdragen](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) in EA Portal.

## <a name="next-steps-after-accepting-billing-ownership"></a>Volgende stappen na het accepteren van het eigendom van facturering

Als u het eigendom van de facturering van een Azure-abonnement hebt geaccepteerd, is het raadzaam de volgende stappen te volgen:

1. Controleer de servicebeheerders, co-beheerder en andere RBAC-rollen en werk ze bij. Zie [Toevoegen of wijzigen van de beheerders van Azure-abonnementen](add-change-subscription-administrator.md) en [Toegang beheren met behulp van RBAC en Azure Portal](../../role-based-access-control/role-assignments-portal.md) voor meer informatie.
1. Referenties bijwerken die zijn gekoppeld aan de services van dit abonnement, waaronder:
   1. Beheercertificaten die de gebruiker beheerdersrechten verlenen voor de abonnementsresources. Zie [Een beheercertificaat voor Azure maken en uploaden](../../cloud-services/cloud-services-certs-create.md) voor meer informatie
   1. Toegangssleutels voor services zoals Storage. Zie [Over Azure-opslagaccounts](../../storage/common/storage-create-storage-account.md) voor meer informatie
   1. Referenties voor externe toegang voor services zoals Azure Virtual Machines.
1. Als u met een partner werkt, kunt u overwegen om de partner-id voor dit abonnement bij te werken. U kunt de partner-id bijwerken in [Azure Portal](https://portal.azure.com). Zie [Een partner-id aan uw Azure-accounts koppelen](link-partner-id.md) voor meer informatie

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Ondersteunde abonnementstypen

De onderstaande typen abonnementen kunnen in Azure Portal worden overgedragen. Momenteel kunnen er geen [gratis proefabonnementen](https://azure.microsoft.com/offers/ms-azr-0044p/) of [AIO-abonnementen (Azure in Open)](https://azure.microsoft.com/offers/ms-azr-0111p/) worden overgedragen. Zie [Resources naar een nieuwe groep of nieuw abonnement verplaatsen](../../azure-resource-manager/management/move-resource-group-and-subscription.md) voor een tijdelijke oplossing. Als u andere abonnementen, zoals [Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) of ondersteuningsplannen wilt overdragen, [neemt u contact op met Azure Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Visual Studio Enterprise (MPN)-abonnees](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [via de EA-Portal](#EA).

\*\* Alleen ondersteund voor accounts die worden gemaakt tijdens het aanmelden op de Azure-website.

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Veelgestelde vragen (FAQ) voor verzenders

Deze veelgestelde vragen zijn van toepassing op gebruikers die het eigendom van de facturering voor een Azure-abonnement overdragen naar een ander account.

### <a name="whoisaa"></a> Wie is een factureringsbeheerder van een account?

Een factureringsbeheerder is een persoon die gemachtigd is om de facturering voor een account te beheren. Ze hebben toegang tot de facturering in [Azure Portal](https://portal.azure.com) en kunnen diverse factureringstaken uitvoeren. Ze kunnen bijvoorbeeld abonnementen maken, facturen weergeven en betalen van facturen of betalingswijzen bijwerken.

Voer de volgende stappen uit om te achterhalen voor welke accounts u een factureringsbeheerder bent:

1. Ga in Azure Portal naar de pagina [Kostenbeheer en facturering](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Selecteer in het linkerdeelvenster **Alle factureringsbereiken**.
1. De pagina met abonnementen bevat alle abonnementen waarvoor u een factureringsbeheerder bent.

Als u niet zeker weet wie de accountbeheerder is voor een abonnement, gebruikt u de volgende stappen om erachter te komen.

1. Ga naar de [pagina Abonnementen in de Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecteer het abonnement dat u wilt controleren en kijk vervolgens bij **Instellingen**.
1. Selecteer **Eigenschappen**. De accountbeheerder van het abonnement wordt weergegeven in het vak **Accountbeheerder**.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Wordt alles overgedragen? Inclusief resourcegroepen, Vm's, schijven en andere actieve services?

Alle resources zoals VM's, schijven en websites worden overgedragen naar het nieuwe account. Als u echter een abonnement overdraagt naar een account in een andere Azure AD-tenant, worden de [beheerdersrollen](add-change-subscription-administrator.md) en het [ RBAC-toewijzingen (op rollen gebaseerd toegangsbeheer)](../../role-based-access-control/role-assignments-portal.md) voor het abonnement [niet overgedragen](#transferring-subscription-to-an-account-in-another-azure-ad-tenant). Bovendien worden [app-registraties](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) en andere tenantspecifieke services niet overgedragen met het abonnement.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>Kan ik het eigendom overdragen naar een account in een ander land?
U kunt geen overdrachten naar andere landen uitvoeren in Azure Portal. Als u uw abonnement wilt overdragen naar een ander land, [neemt u contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Ik ben een beheerder van twee accounts. Kan ik een abonnement overdragen van het ene account naar de andere?
Ja, u kunt een abonnement overdragen tussen uw accounts. Uw accounts worden conceptueel gezien als accounts van twee verschillende gebruikers, zodat u de bovenstaande stappen kunt gebruiken om abonnementen tussen uw accounts over te dragen.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Resulteert een abonnementsoverdracht in servicedowntime?

Als u een abonnement overdraagt naar een account in dezelfde Azure AD-tenant, is dit niet van invloed op de actieve resources in het abonnement. Als u het abonnement echter overdraagt naar een account in een andere tenant en besluit het abonnement naar de tenant te verplaatsen, verliezen alle gebruikers, groepen en service-principals met [op rollen gebaseerde toegang (RBAC)](../../role-based-access-control/overview.md) voor het beheren van de resources in het abonnement, hun toegang. Dit kan resulteren in servicedowntime.

### <a name="do-users-in-new-account-have-access-to-usage-and-billing-history"></a>Hebben gebruikers in het nieuwe account toegang tot de gebruiks- en factureringsgeschiedenis?

De enige informatie die beschikbaar is voor de gebruikers in het nieuwe account, zijn de abonnementskosten voor de afgelopen maand. De rest van de gebruiks- en factureringsgeschiedenis wordt niet overgedragen met het abonnement

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Hoe migreer ik gegevens en services voor mijn Azure-abonnement naar een nieuw abonnement?

Als u het eigendom van het abonnement niet kunt overdragen, kunt u uw resources handmatig te migreren. Zie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Als ik een Visual Studio- of Microsoft Partner Network-abonnement overdraag, wordt mijn tegoed dan getransporteerd naar het abonnement in het nieuwe account?

Nee, uw tegoed is niet beschikbaar in het nieuwe account. De gebruiker die de overdrachtsaanvraag accepteert, moet een Visual Studio-licentie hebben om de overdrachtsaanvraag te accepteren. Het abonnement maakt gebruik van het Visual Studio-tegoed dat beschikbaar is in het account van de gebruiker. Zie [Abonnementen voor Visual Studio, MPN (Microsoft Partner Network) en Betalen per gebruik Dev/Test overdragen](#transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions) voor meer informatie.


## <a name="frequently-asked-questions-faq-for-recipients"></a>Veelgestelde vragen (FAQ) voor ontvangers

Deze veelgestelde vragen zijn van toepassing op gebruikers die het eigendom van de facturering voor een Azure-abonnement van een ander account accepteren.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Als ik het eigendom van de facturering van een abonnement van een ander account overneem, hebben gebruikers in dat account dan nog steeds toegang tot mijn resources?

Ja. Als uw account zich echter in een Azure AD-tenant bevindt die afwijkt van de tenant van het abonnement en de gebruiker die de overdrachtsaanvraag heeft verzonden, het abonnement verplaatst naar de tenant van uw account, worden de [beheerdersrollen](add-change-subscription-administrator.md) en [RBAC-toewijzingen (op rollen gebaseerd toegangsbeheer)](../../role-based-access-control/role-assignments-portal.md) verwijderd. Als u de gebruikers wilt weergeven die [toegang op basis van rollen (RBAC)](../../role-based-access-control/overview.md) hebben om de resources in het abonnement te beheren, gebruikt u de volgende stappen:

1. Ga naar de [pagina Abonnement in Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecteer het abonnement dat u wilt controleren en selecteer in het linkerdeelvenster vervolgens **Toegangsbeheer (IAM)** .
1. Selecteer bovenaan de pagina **Roltoewijzingen**. De pagina met roltoewijzingen bevat een overzicht van alle gebruikers met RBAC-toegang voor het abonnement.

Zelfs als de [RBAC-toewijzingen (op rollen gebaseerd toegangsbeheer)](../../role-based-access-control/role-assignments-portal.md) tijdens de overdracht worden verwijderd, hebben gebruikers in het account van de oorspronkelijke eigenaar via beveiligingsmechanismen mogelijk nog toegang tot het abonnement, inclusief:

* Beheercertificaten die de gebruiker beheerdersrechten verlenen voor de abonnementsresources. Zie [Een beheercertificaat voor Azure maken en uploaden](../../cloud-services/cloud-services-certs-create.md) voor meer informatie.
* Toegangssleutels voor services zoals Storage. Zie [Over Azure-opslagaccounts](../../storage/common/storage-create-storage-account.md) voor meer informatie.
* Referenties voor externe toegang voor services zoals Azure Virtual Machines.

Als ontvangers de toegang tot hun resources willen beperken, moeten ze overwegen om de geheimen bij te werken die zijn gekoppeld aan de service. De meeste resources kunnen worden bijgewerkt door de volgende stappen uit te voeren:

  1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
  2. Selecteer in het Hub-menu **Alle resources**.
  3. Selecteer de resource.
  4. Klik op de resourcepagina op **Instellingen**. Hier kunt u bestaande geheimen weergeven en bijwerken.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Als ik het eigendom van de facturering tijdens een factureringscyclus overneem, moet ik dan betalen voor de volledige factureringscyclus?

Uw account is verantwoordelijk voor de betaling van het gebruik dat wordt gerapporteerd vanaf het tijdstip van de overdracht. Het is mogelijk dat bepaald gebruik dat plaatsvond vóór de overdracht, pas daarna wordt gerapporteerd. Het gebruik is opgenomen in de factuur van uw account.

### <a name="can-i-use-a-different-payment-method"></a>Kan ik een andere betalingswijze gebruiken?

Ja. Wanneer u de overdrachtsaanvraag accepteert, kunt u een bestaande betalingswijze selecteren die aan uw account is gekoppeld of een nieuwe betalingswijze toevoegen.

### <a name="how-can-i-transfer-ownership-of-my-enterprise-agreement-ea-subscription-account-ownership-if-the-original-account-owner-is-no-longer-with-the-organization"></a>Hoe kan ik het accounteigendom van mijn EA-abonnement (Enterprise Agreement) overdragen als de eigenaar van het oorspronkelijke account niet langer voor de organisatie werkt?

De Enterprise-beheerder kan het eigendom voor elk account bijwerken, zelfs nadat de eigenaar van het oorspronkelijke account niet langer deel uitmaakt van de organisatie. Ze kunnen dit doen door de instructies te volgen voor [het overdragen van het accounteigendom voor alle abonnementen](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) in de EA-portal.

## <a name="troubleshooting"></a>Problemen oplossen

### <a id="no-button"></a> Waarom zie ik de knop Abonnement overdragen niet?

De overdracht van selfserviceabonnementen is niet beschikbaar voor uw factureringsaccount. Het is momenteel niet mogelijk om het eigendom van de facturering van abonnementen in EA-accounts (Enterprise Agreement) in Azure Portal te verplaatsen. Bovendien bieden Microsoft Customer Agreement-accounts die zijn gemaakt in samenwerking met een vertegenwoordiger van Microsoft, geen ondersteuning voor de overdracht van het eigendom van de facturering.

### <a id="no-button"></a> Waarom biedt mijn type abonnement geen ondersteuning voor overdrachten?

Niet alle typen abonnementen bieden ondersteuning voor de overdracht van het eigendom van de facturering. Zie [Ondersteunde typen abonnementen](#supported-subscription-types) voor een overzicht van typen abonnementen die overdrachten ondersteunen

### <a id="no-button"></a> Waarom ontvang ik een foutbericht dat de toegang is geweigerd wanneer ik het eigendom van een abonnement wil overdragen?

Deze fout wordt weergegeven wanneer u een Microsoft Azure Plan-abonnement probeert over te dragen en u niet over de benodigde machtigingen beschikt. Als u een Microsoft Azure Plan-abonnement wilt overdragen, moet u een eigenaar of bijdrager zijn in de factuursectie waarnaar het abonnement wordt gefactureerd. Zie [Abonnementen beheren voor de factuursectie](understand-mca-roles.md#manage-subscriptions-for-invoice-section) voor meer informatie.


## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u een vraag wilt stellen of hulp nodig hebt, maakt u een [ondersteuningsaanvraag](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- Controleer de servicebeheerders, co-beheerder en andere RBAC-rollen en werk ze bij. Zie [Toevoegen of wijzigen van de beheerders van Azure-abonnementen](add-change-subscription-administrator.md) en [Toegang beheren met behulp van RBAC en Azure Portal](../../role-based-access-control/role-assignments-portal.md) voor meer informatie.
