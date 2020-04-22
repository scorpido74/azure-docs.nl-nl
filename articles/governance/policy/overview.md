---
title: Overzicht van Azure-beleid
description: Azure Policy is een service in Azure die u gebruikt om beleidsdefinities in uw Azure-omgeving te maken, toe te wijzen en te beheren.
ms.date: 04/21/2020
ms.topic: overview
ms.openlocfilehash: 68005a9a07e7f081a646c75566a0be4046f078ad
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770586"
---
# <a name="what-is-azure-policy"></a>Wat is Azure Policy?

Azure Policy helpt bij het afdwingen van organisatienormen en het beoordelen van naleving op schaal. Via het compliance-dashboard biedt het een geaggregeerde weergave om de algehele toestand van de omgeving te evalueren, met de mogelijkheid om in te zoomen op de granulariteit per resource per beleid. Het helpt ook om uw resources te voldoen aan de regels door middel van bulksanering voor bestaande resources en automatische herstel voor nieuwe resources.

Veelvoorkomende gebruiksvoorbeelden voor Azure Policy zijn het implementeren van governance voor consistentie van resources, naleving van de regelgeving, beveiliging, kosten en beheer. Beleidsdefinities voor deze algemene use cases zijn al beschikbaar in uw Azure-omgeving als ingebouwde omgevingom eenvoudig aan de slag te gaan.

## <a name="overview"></a>Overzicht

Azure Policy evalueert resources in Azure door de eigenschappen van deze resources te vergelijken met bedrijfsregels. Deze bedrijfsregels, beschreven in [JSON-formaat](./concepts/definition-structure.md), staan bekend als [beleidsdefinities](#policy-definition). Om het beheer te vereenvoudigen, kunnen verschillende bedrijfsregels worden gegroepeerd om een [beleidsinitiatief](#initiative-definition) te vormen (ook wel _beleidsset_genoemd). Zodra uw bedrijfsregels zijn gevormd, wordt de beleidsdefinitie of het initiatief [toegewezen aan](#assignments) elk bereik van resources dat Azure ondersteunt, zoals [beheergroepen,](../management-groups/overview.md)abonnementen, [resourcegroepen](../../azure-resource-manager/management/overview.md#resource-groups)of afzonderlijke resources. De toewijzing is van toepassing op alle resources die binnen het [bereik](../../azure-resource-manager/management/overview.md#understand-scope) van die toewijzing vallen.
Subscopen kunnen worden uitgesloten, indien nodig.

Azure Policy gebruikt een [JSON-indeling](./concepts/definition-structure.md) om de logica te vormen die de evaluatie gebruikt om te bepalen of een resource compatibel is of niet. Definities omvatten metadata en de beleidsregel. De gedefinieerde regel kan functies, parameters, logische operatoren, voorwaarden en [eigenschapsaliassen](./concepts/definition-structure.md#aliases) gebruiken om precies het gewenste scenario te evenaren. De beleidsregel bepaalt welke resources in het bereik van de toewijzing worden geëvalueerd.

### <a name="understand-evaluation-outcomes"></a>Begrijp evaluatieresultaten

Resources worden geëvalueerd op specifieke tijdstippen tijdens de levenscyclus van de resource, de levenscyclus van beleidstoewijzingen en voor regelmatige voortdurende nalevingsevaluatie. De volgende tijden of gebeurtenissen die ervoor zorgen dat een resource wordt geëvalueerd:

- Een resource wordt gemaakt, bijgewerkt of verwijderd in een bereik met een beleidstoewijzing.
- Een beleid of initiatief is nieuw toegewezen aan een scope.
- Een beleid of initiatief dat al aan een scope is toegewezen, wordt bijgewerkt.
- Tijdens de standaard compliance evaluatiecyclus, die eens per 24 uur plaatsvindt.

Zie [Evaluatietriggers](./how-to/get-compliance-data.md#evaluation-triggers)voor gedetailleerde informatie over wanneer en hoe de beleidsevaluatie plaatsvindt.

### <a name="control-the-response-to-an-evaluation"></a>Controle over de reactie op een evaluatie

Bedrijfsregels voor het verwerken van niet-conforme resources verschillen sterk tussen organisaties. Voorbeelden van hoe een organisatie wil dat het platform reageert op een niet-klachtbron, zijn:

- De resourcewijziging weigeren
- De wijziging in de resource registreren
- De resource wijzigen voordat de wijziging wordt gewijzigd
- De resource wijzigen na de wijziging
- Gerelateerde compatibele resources implementeren

Azure Policy maakt elk van deze zakelijke antwoorden mogelijk door toepassing van [effecten.](./concepts/effects.md) Effecten worden ingesteld in het gedeelte van de **beleidsregel** van de [beleidsdefinitie](./concepts/definition-structure.md).

### <a name="remediate-non-compliant-resources"></a>Niet-compatibele resources herstellen

Hoewel deze effecten voornamelijk van invloed zijn op een resource wanneer de resource wordt gemaakt of bijgewerkt, ondersteunt Azure Policy ook het omgaan met bestaande niet-compatibele resources zonder dat deze resource hoeft te worden gewijzigd. Zie [Resources herstellen](./how-to/remediate-resources.md)voor meer informatie over het compliant maken van bestaande resources.

### <a name="video-overview"></a>Video-overzicht

Het volgende overzicht van Azure Policy is afkomstig van build 2018. Voor het downloaden van dia's of video's, gaat u naar [Azure-omgeving beheren via Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) op Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="getting-started"></a>Aan de slag

### <a name="azure-policy-and-rbac"></a>Azure-beleid en RBAC

Er zijn een paar belangrijke verschillen tussen Azure Policy en role-based access control (RBAC). Azure Policy evalueert de status door eigenschappen te onderzoeken op resources die zijn vertegenwoordigd in Resource Manager en eigenschappen van sommige ResourceProviders. Azure Policy beperkt geen acties (ook wel _bewerkingen_genoemd). Azure Policy zorgt ervoor dat de resourcestatus voldoet aan uw bedrijfsregels zonder dat u zich zorgen maakt over wie de wijziging heeft aangebracht of die toestemming heeft om een wijziging aan te brengen.

RBAC richt zich op het beheren van [gebruikersacties](../../role-based-access-control/resource-provider-operations.md) op verschillende scopes. Als controle van een actie vereist is, dan is RBAC het juiste hulpmiddel om te gebruiken. Zelfs als een persoon toegang heeft tot het uitvoeren van een actie, blokkeert Azure Policy nog steeds de maken of bijwerken als het resultaat een niet-compatibele bron is.

De combinatie van RBAC en Azure Policy biedt volledige scopecontrole in Azure.

### <a name="rbac-permissions-in-azure-policy"></a>RBAC-machtigingen in Azure Policy

Azure Policy heeft diverse machtigingen, oftewel bewerkingen, in twee verschillende resourceproviders:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Veel ingebouwde rollen wijzen machtigingen toe aan Azure Policy-resources. De functie **Resourcebeleidsbijdrager** bevat de meeste Azure-beleidsbewerkingen. Degene met de rol **Eigenaar** heeft volledige rechten. Zowel **inzender** als **Reader** hebben toegang tot alle _leesbewerkingen_ van Azure Policy. **Inzender** kan leiden tot herstel van resources, maar kan geen definities of toewijzingen _maken._

Als geen van de ingebouwde rollen de vereiste machtigingen heeft, maakt u een [aangepaste rol](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> De beheerde identiteit van een **deployIfNotExists-beleidstoewijzing** heeft voldoende machtigingen nodig om resources in de sjabloon te maken of bij te werken. Zie [Beleidsdefinities configureren voor herstel voor](./how-to/remediate-resources.md#configure-policy-definition)meer informatie voor meer informatie.

### <a name="resources-covered-by-azure-policy"></a>Bronnen die onder Azure-beleid vallen

Azure Policy evalueert alle resources in Azure. Voor bepaalde resourceproviders zoals [Gastconfiguratie,](./concepts/guest-configuration.md) [Azure Kubernetes Service](../../aks/intro-kubernetes.md)en Azure Key [Vault](../../key-vault/key-vault-overview.md)is er een diepere integratie voor het beheren van instellingen en objecten. Zie [Resourceprovider-modi](./concepts/definition-structure.md)voor meer informatie.

### <a name="recommendations-for-managing-policies"></a>Aanbevelingen voor het beheren van beleid

Enkele tips om rekening mee te houden:

- Begin met een controle-effect in plaats van een weigeringseffect om bij te houden wat de invloed van uw beleidsdefinitie is op de resources in uw omgeving. Als u al actieve scripts hebt voor automatische schaalaanpassing van uw toepassingen, kan het instellen van een weigeractie een belemmering vormen voor deze automatische taken die u al hebt geïmplementeerd.

- U kunt bij het maken van definities en toewijzingen gebruikmaken van organisatiehiërarchieën. Het is raadzaam om op een hoger niveau definities te maken, zoals op het niveau van de beheergroep of het abonnement. Vervolgens maakt u de toewijzing op het eerstvolgende onderliggende niveau. Als u een definitie voor een beheergroep maakt, kan de toewijzing worden gericht op een abonnement of resource in die beheergroep.

- Het is raadzaam om initiatiefdefinities te maken en toe te wijzen, zelfs voor slechts één beleidsdefinitie.
  U hebt bijvoorbeeld de beleidsdefinitie _policyDefA_ en u maakt deze met de initiatiefdefinitie _initiativeDefC_. Als u later nog een beleidsdefinitie maakt voor _policyDefB_, met doelen die lijken op die van _policyDefA_, kunt u deze toevoegen bij _initiativeDefC_ en ze samen volgen.

- Als u een initiatieftoewijzing hebt gemaakt, worden beleidsdefinities die worden toegevoegd aan het initiatief ook deel van de toewijzingen van het initiatief.

- Wanneer een initiatieftoewijzing wordt beoordeeld, worden alle beleidsregels in het initiatief ook beoordeeld.
  Als u een beleidsregel afzonderlijk wilt beoordelen, is het beter deze niet op te nemen in een initiatief.

## <a name="azure-policy-objects"></a>Azure-beleidsobjecten

### <a name="policy-definition"></a>Beleidsdefinitie

Het maken en implementeren van een beleidsregel in Azure Policy begint met het maken van een beleidsdefinitie. Elke beleidsdefinitie heeft voorwaarden voor het afdwingen ervan. Er is een bijbehorend effect dat wordt uitgevoerd als aan de voorwaarden wordt voldaan.

In Azure Policy wordt een aantal ingebouwde beleidsregels geboden dat standaard beschikbaar is. Bijvoorbeeld:

- **Toegestane SKU's** voor opslagaccount (Weigeren): bepaalt of een opslagaccount dat wordt geïmplementeerd zich binnen een reeks SKU-formaten bevindt. Het bijbehorende effect is om alle opslagaccounts te weigeren die niet in overeenstemming zijn met de gedefinieerde SKU-grootten.
- **Toegestaan resourcetype** (Weigeren): hiermee definieert u de resourcetypen die u implementeren. Het bijbehorende effect is om alle resources te weigeren die geen deel uitmaken van deze gedefinieerde lijst.
- **Toegestane locaties** (Weigeren): beperkt de beschikbare locaties voor nieuwe bronnen. Het bijbehorende effect wordt gebruikt om uw geografisch nalevingsvereisten af te dwingen.
- **Toegestane SKU's** voor virtuele machines (Weigeren): hiermee geeft u een set sku's voor virtuele machines op die u implementeren.
- **Een tag toevoegen aan resources** (Wijzigen): Past een vereiste tag en de standaardwaarde toe als deze niet is opgegeven door de implementatieaanvraag.
- **Tag toevoegen en de standaardwaarde** (Toevoegen): hiermee wordt een vereiste tag en de waarde ervan afgedwongen voor een resource.
- **Niet toegestaan resourcetypen** (Weigeren): hiermee voorkomt u dat een lijst met resourcetypen wordt geïmplementeerd.

Als u deze beleidsdefinities (zowel de ingebouwde als de aangepaste) wilt implementeren, dient u ze eerst toe te wijzen. U kunt elk van deze typen beleid toewijzen via Azure Portal, PowerShell of Azure CLI.

Beleid wordt beoordeeld bij verschillende acties, zoals het toewijzen of bijwerken van beleidsregels. Zie voor een volledige lijst [Beleidbeoordelingstriggers](./how-to/get-compliance-data.md#evaluation-triggers).

Raadpleeg [Structuur van beleidsdefinities](./concepts/definition-structure.md) voor meer informatie over de structuur van beleidsdefinities.

Beleidsparameters helpen uw beleidsbeheer te vereenvoudigen door het aantal beleidsdefinities te verminderen dat u moet maken. U kunt bij het maken van een beleidsdefinitie parameters definiëren om deze algemener te maken. Vervolgens kunt u deze beleidsdefinitie hergebruiken voor verschillende scenario's. Dit doet u door verschillende waarden door te voeren bij het toepassen van de beleidsdefinitie. Bijvoorbeeld, door een reeks locaties voor een abonnement op te geven.

Parameters worden gedefinieerd bij het maken van een beleidsdefinitie. Als een parameter wordt gedefinieerd, krijgt deze een naam en optioneel een waarde. U kunt bijvoorbeeld een parameter definiëren voor een beleid met de naam _locatie_. Vervolgens kunt u deze verschillende waarden toekennen, zoals _EastUS_ of _WestUS_ bij het toewijzen van beleid.

Zie [Definitiestructuur - parameters](./concepts/definition-structure.md#parameters) voor meer informatie over beleidsparameters.

### <a name="initiative-definition"></a>Initiatiefdefinitie

Een initiatiefdefinitie is een verzameling beleidsdefinities die zijn aangepast voor het bereiken van één overkoepelend doel. Initiatiefdefinities maken het beheren en toewijzen van beleidsdefinities eenvoudiger. Dit gebeurt door het groeperen van een reeks beleidsregels als één item. U kunt bijvoorbeeld een initiatief maken met de titel **Controleren inschakelen in Azure Security Center** met als doel om alle beschikbare beveiligingsaanbevelingen in uw Azure Security Center te controleren.

> [!NOTE]
> De SDK, zoals Azure CLI en Azure PowerShell, gebruikt eigenschappen en parameters met de naam **PolicySet** om naar initiatieven te verwijzen.

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
- Geef waarden op voor de parameters van de beleidsdefinities binnen deze initiatiefdefinitie. In dit voorbeeld u een lijst met locaties aan de parameter **policyA** **(toegestaanLocaties** en **policyB's**parameter ) **opgeven, toegestaanSingleLocation**. U kunt ook waarden opgeven bij het toewijzen van dit initiatief.
- Geef een lijst met _waarde_opties op die kunnen worden gebruikt bij het toewijzen van dit initiatief. Als u dit initiatief toewijst, kunnen de overgenomen parameters van de beleidsdefinities binnen het initiatief, alleen waarden hebben uit de opgegeven lijst.

Als u waardeopties maakt in een initiatiefdefinitie, is het niet mogelijk om tijdens het toewijzen van het initiatief andere waarden op te geven, omdat deze niet op de lijst staan.

### <a name="assignments"></a>Toewijzingen

Een toewijzing is een beleidsdefinitie of -initiatief die binnen een specifiek bereik moet plaatsvinden. Dit bereik kan variëren van een [beheergroep](../management-groups/overview.md) tot een afzonderlijke resource. De term _scope_ verwijst naar alle resources, resourcegroepen, abonnementen of beheergroepen waaraan de definitie is toegewezen. Toewijzingen worden overgenomen door alle onderliggende bronnen. Dit ontwerp betekent dat een definitie die wordt toegepast op een resourcegroep ook wordt toegepast op resources in die resourcegroep. U echter wel een subscope uitsluiten van de toewijzing.

U bijvoorbeeld bij het abonnementsbereik een definitie toewijzen die het maken van netwerkbronnen voorkomt. U kunt echter één resourcegroep binnen het abonnement uitsluiten, namelijk degene die is bedoeld voor netwerkinfrastructuur. U verleent vervolgens toegang tot deze netwerkresourcegroep aan gebruikers aan wie u het maken van de netwerkresourcegroep toevertrouwt.

In een ander voorbeeld u een lijstdefinitie van resourcetypen toewijzen op het niveau van de beheergroep. Vervolgens wijst u een meer tolerant beleid toe (waardoor meer resourcetypen) worden toegestaan aan een onderliggende beheergroep of zelfs rechtstreeks op abonnementen. Dit voorbeeld zou echter niet werken omdat Azure Policy een expliciet weigeringssysteem is. In plaats daarvan moet u de onderliggende beheergroep of het abonnement uitsluiten van de toewijzing op managementgroepniveau. Wijs vervolgens de meer tolerante definitie toe aan de onderliggende beheergroep of het abonnementsniveau. Als een toewijzing ertoe leidt dat een resource wordt geweigerd, is de enige manier om de resource toe te staan de weigeringstoewijzing te wijzigen.

Zie [Een beleidstoewijzing maken om niet-compatibele resources in uw Azure-omgeving te identificeren](assign-policy-portal.md)voor meer informatie over het instellen van toewijzingen via de portal. Er zijn ook stappen voor [PowerShell](assign-policy-powershell.md) en [Azure CLI](assign-policy-azurecli.md) beschikbaar.

## <a name="maximum-count-of-azure-policy-objects"></a>Maximaal aantal Azure-beleidsobjecten

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht hebt van Azure Policy en enkele van de belangrijkste geïntroduceerde concepten, ziet u hier de voorgestelde volgende stappen:

- [Bekijk de beleidsdefinitiestructuur](./concepts/definition-structure.md).
- [Een beleidsdefinitie toewijzen met behulp van de portal](./assign-policy-portal.md).
