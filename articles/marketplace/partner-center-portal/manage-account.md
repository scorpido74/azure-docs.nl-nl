---
title: Een commercieel marktplaatsaccount beheren in Microsoft Partner Center
description: Meer informatie over het beheren van een commercieel marktplaatsaccount in Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 2e057ca16e2e4f7fcb16ee39d090be853639fc12
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731446"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Uw commerciële marktplaatsaccount beheren in partnercentrum

Zodra u [een Partner Center-account](./create-account.md)hebt gemaakt, u het [dashboard voor commerciële marktplaatsgebruiken](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) om uw account en aanbiedingen te beheren.

In dit artikel gaan we dieper in op het beheren van je Partner Center-account, inclusief hoe je:

- [Toegang tot de instellingen van uw Partner Center-account](#access-your-account-settings)
- [Uw Publisher ID, Symantec ID, Seller ID, User ID, MPN ID en Azure AD-tenants zoeken](#account-details)
- [Contactgegevens bijwerken](#contact-info)
- [Tracking-GUID's instellen voor het bewaken van het gebruik van klanten](#tracking-guids)
- [Gebruikers beheren](#manage-users)
- [Groepen beheren](#manage-groups)
- [Azure AD-toepassingen beheren](#manage-azure-ad-applications)
- [Gebruikersrollen en machtigingen definiëren](#define-user-roles-and-permissions)
- [Azure AD-tenants beheren (werkaccounts)](#manage-tenants)
- [Partnercentrumovereenkomsten beheren](#agreements)

## <a name="access-your-account-settings"></a>Toegang tot uw accountinstellingen

Als u dit nog niet hebt gedaan, moet u (of de beheerder van uw organisatie) toegang krijgen tot de [accountinstellingen](https://partner.microsoft.com/dashboard/account/management) voor uw Partner Center-account om:
- Controleer de verificatiestatus van uw bedrijf
- Uw Symantec-id, verkoper-id, MPN-id (Microsoft Partner Network), uitgevers-id en contactgegevens bevestigen, waaronder de goedkeurings- en verkopercontactpersoon van het bedrijf
- Gebruikersaccounts maken voor iedereen die uw zakelijke account gebruikt in Partner Center

### <a name="open-developer-settings"></a>Ontwikkelaarsinstellingen openen

**Accountinstellingen** bevinden zich in de rechterbovenhoek van uw [commerciële marktplaatsdashboard](https://partner.microsoft.com/dashboard/commercial-marketplace) in Partner Center. Selecteer het tandwielpictogram (rechtsboven in het dashboard) en selecteer **Vervolgens Ontwikkelaarsinstellingen**.

![Menu Accountinstellingen in Partnercentrum](./media/dashboard-developer-settings.png)

In **accountinstellingen**kun je je:
- **Accountgegevens**: Accounttype en Accountstatus
- **Uitgevers-id's**: verkopers-id, gebruikersnaam, uitgevers-id, Azure AD-tenants, enz.
- **Contactgegevens**: Weergavenaam uitgever, naam van de verkoper, e-mail, telefoon en adres
- **GUID's bijhouden:** alle tracking-GUID's die worden geassocieerd met uw account

### <a name="account-details"></a>Accountgegevens

In de sectie Accountgegevens u basisgegevens zien, zoals uw **accounttype** (bedrijf of individu) en de **verificatiestatus** van uw account. Tijdens het verificatieproces van uw account worden in deze instellingen elke vereiste stap weergegeven, inclusief verificatie via e-mail, verificatie van de werkgelegenheid en bedrijfsverificatie. U uw e-mail hier ook bijwerken en indien nodig de verificatie opnieuw verzenden.

### <a name="publisher-ids"></a>Uitgevers-iD's

In de sectie Uitgevers-id's ziet u uw **Symantec-id,** **verkoper-id,** **gebruikersnaam,** **MPN-id**en **Azure AD-tenants**. Deze waarden worden door Microsoft toegewezen om uw ontwikkelaarsaccount op unieke wijze te identificeren en kunnen niet worden bewerkt.

### <a name="contact-info"></a>Contactgegevens

In de sectie Contactgegevens ziet u de **weergavenaam van**uw uitgever, **de contactgegevens** van de verkoper (de naam van de contactpersoon, e-mail, telefoonnummer en adres voor de verkoper van het bedrijf) en de **bedrijfskeurster** (de naam, e-mail en het telefoonnummer van de persoon die bevoegd is om beslissingen voor het bedrijf goed te keuren).

#### <a name="payout-account"></a>Uitbetalingsrekening

Een uitbetalingsrekening is de bankrekening waarnaar de opbrengst van uw verkoop wordt verzonden. Deze bankrekening moet zich in hetzelfde land bevindt waar u uw Partner Center-account hebt geregistreerd.

Als u uw uitbetalingsaccount wilt instellen, moet u **uw Microsoft-account koppelen:**
1. Ga naar de [overzichtspagina voor commerciële marktplaatsen](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) in Partnercenter.
2. Selecteer onder de sectie Profiel de optie **Uw Microsoft-account koppelen**.
3. Meld je aan met je Microsoft-account (MSA). Dit account kan niet al worden gekoppeld aan een ander Partner Center-account.
4. Als u de installatie van uw uitbetalingsaccount wilt voltooien, meldt u zich af bij Partner Center en meldt u zich vervolgens weer aan met uw Microsoft-account (in plaats van uw werkaccount).

Nu uw Microsoft-account is gekoppeld om een uitbetalingsaccount toe te voegen, moet u:
- **Kies een betalingsmethode:** bankrekening of PayPal
- **Betalingsgegevens toevoegen:** dit kan het kiezen van een accounttype (controleren of sparen), het invoeren van de naam van de rekeninghouder, het rekeningnummer en het routeringsnummer, het factuuradres, het telefoonnummer of het PayPal-e-mailadres. *Zie [PayPal-gegevens](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info)voor meer informatie over het gebruik van PayPal als betalingsmethode voor uw account en om erachter te komen of deze wordt ondersteund in uw marktregio.

> [!IMPORTANT]
> Als u uw uitbetalingsaccount wijzigt, u uw betalingen met maximaal één betalingscyclus vertragen. Deze vertraging treedt op omdat we de accountwijziging moeten verifiëren, net zoals we dat doen bij het voor het eerst instellen van de uitbetalingsrekening. Je krijgt nog steeds het volledige bedrag betaald nadat je account is geverifieerd; alle betalingen die voor de huidige betalingscyclus moeten worden betaald, worden toegevoegd aan de volgende.  

#### <a name="tax-profile"></a>Belastingprofiel

Controleer uw huidige status van belastingprofiel en bevestig dat het juiste **entiteitstype** en **belastingcertificaatgegevens** worden weergegeven. Selecteer **Bewerken** om vereiste formulieren bij te werken of in te vullen.

Om uw fiscale status vast te stellen, moet u uw land van verblijf en burgerschap opgeven en de juiste belastingformulieren invullen die zijn gekoppeld aan uw land/regio.

Ongeacht uw land van verblijf of staatsburgerschap, moet u belastingformulieren van de Verenigde Staten invullen om aanbiedingen via Microsoft te verkopen. Partners die voldoen aan bepaalde verblijfsvereisten in de Verenigde Staten moeten een IRS W-9-formulier invullen. Andere partners buiten de Verenigde Staten moeten een IRS W-8-formulier invullen. U deze formulieren online invullen terwijl u uw belastingprofiel invult.

Een individueel identificatienummer van de belastingbetaler (of ITIN) van de Verenigde Staten is niet vereist om betalingen van Microsoft te ontvangen of belastingvoordelen te claimen.

U uw belastingformulieren elektronisch invullen en indienen in het Partnercenter; in de meeste gevallen hoeft u geen formulieren af te drukken en te mailen.

Verschillende landen en regio's hebben verschillende belastingvereisten. Het exacte bedrag dat u aan belastingen moet betalen, is afhankelijk van de landen en regio's waar u uw aanbiedingen verkoopt. Microsoft maakt in sommige landen gebruik van verkoop en gebruikt namens u belasting. Deze landen worden geïdentificeerd tijdens het aanbieden van uw aanbieding. In andere landen moet u, afhankelijk van waar u bent geregistreerd, mogelijk de verkoop- en gebruiksbelasting voor uw verkoop rechtstreeks aan de lokale belastingdienst overmaken. Bovendien kan de verkoopopbrengst die u ontvangt, belastbaar zijn als inkomen. We raden u ten zeerste aan contact op te nemen met de relevante autoriteit voor uw land of regio die u het beste kan helpen bij het bepalen van de juiste belastinggegevens voor uw Microsoft-verkooptransacties.

##### <a name="withholding-rates"></a>Inhoudingstarieven
De informatie die u in uw belastingformulieren indient, bepaalt het juiste belastinginhoudingspercentage. Het inhoudingspercentage is alleen van toepassing op verkopen die u in de Verenigde Staten maakt; verkopen in niet-Amerikaanse locaties zijn niet onderhevig aan achterhouden. De inhoudingspercentages variëren, maar voor de meeste ontwikkelaars die zich buiten de Verenigde Staten registreren, is het standaardtarief 30%. U hebt de mogelijkheid om dit tarief te verlagen als uw land heeft ingestemd met een inkomstenbelasting verdrag met de Verenigde Staten.

##### <a name="tax-treaty-benefits"></a>Belastingverdrag voordelen
Als u buiten de Verenigde Staten bent, u mogelijk profiteren van belastingvoordelen. Deze voordelen variëren van land tot land en kunnen u het bedrag van de belastingen die Microsoft achterhoudt, verlagen. U belastingvoordelen aanvragen door deel II van het W-8BEN-formulier in te vullen. We raden u aan te communiceren met de juiste bronnen in uw land of regio om te bepalen of deze voordelen op u van toepassing zijn.

[Meer informatie over belastinggegevens voor Windows-app-/game-ontwikkelaars en Azure Marketplace-uitgevers](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Uitbetalingsstatus

Standaard verzendt Microsoft betalingen op maandelijkse basis. U hebt echter de mogelijkheid om uw uitbetalingen in de wacht te zetten, waardoor betalingen naar uw account worden voorkomen. Als je ervoor kiest om je uitbetalingen in de wacht te zetten, blijven we alle inkomsten die je verdient registreren en de details opgeven in je **uitbetalingsoverzicht.** We sturen echter geen betalingen naar je account totdat je de blokkering hebt verwijderd. 

Als u uw betalingen in de wacht wilt plaatsen, gaat u naar **Accountinstellingen**. Schakel onder **Financiële details**in de sectie Status **van uitbetalingsblokkering** de schuifregelaar in **op Aan**. U uw uitbetalingsstatus op elk gewenst moment wijzigen, maar houd er rekening mee dat uw beslissing van invloed zal zijn op de volgende maandelijkse uitbetaling. Als u bijvoorbeeld de uitbetaling van April wilt houden, moet u ervoor zorgen dat uw uitbetalingsstatus voor eind maart wordt ingesteld op **Aan.**

Zodra u uw uitbetalingsstatus hebt ingesteld op **Aan,** worden alle uitbetalingen in de wachtstand staan totdat u de schuifregelaar terugschakelt naar **Uit.** Wanneer u dit doet, wordt u opgenomen tijdens de volgende maandelijkse uitbetalingscyclus (mits aan de toepasselijke betalingsdrempels is voldaan). Als u bijvoorbeeld uw uitbetalingen in de wacht hebt staan, maar een uitbetaling wilt laten genereren in juni, moet u de status van de uitbetalingsstatus voor **het** einde van mei inschakelen.

> [!NOTE]
> De selectie van de **uitbetalingsstatus** is van toepassing op **alle** inkomstenbronnen die worden betaald via Microsoft Partner Center, waaronder Azure Marketplace, AppSource, Microsoft Store, advertenties, enz.). U geen verschillende wachtstatussen selecteren voor elke inkomstenbron.

### <a name="devices"></a>Apparaten

De instellingen voor apparaatbeheer zijn alleen van toepassing op UWP-publicatie. [Meer informatie](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>GUID's bijhouden

Globally Unique Identifiers (GUIDs) zijn unieke referentienummers (met 32 hexadecimale cijfers) die kunnen worden gebruikt voor het bijhouden van uw Azure-gebruik. 

Als u GUID's wilt maken voor tracking, moet u een GUID-generator gebruiken. Het Azure Storage-team heeft een [GUID-generatorformulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR3i8TQB_XnRAsV3-7XmQFpFUMVRVVFFLTDFLS0E2QzNYSkFZR1U3WVJCTSQlQCN0PWcu) gemaakt waarmee u een GUID van de juiste indeling wordt gemaild en kan worden hergebruikt voor de verschillende trackingsystemen.

Wij raden u aan voor elk aanbiedings- en distributiekanaal voor elk product een unieke GUID te maken. U ervoor kiezen om één GUID te gebruiken voor de meerdere distributiekanalen van het product als u niet wilt dat de rapportage wordt gesplitst.

Als u een product implementeert met behulp van een sjabloon en het is beschikbaar op zowel de Azure Marketplace als op GitHub, u twee verschillende GUID's maken en registreren:

*    Product A in Azure Marketplace
*    Product A op GitHub

Rapportage gebeurt op basis van de partnerwaarde (Microsoft Partner ID) en de GUIDs. U GUID's ook op een gedetailleerder niveau bijhouden dat is afgestemd op elk plan binnen uw aanbieding.

Zie de [veelgestelde vragen over Azure-klantgebruik bijhouden met GUIDs.](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq)

## <a name="create-a-billing-profile"></a>Een factureringsprofiel maken

Als u een [Dynamics 365 voor customer engagement & Power Apps](./create-new-customer-engagement-offer.md) of Dynamics [365 for Operations-aanbieding](./create-new-operations-offer.md) publiceert, moet u uw **factureringsprofiel**invullen.

Het factuuradres is vooraf ingevuld vanuit uw rechtspersoon en u dit adres later bijwerken. De velden BTW en BTW-id zijn optioneel.  De landnaam en de bedrijfsnaam kunnen niet worden bewerkt.

## <a name="multi-user-account-management"></a>Accountbeheer voor meerdere gebruikers

Partnercentrum maakt gebruik van [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) voor toegang en beheer van accountgebruikers met meerdere gebruikers. Het Azure AD van uw organisatie wordt automatisch gekoppeld aan uw Partner Center-account als onderdeel van het inschrijvingsproces.

## <a name="manage-users"></a>Gebruikers beheren

Met de sectie **Gebruikers** van Partnercentrum (onder **Accountinstellingen)** u Azure AD gebruiken om de gebruikers, groepen en Azure AD-toepassingen te beheren die toegang hebben tot uw Partner Center-account. Uw account moet machtigingen op Managerniveau hebben voor de Azure [**AD-tenant**](#define-user-roles-and-permissions) [(Azure AD-tenant)](./company-work-accounts.md) waarin u gebruikers wilt toevoegen of bewerken. Om gebruikers binnen een ander werkaccount /tenant te beheren, moet u zich afmelden en vervolgens weer inloggen als gebruiker met **Manager-machtigingen** op dat werkaccount / tenant.

Zodra u bent aangemeld met uw werkaccount (Azure AD-tenant), u het als:

- [Gebruikers toevoegen of verwijderen](#add-existing-users)
- [Een gebruikerswachtwoord wijzigen](#change-a-user-password)
- [Groepen toevoegen of verwijderen](#manage-groups)
- [Azure AD-toepassingen toevoegen of verwijderen](#add-new-azure-ad-applications)
- [Sleutels beheren voor een Azure AD-toepassing](#manage-keys-for-an-azure-ad-application)
- [Gebruikersrollen en machtigingen definiëren](#define-user-roles-and-permissions)

Houd er rekening mee dat alle Gebruikers van het Partnercenter (inclusief groepen en Azure AD-toepassingen) een actief werkaccount moeten hebben in een [Azure AD-tenant](#manage-tenants) die is gekoppeld aan uw Partner Center-account.

### <a name="add-existing-users"></a>Bestaande gebruikers toevoegen

Als u gebruikers wilt toevoegen aan uw Partner Center-account dat al bestaat in het werkaccount van uw bedrijf [(Azure AD-tenant):](./company-work-accounts.md)

1. Ga naar **Gebruikers** (onder **Accountinstellingen)** en selecteer **Gebruikers toevoegen**.
2. Selecteer een of meer gebruikers in de lijst die wordt weergegeven. U het zoekvak gebruiken om te zoeken naar specifieke gebruikers.
*Als u meer dan één gebruiker selecteert die u aan uw Partner Center-account wilt toevoegen, moet u deze gebruikers dezelfde rol of set aangepaste machtigingen toewijzen. Als u meerdere gebruikers met verschillende rollen/machtigingen wilt toevoegen, herhaalt u deze stappen voor elke rol of set aangepaste machtigingen.
3. Wanneer u klaar bent met het kiezen van gebruikers, klikt u op **Geselecteerd toevoegen**.
4. Geef **in** de sectie Rollen de rol(en) of aangepaste machtigingen op voor de geselecteerde gebruiker(s).
5. Selecteer **Opslaan**.

### <a name="create-new-users"></a>Nieuwe gebruikers maken

Als u gloednieuwe gebruikersaccounts wilt maken, moet u een account hebben met machtigingen [**voor globale beheerders.**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

1. Ga naar **Gebruikers** (onder **Accountinstellingen)** selecteer **Gebruikers toevoegen**en kies Vervolgens Nieuwe **gebruikers maken**.
1. Voer voor elke nieuwe gebruiker een voornaam, achternaam en gebruikersnaam in. 
1. Als u wilt dat de nieuwe gebruiker een algemeen beheerdersaccount in de map van uw organisatie heeft, schakelt u het selectievakje **Van deze gebruiker een globale beheerder in uw Azure AD maken, met volledige controle over alle directoryresources**in. Dit geeft de gebruiker volledige toegang tot alle beheerfuncties in het Azure AD van uw bedrijf. Ze kunnen gebruikers toevoegen en beheren in het werkaccount van uw organisatie (Azure AD-tenant), maar niet in partnercentrum, tenzij u het account de juiste rol/machtigingen verleent.
1. Als u het selectievakje hebt ingeschakeld om van deze gebruiker een globale beheerder te **maken,** moet u de gebruiker een **e-mail voor wachtwoordherstel** verstrekken om indien nodig zijn wachtwoord te herstellen.
1. Selecteer in de sectie **Groepslidmaatschap** alle groepen waartoe de nieuwe gebruiker wilt behoren.
1. Geef **in** de sectie Rollen de rol(en) of aangepaste machtigingen voor de gebruiker op.
1. Selecteer **Opslaan**.

Als u een nieuwe gebruiker maakt in het Partnercentrum, wordt er ook een account voor die gebruiker gemaakt in het werkaccount (Azure AD-tenant) waarop u bent aangemeld. Als u wijzigingen aanbrengt in de naam van een gebruiker in het Partnercentrum, worden dezelfde wijzigingen aangebracht in het werkaccount van uw organisatie (Azure AD-tenant).

### <a name="invite-new-users-by-email"></a>Nieuwe gebruikers uitnodigen via e-mail

Als u gebruikers wilt uitnodigen die momenteel geen deel uitmaken van uw bedrijfswerkaccount (Azure AD-tenant) via e-mail, moet u een account hebben met machtigingen [**voor globale beheerders.**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

1. Ga naar **Gebruikers** (onder **Accountinstellingen)** selecteer **Gebruikers toevoegen**en kies Gebruikers uitnodigen **via e-mail**.
2. Voer een of meer e-mailadressen (maximaal tien) in, gescheiden door komma's of puntkomma's.
3. Geef **in** de sectie Rollen de rol(en) of aangepaste machtigingen voor de gebruiker op.
4. Selecteer **Opslaan**.

De gebruikers die je hebt uitgenodigd, ontvangen een e-mailuitnodiging om lid te worden van je Partner Center-account. Er wordt een nieuw gastgebruikersaccount gemaakt in uw werkaccount (Azure AD-tenant). Elke gebruiker moet zijn uitnodiging accepteren voordat hij toegang heeft tot uw account.

Als u een uitnodiging opnieuw wilt verzenden, gaat u naar de pagina **Gebruikers,** zoekt u de uitnodiging in de lijst met gebruikers, selecteert u hun e-mailadres (of de tekst met *de tekst Uitnodiging in behandeling).* Selecteer vervolgens onder aan de pagina Uitnodiging **opnieuw verzenden**.

> [!NOTE]
> Als uw organisatie [directory-integratie](https://go.microsoft.com/fwlink/p/?LinkID=724033) gebruikt om de on-premises directoryservice te synchroniseren met uw Azure AD, u geen nieuwe gebruikers, groepen of Azure AD-toepassingen maken in het Partnercentrum. U (of een andere beheerder in uw on-premises directory) moet ze rechtstreeks in de on-premises directory maken voordat u ze zien en toevoegen in het Partnercentrum.

### <a name="remove-a-user"></a>Een gebruiker verwijderen

Als u een gebruiker uit uw werkaccount (Azure AD-tenant) wilt verwijderen, gaat u naar **Gebruikers** (onder **Accountinstellingen)** en selecteert u de gebruiker die u wilt verwijderen met behulp van het selectievakje in de uiterst rechterkolom en kiest u **Verwijderen** uit de beschikbare acties. Er verschijnt een pop-upvenster waarin u bevestigt dat u de geselecteerde gebruiker(s) wilt verwijderen.

### <a name="change-a-user-password"></a>Een gebruikerswachtwoord wijzigen

Als een van uw gebruikers zijn wachtwoord moet wijzigen, kunnen ze dit zelf doen als u een **e-mail met wachtwoordherstel** hebt opgegeven bij het maken van het gebruikersaccount. U het wachtwoord van een gebruiker ook bijwerken door de onderstaande stappen te volgen. Als u het wachtwoord van een gebruiker wilt wijzigen in uw bedrijfswerkaccount (Azure AD-tenant), moet u zijn aangemeld voor een account met machtigingen [**voor globale beheerders.**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) Houd er rekening mee dat dit het wachtwoord van de gebruiker in uw Azure AD-tenant verandert, samen met het wachtwoord dat ze gebruiken om toegang te krijgen tot Partner Center.

1. Selecteer **op** de pagina Gebruikers (onder **Accountinstellingen)** de naam van het gebruikersaccount dat u wilt bewerken.
2. Selecteer de knop **Wachtwoord opnieuw instellen** onder aan de pagina.
3. Er verschijnt een bevestigingspagina met de inloggegevens van de gebruiker, inclusief een tijdelijk wachtwoord. Zorg ervoor dat u deze informatie afdrukt of kopieert en deze aan de gebruiker verstrekt, omdat u geen toegang meer hebt tot het tijdelijke wachtwoord nadat u deze pagina hebt verlaten.

## <a name="manage-groups"></a>Groepen beheren

Met groepen u meerdere gebruikersrollen en machtigingen samen beheren.

### <a name="add-an-existing-group"></a>Een bestaande groep toevoegen

Ga als u een groep toevoegt die al bestaat in het werkaccount van uw organisatie (Azure AD-tenant) aan uw Partner Center-account:

1. Selecteer **groepen toevoegen**op de pagina **Gebruikers** (onder **Accountinstellingen**).
2. Selecteer een of meer groepen in de lijst die wordt weergegeven. U het zoekvak gebruiken om naar specifieke groepen te zoeken.
Als u meer dan één groep selecteert die u aan uw Partnercenter-account wilt toevoegen, moet u ze dezelfde rol of set aangepaste machtigingen toewijzen. Als u meerdere groepen met verschillende rollen/machtigingen wilt toevoegen, herhaalt u deze stappen voor elke rol of set aangepaste machtigingen.
3. Wanneer u klaar bent met het kiezen van groepen, klikt u op **Geselecteerd toevoegen**.
4. Geef **in** de sectie Rollen de rollen of aangepaste machtigingen op voor de geselecteerde groep(en). Alle leden van de groep hebben toegang tot je Partner Center-account met de machtigingen die je toepast op de groep, ongeacht de rollen en machtigingen die zijn gekoppeld aan hun individuele account.
5. Selecteer **Opslaan**.

Wanneer u een bestaande groep toevoegt, heeft elke gebruiker die lid is van die groep toegang tot uw Partner Center-account, met de machtigingen die zijn gekoppeld aan de toegewezen rol van de groep.

### <a name="add-a-new-group"></a>Een nieuwe groep maken

Ga als volgende over een gloednieuwe groep naar uw Partner Center-account:

1. Selecteer **groepen toevoegen**op de pagina **Gebruikers** (onder **Accountinstellingen**).
2. Selecteer op de volgende pagina **Nieuwe groep**.
3. Voer de weergavenaam voor de nieuwe groep in.
4. Geef de rol(en) of aangepaste machtigingen voor de groep op. Alle leden van de groep hebben toegang tot je Partner Center-account met de machtigingen die je hier toepast, ongeacht de rollen/machtigingen die zijn gekoppeld aan hun individuele account.
5. Selecteer gebruiker(s) voor de nieuwe groep in de lijst die wordt weergegeven. U het zoekvak gebruiken om te zoeken naar specifieke gebruikers.
6. Wanneer u klaar bent met het selecteren van gebruikers, klikt u op **Geselecteerd toevoegen** om ze aan de nieuwe groep toe te voegen.
7. Selecteer **Opslaan**.

Houd er rekening mee dat deze nieuwe groep ook wordt gemaakt in het werkaccount van uw organisatie (Azure AD-tenant), niet alleen in uw Partner Center-account.

### <a name="remove-a-group"></a>Een groep verwijderen

Als u een groep wilt verwijderen uit uw werkaccount (Azure AD-tenant), gaat u naar **Gebruikers** (onder **Accountinstellingen),** selecteert u de groep die u wilt verwijderen met behulp van het selectievakje in de uiterst rechtse kolom en kiest u **Verwijderen** uit de beschikbare acties. Er verschijnt een pop-upvenster om te bevestigen dat u de geselecteerde groep(en) wilt verwijderen.

## <a name="manage-azure-ad-applications"></a>Azure AD-toepassingen beheren

U toepassingen of services die deel uitmaken van het Azure AD van uw bedrijf toegang geven tot uw Partner Center-account.

### <a name="add-existing-azure-ad-applications"></a>Bestaande Azure AD-toepassingen toevoegen

Ga als het gaat om toepassingen toe te voegen die al bestaan in de Azure Active Directory van uw bedrijf:

1. Selecteer Azure **AD-toepassingen toevoegen**op de pagina **Gebruikers** (onder **Accountinstellingen**).
2. Selecteer een of meer Azure AD-toepassingen in de lijst die wordt weergegeven. U het zoekvak gebruiken om te zoeken naar specifieke Azure AD-toepassingen. Als u meer dan één Azure AD-toepassing selecteert die u aan uw Partner Center-account wilt toevoegen, moet u ze dezelfde rol of set aangepaste machtigingen toewijzen. Als u meerdere Azure AD-toepassingen met verschillende rollen/machtigingen wilt toevoegen, herhaalt u deze stappen voor elke rol of set aangepaste machtigingen.
3. Wanneer u klaar bent met het selecteren van Azure AD-toepassingen, klikt u op **Geselecteerd toevoegen**.
4. Geef in de sectie **Rollen** de rollen of aangepaste machtigingen op voor de geselecteerde Azure AD-toepassing(en).
5. Selecteer **Opslaan**.

### <a name="add-new-azure-ad-applications"></a>Nieuwe Azure AD-toepassingen toevoegen

Als u Partner Center toegang wilt verlenen tot een gloednieuw Azure AD-toepassingsaccount, u er een maken in de sectie **Gebruikers.** Houd er rekening mee dat hiermee een nieuw account wordt gemaakt in uw bedrijfswerkaccount (Azure AD-tenant), niet alleen in uw Partner Center-account. Als u deze Azure AD-toepassing voornamelijk gebruikt voor Partner Center-verificatie en gebruikers deze niet rechtstreeks nodig hebt, u een geldig adres invoeren voor de **URL van de antwoord-url** en **de URI van de app-id,** zolang deze waarden niet worden gebruikt door een andere Azure AD-toepassing in uw directory.

1. Selecteer Azure **AD-toepassingen toevoegen**op de pagina **Gebruikers** (onder **Accountinstellingen**).
2. Selecteer op de volgende pagina **Nieuwe Azure AD-toepassing**.
3. Voer de **url van het antwoord** in voor de nieuwe Azure AD-toepassing. Dit is de URL waar gebruikers zich kunnen aanmelden en uw Azure AD-toepassing kunnen gebruiken (ook wel de URL van de app of de URL van aanmelding genoemd). De **URL van het antwoord** mag niet langer zijn dan 256 tekens en moet uniek zijn in uw directory.
4. Voer de **URI voor app-id's** in voor de nieuwe Azure AD-toepassing. Dit is een logische id voor de Azure AD-toepassing die wordt gepresenteerd wanneer één aanmeldingsaanvraag naar Azure AD wordt verzonden. Houd er rekening mee dat uri **voor app-id's** uniek moet zijn voor elke Azure AD-toepassing in uw map. Deze id mag niet langer zijn dan 256 tekens. Zie Toepassingen integreren met Azure [Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-modify-supported-accounts#change-the-application-registration-to-support-different-accounts)voor meer informatie over de URI van de app-id.
5. Geef in de sectie **Rollen** de rol(en) of aangepaste machtigingen op voor de Azure AD-toepassing.
6. Selecteer **Opslaan**.

Nadat u een Azure AD-toepassing hebt toegevoegd of gemaakt, u terugkeren naar de sectie **Gebruikers** en de toepassingsnaam selecteren om instellingen voor de toepassing te controleren, waaronder de tenant-id, client-id, url van antwoord en URI van de app-id.

### <a name="remove-an-azure-ad-application"></a>Een Azure AD-toepassing verwijderen

Als u een toepassing wilt verwijderen uit uw werkaccount (Azure AD-tenant), gaat u naar **Gebruikers** (onder **Accountinstellingen)** en selecteert u de toepassing die u wilt verwijderen met behulp van het selectievakje in de uiterst rechtse kolom en kiest u **Verwijderen** uit de beschikbare acties. Er verschijnt een pop-upvenster waarin u bevestigt dat u de geselecteerde toepassing(en) wilt verwijderen.

### <a name="manage-keys-for-an-azure-ad-application"></a>Sleutels beheren voor een Azure AD-toepassing

Als uw Azure AD-toepassing gegevens leest en schrijft in Microsoft Azure AD, heeft deze een sleutel nodig. U sleutels maken voor een Azure AD-toepassing door de gegevens ervan te bewerken in het Partnercentrum. U ook sleutels verwijderen die niet meer nodig zijn.

1. Selecteer op de pagina **Gebruikers** (onder **Accountinstellingen)** de naam van de Azure AD-toepassing. U ziet alle actieve sleutels voor de Azure AD-toepassing, inclusief de datum waarop de sleutel is gemaakt en wanneer deze verloopt. 
2. Als u een sleutel wilt verwijderen die niet meer nodig is, selecteert u **Verwijderen**.
3. Als u een nieuwe sleutel wilt toevoegen, selecteert u **Nieuwe sleutel toevoegen**.
4. U ziet een scherm met de **client-id** en **de sleutelwaarden.** Zorg ervoor dat u deze informatie afdrukt of kopieert, omdat u deze niet meer openen nadat u deze pagina hebt verlaten.
5. Als u meer toetsen wilt maken, selecteert u **Een andere toets toevoegen**.

## <a name="define-user-roles-and-permissions"></a>Gebruikersrollen en machtigingen definiëren

De gebruikers van uw bedrijf kunnen de volgende rollen en machtigingen voor het Commerciële Marketplace-programma op partnercentrum toegewezen krijgen:

- **Manager**
  - Heeft toegang tot alle microsoft-accountfuncties, behalve belasting- en uitbetalingsinstellingen
  - Kan gebruikers, rollen en werkaccounts beheren (tenants)
- **Developer**
  - Aanbiedingen beheren en publiceren
  - Sommige rapporten van uitgevers kunnen bekijken

> [!NOTE]
> Voor het Commercial Marketplace-programma worden de rollen Global admin, Business Contributor, Financial Contributor en Marketer niet gebruikt. Het toewijzen van deze rollen aan gebruikers heeft geen effect. Alleen de rollen Manager en Ontwikkelaar verlenen machtigingen aan gebruikers.

Zie [Gebruikersrollen en machtigingen toewijzen in Partnercentrum](https://docs.microsoft.com/partner-center/permissions-overview)voor meer informatie over het beheren van rollen en machtigingen in andere gebieden van het Partnercenter, zoals Azure Active Directory (AD), Cloud Solution Provider (CSP), Configuratieschermleverancier (CPV), Gastgebruikers of Microsoft Partner Network (MPN).

## <a name="manage-tenants"></a>Huurders beheren

Een Azure Active Directory (AD)-tenant, ook wel uw 'werkaccount' genoemd in deze documentatie, is een weergave van uw organisatie die is ingesteld in de Azure-portal en helpt u een specifiek exemplaar van Microsoft-cloudservices voor uw interne en externe gebruikers te beheren. Als uw organisatie zich heeft geabonneerd op een Microsoft-cloudservice, zoals Azure, Microsoft Intune of Office 365, is er een Azure AD-tenant voor u ingesteld.

U meerdere tenants instellen om te gebruiken met Partner Center. Elke gebruiker met de **Manager-rol** in het Partnercenter-account heeft de mogelijkheid om Azure AD-tenants uit het account toe te voegen en te verwijderen.  

### <a name="add-an-existing-tenant"></a>Een bestaande tenant toevoegen

Ga als volgende over een azure AD-tenant naar uw Partner Center-account:

1. Selecteer op de pagina **Tenants** (onder **Accountinstellingen)** de optie **Een andere Azure AD-tenant koppelen**.
2. Voer uw Azure AD-referenties in voor de tenant die u wilt koppelen.
3. Bekijk de organisatie en domeinnaam voor uw Azure AD-tenant. Als u de koppeling wilt voltooien, selecteert **u Bevestigen**.

Als de koppeling succesvol is, bent u klaar om accountgebruikers toe te voegen en te beheren in de sectie **Gebruikers** in het partnercentrum.

### <a name="create-a-new-tenant"></a>Een nieuwe tenant maken

Ga als volgende over een gloednieuwe Azure AD-tenant met uw Partner Center-account:

1. Selecteer **op** de pagina Tenants (onder **Accountinstellingen)** de optie **Een nieuwe Azure AD-tenant maken**.
2. Voer de adreslijstgegevens in voor uw nieuwe Azure AD:
    - **Domeinnaam:** de unieke naam die we gebruiken voor uw Azure AD-domein, samen met ".onmicrosoft.com". Als u bijvoorbeeld 'voorbeeld' hebt ingevoerd, is uw Azure AD-domein 'example.onmicrosoft.com'.
    - **Contact e-mail**: Een e-mailadres waar we indien nodig contact met u kunnen opnemen over uw account.
    - **Gegevens over het algemeen beheerdersaccount:** de voornaam, achternaam, gebruikersnaam en wachtwoord die u wilt gebruiken voor het nieuwe globale beheerdersaccount.
3. Selecteer **Maken** om de nieuwe domein- en accountgegevens te bevestigen.
4. Meld u aan met uw nieuwe gebruikersnaam en wachtwoord voor de globale beheerder van Azure AD om gebruikers toe te [voegen en te beheren.](#manage-users)

Zie het artikel [Een nieuwe tenant maken in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)voor meer informatie over het maken van nieuwe tenants in uw Azure-portal in plaats van via de Portal van het Partnercentrum.

### <a name="remove-a-tenant"></a>Een tenant verwijderen

Als u een tenant wilt verwijderen uit uw Partner Center-account, zoekt u de naam ervan op de pagina **Huurders** (in **accountinstellingen)** en selecteert u **Verwijderen**. U wordt gevraagd te bevestigen dat u de tenant wilt verwijderen. Zodra u dit doet, kunnen geen gebruikers in die tenant zich aanmelden bij het Partner Center-account en worden alle machtigingen die u voor die gebruikers hebt geconfigureerd, verwijderd.

Wanneer u een tenant verwijdert, kunnen alle gebruikers die vanuit die tenant zijn toegevoegd aan het Partner Center-account, zich niet meer aanmelden bij het account.

> [!TIP]
> U een tenant niet verwijderen als u momenteel bent aangemeld bij Partner Center met een account in dezelfde tenant. Als u een tenant wilt verwijderen, moet u zich aanmelden bij Partner center als **manager** voor een andere tenant die aan het account is gekoppeld. Als er slechts één tenant aan het account is gekoppeld, kan die tenant alleen worden verwijderd nadat hij zich heeft aanmelding bij het Microsoft-account dat het account heeft geopend.

## <a name="agreements"></a>Overeenkomsten

In de sectie **Overeenkomsten** van Partnercentrum (onder **Accountinstellingen)** u een lijst bekijken met de publicatieovereenkomsten die u hebt geautoriseerd. Deze overeenkomsten worden vermeld op naam en versienummer, inclusief de datum waarop deze is geaccepteerd en de naam van de gebruiker die de overeenkomst heeft geaccepteerd.

**Acties die nodig** zijn, kunnen boven aan deze pagina worden weergegeven als er updates van de overeenkomst zijn die uw aandacht nodig hebben. Als u een bijgewerkte overeenkomst wilt accepteren, leest u eerst de gekoppelde overeenkomstversie en selecteert u **Overeenkomst accepteren**.
