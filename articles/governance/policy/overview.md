---
title: Overzicht van Azure-beleid
description: Azure Policy is een service in Azure die u gebruikt om beleidsdefinities in uw Azure-omgeving te maken, toe te wijzen en te beheren.
ms.date: 04/21/2020
ms.topic: overview
ms.openlocfilehash: 4ec09c8a38e22fc14980422bfe9a80a2bf3edda4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82097367"
---
# <a name="what-is-azure-policy"></a>Wat is Azure Policy?

Azure Policy helpt organisatie standaarden af te dwingen en de naleving op schaal te beoordelen. Via het dash board voor compatibiliteit biedt het een geaggregeerde weer gave om de algehele status van de omgeving te evalueren, met de mogelijkheid om in te zoomen op de nauw keurigheid per bron per beleid. Het helpt u ook om uw resources te laten voldoen aan de naleving van bulksgewijs herstel voor bestaande resources en automatisch herstel voor nieuwe resources.

Veelvoorkomende use cases voor Azure Policy omvatten het implementeren van governance voor bron consistentie, naleving van regelgeving, beveiliging, kosten en beheer. Beleids definities voor deze algemene use cases zijn al beschikbaar in uw Azure-omgeving als ingebouwde invoeg toepassingen om u te helpen aan de slag te gaan.

## <a name="overview"></a>Overzicht

Azure Policy evalueert resources in azure door de eigenschappen van die resources te vergelijken met bedrijfs regels. Deze bedrijfs regels, die worden beschreven in [JSON-indeling](./concepts/definition-structure.md), worden [beleids definities](#policy-definition)genoemd. Om het beheer te vereenvoudigen, kunnen meerdere bedrijfs regels worden gegroepeerd om een [beleids initiatief](#initiative-definition) (ook wel een _beleidsset_genoemd) te vormen. Zodra uw bedrijfs regels zijn gevormd, wordt de beleids definitie of het initiatief [toegewezen](#assignments) aan een bereik van resources die door Azure worden ondersteund, zoals [beheer groepen](../management-groups/overview.md), abonnementen, [resource groepen](../../azure-resource-manager/management/overview.md#resource-groups)of individuele resources. De toewijzing is van toepassing op alle resources binnen het [bereik](../../azure-resource-manager/management/overview.md#understand-scope) van die toewijzing.
Subbereiken kunnen, indien nodig, worden uitgesloten.

Azure Policy een [JSON-indeling](./concepts/definition-structure.md) gebruikt om de logica te vormen die door de evaluatie wordt gebruikt om te bepalen of een resource compatibel is of niet. Definities bevatten meta gegevens en de beleids regel. De gedefinieerde regel kan gebruikmaken van functies, para meters, logische Opera Tors, voor waarden en eigenschaps [aliassen](./concepts/definition-structure.md#aliases) die exact overeenkomen met het scenario dat u wilt. De beleids regel bepaalt welke resources in het bereik van de toewijzing worden geëvalueerd.

### <a name="understand-evaluation-outcomes"></a>Uitleg over evaluatie resultaten

Resources worden geëvalueerd op specifieke tijdstippen tijdens de levens cyclus van resources, de levens cyclus van de beleids toewijzing en voor een regel matige doorlopende evaluatie van de naleving. Hier volgen de tijden of gebeurtenissen die ertoe leiden dat een resource wordt geëvalueerd:

- Een resource wordt gemaakt, bijgewerkt of verwijderd in een bereik met een beleids toewijzing.
- Een beleid of initiatief is nieuw toegewezen aan een bereik.
- Een beleid of initiatief dat al is toegewezen aan een bereik wordt bijgewerkt.
- Tijdens de standaard beoordelings cyclus van de naleving die elke 24 uur plaatsvindt.

Zie [evaluatie triggers](./how-to/get-compliance-data.md#evaluation-triggers)voor meer informatie over wanneer en hoe de beleids evaluatie wordt uitgevoerd.

### <a name="control-the-response-to-an-evaluation"></a>De reactie op een evaluatie controleren

Bedrijfs regels voor het verwerken van niet-compatibele resources variëren in grote mate tussen organisaties. Voor beelden van hoe een organisatie wil dat het platform reageert op een niet-klachten bron, zijn:

- De resource wijziging weigeren
- De wijziging in de resource vastleggen
- De resource wijzigen vóór de wijziging
- De resource wijzigen na de wijziging
- Gerelateerde compatibele resources implementeren

Azure Policy maakt al deze zakelijke antwoorden mogelijk via de toepassing van [effecten](./concepts/effects.md). Effecten worden ingesteld in het gedeelte **beleids regel** van de [beleids definitie](./concepts/definition-structure.md).

### <a name="remediate-non-compliant-resources"></a>Niet-compatibele resources herstellen

Hoewel deze effecten voornamelijk van invloed zijn op een resource wanneer de resource wordt gemaakt of bijgewerkt, ondersteunt Azure Policy ook het verwerken van bestaande niet-compatibele resources zonder dat deze resource hoeft te worden gewijzigd. Zie [bronnen herstellen](./how-to/remediate-resources.md)voor meer informatie over het voldoen aan bestaande resources.

### <a name="video-overview"></a>Video-overzicht

Het volgende overzicht van Azure Policy is afkomstig van build 2018. Voor het downloaden van dia's of video's, gaat u naar [Azure-omgeving beheren via Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) op Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="getting-started"></a>Aan de slag

### <a name="azure-policy-and-rbac"></a>Azure Policy en RBAC

Er zijn enkele belang rijke verschillen tussen Azure Policy en op rollen gebaseerd toegangs beheer (RBAC). Azure Policy evalueert de status door de eigenschappen te controleren van resources die worden weer gegeven in Resource Manager en in de eigenschappen van sommige resource providers. Azure Policy beperkt acties (ook wel _bewerkingen_genoemd) niet. Azure Policy zorgt ervoor dat de resource status voldoet aan uw bedrijfs regels zonder dat u zeker weet wie de wijziging heeft aangebracht of wie toestemming heeft gegeven om een wijziging aan te brengen.

RBAC is gericht op het beheren van gebruikers [acties](../../role-based-access-control/resource-provider-operations.md) in verschillende bereiken. Als het besturings element van een actie vereist is, is RBAC het juiste hulp programma om te gebruiken. Zelfs als een persoon toegang heeft om een actie uit te voeren en het resultaat een niet-compatibele resource is, blokkeert Azure Policy nog steeds het maken of bijwerken.

De combi natie van RBAC en Azure Policy bieden een volledig bereik van beheer in Azure.

### <a name="rbac-permissions-in-azure-policy"></a>RBAC-machtigingen in Azure Policy

Azure Policy heeft diverse machtigingen, oftewel bewerkingen, in twee verschillende resourceproviders:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Veel ingebouwde rollen wijzen machtigingen toe aan Azure Policy-resources. De rol **Inzender voor resource beleid** bevat de meeste Azure Policy bewerkingen. Degene met de rol **Eigenaar** heeft volledige rechten. Zowel **Inzender** als **lezer** hebben toegang tot alle _Lees_ -Azure Policy bewerkingen. **Inzender** kan resource herbemiddeling activeren, maar kan geen definities of toewijzingen _maken_ .

Als geen van de ingebouwde rollen de vereiste machtigingen heeft, maakt u een [aangepaste rol](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> De beheerde identiteit van een **deployIfNotExists** -beleids toewijzing vereist voldoende machtigingen om resources te maken of bij te werken die zijn opgenomen in de sjabloon. Zie [beleids definities voor herstel configureren](./how-to/remediate-resources.md#configure-policy-definition)voor meer informatie.

### <a name="resources-covered-by-azure-policy"></a>Resources die worden gedekt door Azure Policy

Azure Policy evalueert alle resources in Azure. Voor bepaalde resource providers, zoals [gast configuratie](./concepts/guest-configuration.md), [Azure Kubernetes-service](../../aks/intro-kubernetes.md)en [Azure Key Vault](../../key-vault/key-vault-overview.md), is er een diep gaande integratie voor het beheren van instellingen en objecten. Zie voor meer informatie [resource provider modi](./concepts/definition-structure.md).

### <a name="recommendations-for-managing-policies"></a>Aanbevelingen voor het beheren van beleid

Enkele tips om rekening mee te houden:

- Begin met een controle-effect in plaats van een weigeringseffect om bij te houden wat de invloed van uw beleidsdefinitie is op de resources in uw omgeving. Als u al actieve scripts hebt voor automatische schaalaanpassing van uw toepassingen, kan het instellen van een weigeractie een belemmering vormen voor deze automatische taken die u al hebt geïmplementeerd.

- U kunt bij het maken van definities en toewijzingen gebruikmaken van organisatiehiërarchieën. Het is raadzaam om op een hoger niveau definities te maken, zoals op het niveau van de beheergroep of het abonnement. Vervolgens maakt u de toewijzing op het eerstvolgende onderliggende niveau. Als u een definitie voor een beheergroep maakt, kan de toewijzing worden gericht op een abonnement of resource in die beheergroep.

- Het is raadzaam om initiatiefdefinities te maken en toe te wijzen, zelfs voor slechts één beleidsdefinitie.
  U hebt bijvoorbeeld de beleidsdefinitie _policyDefA_ en u maakt deze met de initiatiefdefinitie _initiativeDefC_. Als u later nog een beleidsdefinitie maakt voor _policyDefB_, met doelen die lijken op die van _policyDefA_, kunt u deze toevoegen bij _initiativeDefC_ en ze samen volgen.

- Als u een initiatieftoewijzing hebt gemaakt, worden beleidsdefinities die worden toegevoegd aan het initiatief ook deel van de toewijzingen van het initiatief.

- Wanneer een initiatieftoewijzing wordt beoordeeld, worden alle beleidsregels in het initiatief ook beoordeeld.
  Als u een beleidsregel afzonderlijk wilt beoordelen, is het beter deze niet op te nemen in een initiatief.

## <a name="azure-policy-objects"></a>Azure Policy objecten

### <a name="policy-definition"></a>Beleidsdefinitie

Het maken en implementeren van een beleidsregel in Azure Policy begint met het maken van een beleidsdefinitie. Elke beleidsdefinitie heeft voorwaarden voor het afdwingen ervan. Er is een bijbehorend effect dat wordt uitgevoerd als aan de voorwaarden wordt voldaan.

In Azure Policy wordt een aantal ingebouwde beleidsregels geboden dat standaard beschikbaar is. Bijvoorbeeld:

- **Toegestane opslag account-sku's** (weigeren): bepaalt of een opslag account dat wordt geïmplementeerd binnen een set SKU-grootten valt. Het bijbehorende effect is om alle opslagaccounts te weigeren die niet in overeenstemming zijn met de gedefinieerde SKU-grootten.
- **Toegestaan resource type** (weigeren): definieert de resource typen die u kunt implementeren. Het bijbehorende effect is om alle resources te weigeren die geen deel uitmaken van deze gedefinieerde lijst.
- **Toegestane locaties** (weigeren): Hiermee beperkt u de beschik bare locaties voor nieuwe resources. Het bijbehorende effect wordt gebruikt om uw geografisch nalevingsvereisten af te dwingen.
- **Toegestane sku's van de virtuele machine** (weigeren): Hiermee geeft u een set virtuele machine-sku's op die u kunt implementeren.
- **Een tag toevoegen aan resources** (wijzigen): past een vereiste tag en de standaard waarde toe als deze niet is opgegeven door de implementatie aanvraag.
- **Tag toevoegen en de standaard waarde** (Append): Hiermee wordt een vereiste tag en de waarde ervan afgedwongen voor een resource.
- **Niet-toegestane resource typen** (weigeren): Hiermee wordt voor komen dat een lijst met resource typen wordt geïmplementeerd.

Als u deze beleidsdefinities (zowel de ingebouwde als de aangepaste) wilt implementeren, dient u ze eerst toe te wijzen. U kunt elk van deze typen beleid toewijzen via Azure Portal, PowerShell of Azure CLI.

Beleid wordt beoordeeld bij verschillende acties, zoals het toewijzen of bijwerken van beleidsregels. Zie voor een volledige lijst [Beleidbeoordelingstriggers](./how-to/get-compliance-data.md#evaluation-triggers).

Raadpleeg [Structuur van beleidsdefinities](./concepts/definition-structure.md) voor meer informatie over de structuur van beleidsdefinities.

Beleidsparameters helpen uw beleidsbeheer te vereenvoudigen door het aantal beleidsdefinities te verminderen dat u moet maken. U kunt bij het maken van een beleidsdefinitie parameters definiëren om deze algemener te maken. Vervolgens kunt u deze beleidsdefinitie hergebruiken voor verschillende scenario's. Dit doet u door verschillende waarden door te voeren bij het toepassen van de beleidsdefinitie. Bijvoorbeeld, door een reeks locaties voor een abonnement op te geven.

Parameters worden gedefinieerd bij het maken van een beleidsdefinitie. Als een parameter wordt gedefinieerd, krijgt deze een naam en optioneel een waarde. U kunt bijvoorbeeld een parameter definiëren voor een beleid met de naam _locatie_. Vervolgens kunt u deze verschillende waarden toekennen, zoals _EastUS_ of _WestUS_ bij het toewijzen van beleid.

Zie [Definitiestructuur - parameters](./concepts/definition-structure.md#parameters) voor meer informatie over beleidsparameters.

### <a name="initiative-definition"></a>Initiatiefdefinitie

Een initiatiefdefinitie is een verzameling beleidsdefinities die zijn aangepast voor het bereiken van één overkoepelend doel. Initiatiefdefinities maken het beheren en toewijzen van beleidsdefinities eenvoudiger. Dit gebeurt door het groeperen van een reeks beleidsregels als één item. U kunt bijvoorbeeld een initiatief maken met de titel **Controleren inschakelen in Azure Security Center** met als doel om alle beschikbare beveiligingsaanbevelingen in uw Azure Security Center te controleren.

> [!NOTE]
> De SDK, zoals Azure CLI en Azure PowerShell, gebruiken eigenschappen en para meters met de naam **policyset** om te verwijzen naar initiatieven.

Onder dit initiatief vallen beleidsdefinities zoals:

- **Niet-versleutelde SQL-database controleren in Security Center** – Voor het controleren van niet-versleutelde SQL-databases en -servers.
- **OS-kwetsbaarheden controleren in Security Center**: voor het controleren van servers die niet voldoen aan de geconfigureerde uitgangswaarde.
- **Ontbrekende Endpoint Protection controleren in Security Center** – Voor het controleren van servers zonder een geïnstalleerde Endpoint Protection-agent.

Net zoals beleidsparameters helpen initiatiefparameters om initiatiefbeheer te vereenvoudigen door redundantie te verminderen. Initiatiefparameters zijn de parameters die worden gebruikt voor de beleidsdefinities binnen het initiatief.

Neem bijvoorbeeld een scenario met initiatiefdefinitie **initiativeC**, waarbij voor beleidsdefinities **policyA** en **policyB** elk een ander type parameter wordt verwacht:

| Beleid | Naam van parameter |Type parameter  |Opmerking |
|---|---|---|---|
| policyA | allowedLocations | matrix  |Op basis van deze parameter wordt een lijst met tekenreeksen verwacht voor een waarde, omdat het parametertype is gedefinieerd als een matrix |
| policyB | allowedSingleLocation |tekenreeks |Op basis van deze parameter wordt één woord verwacht voor een waarde, omdat het parametertype is gedefinieerd als een tekenreeks |

In dit scenario hebt u, bij het definiëren van de initiatiefparameters voor **initiativeC**, drie opties:

- Gebruik de parameters van de beleidsdefinities binnen dit initiatief: in dit voorbeeld worden _allowedLocations_ en _allowedSingleLocation_ initiatiefparameters voor **initiativeC **.
- Geef waarden op voor de parameters van de beleidsdefinities binnen deze initiatiefdefinitie. In dit voor beeld kunt u een lijst met locaties opgeven voor de para meter van de **beleids regel**- **allowedLocations** en **policyB**- **allowedSingleLocation**. U kunt ook waarden opgeven bij het toewijzen van dit initiatief.
- Geef een lijst met _waarde_opties op die kunnen worden gebruikt bij het toewijzen van dit initiatief. Als u dit initiatief toewijst, kunnen de overgenomen parameters van de beleidsdefinities binnen het initiatief, alleen waarden hebben uit de opgegeven lijst.

Als u waardeopties maakt in een initiatiefdefinitie, is het niet mogelijk om tijdens het toewijzen van het initiatief andere waarden op te geven, omdat deze niet op de lijst staan.

### <a name="assignments"></a>Toewijzingen

Een toewijzing is een beleids definitie of-initiatief dat is toegewezen voor het uitvoeren van een bepaald bereik. Dit bereik kan variëren van een [beheer groep](../management-groups/overview.md) tot een afzonderlijke resource. Het term _bereik_ verwijst naar alle resources, resource groepen, abonnementen of beheer groepen waaraan de definitie is toegewezen. Toewijzingen worden overgenomen door alle onderliggende resources. Dit ontwerp houdt in dat een definitie die wordt toegepast op een resource groep ook wordt toegepast op resources in die resource groep. U kunt echter een subbereik uitsluiten van de toewijzing.

U kunt bijvoorbeeld bij het abonnements bereik een definitie toewijzen waarmee wordt voor komen dat netwerk bronnen worden gemaakt. U kunt echter één resourcegroep binnen het abonnement uitsluiten, namelijk degene die is bedoeld voor netwerkinfrastructuur. U verleent vervolgens toegang tot deze netwerkresourcegroep aan gebruikers aan wie u het maken van de netwerkresourcegroep toevertrouwt.

In een ander voor beeld kunt u een lijst definitie voor een resource type toestaan toewijzen op het niveau van de beheer groep. Vervolgens wijst u een strengere beleids regel toe (waardoor meer bron typen worden toegestaan) in een onderliggende beheer groep of zelfs rechtstreeks op abonnementen. Dit voor beeld zou echter niet werken omdat Azure Policy een expliciete deny-systeem is. In plaats daarvan moet u de onderliggende beheer groep of het abonnement uitsluiten van de toewijzing van de beheer groep. Vervolgens wijst u de meer strikte definitie toe aan de onderliggende beheer groep of het abonnements niveau. Als een toewijzing resulteert in het weigeren van een resource, is de enige manier om de resource toe te staan de weigerings toewijzing te wijzigen.

Zie [een beleids toewijzing maken om niet-compatibele resources te identificeren in uw Azure-omgeving](assign-policy-portal.md)voor meer informatie over het instellen van toewijzingen via de portal. Er zijn ook stappen voor [PowerShell](assign-policy-powershell.md) en [Azure CLI](assign-policy-azurecli.md) beschikbaar.

## <a name="maximum-count-of-azure-policy-objects"></a>Maximum aantal Azure Policy objecten

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht hebt van Azure Policy en enkele van de belangrijkste geïntroduceerde concepten, ziet u hier de voorgestelde volgende stappen:

- [Controleer de structuur van de beleids definitie](./concepts/definition-structure.md).
- [Een beleids definitie toewijzen met behulp van de portal](./assign-policy-portal.md).
