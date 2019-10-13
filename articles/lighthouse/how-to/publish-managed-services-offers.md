---
title: Een managed services-aanbod publiceren naar Azure Marketplace
description: Meer informatie over het publiceren van een Managed Service-aanbod waarmee klanten worden vrijgegeven aan het beheer van de gedelegeerde resources van Azure.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 09/19/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: c358c728f7fe008ee8d9138cd081932524e7eaf9
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300297"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>Een managed services-aanbod publiceren naar Azure Marketplace

In dit artikel leert u hoe u een aanbieding voor open bare of privé beheerde services publiceert naar [Azure Marketplace](https://azuremarketplace.microsoft.com) met behulp van de [Cloud Partner-Portal](https://cloudpartner.azure.com/), waardoor een klant die de aanbieding heeft gekocht, resources kan kopen voor Azure-gedelegeerde resources CRM.

> [!NOTE]
> U hebt een geldig [account in het partner centrum](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) nodig om deze aanbiedingen te maken en te publiceren. Als u nog geen account hebt, wordt u door het [aanmeldings proces](https://aka.ms/joinmarketplace) geleid door de stappen voor het maken van een account in partner centrum en het inschrijven van het commerciële Marketplace-programma. Uw Microsoft Partner Network-ID (MPN) wordt [automatisch gekoppeld](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started) aan de aanbiedingen die u publiceert voor het bijhouden van invloed op de klant afspraken.
>
> Als u een aanbieding niet wilt publiceren naar Azure Marketplace, kunt u klanten hand matig voorbereiden door Azure Resource Manager sjablonen te gebruiken. Zie voor meer informatie [onboarding van een klant naar Azure gedelegeerd resource beheer](onboard-customer.md).

Het publiceren van een managed services-aanbod is vergelijkbaar met het publiceren van een ander type aanbieding naar Azure Marketplace. Zie voor meer informatie over dat proces [Azure Marketplace en AppSource Publishing Guide](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) en [beheer Azure en AppSource Marketplace-aanbiedingen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers). U moet ook het [beleid voor commerciële Marketplace-certificerings](https://docs.microsoft.com/legal/marketplace/certification-policies)instanties, met name de sectie [Managed Services](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services) , bekijken.

Zodra een klant uw aanbieding heeft toegevoegd, kunnen ze een of meer specifieke abonnementen of resource groepen delegeren die vervolgens voor het [beheer van gedelegeerde resources van Azure](#the-customer-onboarding-process)worden uitgevoerd. Houd er rekening mee dat voordat een abonnement (of resource groepen binnen een abonnement) kan worden opvolgd, het abonnement moet worden geautoriseerd voor onboarding door de resource provider **micro soft. ManagedServices** hand matig te registreren.

> [!IMPORTANT]
> Elk abonnement in een beheerde services-aanbieding bevat een sectie **manifest Details** , waarin u de Azure Active Directory (Azure AD)-entiteiten in uw Tenant definieert die toegang hebben tot de gedelegeerde resource groepen en/of-abonnementen voor klanten die Koop dat plan. Het is belang rijk te weten dat elke groep (of gebruiker of Service-Principal) die u hier opgeeft, dezelfde machtigingen heeft voor elke klant die het plan heeft gekocht. Als u verschillende groepen wilt toewijzen voor gebruik met elke klant, moet u een afzonderlijk privé plan publiceren dat exclusief is voor elke klant.

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>Maak uw aanbieding in de Cloud Partner-portal

1. Meld u aan bij de [Cloud Partner-Portal](https://cloudpartner.azure.com/).
2. Selecteer **nieuwe aanbieding**in het navigatie menu links en selecteer vervolgens **beheerde services**.
3. U ziet een **Editor** sectie voor uw aanbieding met vier delen om in te vullen: **aanbiedings instellingen**, **abonnementen**, **Marketplace**en **ondersteuning**. Zie voor richt lijnen over het volt ooien van deze secties.

## <a name="enter-offer-settings"></a>Aanbiedings instellingen invoeren

Geef in de sectie **instellingen van aanbieding** het volgende op:

|Veld  |Beschrijving  |
|---------|---------|
|**Aanbiedings-ID**     | Een unieke id voor uw aanbieding (binnen uw Publisher-profiel). Deze ID mag alleen kleine letters, streepjes en onderstrepings tekens bevatten, met een maximum van 50 tekens. Houd er rekening mee dat de aanbiedings-ID mogelijk zichtbaar is voor klanten op plaatsen als product-Url's en facturerings rapporten. Zodra u de aanbieding hebt gepubliceerd, kunt u deze waarde niet meer wijzigen.        |
|**Uitgevers-ID**     | De uitgevers-ID die aan de aanbieding wordt gekoppeld. Als u meer dan één uitgever-ID hebt, kunt u het abonnement selecteren dat u wilt gebruiken voor deze aanbieding.       |
|**Naam**     | De naam (Maxi maal 50 tekens) die klanten te zien krijgen voor uw aanbieding in azure Marketplace en in de Azure Portal. Gebruik een herken bare merk naam die klanten begrijpen — als u dit aanbod via uw eigen website promoveert, moet u ervoor zorgen dat u deze precies dezelfde naam gebruikt.        |

Wanneer u klaar bent, selecteert u **Opslaan**. U bent nu klaar om door te gaan naar de sectie **plannen** .

## <a name="create-plans"></a>Plannen maken

Elke aanbieding moet een of meer abonnementen hebben (ook wel Sku's genoemd). U kunt meerdere plannen toevoegen om verschillende functie sets tegen verschillende prijzen te ondersteunen of om een specifiek abonnement voor een beperkt publiek van specifieke klanten aan te passen. Klanten kunnen de plannen weer geven die voor hen beschikbaar zijn onder de bovenliggende aanbieding.

Selecteer in de sectie plannen voor elk plan dat u wilt maken, het **nieuwe plan**. Voer vervolgens een **plan-id**in. Deze ID mag alleen kleine letters, streepjes en onderstrepings tekens bevatten, met een maximum van 50 tekens. De plan-ID is mogelijk zichtbaar voor klanten op plaatsen als in product-Url's en facturerings rapporten. Zodra u de aanbieding hebt gepubliceerd, kunt u deze waarde niet meer wijzigen.

Voer vervolgens de volgende secties uit in de sectie **Details van abonnement** :

|Veld  |Beschrijving  |
|---------|---------|
|**Titel**     | Beschrijvende naam voor het plan dat moet worden weer gegeven. Maximale lengte van 50 tekens.        |
|**Samenvatting**     | Beknopt overzicht van het plan voor weer gave onder de titel. Maximale lengte van 100 tekens.        |
|**Beschrijving**     | Beschrijvende tekst voor een gedetailleerde uitleg van het plan.         |
|**Facturerings model**     | Er zijn twee facturerings modellen die hier worden weer gegeven, maar u moet **Bring your own License** voor aanbiedingen voor beheerde services kiezen. Dit betekent dat u uw klanten rechtstreeks factureert voor kosten met betrekking tot deze aanbieding, en micro soft brengt geen kosten in rekening.   |
|**Is dit een privé abonnement?**     | Hiermee wordt aangegeven of de SKU privé of openbaar is. De standaard waarde is **Nee** (openbaar). Als u deze selectie verlaat, is uw abonnement niet beperkt tot specifieke klanten (of een bepaald aantal klanten). Nadat u een openbaar abonnement hebt gepubliceerd, kunt u dit later niet meer wijzigen in persoonlijk. Als u dit plan alleen beschikbaar wilt maken voor specifieke klanten, selecteert u **Ja**. Wanneer u dit doet, moet u de klanten identificeren door hun abonnement-Id's op te geven. Deze kunnen worden ingevoerd op één (voor Maxi maal 10 abonnementen) of door een CSV-bestand (voor Maxi maal 20.000 abonnementen) te uploaden. Zorg ervoor dat u hier uw eigen abonnementen opneemt, zodat u de aanbieding kunt testen en valideren. Zie voor meer informatie [persoonlijke sku's en abonnementen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  |

Ten slotte vult u de sectie **manifest Details** in. Hiermee maakt u een manifest met autorisatie-informatie voor het beheren van klant resources. De gegevens die u hier opgeeft, zijn nodig voor het onboarden van uw klanten voor het beheer van de gedelegeerde resources van Azure. Zoals hierboven vermeld, worden deze machtigingen toegepast op elke klant die het plan inkoop, dus als u de toegang tot een specifieke klant wilt beperken, moet u een privé-abonnement publiceren voor hun exclusieve gebruik.

- Geef eerst een **versie** op voor het manifest. Gebruik de indeling *n. n. n* (bijvoorbeeld 1.2.5).
- Voer vervolgens uw **Tenant-id**in. Dit is een GUID die is gekoppeld aan de Azure Active Directory Tenant-ID van uw organisatie (dat wil zeggen, de Tenant waarmee u werkt om de resources van uw klanten te beheren). Als u dit niet hebt, kunt u het vinden door over te grenzen van de account naam in de rechter bovenhoek van de Azure Portal of door te klikken op **overschakelen naar een andere map**. 
- Voeg ten slotte een of meer **autorisatie** vermeldingen toe aan uw abonnement. Autorisaties definiëren de entiteiten die toegang hebben tot resources en abonnementen voor klanten die het abonnement hebben gekocht. U moet deze informatie opgeven om toegang te krijgen tot resources namens de klant met behulp van Azure delegated resource management.
  Geef voor elke autorisatie het volgende op. U kunt vervolgens zo vaak als nodig **nieuwe autorisatie** selecteren om meer gebruikers/roldefinities toe te voegen.
  - **Azure AD-object-id**: de Azure ad-id van een gebruiker, gebruikers groep of toepassing waaraan bepaalde machtigingen worden toegekend (zoals beschreven in de roldefinitie) voor de resources van uw klanten.
  - **Weergave naam van Azure AD-object**: een beschrijvende naam om de klant te helpen het doel van deze autorisatie te begrijpen. De klant krijgt deze naam te zien bij het delegeren van resources.
  - **Roldefinitie**: Selecteer een van de beschik bare ingebouwde Azure AD-rollen in de lijst. Met deze rol bepaalt u de machtigingen die de gebruiker in het veld ID van het **Azure AD-object** heeft op de resources van uw klanten. Zie [ingebouwde rollen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)voor meer informatie over deze rollen.
  - **Toewijs bare rollen**: dit is alleen vereist als u gebruikers toegangs beheerder hebt geselecteerd in de **roldefinitie** voor deze autorisatie. Als dat het geval is, moet u hier een of meer toewijs bare rollen toevoegen. De gebruiker in het **object-ID-veld van Azure AD** kan deze **toewijs bare rollen** toewijzen aan [beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Houd er rekening mee dat er geen andere machtigingen zijn gekoppeld aan de rol beheerder van gebruikers toegang voor deze gebruiker. Als u hier niet een of meer rollen selecteert, wordt er door uw inzending geen certificering door gegeven. (Als u geen beheerder voor gebruikers toegang hebt geselecteerd voor de roldefinitie van deze gebruiker, heeft dit veld geen effect.)

> [!TIP]
> In de meeste gevallen moet u machtigingen toewijzen aan een Azure AD-gebruikers groep of Service-Principal, in plaats van aan een reeks afzonderlijke gebruikers accounts. Hiermee kunt u toegang voor afzonderlijke gebruikers toevoegen of verwijderen zonder dat u het plan hoeft bij te werken en opnieuw te publiceren wanneer uw toegangs vereisten veranderen.

Wanneer u klaar bent met het toevoegen van plannen, selecteert u **Opslaan**en gaat u verder naar de sectie **Marketplace** .

## <a name="provide-marketplace-text-and-images"></a>Tekst en afbeeldingen voor Marketplace opgeven

In het gedeelte **Marketplace** kunt u de tekst en afbeeldingen opgeven die klanten kunnen zien in azure Marketplace en de Azure Portal.

Geef informatie op over de volgende velden in het **overzichts** gedeelte:

|Veld  |Beschrijving  |
|---------|---------|
|**Titel**     |  Titel van de aanbieding, vaak de lange, formele naam. Deze titel wordt prominent weer gegeven in de Marketplace. Maximale lengte van 50 tekens. In de meeste gevallen moet dit hetzelfde zijn als de **naam** die u hebt opgegeven in de sectie instellingen voor de **aanbieding** .       |
|**Samenvatting**     | Kort doel of functie van uw aanbieding. Dit wordt meestal weer gegeven onder de titel. Maximale lengte van 100 tekens.        |
|**Lange samen vatting**     | Een langere samen vatting van het doel of de functie van uw aanbieding. Maximale lengte van 256 tekens.        |
|**Beschrijving**     | Meer informatie over uw aanbieding. Dit veld heeft een maximale lengte van 3000 tekens en ondersteunt eenvoudige HTML-opmaak. U moet de woorden "beheerde service" of "Managed Services" ergens in uw beschrijving toevoegen.       |
|**Marketing-id**     | Een unieke URL-beschrijvende id. deze wordt gebruikt in Marketplace-Url's voor deze aanbieding. Als uw uitgevers-ID bijvoorbeeld *Contoso* is en uw marketing-id *sampleApp*is, wordt de URL voor uw aanbieding in azure Marketplace *https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp* .        |
|**Preview-abonnement-Id's**     | Voeg een id toe aan 100-abonnement. De klanten die aan deze abonnementen zijn gekoppeld, kunnen de aanbieding in azure Marketplace bekijken voordat deze live gaat. We stellen uw eigen abonnementen hier voor, zodat u kunt bekijken hoe uw aanbieding wordt weer gegeven op de Azure Marketplace voordat deze beschikbaar is voor klanten.  (Micro soft-ondersteunings-en engineering teams kunnen tijdens deze preview-periode ook uw aanbieding bekijken.)   |
|**Nuttige koppelingen**     | Url's die betrekking hebben op uw aanbieding, zoals documentatie, release opmerkingen, veelgestelde vragen, enzovoort.        |
|**Voorgestelde categorieën (Maxi maal 5)**     | Een of meer categorieën (Maxi maal vijf) die van toepassing zijn op uw aanbieding. Deze categorieën helpen klanten uw aanbieding te ontdekken in azure Marketplace en de Azure Portal.        |

In het gedeelte **marketing artefacten** kunt u logo's en andere assets uploaden die met uw aanbieding moeten worden weer gegeven. U kunt desgewenst scherm afbeeldingen of koppelingen uploaden naar Video's die klanten kunnen helpen bij het begrijpen van uw aanbieding.

Er zijn vier logo grootten vereist: **Small (40x40)** , **medium (90x90)** , **large (115x115)** en **Wide (255x155)** . Volg deze richt lijnen voor uw logo's:

- Het Azure-ontwerp heeft een eenvoudig kleurenpalet. Beperk het aantal primaire en secundaire kleuren in uw logo.
- De themakleuren van de portal zijn wit en zwart. Gebruik deze kleuren niet als de achtergrondkleur voor uw logo. Gebruik een kleur waardoor uw logo opvalt in de portal. We adviseren eenvoudige primaire kleuren.
- Als u een transparante achtergrond gebruikt, moet u ervoor zorgen dat het logo en de tekst niet wit, zwart of blauw zijn.
- Het uiterlijk van uw logo moet plat zijn, zonder kleurovergangen. Gebruik geen achtergrond met een kleurovergang in het logo.
- Plaats geen tekst in het logo, ook niet de naam van uw bedrijf of merk.
- Zorg ervoor dat het logo niet is uitgerekt.

Het **held-logo (815x290)** is optioneel, maar wordt aanbevolen. Als u een held logo bijvoegt, volgt u deze richt lijnen:

- Neem geen tekst op in het held-logo en zorg ervoor dat er 415 pixels aan lege ruimte aan de rechter kant van het logo staan. Dit is vereist om ruimte te laten voor tekst elementen die programmatisch worden inge sloten: de weergave naam van de uitgever, de titel van het abonnement, een lange samen vatting van de aanbieding.
- De achtergrond van uw held-logo mag niet zwart, wit of transparant zijn. Zorg ervoor dat de achtergrond kleur niet te licht is, omdat de Inge sloten tekst wit wordt weer gegeven.
- Zodra u uw aanbieding met een held pictogram hebt gepubliceerd, kunt u deze niet meer verwijderen (hoewel u deze desgewenst kunt bijwerken met een andere versie).

In de sectie **lead beheer** kunt u het CRM-systeem selecteren waarop uw leads worden opgeslagen. Houd er rekening mee dat volgens het [certificerings beleid van beheerde services](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)een doel voor de **lead** is vereist.

Ten slotte geeft u de URL van uw **Privacybeleid** en **Gebruiksvoorwaarden** op in het **juridische** gedeelte. U kunt hier ook opgeven of u het [standaard contract](https://docs.microsoft.com/azure/marketplace/standard-contract) wilt gebruiken voor deze aanbieding.

Zorg ervoor dat u uw wijzigingen opslaat voordat u verdergaat met het **ondersteunings** gedeelte.

## <a name="add-support-info"></a>Ondersteunings informatie toevoegen

Geef in de sectie **ondersteuning** de naam, het e-mail adres en het telefoon nummer op voor een technische contact persoon en een contact persoon voor klant ondersteuning. U moet ook ondersteunings-Url's opgeven. Micro soft kan deze informatie gebruiken om contact met u op te nemen over bedrijfs-en ondersteunings problemen.

Nadat u deze gegevens hebt toegevoegd, selecteert u **opslaan.**

## <a name="publish-your-offer"></a>Uw aanbieding publiceren

Zodra u tevreden bent over alle gegevens die u hebt ingevoerd, is de volgende stap het publiceren van de aanbieding naar Azure Marketplace. Selecteer de knop **publiceren** om het proces van het live-aanbod te initiëren. Zie voor meer informatie over dit proces [Azure Marketplace publiceren en AppSource-aanbiedingen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer).

## <a name="the-customer-onboarding-process"></a>Het onboarding-proces van de klant

Wanneer een klant uw aanbieding toevoegt, kunnen ze [een of meer specifieke abonnementen of resource groepen delegeren](view-manage-service-providers.md#delegate-resources) die vervolgens voor het beheer van gedelegeerde resources van Azure worden uitgevoerd. Als een klant een aanbieding heeft geaccepteerd, maar nog geen resources heeft gedelegeerd, wordt op de pagina [**service providers**](view-manage-service-providers.md) van de Azure Portal een opmerking weer geven boven aan de **provider** .

Voordat een abonnement (of resource groepen binnen een abonnement) kan worden opvolgd, moet het abonnement worden geautoriseerd voor onboarding door de resource provider **micro soft. ManagedServices** hand matig te registreren. Een gebruiker in de Tenant van de klant met de rol Inzender of eigenaar kan dit doen door de stappen te volgen die worden beschreven in [Azure resource providers en-typen](../../azure-resource-manager/resource-manager-supported-services.md).

De klant kan vervolgens controleren of het abonnement gereed is voor onboarding op een van de volgende manieren.

### <a name="azure-portal"></a>Azure Portal

1. Selecteer in de Azure Portal het abonnement.
1. Selecteer **Resourceproviders**.
1. Controleer of **micro soft. ManagedServices** als **geregistreerd**wordt weer gegeven.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Set-AzContext -Subscription <subscriptionId>
Get-AzResourceProvider -ProviderNameSpace 'Microsoft.ManagedServices'
```

De resultaten moeten er als volgt uitzien:

```output
ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationDefinitions}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationAssignments}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {operations}
Locations         : {}
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set –subscription <subscriptionId>
az provider show --namespace "Microsoft.ManagedServices" --output table
```

De resultaten moeten er als volgt uitzien:

```output
Namespace                  RegistrationState
-------------------------  -------------------
Microsoft.ManagedServices  Registered
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ervaring op het beheer van cross-tenants](../concepts/cross-tenant-management-experience.md).
- [Bekijk en beheer klanten](view-manage-customers.md) door naar **mijn klanten** te gaan in de Azure Portal.
