---
title: Uw resources ordenen met beheergroepen - Azure Governance
description: Informatie over de managementgroepen, hoe hun machtigingen werken en hoe u ze gebruikt.
ms.date: 04/15/2020
ms.topic: overview
ms.openlocfilehash: cc60e4555f0fb2b920b8061fb044ce5dde990d38
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381543"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Uw resources organiseren met Azure-beheergroepen

Als uw organisatie veel abonnementen heeft, moet u de toegang, beleidsregels en naleving voor deze abonnementen op een efficiënte manier kunnen beheren. Azure-beheergroepen bieden een scopeniveau boven abonnementen. U ordent abonnementen in containers, zogenaamde 'beheergroepen', en past uw governancevoorwaarden hierop toe. Alle abonnementen in een beheergroep nemen automatisch de voorwaarden over die op de beheergroep zijn toegepast. Beheergroepen bieden u beheer van bedrijfskwaliteit op grote schaal, ongeacht de typen abonnementen die u hebt.
Alle abonnementen binnen een beheergroep moeten afhankelijk zijn van dezelfde Azure Active Directory-tenant.

U kunt bijvoorbeeld beleid toepassen op een beheergroep, dat het aantal regio's beperkt dat beschikbaar is voor het maken van virtuele machines (VM’s). Dit beleid wordt dan toegepast op alle beheergroepen, abonnementen en resources onder die beheergroep, doordat het ervoor zorgt dat er alleen VM’s kunnen worden gemaakt in die regio.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hiërarchie van managementgroepen en abonnementen

U kunt een flexibele structuur van managementgroepen en abonnementen bouwen om uw resources in een hiërarchie te ordenen voor uniform beleid en toegangsbeheer. Het volgende diagram laat een voorbeeld zien van hoe een hiërarchie voor governance kan worden gemaakt met behulp van beheergroepen.

:::image type="content" source="./media/tree.png" alt-text="Voorbeeld van een hiërarchiestructuur voor beheergroepen" border="false":::

U kunt een hiërarchie maken waarmee een beleid wordt toegepast, bijvoorbeeld om VM-locaties tot de regio US - west te beperken in de groep 'Productie'. Dit beleid neemt de voorkeur aan alle Enterprise Agreement(EA)-abonnementen die afstammelingen zijn van die beheergroep en is van toepassing op alle VM's onder die abonnementen. Dit beveiligingsbeleid kan niet worden gewijzigd door de eigenaar van de resource of het abonnement, wat zorgt voor betere governance.

Een ander scenario waarbij u beheergroepen kunt gebruiken, is om gebruikers toegang te geven tot meerdere abonnementen. Als u meerdere abonnementen naar de desbetreffende beheergroep verplaatst, kunt u in de beheergroep één toewijzing voor [op rollen gebaseerd toegangsbeheer](../../role-based-access-control/overview.md) (RBAC) maken, die de toegang doorgeeft aan alle abonnementen. Eén toewijzing in de beheergroep kan gebruikers toegang geven tot alles wat ze nodig hebben. Er hoeven dan geen scripts te worden geschreven voor RBAC-toewijzingen in meerdere abonnementen.

### <a name="important-facts-about-management-groups"></a>Belangrijke feiten over beheergroepen

- In één map kunnen 10.000 beheergroepen worden ondersteund.
- Een beheergroepstructuur ondersteunt tot wel zes niveaus.
  - Deze limiet is exclusief het hoofdniveau of het abonnementsniveau.
- Een beheergroep of abonnement biedt ondersteuning voor slechts één bovenliggend item.
- Elke beheergroep kan een groot aantal onderliggende items hebben.
- Alle abonnementen en beheergroepen bevinden zich in één hiërarchie in elke map. Zie [Belangrijke feiten over de hoofdbeheergroep](#important-facts-about-the-root-management-group).

## <a name="root-management-group-for-each-directory"></a>Hoofdbeheergroep voor elke map

Elke map krijgt één beheergroep op het hoogste niveau, de 'hoofdbeheergroep'. Deze hoofdbeheergroep is zo in de hiërarchie ingebouwd dat alle beheergroepen en abonnementen hierin zijn opgevouwen. Met deze hoofdbeheergroep kunt u algemene beleidsregels en RBAC-toewijzingen op directoryniveau toepassen. De globale beheerder van Azure AD moet eerst de rol Beheerder gebruikerstoegang van deze hoofdgroep [aan zichzelf toewijzen](../../role-based-access-control/elevate-access-global-admin.md). Nadat hij dit heeft gedaan, kan de beheerder een RBAC-rol toewijzen aan andere directory-gebruikers of -groepen om de hiërarchie te beheren. Als beheerder kunt u uw eigen account toewijzen als eigenaar van de hoofdbeheergroep.

### <a name="important-facts-about-the-root-management-group"></a>Belangrijke feiten over de hoofdbeheergroep

- Standaard is de weergavenaam van de hoofdbeheergroep **Tenanthoofdgroep**. De id is de Azure Active Directory-id.
- Als u de weergavenaam wilt wijzigen, moet uw account de rol Eigenaar of Inzender in de hoofdbeheergroep hebben toegewezen gekregen. Zie [De naam van een beheergroep wijzigen](manage.md#change-the-name-of-a-management-group) om de naam van een beheergroep bij te werken.
- In tegenstelling tot andere beheergroepen kan de hoofdbeheergroep niet worden verplaatst of verwijderd.  
- Alle abonnementen en beheergroepen kunnen worden samengevouwen in deze ene hoofdbeheergroep binnen de map.
  - Alle resources in de map kunnen worden samengevouwen in de hoofdbeheergroep voor algemeen beheer.
  - Wanneer er een nieuw abonnement wordt gemaakt, wordt dit standaard automatisch in de hoofdbeheergroep geplaatst.
- Alle Azure-klanten kunnen de hoofdbeheergroep zien, maar niet alle klanten hebben de mogelijkheid om die hoofdbeheergroep te beheren.
  - Iedereen die toegang heeft tot een abonnement, kan de context zien van waar dat abonnement zich in de hiërarchie bevindt.  
  - Niemand krijgt standaard toegang tot de hoofdbeheergroep. Globale beheerders van Azure AD zijn de enige gebruikers die zichzelf toegang kunnen verschaffen. Zodra ze toegang hebben tot de root management groep, kunnen de globale beheerders elke RBAC-rol toewijzen aan andere gebruikers om te beheren  
    Het.
- In SDK fungeert de root management groep, of 'Tenant Root', als een beheergroep.

> [!IMPORTANT]
> Toewijzingen voor gebruikerstoegang of beleidstoewijzingen in de hoofdbeheergroep **zijn van toepassing op alle resources in de map**. Daarom moeten alle klanten goed nadenken over de noodzaak om items op dit niveau toe te wijzen. Gebruikerstoegang en beleidstoewijzingen mogen alleen op deze manier "Must Have" zijn  
> Scope.

## <a name="initial-setup-of-management-groups"></a>Eerste installatie van beheergroepen

Wanneer een gebruiker start met het gebruik van beheergroepen, vindt er een proces plaats voor de eerste installatie. Eerst wordt er een hoofdbeheergroep gemaakt in de map. Zodra deze groep klaar is, worden alle bestaande abonnementen die in de map zijn opgenomen gemaakt tot onderliggende items van de hoofdbeheergroep.
De reden dat dit proces zo werkt, is om er zeker van te zijn dat er binnen een map slechts één beheergroephiërarchie is. Die ene hiërarchie in de map zorgt ervoor dat klanten met een beheerdersrol globale toegang en beleidsregels kunnen toepassen waar andere klanten in de map niet omheen kunnen. Alle zaken die in de hoofdmap zijn toegewezen, zijn van toepassing op de hele hiërarchie, en dus op alle beheergroepen, abonnementen, resourcegroepen en resources binnen die Azure AD-tenant.

## <a name="trouble-seeing-all-subscriptions"></a>Problemen met de weergave van alle abonnementen

Bij een aantal mappen die vroeg in de preview (vóór 25 juni 2018) met beheergroepen begonnen te werken, deed zich een probleem voor waarbij niet alle abonnementen zich in de hiërarchie bevonden. Het proces voor het onderbrengen van alle abonnementen in de hiërarchie werden geïmplementeerd nadat er een rol- of beleidstoewijzing was uitgevoerd in de hoofdbeheergroep in de map.

### <a name="how-to-resolve-the-issue"></a>Het probleem oplossen

U kunt dit probleem op twee manieren verhelpen.

- Alle rol- en beleidstoewijzingen uit de hoofdbeheergroep verwijderen
  - Door alle beleids- en roltoewijzingen van de hoofdbeheergroep te verwijderen, zal de service alle abonnementen in de hiërarchie aanvullen tijdens de volgende nachtelijke cyclus. Dankzij dit proces wordt er niet onbedoeld toegang verleend of beleid toegewezen aan alle abonnementen van de tenant.
  - De beste manier om dit proces uit te voeren zonder uw services te beïnvloeden, is de rol- of beleidstoewijzingen één niveau onder de hoofdbeheergroep toe te passen. Vervolgens kunt u alle toewijzingen van het hoofdbereik verwijderen.
- De API rechtstreeks aanroepen om het backfill-proces te starten
  - Elke klant in de map kan de API's _TenantBackfillStatusRequest_ en _StartTenantBackfillRequest_ aanroepen. Wanneer de API StartTenantBackfillRequest wordt aangeroepen, wordt hiermee het initiële instellingsproces gestart voor het verplaatsen van alle abonnementen naar de hiërarchie. Met dit proces wordt ook afgedwongen dat alle nieuwe abonnementen een onderliggend element van de hoofdbeheergroep worden gemaakt.
    Dit proces kan worden uitgevoerd zonder de toewijzingen op het hoofdniveau te wijzigen. Door de API aan te roepen, geeft u aan dat een beleids- of toegangstoewijzing in de hoofdmap mag worden toegepast op alle abonnementen.

Als u vragen hebt over dit backfill-proces, neemt u contact op met: `managementgroups@microsoft.com`
  
## <a name="management-group-access"></a>Toegang tot beheergroepen

Azure-beheergroepen ondersteunen [op rollen gebaseerd toegangsbeheer (RBAC) van Azure](../../role-based-access-control/overview.md) voor alle soorten toegang tot resources en roldefinities.
Deze machtigingen worden overgenomen door onderliggende resources die in de hiërarchie zijn opgenomen. Elke RBAC-rol kan worden toegewezen aan een beheergroep die de hiërarchie overneemt aan de resources. Zo kan de RBAC-rol van VM-inzender aan een beheergroep worden toegewezen. Deze rol heeft geen actie in de beheergroep, maar wordt overgenomen door alle VM's in die beheergroep.

In de volgende tabel staat een lijst met rollen en de acties die worden ondersteund in beheergroepen.

| Naam RBAC-rol             | Maken | Naam wijzigen | Verplaatsen\*\* | Verwijderen | Toegang toewijzen | Beleid toewijzen | Lezen  |
|:-------------------------- |:------:|:------:|:--------:|:------:|:-------------:| :------------:|:-----:|
|Eigenaar                       | X      | X      | X        | X      | X             | X             | X     |
|Inzender                 | X      | X      | X        | X      |               |               | X     |
|MG-bijdrager\*            | X      | X      | X        | X      |               |               | X     |
|Lezer                      |        |        |          |        |               |               | X     |
|MG-lezer\*                 |        |        |          |        |               |               | X     |
|Inzender voor resourcebeleid |        |        |          |        |               | X             |       |
|Beheerder van gebruikerstoegang   |        |        |          |        | X             | X             |       |

\*: MG Contributor en MG Reader staan gebruikers alleen toe om deze acties uit te voeren op het bereik van de beheergroep.  
\*\*: Roltoewijzingen in de rootbeheergroep zijn niet vereist om een abonnement of beheergroep van en naar deze groep te verplaatsen. Zie [Uw resources beheren met beheergroepen](manage.md) voor meer informatie over het verplaatsen van items binnen de hiërarchie.

## <a name="custom-rbac-role-definition-and-assignment"></a>Aangepaste RBAC-roldefinitie en -toewijzing

Aangepaste RBAC-rolondersteuning voor beheergroepen is momenteel in preview met enkele [beperkingen.](#limitations) U kunt het bereik van de beheergroep definiëren in het toewijsbare bereik van de roldefinitie. Die aangepaste RBAC-rol is dan beschikbaar voor toewijzing in die beheergroep en alle beheergroepen, abonnementen, resourcegroepen en resources daaronder. Deze aangepaste rol neemt machtigingen over in de hiërarchie op dezelfde manier als een ingebouwde rol.  

### <a name="example-definition"></a>Voorbeelddefinitie

[Het definiëren en maken van een aangepaste rol](../../role-based-access-control/custom-roles.md) verandert niet met de opname van beheergroepen. Gebruik het volledige pad om de beheergroep **/providers/Microsoft.Management/managementgroepen/{groupId}** te definiëren.

Gebruik de id van de beheergroep en niet de weergavenaam van de beheergroep. Deze veelvoorkomende fout treedt op omdat beide aangepaste gedefinieerde velden zijn bij het maken van een beheergroep.

```json
...
{
  "Name": "MG Test Custom Role",
  "Id": "id", 
  "IsCustom": true,
  "Description": "This role provides members understand custom roles.",
  "Actions": [
    "Microsoft.Management/managementgroups/delete",
    "Microsoft.Management/managementgroups/read",
    "Microsoft.Management/managementgroup/write",
    "Microsoft.Management/managementgroup/subscriptions/delete",
    "Microsoft.Management/managementgroup/subscriptions/write",
    "Microsoft.resources/subscriptions/read",
    "Microsoft.Authorization/policyAssignments/*",
    "Microsoft.Authorization/policyDefinitions/*",
    "Microsoft.Authorization/policySetDefinitions/*",
    "Microsoft.PolicyInsights/*",
    "Microsoft.Authorization/roleAssignments/*",
    "Microsoft.Authorization/roledefinitions/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
        "/providers/microsoft.management/managementGroups/ContosoCorporate"
  ]
}
...
```

### <a name="issues-with-breaking-the-role-definition-and-assignment-hierarchy-path"></a>Problemen met het verbreken van het pad van roldefinitie en toewijzingshiërarchie

Roldefinities zijn overal binnen de hiërarchie van de beheergroep toewijsbaar bereik. Een roldefinitie kan worden gedefinieerd in een bovenliggende beheergroep, terwijl de werkelijke roltoewijzing bestaat op het onderliggende abonnement. Aangezien er een relatie is tussen de twee items, ontvangt u een foutmelding wanneer u de toewijzing van de definitie probeert te scheiden.

Laten we bijvoorbeeld eenklein gedeelte van een hiërarchie bekijken voor een visual.

:::image type="content" source="./media/subtree.png" alt-text="subboom" border="false":::

Stel dat er een aangepaste rol is gedefinieerd in de marketingbeheergroep. Die aangepaste rol wordt vervolgens toegewezen aan de twee gratis proefabonnementen.  

Als we proberen een van deze abonnementen te verplaatsen naar een onderliggend eis van de groep Productiebeheer, wordt het pad met deze stap afgebroken van de toewijzing van abonnementsrollen naar de roldefinitie van de marketingbeheergroep. In dit scenario ontvangt u een foutmelding dat de verplaatsing niet is toegestaan omdat deze deze relatie wordt afgebroken.  

Er zijn een paar verschillende opties om dit scenario op te lossen:
- Verwijder de roltoewijzing uit het abonnement voordat u het abonnement verplaatst naar een nieuw bovenliggendmg.
- Voeg het abonnement toe aan het toewijsbare bereik van de roldefinitie.
- Wijzig het toewijsbare bereik binnen de roldefinitie. In het bovenstaande voorbeeld u de toewijsbare scopes bijwerken van Marketing naar Root Management Group, zodat de definitie kan worden bereikt door beide takken van de hiërarchie.  
- Maak een extra aangepaste rol die in de andere branch wordt gedefinieerd. Voor deze nieuwe rol moet de roltoewijzing ook op het abonnement worden gewijzigd.  

### <a name="limitations"></a>Beperkingen  

Er zijn beperkingen die bestaan bij het gebruik van aangepaste rollen in beheergroepen. 

 - U slechts één beheergroep definiëren in de toewijsbare scopes van een nieuwe rol. Deze beperking is van kracht om het aantal situaties te verminderen waarin roldefinities en roltoewijzingen worden losgekoppeld. Dit gebeurt wanneer een abonnement of beheergroep met een roltoewijzing wordt verplaatst naar een andere bovenliggende ouder die niet over de roldefinitie beschikt.  
 - RBAC Data Plane-acties mogen niet worden gedefinieerd in aangepaste rollen van de beheergroep. Deze beperking is van kracht omdat er een latentieprobleem is met RBAC-acties die de resourceproviders van het gegevensvlak bijwerken. Aan dit probleem met de latentie wordt gewerkt en deze acties worden uitgeschakeld in de roldefinitie om eventuele risico's te beperken.
 - De Azure Resource Manager valideert het bestaan van de beheergroep niet in het toewijsbare bereik van de roldefinitie. Als er een typefout of een onjuiste beheergroep-id wordt vermeld, wordt de roldefinitie nog steeds gemaakt.  

## <a name="moving-management-groups-and-subscriptions"></a>Beheergroepen en abonnementen verplaatsen 

Om een beheergroep of een abonnement om een kind van een andere beheergroep te zijn, moeten drie regels als waar worden beoordeeld.

Als u de verhuisactie doet, moet u het zelfde 

- Managementgroep schrijven en Roltoewijzing schrijven machtigingen voor het onderliggende abonnement of beheergroep.
  - **Voorbeeld-eigenaar** van een ingebouwde rol
- Managementgroep schrijft toegang op de doelgroep management groep.
  - Voorbeeld van ingebouwde rollen: **eigenaar**, **bijdrager**, **managementteambijdrager**
- Beheergroep schrijft toegang op de bestaande bovenliggende beheergroep.
  - Voorbeeld van ingebouwde rollen: **eigenaar**, **bijdrager**, **managementteambijdrager**

**Uitzondering:** Als de doelgroep of de bestaande bovenliggende beheergroep de hoofdbeheergroep is, zijn de machtigingenvereisten niet van toepassing. Aangezien de hoofdbeheergroep de standaardbestemmingsplek is voor alle nieuwe beheergroepen en abonnementen, hebt u geen machtigingen nodig om een item te verplaatsen.

Als de rol Eigenaar van het abonnement is overgenomen van de huidige beheergroep, zijn uw verplaatsingsdoelen beperkt. U het abonnement alleen verplaatsen naar een andere beheergroep waar u de rol Eigenaar hebt. U het niet verplaatsen naar een beheergroep waar u een bijdrager levert omdat u het eigendom van het abonnement verliest. Als u rechtstreeks bent toegewezen aan de rol Eigenaar voor het abonnement (niet overgenomen van de beheergroep), u deze verplaatsen naar elke beheergroep waar u een bijdrager levert.

## <a name="audit-management-groups-using-activity-logs"></a>Beheergroepen controleren met behulp van activiteitenlogboeken

Beheergroepen worden ondersteund door het [Azure-activiteitenlogboek](../../azure-monitor/platform/platform-logs-overview.md). U kunt op dezelfde centrale locatie als andere Azure-resources zoeken naar alle gebeurtenissen die in een beheergroep plaatsvinden. Zo kunt u alle gewijzigde rol- of beleidstoewijzingen binnen een bepaalde beheergroep bekijken.

:::image type="content" source="./media/al-mg.png" alt-text="Activiteitenlogboeken met beheergroepen" border="false":::

Bij het uitvoeren van query's op beheergroepen buiten de Azure-portal, ziet het doelbereik voor beheergroepen er als volgt uit: **/ providers/Microsoft.Management/managementGroups/{yourMgID}**.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over beheergroepen gaat u naar:

- [Beheergroepen maken om Azure-resources te ordenen](./create.md)
- [Uw beheergroepen wijzigen, verwijderen of beheren](./manage.md)
- [Beheergroepen bekijken via de module voor Azure PowerShell-resources](/powershell/module/az.resources#resources)
- [Beheergroepen bekijken via de REST-API](/rest/api/resources/managementgroups)
- [Beheergroepen bekijken via de Azure-CLI](/cli/azure/account/management-group)
