---
title: Beheer van Azure EA Portal
description: In dit artikel worden de algemene taken beschreven die een beheerder in Azure EA Portal uitvoert.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/25/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 87947fb29d8fa7dd86818caef139e776c04c7650
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71308093"
---
# <a name="azure-ea-portal-administration"></a>Beheer van Azure EA Portal

In dit artikel worden de algemene taken beschreven die een beheerder in Azure EA Portal uitvoert (https://ea.azure.com). Azure EA Portal is een online beheerportal waarmee klanten de kosten van hun Azure EA-services kunnen beheren. Zie het artikel [Aan de slag met Azure EA Portal](billing-ea-portal-get-started.md) voor inleidende informatie over Azure EA Portal.

## <a name="add-a-new-enterprise-administrator"></a>Een nieuwe ondernemingsbeheerder toevoegen

Ondernemingsbeheerders beschikken over de meeste bevoegdheden voor het beheren van een Azure EA-inschrijving. De eerste Azure EA-beheerder is gemaakt toen de EA-overeenkomst is gesloten. U kunt echter op elk moment nieuwe beheerders toevoegen of verwijderen. Nieuwe beheerders kunnen alleen worden toegevoegd door bestaande beheerders. Zie [Een andere ondernemingsbeheerder maken](billing-ea-portal-get-started.md#create-another-enterprise-admin) voor meer informatie over het toevoegen van ondernemingsbeheerders. Zie [Rollen en taken voor factureringsprofiel ](billing-understand-mca-roles.md#billing-profile-roles-and-tasks) voor meer informatie over de rollen en taken voor het factureringsprofiel.

## <a name="update-user-state-from-pending-to-active"></a>De status van een gebruiker bijwerken van in behandeling naar actief

Wanneer nieuwe accounteigenaren (AO) voor de eerste keer worden toegevoegd aan een Azure EA-inschrijving, krijgen ze de status _In behandeling_. Zodra een nieuwe accounteigenaar de welkomstmail voor de activering ontvangt, kan deze zich aanmelden om het account te activeren. Wanneer het account wordt geactiveerd, wordt de accountstatus bijgewerkt van _In behandeling_ naar _Actief_. Nieuwe gebruikers worden mogelijk gevraagd hun voor- en achternaam op te geven om een Commerce-account te maken. Als dat het geval is, moeten ze de vereiste informatie toevoegen om door te gaan, waarna het account wordt geactiveerd.

## <a name="add-a-department-admin"></a>Een afdelingsbeheerder toevoegen

Nadat een Azure EA-beheerder een afdeling heeft gemaakt, kan de Azure Enterprise-beheerder afdelingsbeheerders toevoegen en deze koppelen aan een afdeling. Een afdelingsbeheerder kan nieuwe accounts maken. Nieuwe accounts zijn nodig om nieuwe Azure EA-abonnementen te maken.

Raadpleeg [Een Azure EA-afdelingsbeheerder](billing-ea-portal-get-started.md#add-a-department-admin) maken voor meer informatie over het toevoegen van een afdelingsbeheerder.

## <a name="enterprise-user-roles"></a>Enterprise-gebruikersrollen

Met Azure EA Portal kunt u de kosten en het gebruik van Azure EA beheren. Er zijn drie hoofdrollen in Azure EA Portal:

- EA-beheerder
- Afdelingsbeheerder
- Accounteigenaar

Elke rol heeft een ander toegangs- en autoriteitsniveau.

Zie Enterprise-gebruikersrollen voor meer informatie over rollen.

## <a name="add-an-azure-ea-account"></a>Een Azure EA-account toevoegen

Het Azure EA-account is een organisatie-eenheid in Azure EA Portal die wordt gebruikt voor abonnementsbeheer en rapportagedoeleinden. Voor toegang tot en het gebruik van Azure-services moet u een account maken of laten maken.

Zie Een account toevoegen voor meer informatie over Azure-accounts.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Een ondernemingsaccount overdragen naar een nieuwe inschrijving

Houd rekening met de volgende punten wanneer u een Enterprise-account overdraagt naar een nieuwe inschrijving:

- Alleen de accounts in de aanvraag worden overgedragen. Als alle accounts worden gekozen, worden ze allemaal overgedragen.
- De status van de broninschrijving blijft actief of uitgebreid. U kunt de inschrijving blijven gebruiken totdat deze verloopt.

### <a name="effective-transfer-date"></a>Werkelijke overdrachtsdatum

De werkelijke overdrachtsdatum kan een datum zijn op of na de start van de inschrijving waarnaar u wilt overdragen. De inschrijving waarnaar u overdraagt, is de _doelinschrijving_. Na de accountoverdracht blijven de gebruiksgegevens die al aanwezig waren vóór de overdrachtsdatum, beschikbaar in de inschrijving van waaruit u de overdracht uitvoert. De inschrijving van waaruit u overdraagt, is de _broninschrijving_.  Het gebruik van de broninschrijving wordt verrekend met de financiële toezegging of in rekening gebracht als overschrijding. Gebruik dat plaatsvindt na de werkelijke overdrachtsdatum, wordt overgedragen naar de nieuwe inschrijving en dienovereenkomstig in rekening gebracht.

U kunt een accountoverdracht antedateren tot de begindatum van de doelinschrijving. Of zo ver terug als de feitelijk begindatum van de inschrijving.

### <a name="monetary-commitment"></a>Financiële toezegging

De financiële toezegging is niet overdraagbaar tussen inschrijvingen. De financiële toezeggingssaldi zijn contractueel gebonden aan de inschrijving waar ze zijn besteld. De financiële toezegging is niet overdraagbaar als onderdeel van het overdrachtsproces voor het account of de inschrijving.

### <a name="services-affected"></a>Betrokken services

Er is geen downtime tijdens de accountoverdracht. Indien alle vereiste gegevens worden verstrekt, kan de overdracht nog op de dag van uw aanvraag worden voltooid.

### <a name="prerequisites"></a>Vereisten

Wanneer u een accountoverdracht aanvraagt, verstrekt u de volgende gegevens:


- Accountnaam en eigenaar-id van het account dat moet worden overgedragen
- Voor de broninschrijving het inschrijvingsnummer en het account dat moet worden overgedragen
- Voor de doelinschrijving het inschrijvingsnummer waarnaar moet worden overgedragen
- De werkelijke datum van de accountoverdracht kan een datum op of na de begindatum van de doelinschrijving zijn

Andere punten waarmee u rekening moet houden voordat u een account overdraagt:

- Goedkeuring van een EA-beheerder is vereist voor de doel- en broninschrijving
  - In sommige gevallen kan Microsoft vragen om aanvullende goedkeuring van een EA-beheerder van de broninschrijving
- Als een accountoverdracht niet aan uw vereisten voldoet, kunt u een inschrijvingsoverdracht overwegen.
- Met een account overdracht worden alle services, abonnementen, accounts, afdelingen en de volledige inschrijvingsstructuur, inclusief alle EA-afdelingsbeheerders, overgedragen.
- Met de accountoverdracht wordt de status van de broninschrijving ingesteld op _Overgedragen_. Het overgedragen account is alleen beschikbaar voor rapportage van het historische gebruik.
- U kunt geen rollen of abonnementen toevoegen aan een inschrijving met de status Overgedragen. De status voorkomt aanvullend gebruik van de inschrijving.
- Het saldo van de resterende financiële toezegging in de bronovereenkomst gaat verloren, met inbegrip van toekomstige voorwaarden.


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Enterprise-inschrijving overdragen naar een nieuwe inschrijving

Wanneer u de overdracht van een volledige Enterprise-inschrijving naar een inschrijving aanvraagt, worden de volgende acties uitgevoerd:

- Alle Azure-services, -abonnementen, -accounts, -afdelingen en de volledige inschrijvingsstructuur, inclusief alle EA-afdelingsbeheerders, worden overgedragen.
- De inschrijvingsstatus wordt ingesteld op _Overgedragen_. De overgedragen inschrijving is alleen beschikbaar voor rapportage van het historische gebruik.
- U kunt geen rollen of abonnementen toevoegen aan een overgedragen inschrijving. De status Overgedragen voorkomt aanvullend gebruik van de inschrijving.
- Het saldo van de resterende financiële toezegging in de overeenkomst gaat verloren, met inbegrip van toekomstige voorwaarden.

### <a name="effective-transfer-date"></a>Werkelijke overdrachtsdatum

De werkelijke overdrachtsdatum kan een datum zijn op of na de start van de inschrijving die u wilt overdragen naar de doelinschrijving.

Het gebruik van de broninschrijving wordt verrekend met de financiële toezegging of in rekening gebracht als overschrijding. Gebruik dat plaatsvindt na de werkelijke overdrachtsdatum, wordt overgedragen naar de nieuwe inschrijving en dienovereenkomstig in rekening gebracht.

### <a name="effective-transfer-date-in-the-past"></a>Werkelijke overdrachtsdatum in het verleden

U kunt een accountoverdracht antedateren tot de begindatum van de doelinschrijving. Of zo ver terug als de feitelijk begindatum van de broninschrijving.

### <a name="monetary-commitment"></a>Financiële toezegging

De financiële toezegging is niet overdraagbaar tussen inschrijvingen. De financiële toezeggingssaldi zijn contractueel gebonden aan de inschrijving waar ze zijn besteld. De financiële toezegging is niet overdraagbaar als onderdeel van het overdrachtsproces voor het account of de inschrijving.

### <a name="services-affected"></a>Betrokken services

Er is geen downtime tijdens de accountoverdracht. Indien alle vereiste gegevens worden verstrekt, kan de overdracht nog op de dag van uw aanvraag worden voltooid.

### <a name="prerequisites"></a>Vereisten

Wanneer u een inschrijvingsoverdracht aanvraagt, verstrekt u de volgende gegevens:

- Voor de broninschrijving het inschrijvingsnummer en het account dat moet worden overgedragen.
- Voor de doelinschrijving het inschrijvingsnummer waarnaar moet worden overgedragen.
- De werkelijke datum van de inschrijvingsoverdracht kan een datum op of na de begindatum van de doelinschrijving zijn. De gekozen datum is niet van invloed op het gebruik voor een overschrijdingsfactuur die al is uitgegeven.

Andere punten waarmee u rekening moet houden voordat u een inschrijving overdraagt:

- Goedkeuring van een EA-beheerder is vereist voor de doel- en broninschrijving.
  - In sommige gevallen kan Microsoft vragen om aanvullende goedkeuring van een EA-beheerder van de broninschrijving.
- Als een inschrijvingsoverdracht niet aan uw vereisten voldoet, kunt u een accountoverdracht overwegen.
- Alleen de accounts die u opgeeft, worden overgebracht. U kunt aanvragen om al uw accounts over te dragen.
- De status van de broninschrijving blijft actief/uitgebreid. U kunt de inschrijving blijven gebruiken totdat deze verloopt.

## <a name="change-account-owner"></a>Accounteigenaar wijzigen

In Azure EA Portal kunnen abonnementen worden overdragen van de ene accounteigenaar naar de andere. Zie [Accounteigenaar wijzigen](billing-ea-portal-get-started.md#change-account-owner) voor meer informatie.

## <a name="subscription-transfer-effects"></a>Gevolgen van een abonnementsoverdracht

Wanneer een Azure-abonnement wordt overgedragen naar een account in een andere tenant, verliezen alle gebruikers, groepen en service-principals met [op rollen gebaseerde toegang (RBAC)](../role-based-access-control/overview.md) voor het beheren van de resources in het abonnement, hun toegang.

Gebruikers met RBAC-toegang tot het abonnement weergeven:

1. Open in Azure Portal **Abonnementen**.
2. Selecteer het abonnement dat u wilt weergeven en selecteer vervolgens **Toegangsbeheer (IAM)** .
3. Selecteer **Roltoewijzingen**. De pagina met roltoewijzingen bevat een overzicht van alle gebruikers met RBAC-toegang tot het abonnement.

Als het abonnement wordt overgedragen naar een account in een andere Azure AD-tenant, verliezen alle gebruiker, groepen en service-principals met [RBAC](../role-based-access-control/overview.md) om resources te beheren, _hun toegang_. Hoewel er geen RBAC-toegang aanwezig is, is het abonnement mogelijk toegankelijk via een beveiligingsmechanisme, inclusief:

- Beheercertificaten die de gebruiker beheerdersrechten verlenen voor de abonnementsresources. Zie [Een beheercertificaat voor Azure maken en uploaden](../cloud-services/cloud-services-certs-create.md) voor meer informatie.
- Toegangssleutels voor services zoals Storage. Zie [Overzicht van Azure-opslagaccount](../storage/common/storage-account-overview.md) voor meer informatie.
- Referenties voor externe toegang voor services zoals Azure Virtual Machines.

Als ontvangers de toegang tot hun Azure-resources willen beperken, moeten ze overwegen om de geheimen bij te werken die zijn gekoppeld aan de service. De meeste resources kunnen worden bijgewerkt door de volgende stappen uit te voeren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer in het Hub-menu **Alle resources**.
3. Selecteer de resource.
4. Klik op de resourcepagina op **Instellingen** om bestaande geheimen weer te geven en bij te werken.



## <a name="close-an-azure-enterprise-enrollment"></a>Een Azure Enterprise-inschrijving sluiten

Als u uw Azure EA-inschrijving wilt sluiten, kunt u het volgende doen:

- Annuleer al uw abonnementen die zijn gekoppeld aan uw Azure EA in Azure Portal.
- Neem contact op met uw softwareadviseur of partner en vraag deze uw Azure Enterprise Agreement te sluiten.

## <a name="update-notification-settings"></a>Instellingen voor meldingen bijwerken

Ondernemingsbeheerders worden automatisch ingeschreven voor het ontvangen van gebruiksmeldingen die aan hun inschrijving zijn gekoppeld. Iedere ondernemingsbeheerder kan het interval voor de afzonderlijke meldingen wijzigen of deze volledig uitschakelen.

Contactpersonen voor meldingen worden in Azure EA Portal weergegeven in het gedeelte **Contactpersoon voor meldingen**. Door uw contactpersonen voor meldingen te beheren, zorgt u ervoor dat de juiste personen in uw organisatie Azure EA-meldingen ontvangen.

De huidige instellingen voor meldingen weergeven:

1. Ga in Azure EA Portal naar **Beheren** > **Contactpersoon voor meldingen**.
2. E-mailadres: het e-mailadres dat is gekoppeld aan het Microsoft-, werk- of schoolaccount van de ondernemingsbeheerder waarop de meldingen worden ontvangen.
3. Meldingsfrequentie niet-gefactureerd saldo: de ingestelde frequentie waarmee de afzonderlijke ondernemingsbeheerders meldingen ontvangen.

Een contactpersoon toevoegen:

1. Klik op **+Contactpersoon toevoegen**.
2. Voer het e-mailadres in en bevestig dit.
3. Klik op **Opslaan**.

De nieuwe contactpersoon voor meldingen wordt weergegeven in het gedeelte **Contactpersoon voor meldingen**. Als u de meldingsfrequentie wilt wijzigen, selecteert u de contactpersoon voor meldingen en klikt u rechts van de geselecteerde rij op het potloodpictogram. Stel de frequentie in **dagelijks**, **wekelijks**, **maandelijks** of **geen**.

U kunt de _naderende einddatum van de dekkingsperiode_ onderdrukken en de meldingen over de levenscyclus _uitschakelen en de inrichting van de datum ongedaan maken_. Als u de meldingen over de levenscyclus uitschakelt, worden meldingen over de dekkingsperiode en de einddatum van de overeenkomst onderdrukt.

## <a name="manage-partner-notifications"></a>Partnermeldingen beheren

Partnerbeheerders kunnen beheren met welke frequentie ze gebruiksmeldingen voor hun inschrijvingen ontvangen. Ze ontvangen automatisch wekelijkse meldingen over hun niet-gefactureerd saldo. Ze kunnen de frequentie van afzonderlijke meldingen wijzigen in maandelijks, wekelijks, dagelijks of ze volledig uitschakelen.

Als er geen melding door een gebruiker wordt ontvangen, controleert u aan de hand van de volgende stappen of de instellingen voor meldingen van de gebruiker juist.

1. Meld u als een partnerbeheerder aan bij Azure EA Portal.
2. Klik op **Beheren** en klik vervolgens op het tabblad **Partner**.
3. Bekijk in het gedeelte **Beheerder** de lijst met beheerders.
4. Als u de meldingsvoorkeuren wilt bewerken, plaatst u de muisaanwijzer op de desbetreffende beheerder en klikt u op het potloodpictogram.
5. Werk de meldingsfrequentie en de meldingen over de levenscyclus naar wens bij.
6. Voeg indien nodig een contactpersoon toe en klik op **Toevoegen**.
7. Klik op **Opslaan**.

![Voorbeeld met het venster Contactpersoon toevoegen ](./media/billing-ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="azure-sponsorship-offer"></a>Azure Sponsorship-aanbieding
De Azure Sponsorship-aanbieding is een beperkt gesponsord Microsoft Azure-account. De aanbieding is beschikbaar via een e-mailuitnodiging en alleen voor een beperkt aantal, door Microsoft geselecteerde klanten. Als u recht hebt op de aanbieding voor Microsoft Azure Sponsorship, ontvangt u een e-mailuitnodiging voor uw account-id.
Zie voor meer informatie:

- Overzicht van de Sponsorship-aanbieding: https://azure.microsoft.com/en-us/offers/ms-azr-0143p/
- Portal met het Sponsorship-saldo: https://www.microsoftazuresponsorships.com/balance  
- Externe veelgestelde vragen over Sponsorship: https://azuresponsorships-staging.azurewebsites.net/faq
- Ondersteuningsaanvraag voor Sponsorship-activering: http://aka.ms/azrsponsorship

## <a name="next-steps"></a>Volgende stappen
- Lees hoe u geld kunt besparen met [VM-reserveringen](billing-ea-portal-vm-reservations.md).
- Zie [Problemen met toegang tot Azure EA Portal](billing-ea-portal-troubleshoot.md) als u hulp nodig hebt met het oplossen van problemen Azure EA Portal.
