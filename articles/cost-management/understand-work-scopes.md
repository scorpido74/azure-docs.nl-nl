---
title: Azure Cost Management bereiken begrijpen en ermee werken
description: Dit artikel helpt u inzicht te krijgen in de facturerings-en resource beheer bereiken die beschikbaar zijn in Azure en hoe u de bereiken gebruikt in Cost Management en Api's.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: 91a71f914cff25dcdf4a85f3baafcfd9bc96b104
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74218876"
---
# <a name="understand-and-work-with-scopes"></a>Bereiken begrijpen en gebruiken

Dit artikel helpt u inzicht te krijgen in de facturerings-en resource beheer bereiken die beschikbaar zijn in Azure en hoe u de bereiken gebruikt in Cost Management en Api's.

## <a name="scopes"></a>Bereiken

Een _bereik_ is een knoop punt in de Azure-resource hiërarchie waar Azure AD-gebruikers services kunnen openen en beheren. De meeste Azure-resources worden gemaakt en geïmplementeerd in resource groepen, die deel uitmaken van abonnementen. Micro soft biedt ook twee hiërarchieën boven Azure-abonnementen met gespecialiseerde rollen voor het beheren van facturerings gegevens:
- Facturerings gegevens, zoals betalingen en facturen
- Cloud Services, zoals kosten en beleids beheer

Met bereiken beheert u facturerings gegevens, beschikt u over functies die specifiek zijn voor betalingen, het weer geven van facturen en het uitvoeren van algemeen account beheer. Facturerings-en account rollen worden afzonderlijk beheerd vanaf de functies die worden gebruikt voor resource beheer, die gebruikmaken van [Azure RBAC](../role-based-access-control/overview.md). Om het doel van de afzonderlijke bereiken duidelijk te onderscheiden, met inbegrip van de verschillen in toegangs beheer, worden deze respectievelijk _facturerings bereiken_ en _RBAC-bereiken_genoemd.

## <a name="how-cost-management-uses-scopes"></a>Hoe Cost Management scopes gebruikt?

Cost Management werkt in alle bereiken boven de resources om organisaties in staat te stellen kosten te beheren op het niveau waarop ze toegang hebben, ongeacht of dat nu het hele facturerings account of één resource groep is. Hoewel de facturerings bereiken verschillen op basis van uw micro soft-overeenkomst (type abonnement), hebben de RBAC-bereiken dat niet.

## <a name="azure-rbac-scopes"></a>Azure RBAC-bereiken

Azure ondersteunt drie bereiken voor resource beheer. Elk bereik biedt ondersteuning voor het beheer van toegang en governance, inclusief, maar niet beperkt tot, kosten beheer.

- [**Beheer groepen**](../governance/management-groups/overview.md) -hiërarchische containers, Maxi maal acht niveaus, om Azure-abonnementen te organiseren.

    Resource type: [micro soft. Management/managementGroups](/rest/api/resources/managementgroups)

- **Abonnementen** : primaire containers voor Azure-resources.

    Resource type: [micro soft. resources/abonnementen](/rest/api/resources/subscriptions)

- [**Resource groepen**](../azure-resource-manager/resource-group-overview.md#resource-groups) : logische groeperingen van gerelateerde resources voor een Azure-oplossing die dezelfde levens cyclus delen. Bijvoorbeeld resources die samen worden geïmplementeerd en verwijderd.

    Resource type: [micro soft. resources/abonnementen/resourceGroups](/rest/api/resources/resourcegroups)

Met beheer groepen kunt u abonnementen indelen in een-hiërarchie. U kunt bijvoorbeeld een logische organisatie hiërarchie maken met behulp van beheer groepen. Geef vervolgens teams abonnementen op voor productie-en dev/test-workloads. En vervolgens resource groepen maken in de abonnementen voor het beheren van elk subsysteem of onderdeel.

Door een organisatie hiërarchie te maken, kunt u de kosten en beleids naleving per organisatie samen vouwen. Elke leider kan vervolgens hun huidige kosten bekijken en analyseren. En vervolgens kunnen ze budgetten maken om te voorzien in onjuiste bestedings patronen en de kosten te optimaliseren met Advisor-aanbevelingen op het laagste niveau.

Het verlenen van toegang voor het weer geven van kosten en optioneel beheer van kosten configuratie, zoals budgetten en export bewerkingen, wordt uitgevoerd op beheer bereiken met behulp van Azure RBAC. U gebruikt Azure RBAC om Azure AD-gebruikers en-groepen toegang te verlenen tot het uitvoeren van een vooraf gedefinieerde set acties die zijn gedefinieerd in een rol voor een specifiek bereik en hieronder. Zo verleent een rol die is toegewezen aan een beheer groeps bereik ook dezelfde machtigingen voor geneste abonnementen en resource groepen.

Cost Management ondersteunt de volgende ingebouwde rollen voor elk van de volgende bereiken:

- [**Eigenaar**](../role-based-access-control/built-in-roles.md#owner) : kan kosten bekijken en alles beheren, met inbegrip van de kosten configuratie.
- [**Inzender**](../role-based-access-control/built-in-roles.md#contributor) : kan kosten bekijken en alles beheren, inclusief kosten configuratie, maar exclusief toegangs beheer.
- [**Lezer**](../role-based-access-control/built-in-roles.md#reader) : kan alles weer geven, inclusief kosten gegevens en configuratie, maar kan geen wijzigingen aanbrengen.
- [**Cost Management Inzender**](../role-based-access-control/built-in-roles.md#cost-management-contributor) : kan kosten bekijken, kosten configuratie beheren en aanbevelingen weer geven.
- [**Cost Management lezer**](../role-based-access-control/built-in-roles.md#cost-management-reader) : kan kosten gegevens, kosten configuratie en aanbevelingen weer geven.

Cost Management Inzender is de aanbevolen rol met minimale bevoegdheden. Hiermee kunnen gebruikers budgetten maken en beheren en exporteren om de kosten effectiever te controleren en te rapporteren. Cost Management inzenders kunnen ook extra rollen vereisen om end-to-end kosten beheer scenario's te ondersteunen. Houd rekening met de volgende scenario's:

- **Reageren wanneer budgetten worden overschreden** : Cost Management mede werkers moeten ook toegang hebben tot het maken en/of beheren van actie groepen om automatisch te reageren op overschrijdingen. Overweeg het verlenen van [bewakings bijdrage](../role-based-access-control/built-in-roles.md#monitoring-contributor) aan een resource groep die de actie groep bevat die moet worden gebruikt wanneer de budget drempels worden overschreden. Voor het automatiseren van specifieke acties zijn aanvullende rollen vereist voor de specifieke services die worden gebruikt, zoals automatisering en Azure Functions.
- **Schedule cost data export** : Cost Management-mede werkers moeten ook toegang hebben tot het beheren van opslag accounts om een export te plannen om gegevens naar een opslag account te kopiëren. Overweeg het verlenen van [Inzender voor opslag accounts](../role-based-access-control/built-in-roles.md#storage-account-contributor) aan een resource groep die het opslag account bevat waarin kosten gegevens worden geëxporteerd.
- **Aanbevelingen voor het besparen van kosten bekijken** : Cost Management lezers en Cost Management mede werkers hebben standaard toegang tot het *weer geven* van kosten aanbevelingen. Toegang tot de kosten aanbevelingen vereist echter toegang tot afzonderlijke resources. Overweeg een [servicespecifieke functie](../role-based-access-control/built-in-roles.md#built-in-role-descriptions) te verlenen als u op kosten gebaseerde aanbeveling wilt handelen.

## <a name="enterprise-agreement-scopes"></a>Enterprise Agreement bereiken

Enterprise Agreement (EA)-facturerings accounts, ook wel inschrijvingen genoemd, hebben de volgende bereiken:

- [**Facturerings account**](../billing/billing-view-all-accounts.md) : vertegenwoordigt een EA-inschrijving. Facturen worden op dit bereik gegenereerd. Aankopen waarvoor geen gebruik is gemaakt, zoals Marketplace en reserve ringen, zijn alleen beschikbaar in dit bereik. Ze worden niet weer gegeven in afdelingen of inschrijvings accounts.

    Resource type: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Afdeling** -optioneel groepering van inschrijvings accounts.

    Resource type: `Billing/billingAccounts/departments`

- **Inschrijvings account** : vertegenwoordigt een enkele eigenaar van een account. Biedt geen ondersteuning voor het verlenen van toegang aan meerdere personen.

    Resource type: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Hoewel governance-bereiken zijn gebonden aan één map, zijn geen EA-facturerings bereik. Een EA-facturerings account kan abonnementen hebben op elk wille keurig aantal Azure AD-directory's.

EA-facturerings bereik ondersteunen de volgende rollen:

- **Ondernemings Administrator** : kan instellingen en toegang van het facturerings account beheren, kan alle kosten bekijken en de kosten configuratie beheren. Bijvoorbeeld budgetten en exports. In functie is het facturerings bereik EA hetzelfde als [Cost Management Inzender rol van Azure RBAC](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Enter prise alleen-lezen gebruiker** – kan instellingen voor het facturerings account, kosten gegevens en kosten configuratie weer geven. Bijvoorbeeld budgetten en exports. In functie is het facturerings bereik EA hetzelfde als de [Azure RBAC-rol Cost Management Reader](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Afdelings beheerder** : kan afdelings instellingen, zoals kosten centrum, beheren en alle kosten bekijken, en de kosten configuratie beheren. Bijvoorbeeld budgetten en exports.  De instelling voor het facturerings account voor **da-weer gave** moet zijn ingeschakeld voor afdelings beheerders en alleen-lezen gebruikers om de kosten te bekijken. Als de **da-weergave kosten** is uitgeschakeld, kunnen de afdelings gebruikers geen kosten op elk niveau zien, zelfs niet als ze een account of eigenaar van het abonnement zijn.
- **Alleen-lezen gebruiker** van de afdeling: kan afdelings instellingen, kosten gegevens en kosten configuratie weer geven. Bijvoorbeeld budgetten en exports. Als de **da-weergave kosten** is uitgeschakeld, kunnen de afdelings gebruikers geen kosten op elk niveau zien, zelfs niet als ze een account of eigenaar van het abonnement zijn.
- **Eigenaar** van het account: kan instellingen voor het inschrijvings account beheren (zoals het kosten centrum), alle kosten weer geven en de kosten configuratie (zoals budgetten en export bewerkingen) voor het inschrijvings account beheren. De instelling van het facturerings account **ao** moet zijn ingeschakeld voor account eigenaren en RBAC-gebruikers om de kosten te bekijken.

EA-facturerings account gebruikers hebben geen directe toegang tot facturen. Facturen zijn verkrijgbaar via een extern volume licentie systeem.

Azure-abonnementen zijn genest onder inschrijvings accounts. Facturerings gebruikers hebben toegang tot kosten gegevens voor de abonnementen en resource groepen die zich onder hun respectieve bereik bevinden. Ze hebben geen toegang tot het weer geven of beheren van resources in de Azure Portal. Facturerings gebruikers kunnen kosten weer geven door te navigeren naar **Cost Management en facturering** in de Azure Portal lijst met Services. Vervolgens kunnen ze de kosten filteren voor de specifieke abonnementen en resource groepen die ze nodig hebben om te rapporteren.

Facturerings gebruikers hebben geen toegang tot beheer groepen omdat ze niet expliciet onder een specifiek facturerings account vallen. Toegang moet expliciet aan beheer groepen worden verleend. De totale kosten van beheer groepen van alle geneste abonnementen. Ze omvatten echter alleen aankopen op basis van gebruik. Ze omvatten geen aankopen zoals reserve ringen en Marketplace-aanbiedingen van derden. Gebruik het account EA om deze kosten weer te geven.

## <a name="individual-agreement-scopes"></a>Scopes voor individuele overeenkomsten

Azure-abonnementen die zijn gemaakt op basis van afzonderlijke aanbiedingen, zoals betalen per gebruik en gerelateerde typen, zoals gratis proef versie en ontwikkel-en test aanbiedingen, hebben geen expliciet account bereik voor facturering. In plaats daarvan heeft elk abonnement een account eigenaar of account beheerder, zoals de eigenaar van het EA-account.

- [**Facturerings account**](../billing/billing-view-all-accounts.md) : vertegenwoordigt een enkele eigenaar van een account voor een of meer Azure-abonnementen. Het biedt momenteel geen ondersteuning voor het verlenen van toegang aan meerdere personen of toegang tot samengevoegde kosten weergaven.

    Resource type: niet van toepassing

Afzonderlijke accounts beheerders van een Azure-abonnement kunnen facturerings gegevens, zoals facturen en betalingen, bekijken en beheren vanuit de [Azure-Accountcentrum](https://account.azure.com/subscriptions). Ze kunnen echter geen kosten gegevens weer geven of resources in de Azure Portal beheren. Als u toegang wilt verlenen aan de account beheerder, gebruikt u de eerder genoemde Cost Management-functies.

In tegens telling tot EA kunnen beheerders van afzonderlijke Azure-abonnements accounts hun facturen zien in de Azure Portal. Houd er wel voor dat Cost Management lezers en Cost Management Inzender rollen geen toegang tot facturen bieden. Zie [toegang tot facturen verlenen](../billing/billing-manage-access.md##give-read-only-access-to-billing)voor meer informatie.

## <a name="microsoft-customer-agreement-scopes"></a>Micro soft-gebruikers overeenkomsten

Factuur accounts voor micro soft-klanten overeenkomst hebben de volgende bereiken:

- **Facturerings account** : vertegenwoordigt een klant overeenkomst voor meerdere micro soft-producten en-services. Facturerings accounts van klant overeenkomst zijn niet functioneel hetzelfde als EA-inschrijvingen. EA-inschrijvingen zijn nauw keuriger afgestemd op facturerings profielen.

    Resource type: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Facturerings profiel** : Hiermee worden de abonnementen gedefinieerd die zijn opgenomen in een factuur. Facturerings profielen zijn het functionele equivalent van een EA-inschrijving, omdat dat de omvang is van facturen die op worden gegenereerd. Op dezelfde manier zijn aankopen waarvoor geen gebruik is gemaakt (zoals Marketplace en reserve ringen) alleen beschikbaar in dit bereik. Ze zijn niet opgenomen in de gedeelten van de factuur.

    Resource type: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Factuur gedeelte** : dit is een groep abonnementen in een factuur of facturerings profiel. Factuur secties zijn vergelijkbaar met afdelingen: meerdere personen kunnen toegang hebben tot een factuur gedeelte.

    Resource type: `Microsoft.Billing/billingAccounts/invoiceSections`

- **Klant** : vertegenwoordigt een groep abonnementen die zijn gekoppeld aan een specifieke klant die is geboardd naar een micro soft-klant overeenkomst per partner. Dit bereik is specifiek voor CSP.

In tegens telling tot EA-facturerings bereiken _zijn_ facturerings accounts voor klant overeenkomsten gebonden aan één map en kunnen ze geen abonnementen hebben op meerdere Azure AD-directory's.

Facturerings bereik van de klant overeenkomst gelden niet voor partners. Partner rollen en-machtigingen worden beschreven in [gebruikers rollen en-machtigingen toewijzen](/partner-center/permissions-overview).

Facturerings bereik van de klant overeenkomst ondersteunen de volgende rollen:

- **Eigenaar** : kan facturerings instellingen en toegang beheren, alle kosten bekijken en de kosten configuratie beheren. Bijvoorbeeld budgetten en exports. In functie is dit factuur bereik voor klanten overeenkomst hetzelfde als de [Cost Management Inzender rol van Azure RBAC](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Inzender** : kan facturerings instellingen beheren, met uitzonde ring van toegang, alle kosten weer geven en de kosten configuratie beheren. Bijvoorbeeld budgetten en exports. In functie is dit factuur bereik voor klanten overeenkomst hetzelfde als de [Cost Management Inzender rol van Azure RBAC](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Lezer** : kan facturerings instellingen, kosten gegevens en kosten configuratie weer geven. Bijvoorbeeld budgetten en exports. In functie is dit factuur bereik voor klanten overeenkomst hetzelfde als de [Cost Management Reader van Azure RBAC-rol](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Factuur beheerder** : kan facturen bekijken en betalen en kan kosten gegevens en-configuratie bekijken. Bijvoorbeeld budgetten en exports. In functie is dit factuur bereik voor klanten overeenkomst hetzelfde als de [Cost Management Reader van Azure RBAC-rol](../role-based-access-control/built-in-roles.md#cost-management-reader).
- Ontwikkel **aars van Azure** -abonnementen: kunnen Azure-abonnementen maken, kosten bekijken en de kosten configuratie beheren. Bijvoorbeeld budgetten en exports. In functie is dit factuur bereik voor klanten overeenkomst hetzelfde als de rol van de account eigenaar van het EA-inschrijving.

Azure-abonnementen zijn genest onder factuur secties, zoals hoe ze onder EA-inschrijvings accounts vallen. Facturerings gebruikers hebben toegang tot kosten gegevens voor de abonnementen en resource groepen die zich onder hun respectieve bereik bevinden. Ze hebben echter geen toegang tot het weer geven of beheren van resources in de Azure Portal. Facturerings gebruikers kunnen kosten weer geven door te navigeren naar **Cost Management en facturering** in de Azure Portal lijst met Services. Filter vervolgens de kosten voor de specifieke abonnementen en resource groepen waarover ze moeten rapporteren.

Facturerings gebruikers hebben geen toegang tot beheer groepen omdat ze niet expliciet onder het facturerings account vallen. Als er echter beheer groepen zijn ingeschakeld voor de organisatie, worden alle abonnements kosten op basis van het facturerings account en de hoofd beheer groep gedistribueerd, omdat deze beide zijn beperkt tot één map. Beheer groepen bevatten alleen aankopen die gebaseerd zijn op gebruik. Aankopen als reserve ringen en Marketplace-aanbiedingen van derden zijn niet opgenomen in beheer groepen. Het facturerings account en de hoofd beheer groep kunnen dus verschillende totalen rapporteren. Gebruik het facturerings account of het respectieve facturerings profiel om deze kosten weer te geven.

## <a name="aws-scopes"></a>AWS-bereiken

Nadat de AWS-integratie is voltooid, raadpleegt [u de installatie en configuratie van AWS-integratie](aws-integration-set-up-configure.md). De volgende bereiken zijn beschikbaar:

- **Externe facturerings account** : vertegenwoordigt een klant overeenkomst met een externe leverancier. Dit is vergelijkbaar met het facturerings account EA.

    Resource type: `Microsoft.CostManagement/externalBillingAccounts`

- **Extern abonnement** : vertegenwoordigt een operationeel gebruikers account met een externe leverancier. Dit is vergelijkbaar met een Azure-abonnement.

    Resource type: `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>De scopes van de Cloud Solution Provider (CSP)

De volgende bereiken worden ondersteund voor Csp's met klanten in een micro soft-klant overeenkomst:

- **Facturerings account** : vertegenwoordigt een klant overeenkomst voor meerdere micro soft-producten en-services. Facturerings accounts van klant overeenkomst zijn niet functioneel hetzelfde als EA-inschrijvingen. EA-inschrijvingen zijn nauw keuriger afgestemd op facturerings profielen.

    Resource type: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Facturerings profiel** : Hiermee worden de abonnementen gedefinieerd die zijn opgenomen in een factuur. Facturerings profielen zijn het functionele equivalent van een EA-inschrijving, omdat dat de omvang is van facturen die op worden gegenereerd. Op dezelfde manier zijn aankopen waarvoor geen gebruik is gemaakt (zoals Marketplace en reserve ringen) alleen beschikbaar in dit bereik.

    Resource type: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Klant** : vertegenwoordigt een groep abonnementen die zijn gekoppeld aan een specifieke klant die door een partner aan een micro soft-klant overeenkomst is toegevoegd.

Alleen gebruikers met de rol van *globale beheerder* en *beheerder* kunnen kosten voor facturerings accounts, facturerings profielen en klanten rechtstreeks in de Azure-Tenant van de partner beheren en weer geven. Zie [gebruikers rollen en machtigingen toewijzen](/partner-center/permissions-overview)voor meer informatie over de functies van het partner centrum.

Azure Cost Management ondersteunt alleen CSP-partner klanten als de klanten een micro soft-klant overeenkomst hebben. Zie [partner centrum](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview)voor de door csp's ondersteunde klanten die nog geen micro soft-klant overeenkomst hebben.

## <a name="switch-between-scopes-in-cost-management"></a>Scha kelen tussen bereiken in Cost Management

Alle Cost Management weer gaven in de Azure Portal bevatten een **bereik** selectie Pill in de linkerbovenhoek van de weer gave. Gebruik het om het bereik snel te wijzigen. Klik op de **Scope** Pill om de bereik kiezer te openen. Hierin worden facturerings accounts, de hoofd beheer groep en abonnementen weer gegeven die niet zijn genest onder de hoofd beheer groep. Als u een bereik wilt selecteren, klikt u op de achtergrond om deze te markeren en klikt u onderaan op **selecteren** . Als u wilt inzoomen op geneste bereiken, zoals resource groepen in een abonnement, klikt u op de koppeling Scope naam. Als u het bovenliggende bereik op een genest niveau wilt selecteren, klikt u op **dit &lt;bereik selecteren&gt;** boven aan de bereik kiezer.

## <a name="identify-the-resource-id-for-a-scope"></a>De resource-ID voor een bereik identificeren

Wanneer u werkt met Cost Management Api's, weet u zeker dat de scope kritiek is. Gebruik de volgende informatie om de juiste Scope-URI te maken voor Cost Management-Api's.

### <a name="billing-accounts"></a>Facturerings accounts

1. Open de Azure Portal en navigeer vervolgens naar **Cost Management en facturering** in de lijst met Services.
2. Selecteer **Eigenschappen** in het menu van het facturerings account.
3. Kopieer de ID van het facturerings account.
4. Uw bereik is: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Factureringsprofielen

1. Open de Azure Portal en navigeer vervolgens naar **Cost Management en facturering** in de lijst met Services.
2. Selecteer **facturerings profielen** in het menu van het facturerings account.
3. Klik op de naam van het gewenste facturerings profiel.
4. Selecteer **Eigenschappen** in het menu van het facturerings profiel.
5. Kopieer het facturerings account en de facturerings profiel-Id's.
6. Uw bereik is: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Factuursecties

1. Open de Azure Portal en navigeer vervolgens naar **Cost Management en facturering** in de lijst met Services.
2. Selecteer **factuur secties** in het menu van het facturerings account.
3. Klik op de naam van de gewenste factuur sectie.
4. Selecteer **Eigenschappen** in het menu van de factuur sectie.
5. Kopieer de id van het facturerings account en de factuur sectie.
6. Uw bereik is: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>EA-afdelingen

1. Open de Azure Portal en navigeer vervolgens naar **Cost Management en facturering** in de lijst met Services.
2. Selecteer **afdelingen** in het menu van het facturerings account.
3. Klik op de naam van de gewenste afdeling.
4. Selecteer **Eigenschappen** in het menu afdeling.
5. Kopieer het facturerings account en de afdelings-Id's.
6. Uw bereik is: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>EA-inschrijvings account

1. Open de Azure Portal en navigeer naar **Cost Management en facturering** in de lijst met Services.
2. Selecteer **inschrijvings accounts** in het menu van het facturerings account.
3. Klik op de naam van het gewenste inschrijvings account.
4. Selecteer **Eigenschappen** in het menu inschrijvings account.
5. Kopieer de id van het facturerings account en het inschrijvings account.
6. Uw bereik is: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Beheergroep

1. Open de Azure Portal en navigeer naar **beheer groepen** in de lijst met Services.
2. Ga naar de gewenste beheer groep.
3. Kopieer de ID van de beheer groep uit de tabel.
4. Uw bereik is: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Abonnement

1. Open de Azure Portal en navigeer naar **abonnementen** in de lijst met Services.
2. Kopieer de abonnements-ID uit de tabel.
3. Uw bereik is: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Resourcegroepen

1. Open de Azure Portal en navigeer naar **resource groepen** in de lijst met Services.
2. Klik op de naam van de gewenste resource groep.
3. Selecteer **Eigenschappen** in het menu van de resource groep.
4. Kopieer de waarde van het veld Resource-ID.
5. Uw bereik is: `"/subscriptions/{id}/resourceGroups/{name}"`

Cost Management wordt momenteel ondersteund in [Azure Global](https://management.azure.com) en [Azure Government](https://management.usgovcloudapi.net). Zie [Azure Global and Government API-eind punten](../azure-government/documentation-government-developer-guide.md#endpoint-mapping)voor meer informatie over Azure Government _._

## <a name="next-steps"></a>Volgende stappen

- Als u de eerste Snelstartgids voor Cost Management nog niet hebt voltooid, kunt u deze lezen bij het analyseren van de [kosten](quick-acm-cost-analysis.md).
