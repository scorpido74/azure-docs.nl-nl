---
title: Eigendom van facturering voor een Azure-abonnement overdragen
description: Dit artikel bevat informatie over hoe u het eigendom van een Azure-abonnement overdraagt aan een ander account en enkele veelgestelde vragen over het proces
keywords: transfer azure subscription, azure transfer subscription, move azure subscription to another account,azure change subscription owner, transfer azure subscription to another account, azure transfer billing
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 722d1bca7f983c124c85e6d675f51d29c5357522
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85854944"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Eigendom van de facturering van een Azure-abonnement overdragen aan een ander account

Mogelijk wilt u het eigendom van de facturering van uw Azure-abonnement overdragen als u uw organisatie verlaat of wilt u dat het abonnement wordt gefactureerd aan een ander account. Door het eigendom van de facturering aan een ander account over te dragen, zijn de beheerders in het nieuwe account gemachtigd factureringstaken uit te voeren. Ze kunnen de betalingswijze veranderen, kosten bekijken en het abonnement annuleren.

Zie [Uw Azure-abonnement overzetten naar een andere aanbieding](switch-azure-offer.md) als het abonnement uw eigendom moet blijven maar u het type abonnement wilt wijzigen. Zie [Ingebouwde rollen van Azure](../../role-based-access-control/built-in-roles.md) als u wilt bepalen wie toegang hebben tot resources in het abonnement.

Als u een EA-klant (Enterprise Agreement) bent, kan uw ondernemingsbeheerder het eigendom van de facturering overdragen naar andere accounts. Zie [Eigendom van facturering overdragen voor EA-abonnementen (Enterprise Agreement)](#EA) voor meer informatie.

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Eigendom van facturering voor een Azure-abonnement overdragen

1. Meld u aan bij [Azure Portal](https://portal.azure.com) als beheerder van het factureringsaccount met het abonnement dat u wilt overdragen. Zie [Veelgestelde vragen](#faq)als u wilt weten of u een beheerder bent.

1. Zoek naar **kostenbeheer en facturering**.

   ![Schermopname van de zoekopdracht in de Azure-portal](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Selecteer in het linkerdeelvenster **Abonnementen**. Afhankelijk van de toegangsmogelijkheden moet u mogelijk een factureringsbereik en vervolgens **Abonnementen** of **Azure-abonnementen** selecteren.

1. Selecteer **Eigendom van facturering overdragen** voor het abonnement dat u wilt overdragen.

   ![Het abonnement selecteren dat u wilt overdragen](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Voer het e-mailadres in van een gebruiker die een factureringsbeheerder is voor het account dat de nieuwe eigenaar van het abonnement wordt.

1. Als u uw abonnement overdraagt naar een account in een andere Azure AD-tenant, selecteert u of u het abonnement wilt verplaatsen naar de tenant van het nieuwe account. Zie [Abonnementen overdragen naar een account in een andere Azure AD-tenant](#transfer-a-subscription-to-another-azure-ad-tenant-account) voor meer informatie

    > [!IMPORTANT]
    >
    > Als u ervoor kiest om het abonnement te verplaatsen naar de Azure AD-tenant van het nieuwe account, worden alle [Azure-roltoewijzingen](../../role-based-access-control/role-assignments-portal.md) voor toegang tot de resources in het abonnement definitief verwijderd. Alleen de gebruiker in het nieuwe account die uw overdrachtsaanvraag accepteert, heeft toegang om de resources in het abonnement te beheren. Zie de volgende sectie, [Een abonnement overdragen naar een account in een andere Azure AD-tenant](#transfer-a-subscription-to-another-azure-ad-tenant-account), voor meer informatie. U kunt eventueel het vinkje voor **Azure AD-tenant van abonnement** verwijderen om het eigendom van de facturering over te dragen zonder dat het abonnement naar de tenant van het nieuwe account wordt verplaatst. Als u dat doet, blijven bestaande Azure-roltoewijzingen voor toegang tot Azure-resources intact.

    ![Overdrachtspagina verzenden](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. Selecteer **Overdrachtsaanvraag verzenden**.

1. De gebruiker ontvangt een e-mail met instructies voor het beoordelen van uw overdrachtsaanvraag.

   ![E-mail voor de abonnementsoverdracht verzonden naar de ontvanger](./media/billing-subscription-transfer/billing-receiver-email.png)

1. De gebruiker kan de overdrachtsaanvraag goedkeuren door de koppeling in de e-mail te selecteren en de instructies te volgen. De gebruiker selecteert vervolgens een betalingswijze die wordt gebruikt om voor het abonnement te betalen. Als de gebruiker niet beschikt over een Azure-account, moet deze zich registreren voor een nieuw account.

   ![Eerste webpagina voor abonnementsoverdracht](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Tweede webpagina voor abonnementsoverdracht](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Tweede webpagina voor abonnementsoverdracht](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Gelukt! Het abonnement wordt nu overgedragen.

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>Een abonnement overdragen aan een ander account van uw Azure AD-tenant

Er wordt een Azure Active Directory-tenant (AD) voor u gemaakt wanneer u zich aanmeldt voor Azure. De tenant vertegenwoordigt uw account. U gebruikt de tenant om de toegang tot uw abonnementen en resources te beheren.

Wanneer u een nieuw abonnement maakt, wordt dit gehost in de Azure AD-tenant van uw account. Als u anderen toegang wilt geven tot uw abonnement of de bijbehorende resources, moet u ze uitnodigen om lid te worden van uw tenant. Zo kunt u de toegang tot uw abonnementen en resources beheren.

Wanneer u het eigendom van de facturering van uw abonnement overdraagt naar een account in een andere Azure AD-tenant, kunt u het abonnement verplaatsen naar de tenant van het nieuwe account. Als u dit doet, raken alle gebruikers, groepen of service-principals die [Azure-roltoewijzingen](../../role-based-access-control/role-assignments-portal.md) hadden om abonnementen en de bijbehorende resources te beheren, hun toegang kwijt. Alleen de gebruiker in het nieuwe account die uw overdrachtsaanvraag accepteert, heeft toegang om de resources te beheren. De nieuwe eigenaar moet deze gebruikers handmatig toevoegen aan het abonnement om toegang te bieden voor gebruikers die de toegang zijn kwijtgeraakt. Zie voor meer informatie [Transfer an Azure subscription to a different Azure AD directory (Preview)](../../role-based-access-control/transfer-subscription.md).


## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>Visual Studio- en Microsoft Partner Network-abonnementen overdragen

Voor Visual Studio- en Microsoft Partner Network-abonnementen geldt een maandelijks terugkerend Azure-tegoed. Wanneer u deze abonnementen overdraagt, is uw tegoed niet beschikbaar in het doelfactureringsaccount. Het tegoed in het doelfactureringsaccount wordt gebruikt voor het abonnement. Als Bob bijvoorbeeld op 9 september een Visual Studio Enterprise-abonnement overdraagt naar het account van Jeanette en Jeanette de overdracht accepteert. Zodra de overdracht is voltooid, wordt het tegoed in het account van Jeanette gebruikt voor het abonnement. Het tegoed wordt elke negende dag van de maand opnieuw ingesteld.


<a id="EA"></a>

## <a name="transfer-ea-subscription-billing-ownership"></a>Eigendom van facturering van EA-abonnementen overdragen

De ondernemingsbeheerder kan het eigendom van abonnementen overdragen tussen accounts in een inschrijving. Zie [Accounteigenaar wijzigen](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-get-started#change-account-owner) in de EA-portal voor meer informatie.

## <a name="next-steps-after-accepting-billing-ownership"></a>Volgende stappen na het accepteren van het eigendom van facturering

Als u het eigendom van de facturering van een Azure-abonnement hebt geaccepteerd, is het raadzaam de volgende stappen te volgen:

1. Controleer de servicebeheerder, co-beheerders en andere Azure-roltoewijzingen en werk ze bij. Zie [Toevoegen of wijzigen van de beheerders van Azure-abonnementen](add-change-subscription-administrator.md) en [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md) voor meer informatie.
1. Referenties bijwerken die zijn gekoppeld aan de services van dit abonnement, waaronder:
   1. Beheercertificaten die de gebruiker beheerdersrechten verlenen voor de abonnementsresources. Zie [Een beheercertificaat voor Azure maken en uploaden](../../cloud-services/cloud-services-certs-create.md) voor meer informatie
   1. Toegangssleutels voor services zoals Storage. Zie [Over Azure-opslagaccounts](../../storage/common/storage-create-storage-account.md) voor meer informatie
   1. Referenties voor externe toegang voor services zoals Azure Virtual Machines.
1. Als u met een partner werkt, kunt u overwegen om de partner-id voor dit abonnement bij te werken. U kunt de partner-id bijwerken in [Azure Portal](https://portal.azure.com). Zie [Een partner-id aan uw Azure-accounts koppelen](link-partner-id.md) voor meer informatie

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Ondersteunde abonnementstypen

De onderstaande typen abonnementen kunnen in Azure Portal worden overgedragen. Momenteel kunnen er geen [gratis proefabonnementen](https://azure.microsoft.com/offers/ms-azr-0044p/) of [AIO-abonnementen (Azure in Open)](https://azure.microsoft.com/offers/ms-azr-0111p/) worden overgedragen. Zie [Resources naar een nieuwe groep of nieuw abonnement verplaatsen](../../azure-resource-manager/management/move-resource-group-and-subscription.md) voor een tijdelijke oplossing. Als u andere abonnementen, zoals ondersteuningsplannen wilt overdragen, [neemt u contact op met de ondersteuning voor Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Visual Studio Enterprise (MPN)-abonnees](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [Via de EA Portal](#EA).

\*\* Alleen ondersteund voor accounts die worden gemaakt tijdens het aanmelden op de Azure-website.

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Veelgestelde vragen (FAQ) voor verzenders

Deze veelgestelde vragen zijn van toepassing op gebruikers die het eigendom van de facturering voor een Azure-abonnement overdragen naar een ander account.

### <a name="who-is-a-billing-administrator-of-an-account"></a><a name="whoisaa"></a> Wie is een factureringsbeheerder van een account?

Een factureringsbeheerder is een persoon die gemachtigd is om de facturering voor een account te beheren. Ze hebben toegang tot de facturering in de [Azure-portal](https://portal.azure.com) en kunnen diverse factureringstaken uitvoeren. Ze kunnen bijvoorbeeld abonnementen maken, facturen weergeven en betalen van facturen of betalingswijzen bijwerken.

Voer de volgende stappen uit om te achterhalen voor welke accounts u een factureringsbeheerder bent:

1. Ga in Azure Portal naar de pagina [Kostenbeheer en facturering](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Selecteer in het linkerdeelvenster **Alle factureringsbereiken**.
1. De pagina met abonnementen bevat alle abonnementen waarvoor u factureringsbeheerder bent.

Als u niet zeker weet wie de accountbeheerder is voor een abonnement, gebruikt u de volgende stappen om erachter te komen.

1. Ga naar de [pagina Abonnementen in de Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecteer het abonnement dat u wilt controleren en kijk vervolgens bij **Instellingen**.
1. Selecteer **Eigenschappen**. De accountbeheerder van het abonnement wordt weergegeven in het vak **Accountbeheerder**.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Wordt alles overgedragen? Inclusief resourcegroepen, Vm's, schijven en andere actieve services?

Alle resources zoals VM's, schijven en websites worden overgedragen naar het nieuwe account. Als u echter een abonnement overdraagt naar een account in een andere Azure AD-tenant, worden de [beheerdersrollen](add-change-subscription-administrator.md) en [Azure-roltoewijzingen](../../role-based-access-control/role-assignments-portal.md) voor het abonnement [niet overgedragen](#transfer-a-subscription-to-another-azure-ad-tenant-account). Bovendien worden [app-registraties](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) en andere tenantspecifieke services niet overgedragen met het abonnement.

### <a name="can-i-transfer-ownership-to-an-account-in-another-countryregion"></a>Kan ik het eigendom overdragen naar een account in een ander land of andere regio?
U kunt geen overdrachten naar andere landen/regio's uitvoeren in de Azure Portal. Als u uw abonnement wilt overdragen naar landen/regio's, [neemt u contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Ik ben een beheerder van twee accounts. Kan ik een abonnement overdragen van het ene account naar de andere?
Ja, u kunt een abonnement overdragen tussen uw accounts. Uw accounts worden conceptueel gezien als accounts van twee verschillende gebruikers, zodat u de bovenstaande stappen kunt gebruiken om abonnementen tussen uw accounts over te dragen.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Resulteert een abonnementsoverdracht in servicedowntime?

Als u een abonnement overdraagt naar een account in dezelfde Azure AD-tenant, is dit niet van invloed op de actieve resources in het abonnement. Contextgegevens die zijn opgeslagen in PowerShell, worden echter niet bijgewerkt, dus moet u deze mogelijk wissen of instellingen wijzigen. Als u het abonnement overdraagt naar een account in een andere tenant en besluit het abonnement naar de tenant te verplaatsen, verliezen alle gebruikers, groepen en service-principals die [Azure-roltoewijzingen](../../role-based-access-control/role-assignments-portal.md) hadden voor toegang tot resources in het abonnement, hun toegang. Uitvaltijd van de service kan het gevolg zijn.

### <a name="can-users-in-new-account-access-usage-and-billing-history"></a>Hebben gebruikers in het nieuwe account toegang tot de gebruiks- en factureringsgeschiedenis?

De enige informatie die beschikbaar is voor de gebruikers in het nieuwe account, zijn de abonnementskosten voor de afgelopen maand. De rest van de gebruiks- en factureringsgeschiedenis wordt niet overgedragen met het abonnement

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Hoe migreer ik gegevens en services voor mijn Azure-abonnement naar een nieuw abonnement?

Als u het eigendom van het abonnement niet kunt overdragen, kunt u uw resources handmatig te migreren. Zie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Als ik een Visual Studio- of Microsoft Partner Network-abonnement overdraag, wordt mijn tegoed dan getransporteerd naar het abonnement in het nieuwe account?

Nee, uw tegoed is niet beschikbaar in het nieuwe account. De gebruiker die de overdrachtsaanvraag accepteert, moet een Visual Studio-licentie hebben om de overdrachtsaanvraag te accepteren. Het abonnement maakt gebruik van het Visual Studio-tegoed dat beschikbaar is in het account van de gebruiker. Zie [Abonnementen voor Visual Studio en Partner Network overdragen](#transfer-visual-studio-and-partner-network-subscriptions) voor meer informatie.


## <a name="frequently-asked-questions-faq-for-recipients"></a>Veelgestelde vragen (FAQ) voor ontvangers

Deze veelgestelde vragen zijn van toepassing op gebruikers die het eigendom van de facturering voor een Azure-abonnement van een ander account accepteren.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Als ik het eigendom van de facturering van een abonnement van een ander account overneem, hebben gebruikers in dat account dan nog steeds toegang tot mijn resources?

Ja. [Beheerdersrollen](add-change-subscription-administrator.md) en [Azure-roltoewijzingen](../../role-based-access-control/role-assignments-portal.md) kunnen echter worden verwijderd. De toegang wordt verbroken als uw account zich in een andere Azure AD-tenant bevindt dan de tenant van het abonnement en de gebruiker die de overdrachtaanvraag heeft verzonden, het abonnement verplaatst naar de tenant van uw account. Als u de gebruikers wilt weergeven die Azure-roltoewijzingen hebben om toegang te krijgen tot de resources in het abonnement, gebruikt u de volgende stappen:

1. Ga naar de [pagina Abonnement in Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecteer het abonnement dat u wilt controleren en selecteer in het linkerdeelvenster vervolgens **Toegangsbeheer (IAM)** .
1. Selecteer bovenaan de pagina **Roltoewijzingen**. De pagina met roltoewijzingen bevat een overzicht van alle gebruikers met toegang voor het abonnement.

Zelfs als de [Azure-roltoewijzingen](../../role-based-access-control/role-assignments-portal.md) tijdens de overdracht worden verwijderd, hebben gebruikers in het account van de oorspronkelijke eigenaar mogelijk nog toegang tot het abonnement via andere beveiligingsmechanismen, waaronder:

* Beheercertificaten die de gebruiker beheerdersrechten verlenen voor de abonnementsresources. Zie [Een beheercertificaat voor Azure maken en uploaden](../../cloud-services/cloud-services-certs-create.md) voor meer informatie.
* Toegangssleutels voor services zoals Storage. Zie [Over Azure-opslagaccounts](../../storage/common/storage-create-storage-account.md) voor meer informatie.
* Referenties voor externe toegang voor services zoals Azure Virtual Machines.

Als ontvangers de toegang tot hun resources willen beperken, moeten ze overwegen om de geheimen bij te werken die zijn gekoppeld aan de service. De meeste resources kunnen worden bijgewerkt door de volgende stappen uit te voeren:

  1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
  2. Selecteer in het Hub-menu **Alle resources**.
  3. Selecteer de resource.
  4. Selecteer **Instellingen** op de resourcepagina. Hier kunt u bestaande geheimen weergeven en bijwerken.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Als ik het eigendom van de facturering tijdens een factureringscyclus overneem, moet ik dan betalen voor de volledige factureringscyclus?

Uw account is verantwoordelijk voor de betaling van het gebruik dat wordt gerapporteerd vanaf het tijdstip van de overdracht. Het is mogelijk dat bepaald gebruik dat plaatsvond vóór de overdracht, pas daarna wordt gerapporteerd. Het gebruik is opgenomen in de factuur van uw account.

### <a name="can-i-use-a-different-payment-method"></a>Kan ik een andere betalingswijze gebruiken?

Ja. Wanneer u de overdrachtsaanvraag accepteert, kunt u een bestaande betalingswijze selecteren die aan uw account is gekoppeld of een nieuwe betalingswijze toevoegen.

### <a name="how-can-i-transfer-ownership-of-my-enterprise-agreement-ea-subscription-account-ownership-if-the-original-account-owner-is-no-longer-with-the-organization"></a>Hoe kan ik het accounteigendom van mijn EA-abonnement (Enterprise Agreement) overdragen als de eigenaar van het oorspronkelijke account niet langer voor de organisatie werkt?

De Enterprise-beheerder kan het eigendom voor elk account bijwerken, zelfs nadat de eigenaar van het oorspronkelijke account niet langer deel uitmaakt van de organisatie. Ze kunnen dit doen door de instructies te volgen voor [het overdragen van het accounteigendom voor alle abonnementen](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) in de EA-portal.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="why-dont-i-see-the-transfer-subscription-button"></a><a id="no-button"></a> Waarom zie ik de knop Abonnement overdragen niet?

De overdracht van selfserviceabonnementen is niet beschikbaar voor uw factureringsaccount. Het is momenteel niet mogelijk om het eigendom van de facturering van abonnementen in EA-accounts (Enterprise Agreement) in Azure Portal te verplaatsen. Bovendien bieden Microsoft Customer Agreement-accounts die zijn gemaakt in samenwerking met een vertegenwoordiger van Microsoft, geen ondersteuning voor de overdracht van het eigendom van de facturering.

### <a name="why-doesnt-my-subscription-type-support-transfer"></a><a id="no-button"></a> Waarom biedt mijn type abonnement geen ondersteuning voor overdrachten?

Niet alle typen abonnementen bieden ondersteuning voor de overdracht van het eigendom van de facturering. Zie [Ondersteunde typen abonnementen](#supported-subscription-types) voor een overzicht van typen abonnementen die overdrachten ondersteunen

### <a name="why-am-i-receiving-an-access-denied-error-when-i-try-to-transfer-billing-ownership-of-a-subscription"></a><a id="no-button"></a> Waarom ontvang ik een foutbericht dat de toegang is geweigerd wanneer ik het eigendom van een abonnement wil overdragen?

Deze fout wordt weergegeven wanneer u een Microsoft Azure Plan-abonnement probeert over te dragen en u niet over de benodigde machtigingen beschikt. Als u een Microsoft Azure Plan-abonnement wilt overdragen, moet u een eigenaar of bijdrager zijn in de factuursectie waarnaar het abonnement wordt gefactureerd. Zie [Abonnementen beheren voor de factuursectie](understand-mca-roles.md#manage-subscriptions-for-invoice-section) voor meer informatie.


## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- Controleer de servicebeheerder, co-beheerders en andere Azure-roltoewijzingen en werk ze bij. Zie [Toevoegen of wijzigen van de beheerders van Azure-abonnementen](add-change-subscription-administrator.md) en [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md) voor meer informatie.
