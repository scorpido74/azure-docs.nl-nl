---
title: Beheer van Azure EA Portal
description: In dit artikel worden de algemene taken beschreven die een beheerder in Azure EA Portal uitvoert.
author: bandersmsft
ms.author: banders
ms.date: 09/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 27073cb7cbd78183e519290c676c2499700b0cca
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442544"
---
# <a name="azure-ea-portal-administration"></a>Beheer van Azure EA Portal

In dit artikel worden de algemene taken beschreven die een beheerder in Azure EA Portal uitvoert (https://ea.azure.com). Azure EA Portal is een online beheerportal waarmee klanten de kosten van hun Azure EA-services kunnen beheren. Zie het artikel [Aan de slag met Azure EA Portal](ea-portal-get-started.md) voor inleidende informatie over Azure EA Portal.

## <a name="associate-an-account-to-a-department"></a>Een account aan een afdeling koppelen

Ondernemingsbeheerders kunnen bestaande accounts aan afdelingen onder de inschrijving koppelen.

### <a name="to-associate-an-account-to-a-department"></a>Een account aan een afdeling koppelen

1. Meld u als ondernemingsbeheerder aan bij Azure EA Portal.
1. Selecteer **Beheren** in het navigatievenster aan de linkerkant.
1. Selecteer **Afdeling**.
1. Beweeg de muisaanwijzer over de rij met het account en selecteer het potloodpictogram aan de rechterkant.
1. Selecteer de afdeling in het vervolgkeuzemenu.
1. Selecteer **Opslaan**.

## <a name="department-spending-quotas"></a>Bestedingsquota van afdeling

EA-klanten kunnen bestedingsquota instellen of wijzigen voor elke afdeling onder een inschrijving. De hoogte van het bestedingsquotum wordt voor de huidige vooruitbetalingstermijn ingesteld. Aan het einde van de huidige vooruitbetalingstermijn wordt het bestaande bestedingsquotum naar de volgende vooruitbetalingstermijn uitgebreid, tenzij de waarden worden bijgewerkt.

De afdelingsbeheerder kan het bestedingsquotum zien, maar de hoogte ervan kan uitsluitend door de ondernemingsbeheerder worden bijgewerkt. De ondernemingsbeheerder en de afdelingsbeheerder krijgen meldingen wanneer 50%, 75%, 90% en 100% van een quotum is bereikt.

### <a name="enterprise-administrator-to-set-the-quota"></a>Een ondernemingsbeheerder kan het quotum als volgt instellen:

 1. Open Azure EA Portal.
 1. Selecteer **Beheren** in het navigatievenster aan de linkerkant.
 1. Selecteer het tabblad **Afdeling**.
 1. Selecteer de afdeling.
 1. Selecteer in de sectie Afdelingsgegevens het potloodpictogram of selecteer het pictogram **+ Afdeling toevoegen** om een bestedingsquotum aan een nieuwe afdeling toe te voegen.
 1. Voer onder Afdelingsgegevens in het vak Bestedingsquotum $ de hoogte van het bestedingsquotum in (in de valuta van de inschrijving). Deze waarde moet hoger zijn dan 0.
    - Op dit punt kunt u ook de Afdelingsnaam en de Kostenplaats bewerken.
 1. Selecteer **Opslaan**.

Het bestedingsquotum voor de afdeling is nu zichtbaar in de weergave Afdelingslijst op het tabblad Afdeling. Aan het einde van de huidige vooruitbetaling blijven de bestedingsquota voor de volgende vooruitbetalingstermijn door Azure EA Portal gehandhaafd.

De hoogte van het bestedingsquotum is onafhankelijk van de huidige Azure-vooruitbetaling, en de hoogte van het quotum en meldingen zijn alleen van toepassing op gebruik van interne producten. Het bestedingsquotum voor de afdeling is alleen bedoeld ter informatie en kan niet worden gebruikt om bestedingslimieten af te dwingen.

### <a name="department-administrator-to-view-the-quota"></a>De afdelingsbeheerder kan het quotum als volgt bekijken:

1. Open Azure EA Portal.
1. Selecteer **Beheren** in het navigatievenster aan de linkerkant.
1. Selecteer het tabblad **Afdeling** en bekijk de weergave Afdelingslijst met daarin de bestedingsquota.

Als u een indirecte klant bent, moeten kostenfuncties door uw kanaalpartner zijn ingesteld.

## <a name="enterprise-user-roles"></a>Enterprise-gebruikersrollen

Met Azure EA Portal kunt u de kosten en het gebruik van Azure EA beheren. Er zijn drie hoofdrollen in Azure EA Portal:

- EA-beheerder
- Afdelingsbeheerder
- Accounteigenaar

Elke rol heeft een ander toegangs- en autoriteitsniveau.

Zie [Enterprise-gebruikersrollen](https://docs.microsoft.com/azure/billing/billing-ea-portal-get-started#enterprise-user-roles) voor meer informatie over rollen.

## <a name="add-an-azure-ea-account"></a>Een Azure EA-account toevoegen

Het Azure EA-account is een organisatie-eenheid in de Azure EA-portal. Dit account wordt gebruikt om abonnementen te beheren en rapporten te maken. Voor toegang tot en het gebruik van Azure-services moet u een account maken of laten maken.

Zie [Een account toevoegen](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-get-started#add-an-account) voor meer informatie over Azure-accounts.

## <a name="enterprise-devtest-offer"></a>Aanbieding voor Enterprise Dev/Test

Als Azure-ondernemingsbeheerder kunt u accounteigenaren in uw organisatie de mogelijkheid geven om abonnementen te maken op basis van de EA Dev/Test-aanbieding. Als u dit wilt doen, selecteert u het vak Dev/Test voor de accounteigenaar in de Azure EA-portal.

Zodra u het selectievakje Dev/Test hebt ingeschakeld, brengt u de accounteigenaar hiervan op de hoogte zodat hij of zij de EA Dev/Test-abonnementen kan instellen die voor zijn of haar teams van Dev/Test-abonnees benodigd zijn.

Hierdoor kunnen actieve Visual Studio-abonnees de ontwikkeling uitvoeren en de werklast op Azure met speciale Dev/Test-snelheden testen. Het biedt toegang tot de volledige galerie Dev/Test-installatiekopieën, waaronder Windows 8.1 en Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>U kunt de Enterprise Dev/Test-aanbieding als volgt instellen:

1. Meld u aan als de ondernemingsbeheerder.
1. Selecteer **Beheren** in het navigatievenster aan de linkerkant.
1. Selecteer het tabblad **Account**.
1. Selecteer de rij voor het account waarvoor u toegang tot Dev/Test wilt inschakelen.
1. Selecteer rechts naast de rij het potloodpictogram.
1. Schakel het selectievakje Dev/Test in.
1. Selecteer **Opslaan**.

Wanneer een gebruiker via Azure EA Portal als accounteigenaar wordt toegevoegd, worden alle Azure-abonnementen die aan de accounteigenaar zijn gekoppeld en zijn gebaseerd op ofwel de Dev/Test-aanbieding met betalen per gebruik of de aanbiedingen voor maandelijks tegoed voor Visual Studio-abonnees omgezet naar de EA Dev/Test-aanbieding. Abonnementen op basis van andere typen, zoals betalen per gebruik, die aan de accounteigenaar zijn gekoppeld, worden omgezet naar Microsoft Azure Enterprise-aanbiedingen.

De Dev/Test-aanbieding is op dit moment niet van toepassing op Azure Gov-klanten.

## <a name="delete-subscription"></a>Abonnement verwijderen

Als u een abonnement waarvoor u de accounteigenaar bent, wilt verwijderen, doet u het volgende:

1. Meld u aan bij Azure Portal met de referenties die bij uw account horen.
1. Selecteer **Abonnementen** op het Hub-menu.
1. Ga naar het tabblad Abonnementen in de linkerbovenhoek van de pagina, selecteer het abonnement dat u wilt opzeggen en selecteer **Abonnement opzeggen** om het tabblad Opzeggen te openen.
1. Voer de abonnementsnaam in en kies een reden voor het opzeggen. Selecteer daarna de knop **Abonnement opzeggen**.

Alleen accountbeheerders kunnen abonnementen opzeggen.

Zie voor meer informatie [Wat gebeurt er nadat ik mijn abonnement heb opgezegd?](cancel-azure-subscription.md#what-happens-after-i-cancel-my-subscription)

## <a name="delete-an-account"></a>Een account verwijderen

De verwijdering van een account kan alleen worden voltooid voor actieve accounts zonder actieve abonnementen.

1. Selecteer in Enterprise-portal in het linkernavigatievenster de optie **Beheren**.
1. Selecteer het tabblad **Account**.
1. Ga naar de tabel Accounts en selecteer het account dat u wilt verwijderen.
1. Selecteer het X-pictogram rechts naast de rij Account.
1. Zodra het account geen actieve abonnementen meer heeft, selecteert u **Ja** onder de rij Account om te bevestigen dat u het account wilt verwijderen.

## <a name="update-notification-settings"></a>Instellingen voor meldingen bijwerken

Ondernemingsbeheerders worden automatisch ingeschreven voor het ontvangen van gebruiksmeldingen die aan hun inschrijving zijn gekoppeld. Iedere ondernemingsbeheerder kan het interval voor de afzonderlijke meldingen wijzigen of deze volledig uitschakelen.

Contactpersonen voor meldingen worden in Azure EA Portal weergegeven in het gedeelte **Contactpersoon voor meldingen**. Door uw contactpersonen voor meldingen te beheren, zorgt u ervoor dat de juiste personen in uw organisatie Azure EA-meldingen ontvangen.

De huidige instellingen voor meldingen weergeven:

1. Ga in Azure EA Portal naar **Beheren** > **Contactpersoon voor meldingen**.
2. E-mailadres: het e-mailadres dat is gekoppeld aan het Microsoft-, werk- of schoolaccount van de ondernemingsbeheerder waarop de meldingen worden ontvangen.
3. Meldingsfrequentie niet-gefactureerd saldo: de ingestelde frequentie waarmee de afzonderlijke ondernemingsbeheerders meldingen ontvangen.

Een contactpersoon toevoegen:

1. Selecteer **+Contactpersoon toevoegen**.
2. Voer het e-mailadres in en bevestig dit.
3. Selecteer **Opslaan**.

De nieuwe contactpersoon voor meldingen wordt weergegeven in het gedeelte **Contactpersoon voor meldingen**. Als u de meldingsfrequentie wilt wijzigen, selecteert u de contactpersoon voor meldingen en selecteert u rechts van de geselecteerde rij het potloodpictogram. Stel de frequentie in **dagelijks**, **wekelijks**, **maandelijks** of **geen**.

U kunt de _naderende einddatum van de dekkingsperiode_ onderdrukken en de meldingen over de levenscyclus _uitschakelen en de inrichting van de datum ongedaan maken_. Als u de meldingen over de levenscyclus uitschakelt, worden meldingen over de dekkingsperiode en de einddatum van de overeenkomst onderdrukt.

## <a name="azure-sponsorship-offer"></a>Azure Sponsorship-aanbieding

De Azure Sponsorship-aanbieding is een beperkt gesponsord Microsoft Azure-account. De aanbieding is beschikbaar via een e-mailuitnodiging en alleen voor een beperkt aantal, door Microsoft geselecteerde klanten. Als u recht hebt op de aanbieding voor Microsoft Azure Sponsorship, ontvangt u een e-mailuitnodiging voor uw account-id.

Maak een [ondersteuningsaanvraag voor activering van sponsoring](https://aka.ms/azrsponsorship) voor meer informatie.

## <a name="conversion-to-work-or-school-account-authentication"></a>Verificatie van de omzetting naar een werk- of schoolaccount

Azure Enterprise-gebruikers kunnen een Microsoft-account (MSA of Live ID) omzetten naar het verificatietype van een werk- of schoolaccount (waarvoor Active Directory in Azure wordt gebruikt).

U gaat als volgt aan de slag:

1. Voeg het werk- of schoolaccount toe aan Azure EA Portal bij de benodigde rol(len).
1. Indien er foutmeldingen worden weergegeven, is het account mogelijk niet geldig in de Active Directory.  Azure maakt gebruik van User Principal Name (UPN). Dit is niet altijd hetzelfde als het e-mailadres.
1. Voer een verificatie bij Azure EA Portal uit met het werk- of schoolaccount.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>U kunt abonnementen van Microsoft-accounts als volgt omzetten naar werk- of schoolaccounts:

1. Meld u aan bij de beheerportal met het Microsoft-account dat eigenaar is van de abonnementen.
1. Voer een overdracht van het accounteigendom uit om naar het nieuwe account over te stappen.
1. Als het goed is, bevat het Microsoft-account nu geen actieve abonnementen meer en kan het account dus worden verwijderd.
1. Verwijderde accounts blijven voor historische factureringsredenen in de portal zichtbaar met de status Inactief.  U kunt deze accounts met behulp van een filter verbergen door een selectievakje in te schakelen zodat alleen actieve accounts worden weergegeven.

## <a name="account-subscription-ownership-faq"></a>Veelgestelde vragen over het eigendom van accountabonnementen

In dit document worden veelgestelde vragen beantwoord met betrekking tot het eigendom van accountabonnementen.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Hoeveel Azure-accounteigenaren mogen er per abonnement worden gebruikt?

Er is per abonnement maar één accounteigenaar toegestaan.  U kunt aanvullende rollen toevoegen met de opties Op rollen gebaseerde toegang of (Toegangsbeheer (IAM)) op het tabblad Abonnement in de linkerbovenhoek van de pagina op de [Azure-portal](https://portal.azure.com).

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Kan een Azure-accounteigenaar worden vermeld onder meer dan één afdeling?

Nee, een accounteigenaar kan maar aan één afdeling worden gekoppeld. We hanteren dit beleid om ervoor te zorgen dat de kosten en uitgaven die aan de afdeling waarmee ze onder de EA-inschrijving in de Azure EA-portal zijn afgestemd, nauwkeurig worden bijgehouden en verdeeld.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Kan een Azure-accounteigenaar als een beveiligingsgroep worden vermeld?

Nee, de eigenaar van een abonnement moet een uniek Microsoft-account (MSA) of een unieke Azure Active Directory-verificatie (AAD) zijn. Als u opvolging binnen uw organisatie wilt inzetten, kunt u generieke accounts maken en AAD gebruiken om toegang tot abonnementen te beheren.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Kan een individuele gebruiker de eigenaar van meerdere abonnementen zijn?

Een Azure-accounteigenaar kan een onbeperkt aantal abonnementen maken en beheren.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Hoe krijg ik toegang tot alle abonnementen van mijn organisatie en hoe kan ik ze weergeven?

Tegenwoordig wordt dit vanuit een beleid geregeld. Dit houdt in dat u voor elk abonnement dat wordt gemaakt, moet vereisen dat uw account aan een abonnementsrol wordt toegevoegd met behulp van op rollen gebaseerde toegang.

### <a name="where-do-i-go-to-create-a-subscription"></a>Waar kan ik een abonnement maken?

Voordat u een abonnement voor een Enterprise Azure-aanbieding (EA) kunt maken, moet uw account door de beheerder van uw EA-inschrijving in de Azure EA-portal aan de rol van accounteigenaar worden toegevoegd. Vervolgens moet u zich bij Azure de EA-portal aanmelden om recht te krijgen op het maken van abonnementen van het EA-aanbiedingstype. U wordt aangeraden om uw eerste EA-abonnement te maken via de koppeling '+ Abonnement toevoegen', op het tabblad Abonnement in de EA-portal.  Zodra uw account echter over de juiste machtigingen beschikt, is het wellicht eenvoudiger om abonnementen te maken op het tabblad Abonnement in de linkerbovenhoek van de pagina op portal.azure.com. Hier kunt u in één stap zowel uw abonnement maken als de naam daarvan wijzigen.

### <a name="who-can-create-a-subscription"></a>Wie kan een abonnement maken?

Als u een abonnement van het Enterprise Azure-aanbiedingstype wilt maken, moet u zijn gemachtigd in de rol van accounteigenaar op [EA Portal](https://ea.azure.com).

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u geld kunt besparen met [VM-reserveringen](ea-portal-vm-reservations.md).
- Zie [Problemen met toegang tot Azure EA Portal](ea-portal-troubleshoot.md) als u hulp nodig hebt met het oplossen van problemen Azure EA Portal.
