---
title: Een nieuwe Managed Service-aanbieding maken op de commerciële marktplaats
description: Een nieuwe Managed Service-aanbieding maken voor vermelding in Azure Marketplace met behulp van de Commerciële Marketplace-portal in partnercentrum.
author: JnHs
ms.author: jenhayes
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: de6e08534722deccb8be11248dd8af3ca2e66739
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983209"
---
# <a name="create-a-new-managed-service-offer"></a>Een nieuwe Managed Service-aanbieding maken

> [!IMPORTANT]
> We verplaatsen het beheer van uw managed services-aanbiedingen van Cloud Partner Portal naar Partner Center. Totdat uw aanbiedingen zijn gemigreerd, volgt u de instructies in [Een beheerde serviceaanbieding publiceren naar Azure Marketplace](../../lighthouse/how-to/publish-managed-services-offers.md) om uw aanbiedingen te beheren.

Managed Service biedt hulp bij het inschakelen van [Azure Lighthouse-scenario's.](../../lighthouse/overview.md) Wanneer een klant een Managed Service-aanbieding accepteert, kunnen ze resources voor [azure-gedelegeerd resourcebeheer](../../lighthouse/concepts/azure-delegated-resource-management.md)aan boord nemen.

Als u wilt beginnen met het maken van managed service-aanbiedingen, moet u ervoor zorgen dat u eerst [een Partnercenter-account maakt](./create-account.md) en het [dashboard Commerciële Marktplaats](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)opent, waarbij de **pagina Overzicht** is geselecteerd. U moet een [competentieniveau van het Silver- of Gold Cloud Platform](https://partner.microsoft.com/membership/cloud-platform-competency) hebben of een Azure Expert [MSP](https://partner.microsoft.com/membership/azure-expert-msp) zijn om een Managed Service-aanbieding te publiceren.

![Commercieel Marketplace-dashboard in partnercentrum](./media/new-offer-overview.png)

>[!Note]
> Zodra een aanbieding is gepubliceerd, worden bewerkingen van de aanbieding in het Partnercenter alleen bijgewerkt in het systeem en worden de fronten na het opnieuw publiceren bijgewerkt. Zorg ervoor dat u de aanbieding voor publicatie indient nadat u wijzigingen hebt aangebracht.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

Selecteer de knop **+ Nieuwe aanbieding** en selecteer vervolgens het **menu-item Managed Service.** Het dialoogvenster **Nieuwe aanbieding** wordt weergegeven.

### <a name="offer-id-and-alias"></a>Aanbiedings-ID en alias

- **Aanbiedings-id:** unieke id voor elke aanbieding in uw account. Deze ID is zichtbaar voor klanten in het URL-adres voor de marktplaatsaanbieding. Deze id kan alleen alfanumerieke tekens voor kleine letters bevatten (inclusief koppeltekens en underscores, maar geen witruimte), beperkt tot 50 tekens en kan niet worden gewijzigd nadat u **Maken**hebt geselecteerd.  Als u hier bijvoorbeeld *test-aanbieding-1* invoert, `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`is de URL van de aanbieding .

- **Aanbiedingsalias**: De naam die wordt gebruikt om te verwijzen naar de aanbieding binnen het Partnercentrum. Deze naam wordt niet gebruikt in de marktplaats en is anders dan de aanbiedingsnaam en andere waarden die aan klanten worden getoond. Deze waarde kan niet worden gewijzigd nadat u Maken hebt **geselecteerd.**

Zodra u uw **aanbiedings-id** en **aanbiedingsalias**invoert, selecteert u **Maken**. U dan aan alle verschillende onderdelen van uw aanbod werken.

## <a name="offer-setup"></a>Instelling voor aanbieding

Op de pagina **Installatie aanbieding** wordt om de volgende informatie gevraagd. Zorg ervoor dat u **Opslaan** selecteert nadat u deze velden hebt voltooid.

## <a name="connect-lead-management"></a>Leadbeheer verbinden

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Houd er rekening mee dat volgens het [beleid voor beheerde services](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)een **leadbestemming** vereist is. Dit zorgt voor een record in uw CRM-systeem telkens wanneer een klant uw aanbieding implementeert.

Zie [Overzicht van leadbeheer](./commercial-marketplace-get-customer-leads.md)voor meer informatie.

Vergeet niet de pagina op te **slaan** voordat u naar de volgende sectie gaat.

## <a name="properties"></a>Eigenschappen

Op de pagina **Eigenschappen** u de categorieën definiëren die worden gebruikt om uw aanbieding op de marktplaats te groeperen en de juridische contracten die uw aanbieding ondersteunen. Selecteer **Opslaan** nadat u deze pagina hebt voltooid.

### <a name="category"></a>Categorie

Selecteer minimaal één en maximaal vijf categorieën die worden gebruikt om uw aanbieding in de juiste zoekgebieden op marktplaats te plaatsen. Zorg ervoor dat u in de aanbiedingsbeschrijving roept hoe uw aanbieding deze categorieën ondersteunt.

### <a name="terms-and-conditions"></a>Voorwaarden

Geef uw eigen wettelijke voorwaarden op het gebied **van Algemene Voorwaarden.** U ook de URL opgeven waar uw algemene voorwaarden te vinden zijn. Klanten moeten deze voorwaarden accepteren voordat ze uw aanbieding kunnen proberen.

## <a name="offer-listing"></a>Aanbieding aanbieden

Op de pagina **Aanbiedingsaanbieding** u marktplaatsgegevens (aanbiedingsnaam, beschrijving, afbeeldingen, enz.) voor uw aanbieding definiëren.

> [!NOTE]
> Aanbieding sominhoud (zoals de beschrijving, documenten, screenshots, gebruiksvoorwaarden, enz.) is niet verplicht om in het Engels te zijn, zolang de aanbiedingsbeschrijving begint met de zinsnede: "Deze toepassing is alleen beschikbaar in [niet-Engelse taal]." Het is ook aanvaardbaar om een *URL voor nuttige koppelingen* op te geven om inhoud aan te bieden in een andere taal dan die welke wordt gebruikt in de inhoud van de aanbiedingsvermelding.

### <a name="name"></a>Name

De naam die u hier invoert, wordt aan klanten weergegeven als de titel van uw aanbiedingsvermelding. Dit veld wordt vooraf ingevuld met de tekst die u hebt ingevoerd voor **Alias Aanbieding** toen u de aanbieding maakte, maar u deze waarde wijzigen. Deze naam kan een handelsmerk hebben (en u handelsmerk- of auteursrechtsymbolen bevatten). De naam mag niet meer dan 50 tekens bevatten en kan geen emoji's bevatten.

### <a name="search-results-summary"></a>Overzicht van zoekresultaten

Geef een korte beschrijving van uw aanbieding (maximaal 100 tekens), die kan worden gebruikt in de zoekresultaten van marketplaces.

### <a name="long-summary"></a>Lange samenvatting

Geef een langere beschrijving van uw aanbieding (tot 256 tekens). Deze lange samenvatting kan ook worden gebruikt in de zoekresultaten van marktplaatsen.

### <a name="description"></a>Beschrijving

Geef een langere beschrijving van uw aanbieding (tot 3.000 tekens). Deze beschrijving wordt weergegeven aan klanten in het overzicht van de marktplaatsvermelding. Neem de waardepropositie van uw aanbieding, belangrijke voordelen, categorie- en/of brancheorganisaties, in-app aankoopmogelijkheden en eventuele vereiste informatie op.

Enkele tips voor het schrijven van uw beschrijving:  

- Beschrijf duidelijk de waardepropositie van uw aanbieding in de eerste paar zinnen van uw beschrijving. Neem de volgende items op in uw waardepropositie:
  - Beschrijving van het aanbod
  - Het type gebruiker dat profiteert van het aanbod
  - Behoeften van de klant of pijn die het aanbod adressen
- Houd er rekening mee dat de eerste paar zinnen kunnen worden weergegeven in de resultaten van zoekmachines.  
- Vertrouw niet op functies en functionaliteit om uw product te verkopen. Richt u in plaats daarvan op de waarde die u levert.  
- Gebruik zoveel mogelijk gebruik van branchespecifieke woordenschat of op voordelen gebaseerde formuleringen.

Als u de beschrijving van uw aanbieding aantrekkelijker wilt maken, gebruikt u de teksteditor met rijke tekst om uw beschrijving op te maken.

![De teksteditor met rijke tekst gebruiken](./media/text-editor2.png)

Gebruik de volgende instructies om de teksteditor met rijke tekst te gebruiken:

- Als u de indeling van uw inhoud wilt wijzigen, markeert u de tekst die u wilt opmaken en selecteert u een tekststijl, zoals hieronder wordt weergegeven:

     ![De teksteditor met rijke tekst gebruiken om de tekstnotatie te wijzigen](./media/text-editor3.png)

- Als u een lijst met opsommingstekens of nummers aan de tekst wilt toevoegen, gebruikt u de onderstaande opties:

     ![De teksteditor met rijke tekst gebruiken om lijsten toe te voegen](./media/text-editor4.png)

- Als u inspringing aan de tekst wilt toevoegen of verwijderen, gebruikt u de onderstaande opties:

     ![De teksteditor met rijke tekst gebruiken om in te springen](./media/text-editor5.png)

### <a name="privacy-policy-link"></a>Koppeling Privacybeleid

Voer de URL in voor het privacybeleid van uw organisatie (gehost op uw site). U bent verantwoordelijk voor het waarborgen dat uw app voldoet aan de privacywet- en regelgeving en voor het verstrekken van een geldig privacybeleid.

### <a name="useful-links"></a>Handige koppelingen

Geef optionele aanvullende online documenten over uw oplossing. Voeg extra handige koppelingen toe door op **+ Een koppeling toevoegen te**klikken .

### <a name="contact-information"></a>Contactgegevens

In deze sectie moet u de naam, e-mail en het telefoonnummer opgeven voor een **contactpersoon voor ondersteuning** en een contactpersoon voor **engineering.** Deze informatie wordt niet aan klanten weergegeven, maar is beschikbaar voor Microsoft en kan worden verstrekt aan CSP-partners.

### <a name="support-urls"></a>URL's ondersteunen

Als u ondersteuningswebsites hebt voor **Azure Global-klanten** en/of **Azure Government-klanten,** u deze URL's hier verstrekken.

### <a name="marketplace-images"></a>Marketplace-afbeeldingen

In deze sectie u logo's en afbeeldingen verstrekken die worden gebruikt bij het weergeven van uw aanbieding aan de klant. Alle afbeeldingen moeten in .png-indeling zijn.

#### <a name="marketplace-logos"></a>Marketplace-logo's

Er zijn vier logoformaten vereist: **Klein (40x40),** **Medium (90x90),** **Large (115x115)** en **Wide (255x115)**. Volg deze richtlijnen voor uw logo's:

- Het Azure-ontwerp heeft een eenvoudig kleurenpalet. Beperk het aantal primaire en secundaire kleuren in uw logo.
- De themakleuren van de portal zijn wit en zwart. Gebruik deze kleuren niet als de achtergrondkleur voor uw logo. Gebruik een kleur waardoor uw logo opvalt in de portal. We adviseren eenvoudige primaire kleuren.
- Als u een transparante achtergrond gebruikt, moeten het logo en de tekst niet wit, zwart of blauw zijn.
- Het uiterlijk van uw logo moet plat zijn, zonder kleurovergangen. Gebruik geen achtergrond met een kleurovergang in het logo.
- Plaats geen tekst in het logo, ook niet de naam van uw bedrijf of merk.
- Zorg ervoor dat het logo niet is uitgerekt.

#### <a name="screenshots"></a>Schermopnamen

Voeg maximaal vijf screenshots toe die laten zien hoe uw aanbieding werkt. Alle screenshots moeten 1280 x 720 pixels zijn.

#### <a name="videos"></a>Video's

Je optioneel maximaal vijf video's toevoegen die je aanbod demonstreren. Deze video's moeten worden gehost op YouTube en/of Vimeo. Voer voor elke video de naam, de URL en een miniatuurafbeelding van de video (1280 x 720 pixels) in.

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende bronnen voor marktplaatsvermeldingen

- [Aanbevolen procedures voor aanbiedingen voor marktplaatsaanbiedingen](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Preview

Voordat je je aanbieding live publiceert naar het bredere marketplace-aanbod, moet je het eerst beschikbaar maken voor een beperkt preview-publiek. Hiermee u bevestigen hoe u deze aanbieding in de Azure Marketplace aanbiedt voordat u deze beschikbaar maakt voor klanten. Ondersteunings- en engineeringteams van Microsoft kunnen uw aanbieding ook bekijken tijdens deze previewperiode.

U de voorvertoningsdoelgroep definiëren door azure-abonnements-id's in te voeren in de sectie **Voorvertoningsdoelgroep.** U maximaal 10 abonnements-id's handmatig invoeren of een CSV-bestand uploaden met maximaal 100 abonnements-id's.

Klanten die aan deze abonnementen zijn gekoppeld, kunnen de aanbieding in Azure Marketplace bekijken voordat deze live gaat. Zorg ervoor dat u hier uw eigen abonnementen opneemt, zodat u een voorbeeld van uw aanbieding bekijken.

## <a name="plan-overview"></a>Overzicht van plannen

Elke aanbieding moet een of meer plannen hebben (soms aangeduid als SKU's). U meerdere plannen toevoegen om verschillende functiesets tegen verschillende prijzen te ondersteunen of om een specifiek plan aan te passen voor een beperkt publiek van specifieke klanten. Klanten kunnen de plannen bekijken die voor hen beschikbaar zijn onder het bovenliggende aanbod.

Selecteer **op** de pagina Overzicht van het abonnement de optie **+ Nieuw plan maken**. Voer vervolgens een **plan-id** en een **naam van het plan in.** Beide waarden kunnen alleen alfanumerieke letters, streepjes en underscores bevatten met een maximum van 50 tekens. Deze waarden kunnen zichtbaar zijn voor klanten en kunnen niet worden gewijzigd nadat u de aanbieding hebt gepubliceerd.

Selecteer **Maken** zodra u deze waarden hebt ingevoerd om aan uw plan te blijven werken. Er zijn drie secties om te voltooien: **Aanbieding plannen,** **Prijzen en beschikbaarheid**en Technische **configuratie**.

### <a name="plan-listing"></a>Aanbieding plannen

Geef eerst een **overzicht van** de zoekresultaten voor het plan. Dit is een korte beschrijving van uw plan (maximaal 100 tekens), dat kan worden gebruikt in de zoekresultaten van marktplaatsen.

Voer vervolgens een **beschrijving** in die een meer gedetailleerde uitleg van het plan geeft.

### <a name="pricing-and-availability"></a>Prijzen en beschikbaarheid

Momenteel is er slechts één prijsmodel dat kan worden gebruikt voor Managed Service-aanbieding: **Breng uw eigen licentie (BYOL)** mee. Dit betekent dat u uw klanten rechtstreeks in rekening brengt voor kosten in verband met deze aanbieding en dat Microsoft u geen kosten in rekening brengt.

Met de sectie **Zichtbaarheid plannen** u aangeven of dit plan [privé](../../marketplace/private-offers.md)moet zijn. Als u het vak **Dit is een privé-abonnement** niet aangevinkt laat, wordt uw abonnement niet beperkt tot specifieke klanten (of tot een bepaald aantal klanten).

Als u dit abonnement alleen beschikbaar wilt maken voor specifieke klanten, selecteert u **Ja**. Wanneer u dit doet, moet u de klanten identificeren door hun abonnements-id's aan te bieden. Deze kunnen één voor één worden ingevoerd (voor maximaal 10 abonnementen) of door een CSV-bestand te uploaden (voor maximaal 10.000 abonnementen in alle abonnementen). Zorg ervoor dat u hier uw eigen abonnementen opneemt, zodat u de aanbieding testen en valideren.

> [!IMPORTANT]
> Zodra een plan als openbaar is gepubliceerd, u het niet privé wijzigen. Als u wilt bepalen welke klanten uw aanbieding kunnen accepteren en resources kunnen delegeren, gebruikt u een privéabonnement. Met een openbaar plan u de beschikbaarheid niet beperken tot bepaalde klanten of zelfs tot een bepaald aantal klanten (hoewel u stoppen met de verkoop van het abonnement volledig als u ervoor kiest om dit te doen). U [de toegang tot een delegatie alleen verwijderen](../../lighthouse/how-to/onboard-customer.md#remove-access-to-a-delegation) nadat een klant een aanbieding heeft geaccepteerd als u een **autorisatie** hebt opgenomen met de **roldefinitiedie** is ingesteld op [de rol Van registratietoewijzing voor managed services](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) wanneer u de aanbieding hebt gepubliceerd. U ook contact opnemen met de klant en hen vragen om uw toegang te [verwijderen.](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)

### <a name="technical-configuration"></a>Technische configuratie

Dit gedeelte van uw plan maakt een manifest met autorisatiegegevens voor het beheren van klantbronnen. Deze informatie is vereist om [Azure gedelegeerd bronbeheer](../../lighthouse/concepts/azure-delegated-resource-management.md)in te schakelen.

Controleer [tenants, rollen en gebruikers in Azure Lighthouse-scenario's](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) om te begrijpen welke rollen worden ondersteund en welke aanbevolen procedures voor het definiëren van uw autorisaties.

> [!NOTE]
> Zoals hierboven vermeld, zijn de gebruikers en rollen in uw **autorisatievermeldingen** van toepassing op elke klant die het abonnement koopt. Als u de toegang tot een specifieke klant wilt beperken, moet u een privéplan publiceren voor exclusief gebruik.

#### <a name="manifest"></a>Manifest

Geef eerst een **versie** voor het manifest. Gebruik de indeling *n.n.n* (bijvoorbeeld 1.2.5).

Voer vervolgens uw **tenant-id**in. Dit is een GUID die is gekoppeld aan de Azure Active Directory -tenant-id (Azure AD) van uw organisatie. dat wil zeggen, de beherende tenant van waaruit u toegang krijgt tot de bronnen van uw klanten. Als u dit niet bij de hand hebt, u deze vinden door boven aan de rechterbovenzijde van de Azure-portal over uw accountnaam te zweven of **door de schakelmap te**selecteren.

Als u een nieuwe versie van uw aanbieding publiceert en een bijgewerkt manifest moet maken, selecteert u **+ Nieuw manifest**. Zorg ervoor dat u het versienummer van de vorige manifestversie verhoogt.

#### <a name="authorization"></a>Autorisatie

Autorisaties definiëren de entiteiten in uw beheertenant die toegang hebben tot bronnen en abonnementen voor klanten die het abonnement kopen. Elk van deze entiteiten krijgt een ingebouwde rol die specifieke toegangsniveaus verleent.

U maximaal twintig autorisaties maken voor elk plan.

> [!TIP]
> In de meeste gevallen wilt u rollen toewijzen aan een Azure AD-gebruikersgroep of serviceprincipal, in plaats van aan een reeks afzonderlijke gebruikersaccounts. Hiermee u toegang toevoegen of verwijderen voor individuele gebruikers zonder dat u het abonnement hoeft bij te werken en opnieuw te publiceren wanneer uw toegangsvereisten worden gewijzigd. Wanneer u rollen toewijs aan Azure AD-groepen [toestelt, moet u ervoor zorgen dat het **groepstype** **Beveiliging** is en niet **Office 365**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Zie [Tenants, rollen en gebruikers in Azure Lighthouse-scenario's](../../lighthouse/concepts/tenants-users-roles.md)voor aanvullende aanbevelingen.

Voor elke **autorisatie**moet u het volgende geven. U vervolgens zo vaak als nodig **autorisatie** toevoegen selecteren om meer gebruikers en roldefinities toe te voegen.

- **Azure AD-object-id:** de Azure AD-id van een gebruiker, gebruikersgroep of toepassing die bepaalde machtigingen (zoals gedefinieerd door de roldefinitie) aan de resources van uw klanten krijgt.
- **Azure AD Object Display Name:** Een vriendelijke naam om de klant inzicht te geven in het doel van deze autorisatie. De klant ziet deze naam bij het delegeren van resources.
- **Roldefinitie:** selecteer een van de beschikbare ingebouwde Azure AD-rollen in de lijst. In deze rol worden de machtigingen bepaald die de gebruiker in het veld **Azure AD-object-id** heeft op de resources van uw klanten. Zie Ingebouwde rollen en [Functieondersteuning voor Azure-gedelegeerd bronbeheer voor](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management)beschrijvingen van deze rollen. [Built-in roles](../../role-based-access-control/built-in-roles.md)
  > [!NOTE]
  > Als toepasselijke nieuwe ingebouwde rollen worden toegevoegd aan Azure, zullen ze hier beschikbaar komen, hoewel er enige vertraging kan optreden voordat ze worden weergegeven.
- **Toewijsbare rollen:** deze optie wordt alleen weergegeven als u gebruikerstoegangsbeheerder hebt geselecteerd in de **roldefinitie** voor deze autorisatie. Als dat het zo is, moet u hier een of meer toewijsbare rollen toevoegen. De gebruiker in het azure **AD-object-id-veld** kan deze rollen toewijzen aan [beheerde identiteiten,](../../active-directory/managed-identities-azure-resources/overview.md)wat nodig is om beleid te [implementeren dat kan worden gesaneerd.](../../lighthouse/how-to/deploy-policy-remediation.md) Houd er rekening mee dat er geen andere machtigingen zijn die normaal zijn gekoppeld aan de functie Administrator voor gebruikerstoegang, voor deze gebruiker.

> [!TIP]
> Als u ervoor wilt zorgen dat u de [toegang tot een delegatie](../../lighthouse/how-to/onboard-customer.md#remove-access-to-a-delegation) verwijderen als dat nodig is, neemt u een **autorisatie** op met de **functiedefinitie** die is ingesteld op [de rol Registratietoewijzing voor beheerde services](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Als deze rol niet is toegewezen, kunnen gedelegeerde resources alleen worden verwijderd door een gebruiker in de tenant van de klant.

Zodra u alle secties voor uw plan hebt voltooid, u zo vaak als u extra plannen wilt maken, nieuw **plan** maken. Selecteer **Opslaan** als u klaar bent.

## <a name="publish"></a>Publiceren

### <a name="submit-offer-to-preview"></a>Voorstel indienen om een voorbeeld te bekijken

Zodra u alle vereiste gedeelten van de aanbieding hebt voltooid, selecteert u **publiceren** in de rechterbovenhoek van het portaal. U wordt doorgestuurd naar de **pagina Beoordeling en publicatie.**

Als het de eerste keer is dat u deze aanbieding publiceert, u het als volgt doen:

- Zie de voltooiingsstatus voor elk gedeelte van de aanbieding.
  - *Niet gestart* - betekent dat de sectie niet is aangeraakt en moet worden voltooid.
  - *Onvolledig* - betekent dat de sectie fouten bevat die moeten worden opgelost of dat er meer informatie moet worden verstrekt. Ga terug naar de sectie(s) en werk deze bij.
  - *Voltooid* - betekent dat de sectie volledig is, alle vereiste gegevens zijn verstrekt en er geen fouten zijn. Alle onderdelen van de aanbieding moeten volledig zijn voordat u de aanbieding indienen.
- Geef in de sectie **Notities voor certificering** testinstructies aan het certificeringsteam om ervoor te zorgen dat uw app correct is getest, naast eventuele aanvullende notities die nuttig zijn om uw app te begrijpen.
- Dien de publicatieaanbieding in door **Verzenden te selecteren.** We sturen je een e-mail wanneer er een preview-versie van de aanbieding beschikbaar is om te bekijken en goed te keuren. Keer terug naar partnercentrum en selecteer **Go-live** voor de aanbieding om uw aanbieding aan het publiek te publiceren (of als een privé-aanbieding, aan het particuliere publiek).

### <a name="customer-experience-and-offer-management"></a>Klantervaring en aanbod management

Wanneer een klant uw aanbieding implementeert, kunnen ze abonnementen of resourcegroepen delegeren voor [azure-gedelegeerd resourcebeheer](../../lighthouse/concepts/azure-delegated-resource-management.md). Zie [Het onboardingproces van de klant](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process)voor meer informatie over dit proces.

U op elk gewenst moment [een bijgewerkte versie van uw aanbieding publiceren.](update-existing-offer.md) U bijvoorbeeld een nieuwe roldefinitie toevoegen aan een eerder gepubliceerde aanbieding. Wanneer u dit doet, zien klanten die de aanbieding al hebben toegevoegd een pictogram op de pagina [**Serviceproviders**](../../lighthouse/how-to/view-manage-service-providers.md) in de Azure-portal waarmee ze weten dat er een update beschikbaar is. Elke klant kan de wijzigingen bekijken en beslissen of hij of zij de nieuwe versie wil bijwerken.

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande aanbieding bijwerken in Commerciële Marketplace](./update-existing-offer.md)
- [Meer informatie over Azure Lighthouse](../../lighthouse/overview.md)