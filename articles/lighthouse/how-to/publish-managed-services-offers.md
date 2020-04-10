---
title: Een Managed Service-aanbieding publiceren naar Azure Marketplace
description: Meer informatie over het publiceren van een Managed Service-aanbieding die klanten aanbiedt aan Azure-gedelegeerd bronbeheer.
ms.date: 04/08/2020
ms.topic: conceptual
ms.openlocfilehash: 247f711188fa10de19cece27f164fdfa71612d1b
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991906"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Een Managed Service-aanbieding publiceren naar Azure Marketplace

> [!IMPORTANT]
> Vanaf 13 april 2020 beginnen we met het verplaatsen van het beheer van uw Managed Service-aanbiedingen naar Partner Center. Na de migratie maak en beheer je je aanbiedingen in partnercentrum. Volg de instructies in [Een nieuwe Managed Service-aanbieding maken](../../marketplace/partner-center-portal/create-new-managed-service-offer.md) om uw gemigreerde aanbiedingen te beheren.

In dit artikel leert u hoe u een openbare of private Managed Service-aanbieding publiceert naar [Azure Marketplace](https://azuremarketplace.microsoft.com) met behulp van Cloud [Partner Portal.](https://cloudpartner.azure.com/) Klanten die de aanbieding kopen, kunnen vervolgens abonnementen en resourcegroepen aan boord nemen voor [azure-gedelegeerd resourcebeheer.](../concepts/azure-delegated-resource-management.md)

## <a name="publishing-requirements"></a>Publicatievereisten

Je moet een geldig account hebben [in Partner Center](../../marketplace/partner-center-portal/create-account.md) om aanbiedingen te maken en te publiceren. Als je nog geen account hebt, leidt het [aanmeldingsproces](https://aka.ms/joinmarketplace) je door de stappen van het maken van een account in Partner Center en je inschrijven voor het Commercial Marketplace-programma.

Volgens de [Managed Service-certificeringsvereisten](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements)moet u een competentieniveau van het [Silver- of Gold Cloud Platform](https://docs.microsoft.com/partner-center/learn-about-competencies) hebben of een Azure Expert [MSP](https://partner.microsoft.com/membership/azure-expert-msp) zijn om een Managed Service-aanbieding te publiceren.

Uw MPN-id (Microsoft Partner Network) wordt [automatisch gekoppeld aan](../../billing/billing-partner-admin-link-started.md) de aanbiedingen die u publiceert om uw impact op klantinteracties bij te houden.

> [!NOTE]
> Als u een aanbieding niet wilt publiceren naar Azure Marketplace, u klanten handmatig ingebruiknemen met Azure Resource Manager-sjablonen. Zie [Een klant aan boord van Azure-gedelegeerd resourcebeheer](onboard-customer.md)voor meer informatie.

Het publiceren van een Managed Service-aanbieding is vergelijkbaar met het publiceren van een ander type aanbieding naar Azure Marketplace. Zie [Azure Marketplace en AppSource Publishing Guide](../../marketplace/marketplace-publishers-guide.md)voor meer informatie over het algemene publicatieproces. U moet ook het [certificeringsbeleid voor commerciële marktplaatsen](https://docs.microsoft.com/legal/marketplace/certification-policies)bekijken, met name de sectie [Managed Services.](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)

Zodra een klant uw aanbieding toevoegt, kan deze een of meer abonnementen of resourcegroepen delegeren, die vervolgens worden [ingeschakeld voor azure-gedelegeerd bronbeheer.](#the-customer-onboarding-process)

> [!IMPORTANT]
> Elk plan in een beheerde serviceaanbieding bevat een sectie **Manifestgegevens,** waarin u de Azure Active Directory-entiteiten (Azure AD) in uw tenant definieert die toegang hebben tot de gedelegeerde brongroepen en/of abonnementen voor klanten die dat abonnement kopen. Het is belangrijk om te weten dat elke groep (of gebruiker of service principal) die u opneemt, dezelfde machtigingen heeft voor elke klant die het abonnement koopt. Als u verschillende groepen wilt toewijzen om met elke klant samen te werken, moet u een afzonderlijk [privéplan](../../marketplace/private-offers.md) publiceren dat exclusief is voor elke klant.

## <a name="create-your-offer"></a>Uw voorstel maken

1. Meld u aan bij de [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Selecteer in het linkernavigatiemenu De optie **Nieuwe aanbieding**en selecteer **Beheerde services**.
3. Je ziet een **editorsectie** voor je aanbieding met vier onderdelen om in te vullen: **Aanbiedingsinstellingen,** **Abonnementen,** **Marktplaats**en **Ondersteuning.** Lees verder voor meer informatie over het invullen van deze secties.

### <a name="enter-offer-settings"></a>Aanbiedingsinstellingen invoeren

Geef in de sectie **Aanbiedingsinstellingen** het volgende op:

|Veld  |Beschrijving  |
|---------|---------|
|**Aanbiedings-id**     | Een unieke id voor uw aanbieding (binnen uw uitgeversprofiel). Deze ID kan alleen alfanumerieke letters, streepjes en underscores bevatten met een maximum van 50 tekens. Houd er rekening mee dat de aanbiedings-id zichtbaar kan zijn voor klanten op plaatsen zoals in product-URL's en factureringsrapporten. Zodra u de aanbieding publiceert, u deze waarde niet meer wijzigen.        |
|**Publisher-id**     | De uitgever-id die aan de aanbieding wordt gekoppeld. Als u meer dan één publisher ID hebt, u degene selecteren die u voor deze aanbieding wilt gebruiken.       |
|**Naam**     | De naam (maximaal 50 tekens) die klanten zien voor uw aanbieding in Azure Marketplace en in de Azure-portal. Gebruik een herkenbare merknaam die klanten zullen begrijpen: als u deze aanbieding promoot via uw eigen website, moet u hier exact dezelfde naam gebruiken.        |

Wanneer u klaar bent, selecteert u **Opslaan**. Nu bent u klaar om verder te gaan naar de **sectie Plannen.**

### <a name="create-plans"></a>Plannen maken

Elke aanbieding moet een of meer plannen hebben (soms aangeduid als SKU's). U meerdere plannen toevoegen om verschillende functiesets tegen verschillende prijzen te ondersteunen of om een specifiek plan aan te passen voor een beperkt publiek van specifieke klanten. Klanten kunnen de plannen bekijken die voor hen beschikbaar zijn onder het bovenliggende aanbod.

Selecteer Nieuw **plan**in de sectie Plannen . Voer vervolgens een **plan-id in**. Deze ID kan alleen alfanumerieke letters, streepjes en underscores bevatten met een maximum van 50 tekens. De plan-id kan zichtbaar zijn voor klanten op plaatsen zoals in product-URL's en factureringsrapporten. Zodra u de aanbieding publiceert, u deze waarde niet meer wijzigen.

#### <a name="plan-details"></a>Details plannen

Vul de volgende secties in de sectie **Plandetails in:**

|Veld  |Beschrijving  |
|---------|---------|
|**Titel**     | Vriendelijke naam voor het plan voor weergave. Maximale lengte van 50 tekens.        |
|**Samenvatting**     | Beknopte beschrijving van het plan voor weergave onder de titel. Maximale lengte van 100 tekens.        |
|**Beschrijving**     | Beschrijvingstekst die een meer gedetailleerde uitleg van het plan geeft.         |
|**Factureringsmodel**     | Er zijn hier 2 factureringsmodellen weergegeven, maar u moet uw **eigen licentie** voor beheerde services aanbieden kiezen. Dit betekent dat u uw klanten rechtstreeks in rekening brengt voor kosten in verband met deze aanbieding en dat Microsoft u geen kosten in rekening brengt.   |
|**Is dit een privé plan?**     | Geeft aan of de SKU privé of openbaar is. De standaardinstelling is **Nee** (openbaar). Als u deze selectie verlaat, wordt uw abonnement niet beperkt tot specifieke klanten (of tot een bepaald aantal klanten); nadat u een openbaar plan hebt gepubliceerd, u het niet later wijzigen in privé. Als u dit abonnement alleen beschikbaar wilt maken voor specifieke klanten, selecteert u **Ja**. Wanneer u dit doet, moet u de klanten identificeren door hun abonnements-id's aan te bieden. Deze kunnen één voor één worden ingevoerd (voor maximaal 10 abonnementen) of door een CSV-bestand te uploaden (voor maximaal 20.000 abonnementen). Zorg ervoor dat u hier uw eigen abonnementen opneemt, zodat u de aanbieding testen en valideren. Zie [Privé-SKU's en -plannen](../../marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)voor meer informatie.  |

> [!IMPORTANT]
> Zodra een plan als openbaar is gepubliceerd, u het niet privé wijzigen. Als u wilt bepalen welke klanten uw aanbieding kunnen accepteren en resources kunnen delegeren, gebruikt u een privéabonnement. Met een openbaar plan u de beschikbaarheid niet beperken tot bepaalde klanten of zelfs tot een bepaald aantal klanten (hoewel u stoppen met de verkoop van het abonnement volledig als u ervoor kiest om dit te doen). U [de toegang tot een delegatie alleen verwijderen](onboard-customer.md#remove-access-to-a-delegation) nadat een klant een aanbieding heeft geaccepteerd als u een **autorisatie** hebt opgenomen met de **roldefinitiedie** is ingesteld op [de rol Van registratietoewijzing voor managed services](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) wanneer u de aanbieding hebt gepubliceerd. U ook contact opnemen met de klant en hen vragen om uw toegang te [verwijderen.](view-manage-service-providers.md#add-or-remove-service-provider-offers)

#### <a name="manifest-details"></a>Manifestdetails

Vul de sectie **Manifestdetails** voor uw abonnement in. Dit maakt een manifest met autorisatie-informatie voor het beheren van klantbronnen. Deze informatie is vereist om Azure gedelegeerd bronbeheer in te schakelen.

> [!NOTE]
> Zoals hierboven vermeld, zijn de gebruikers en rollen in uw **autorisatievermeldingen** van toepassing op elke klant die het abonnement koopt. Als u de toegang tot een specifieke klant wilt beperken, moet u een privéplan publiceren voor exclusief gebruik.

Geef eerst een **versie** voor het manifest. Gebruik de indeling *n.n.n* (bijvoorbeeld 1.2.5).

Voer vervolgens uw **tenant-id**in. Dit is een GUID die is gekoppeld aan de Azure Active Directory-tenant-id van uw organisatie (d.w.z. de tenant waarin u werkt om de resources van uw klanten te beheren). Als u dit niet bij de hand hebt, u deze vinden door boven aan de rechterbovenzijde van de Azure-portal over uw accountnaam te zweven of **door de schakelmap te**selecteren.

Voeg ten slotte een of meer **autorisatievermeldingen** toe aan uw abonnement. Autorisaties definiëren de entiteiten die toegang hebben tot bronnen en abonnementen voor klanten die het abonnement kopen en toewijzen rollen die specifieke toegangsniveaus toekennen.

> [!TIP]
> In de meeste gevallen wilt u machtigingen toewijzen aan een Azure AD-gebruikersgroep of serviceprincipal, in plaats van aan een reeks afzonderlijke gebruikersaccounts. Hiermee u toegang toevoegen of verwijderen voor individuele gebruikers zonder dat u het abonnement hoeft bij te werken en opnieuw te publiceren wanneer uw toegangsvereisten worden gewijzigd. Zie [Tenants, rollen en gebruikers in Azure Lighthouse-scenario's](../concepts/tenants-users-roles.md)voor aanvullende aanbevelingen.

Voor elke **autorisatie**moet u het volgende geven. U vervolgens **Nieuwe autorisatie** zo vaak selecteren als nodig is om meer gebruikers en roldefinities toe te voegen.

- **Azure AD-object-id:** de Azure AD-id van een gebruiker, gebruikersgroep of toepassing die bepaalde machtigingen (zoals beschreven in de roldefinitie) aan de bronnen van uw klanten zal krijgen.
- **Azure AD Object Display Name:** Een vriendelijke naam om de klant inzicht te geven in het doel van deze autorisatie. De klant ziet deze naam bij het delegeren van resources.
- **Roldefinitie:** selecteer een van de beschikbare ingebouwde Azure AD-rollen in de lijst. In deze rol worden de machtigingen bepaald die de gebruiker in het veld **Azure AD-object-id** heeft op de resources van uw klanten. Zie Ingebouwde rollen en [Functieondersteuning voor Azure-gedelegeerd bronbeheer voor](../concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management)beschrijvingen van deze rollen. [Built-in roles](../../role-based-access-control/built-in-roles.md)
  > [!NOTE]
  > Als toepasselijke nieuwe ingebouwde rollen worden toegevoegd aan Azure, zullen ze hier beschikbaar komen, hoewel er enige vertraging kan optreden voordat ze worden weergegeven.
- **Toewijsbare rollen:** dit is alleen vereist als u gebruikerstoegangsbeheerder hebt geselecteerd in de **roldefinitie** voor deze autorisatie. Als dat het zo is, moet u hier een of meer toewijsbare rollen toevoegen. De gebruiker in het azure **AD-object-id-veld** kan deze **toewijsbare rollen** toewijzen aan [beheerde identiteiten,](../../active-directory/managed-identities-azure-resources/overview.md)wat nodig is om beleid te [implementeren dat kan worden gesaneerd.](deploy-policy-remediation.md) Houd er rekening mee dat er geen andere machtigingen zijn die normaal zijn gekoppeld aan de functie Administrator voor gebruikerstoegang, voor deze gebruiker. Als u hier niet een of meer rollen selecteert, wordt uw inzending niet gecertificeerd. (Als u gebruikerstoegangsbeheerder niet hebt geselecteerd voor de roldefinitie van deze gebruiker, heeft dit veld geen effect.)

> [!TIP]
> Als u ervoor wilt zorgen dat u de [toegang tot een delegatie](onboard-customer.md#remove-access-to-a-delegation) verwijderen als dat nodig is, neemt u een **autorisatie** op met de **functiedefinitie** die is ingesteld op [de rol Registratietoewijzing voor beheerde services](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Als deze rol niet is toegewezen, kunnen gedelegeerde resources alleen worden verwijderd door een gebruiker in de tenant van de klant.

Zodra u de informatie hebt voltooid, u **Nieuw abonnement** zo vaak selecteren als nodig is om extra plannen te maken. Wanneer u klaar bent, selecteert u **Opslaan**en gaat u verder naar de sectie **Marketplace.**

### <a name="provide-marketplace-text-and-images"></a>Marketplace-tekst en -afbeeldingen aanbieden

In de sectie **Marketplace** u de tekst en afbeeldingen weergeven die klanten in Azure Marketplace en de Azure-portal zien.

Vul de volgende velden in de sectie **Overzicht** in:

|Veld  |Beschrijving  |
|---------|---------|
|**Titel**     |  Titel van het aanbod, vaak de lange, formele naam. Deze titel zal prominent worden weergegeven in de markt. Maximale lengte van 50 tekens. In de meeste gevallen moet dit hetzelfde zijn als de **naam die** u hebt ingevoerd in de sectie **Aanbiedingsinstellingen.**       |
|**Samenvatting**     | Korte doel of functie van uw aanbod. Dit wordt meestal weergegeven onder de titel. Maximale lengte van 100 tekens.        |
|**Lange samenvatting**     | Een langere samenvatting van het doel of de functie van uw aanbod. Maximale lengte van 256 tekens.        |
|**Beschrijving**     | Meer informatie over uw aanbod. Dit veld heeft een maximale lengte van 3000 tekens en ondersteunt eenvoudige HTML-opmaak. U moet de woorden 'managed service' of 'managed services' ergens in uw beschrijving opnemen.       |
|**Marketing-id**     | Een unieke URL-vriendelijke id. Deze id kan alleen alfanumerieke tekens en streepjes bevatten. Het zal worden gebruikt in Marketplace URL's voor deze aanbieding. Als uw uitgever-id bijvoorbeeld *contoso* is en uw marketing-id *voorbeeldApp*is, *https://azuremarketplace.microsoft.com/marketplace/apps/contoso-sampleApp*is de URL voor uw aanbieding in Azure Marketplace .        |
|**Voorbeeld van abonnements-iD's bekijken**     | Voeg één tot 100 abonnements-id's toe. De klanten die aan deze abonnementen zijn gekoppeld, kunnen de aanbieding in Azure Marketplace bekijken voordat deze live gaat. We raden u aan uw eigen abonnementen hier op te nemen, zodat u een voorbeeld bekijken van hoe uw aanbieding wordt weergegeven in de Azure Marketplace voordat u deze beschikbaar maakt voor klanten.  (Microsoft-ondersteunings- en engineeringteams kunnen uw aanbieding ook bekijken tijdens deze previewperiode.)   |
|**Nuttige links**     | URL's met betrekking tot uw aanbieding, zoals documentatie, releasenotes, veelgestelde vragen, enz.        |
|**Voorgestelde categorieën (Max 5)**     | Een of meer categorieën (maximaal vijf) die van toepassing zijn op uw aanbod. Met deze categorieën kunnen klanten uw aanbieding ontdekken in Azure Marketplace en de Azure-portal.        |

In de sectie **Marketingartefacten** u logo's en andere elementen uploaden om met uw aanbieding te worden weergegeven. U optioneel screenshots of koppelingen uploaden naar video's die klanten kunnen helpen uw aanbieding te begrijpen.

Er zijn vier logoformaten vereist: **Klein (40x40),** **Medium (90x90),** **Large (115x115)** en **Wide (255x115)**. Volg deze richtlijnen voor uw logo's:

- Het Azure-ontwerp heeft een eenvoudig kleurenpalet. Beperk het aantal primaire en secundaire kleuren in uw logo.
- De themakleuren van de portal zijn wit en zwart. Gebruik deze kleuren niet als de achtergrondkleur voor uw logo. Gebruik een kleur waardoor uw logo opvalt in de portal. We adviseren eenvoudige primaire kleuren.
- Als u een transparante achtergrond gebruikt, moeten het logo en de tekst niet wit, zwart of blauw zijn.
- Het uiterlijk van uw logo moet plat zijn, zonder kleurovergangen. Gebruik geen achtergrond met een kleurovergang in het logo.
- Plaats geen tekst in het logo, ook niet de naam van uw bedrijf of merk.
- Zorg ervoor dat het logo niet is uitgerekt.

Het **Hero (815x290)** logo is optioneel, maar wordt aanbevolen. Als u een heldenlogo opneemt, volgt u de volgende richtlijnen:

- Neem geen tekst op in het hero-logo en laat 415 pixels lege ruimte achter aan de rechterkant van het logo. Dit is vereist om ruimte te laten voor tekstelementen die programmatisch worden ingebed: uw uitgever weergavenaam, plantitel, bieden lange samenvatting.
- De achtergrond van je heldenlogo is mogelijk niet zwart, wit of transparant. Zorg ervoor dat uw achtergrondkleur niet te licht is, omdat de ingesloten tekst in het wit wordt weergegeven.
- Zodra u uw aanbieding met een heldenpictogram publiceert, u het niet verwijderen (hoewel u het met een verschillende versie indien gewenst bijwerken).

In de sectie **Leadmanagement** u het CRM-systeem selecteren waar uw leads worden opgeslagen. Houd er rekening mee dat volgens het [beleid voor beheerde services](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)een **leadbestemming** vereist is.

Geef ten slotte uw **URL voor het privacybeleid** en de **gebruiksvoorwaarden** op in de sectie **Juridische.** U hier ook aangeven of u het [standaardcontract](../../marketplace/standard-contract.md) al dan niet voor deze aanbieding wilt gebruiken.

Zorg ervoor dat u de wijzigingen opslaat voordat u naar de sectie **Ondersteuning** gaat.

### <a name="add-support-info"></a>Ondersteuningsgegevens toevoegen

Geef in de sectie **Ondersteuning** de naam, e-mail en telefoonnummer op voor een contactpersoon voor engineering en een contactpersoon voor klantenondersteuning. U moet ook ondersteunings-URL's bieden. Microsoft kan deze informatie gebruiken wanneer we contact met u moeten opnemen over zakelijke en ondersteuningsproblemen.

Zodra u deze informatie hebt toegevoegd, selecteert u **Opslaan.**

## <a name="publish-your-offer"></a>Uw voorstel publiceren

Zodra u alle secties hebt voltooid, is uw volgende stap het publiceren van de aanbieding naar Azure Marketplace. Selecteer de knop **Publiceren** om het proces van het live maken van uw aanbieding te starten. Zie [Azure Marketplace- en AppSource-aanbiedingen publiceren](../../marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer.md)voor meer informatie over dit proces.

U op elk gewenst moment [een bijgewerkte versie van uw aanbieding publiceren.](../../marketplace/cloud-partner-portal/manage-offers/cpp-update-offer.md) U bijvoorbeeld een nieuwe roldefinitie toevoegen aan een eerder gepubliceerde aanbieding. Wanneer u dit doet, zien klanten die de aanbieding al hebben toegevoegd een pictogram op de pagina [**Serviceproviders**](view-manage-service-providers.md) in de Azure-portal waarmee ze weten dat er een update beschikbaar is. Elke klant kan [de wijzigingen bekijken](view-manage-service-providers.md#update-service-provider-offers) en beslissen of hij of zij de nieuwe versie wil bijwerken. 

## <a name="the-customer-onboarding-process"></a>Het onboardingproces van de klant

Nadat een klant uw aanbieding heeft toegevoegd, kunnen ze [een of meer specifieke abonnementen of resourcegroepen delegeren,](view-manage-service-providers.md#delegate-resources)die vervolgens worden ingeschakeld voor azure-gedelegeerd bronbeheer. Als een klant een aanbieding heeft geaccepteerd, maar nog geen bronnen heeft gedelegeerd, ziet hij een notitie boven aan het gedeelte **Provideraanbiedingen** van de pagina [**Serviceproviders**](view-manage-service-providers.md) in de Azure-portal.

> [!IMPORTANT]
> Delegeren moet worden gedaan door een niet-gastaccount in de tenant van de klant, waarbij de [eigenaar de ingebouwde rol](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) heeft voor het abonnement dat wordt aanboord (of die de resourcegroepen bevat die aan boord worden genomen). Als u alle gebruikers wilt zien die het abonnement kunnen delegeren, kan een gebruiker in de tenant van de klant het abonnement selecteren in de Azure-portal, **Access-besturingselement (IAM)** openen en [alle gebruikers weergeven met de rol Eigenaar](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Zodra de klant een abonnement heeft gedelegeerd (of een of meer resourcegroepen binnen een abonnement), wordt de **Microsoft.ManagedServices-resourceprovider** geregistreerd voor dat abonnement en hebben gebruikers in uw tenant toegang tot de gedelegeerde resources op basis van de autorisaties in uw aanbieding.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [commerciële marktplaats](../../marketplace/partner-center-portal/commercial-marketplace-overview.md).
- Meer informatie over [cross-tenant management ervaringen](../concepts/cross-tenant-management-experience.md).
- [Klanten bekijken en beheren](view-manage-customers.md) door naar **Mijn klanten** te gaan in de Azure-portal.
