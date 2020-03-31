---
title: Beleid ontwerpen als codewerkstromen
description: Leer werkstromen ontwerpen om uw Azure-beleidsdefinities als code te implementeren en resources automatisch te valideren.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 0914ba6510c9d2ef87d3f83417f97340d42c8bce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74267269"
---
# <a name="design-policy-as-code-workflows"></a>Beleid ontwerpen als codewerkstromen

Naarmate u verder komt op uw reis met Cloud Governance, wilt u overstappen van het handmatig beheren van elke beleidsdefinitie in de Azure-portal of via de verschillende SDK's naar iets dat beter beheersbaar en herhaalbaar is op bedrijfsschaal. Twee van de belangrijkste benaderingen voor het beheren van systemen op schaal in de cloud zijn:

- Infrastructuur als code: de praktijk van het behandelen van de inhoud die uw omgevingen definieert, van Resource Manager-sjablonen tot Azure Policy-definities tot Azure Blueprints, als broncode.
- DevOps: De vereniging van mensen, processen en producten om continue levering van waarde aan onze eindgebruikers mogelijk te maken.

Beleid als Code is de combinatie van deze ideeën. In wezen, houd uw beleidsdefinities in bronbeheer en wanneer een wijziging wordt aangebracht, test en valideer die wijziging. Dat mag echter niet de omvang zijn van beleidsbetrokkenheid bij Infrastructuur als Code of DevOps.

De validatiestap moet ook een onderdeel zijn van andere continue integratie- of continue implementatieworkflows. Voorbeelden hiervan zijn het implementeren van een toepassingsomgeving of virtuele infrastructuur. Door azure policy validation een vroeg onderdeel van het build- en implementatieproces te maken, ontdekken de toepassings- en bedrijfsteams of hun wijzigingen niet-klacht zijn, lang voordat het te laat is en ze proberen te implementeren in productie.

## <a name="workflow-overview"></a>Overzicht van werkstroom

De aanbevolen algemene werkstroom van Beleid als Code ziet er als volgt uit:

![Overzicht van de werkstroom beleid als code](../media/policy-as-code/policy-as-code-workflow.png)

### <a name="create-and-update-policy-definitions"></a>Beleidsdefinities maken en bijwerken

De beleidsdefinities worden gemaakt met JSON en opgeslagen in bronbeheer. Elk beleid heeft zijn eigen set bestanden, zoals de parameters, regels en omgevingsparameters, die in dezelfde map moeten worden opgeslagen. De volgende structuur is een aanbevolen manier om uw beleidsdefinities in bronbeheer te houden.

```text
.
|
|- policies/  ________________________ # Root folder for policies
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

Wanneer een nieuw beleid wordt toegevoegd of een bestaand beleid wordt bijgewerkt, moet de werkstroom de beleidsdefinitie in Azure automatisch bijwerken. Het testen van de nieuwe of bijgewerkte beleidsdefinitie komt in een latere stap.

### <a name="create-and-update-initiative-definitions"></a>Initiatiefdefinities maken en bijwerken

Ook initiatieven hebben hun eigen JSON-bestand en gerelateerde bestanden die moeten worden opgeslagen in dezelfde map. De initiatiefdefinitie vereist dat de beleidsdefinitie al bestaat, dus kan niet worden gemaakt of bijgewerkt totdat de bron voor het beleid is bijgewerkt in bronbeheer en vervolgens is bijgewerkt in Azure. De volgende structuur is een aanbevolen manier om uw initiatiefdefinities in bronbeheer te houden:

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

Net als beleidsdefinities moet de werkstroom bij het toevoegen of bijwerken van een bestaand initiatief de initiatiefdefinitie in Azure automatisch bijwerken. Het testen van de nieuwe of bijgewerkte initiatiefdefinitie komt in een latere stap.

### <a name="test-and-validate-the-updated-definition"></a>De bijgewerkte definitie testen en valideren

Zodra automatisering uw nieuw gemaakte of bijgewerkte beleids- of initiatiefdefinities heeft gemaakt en de update naar het object in Azure heeft uitgevoerd, is het tijd om de wijzigingen te testen die zijn aangebracht. Ofwel het beleid of het initiatief (s) het is een deel van moet dan worden toegewezen aan middelen in het milieu het verst van de productie. Deze omgeving is meestal _Dev_.

De toewijzing moet gebruik maken van [enforcementMode](./assignment-structure.md#enforcement-mode) van _uitgeschakeld,_ zodat het maken van resources en updates niet worden geblokkeerd, maar dat bestaande resources nog steeds worden gecontroleerd op naleving van de bijgewerkte beleidsdefinitie. Zelfs met enforcementMode wordt aanbevolen dat het toewijzingsbereik een resourcegroep of een abonnement is dat specifiek wordt gebruikt voor het valideren van beleid.

> [!NOTE]
> Hoewel de handhavingsmodus nuttig is, is het geen vervanging voor het grondig testen van een beleidsdefinitie onder verschillende voorwaarden. De beleidsdefinitie moet `PUT` worden `PATCH` getest met en REST API-aanroepen, compatibele en niet-conforme resources en randaanvragen zoals een eigenschap die ontbreekt in de bron.

Nadat de toewijzing is geïmplementeerd, gebruikt u de Policy SDK om [nalevingsgegevens](../how-to/get-compliance-data.md) voor de nieuwe toewijzing te krijgen. De omgeving die wordt gebruikt om het beleid en de toewijzingen te testen, moet zowel compatibele als niet-conforme resources hebben. Net als een goede eenheid test voor code, wilt u testen dat de middelen zijn zoals verwacht en dat je ook geen false-positives of false-negatives. Als u alleen test en valideert voor wat u verwacht, kunnen er onverwachte en niet-geïdentificeerde gevolgen van het beleid zijn. Zie [De impact van een nieuw Azure-beleid evalueren](./evaluate-impact.md)voor meer informatie.

### <a name="enable-remediation-tasks"></a>Hersteltaken inschakelen

Als validatie van de toewijzing aan de verwachtingen voldoet, is de volgende stap het valideren van herstel.
Beleidsregels die [deployIfNotExists](./effects.md#deployifnotexists) gebruiken of [wijzigen,](./effects.md#modify) kunnen worden omgezet in een hersteltaak en juiste resources van een niet-conforme status.

De eerste stap om dit te doen is het verlenen van de beleidstoewijzing de roltoewijzing gedefinieerd in de beleidsdefinitie. Deze roltoewijzing geeft de beheerde identiteit van de beleidstoewijzing voldoende rechten om de benodigde wijzigingen aan te brengen om de resource compliant te maken.

Zodra de beleidstoewijzing de juiste rechten heeft, gebruikt u de BeleidsSDK om een hersteltaak te activeren tegen een reeks resources waarvan bekend is dat deze niet-compatibel is. Tegen deze gesaneerde taken moeten drie tests worden uitgevoerd voordat:

- Valideren of de hersteltaak is voltooid
- Voer beleidsevaluatie uit om te zien of de resultaten van de naleving van het beleid worden bijgewerkt zoals verwacht
- Een milieu-eenheidstest uitvoeren op basis van de resources die rechtstreeks zijn gevalideerd om hun eigenschappen te valideren, zijn gewijzigd

Het testen van zowel de bijgewerkte beleidsevaluatieresultaten als de omgeving geeft direct een bevestiging dat de hersteltaken veranderden wat werd verwacht en dat de beleidsdefinitie de naleving heeft zien veranderen zoals verwacht.

### <a name="update-to-enforced-assignments"></a>Bijwerken naar afgedwongen opdrachten

Nadat alle validatiepoorten zijn voltooid, werkt u de toewijzing bij om **enforcementMode** van _ingeschakeld_te gebruiken. Deze wijziging moet in eerste instantie worden aangebracht in dezelfde omgeving ver van de productie. Zodra die omgeving is gevalideerd zoals verwacht, moet de wijziging worden beperkt tot de volgende omgeving en ga zo maar door totdat het beleid wordt geïmplementeerd in productiebronnen.

## <a name="process-integrated-evaluations"></a>Geïntegreerde evaluaties van het proces

De algemene workflow voor Beleid als Code is voor het ontwikkelen en implementeren van beleid en initiatieven in een omgeving op schaal. Beleidsevaluatie moet echter deel uitmaken van het implementatieproces voor elke werkstroom die resources implementeert of maakt in Azure, zoals het implementeren van toepassingen of het uitvoeren van Resource Manager-sjablonen om infrastructuur te maken.

In deze gevallen moet, nadat de implementatie van de toepassing of infrastructuur is uitgevoerd naar een testabonnement of resourcegroep, beleidsevaluatie worden uitgevoerd voor die scope die de validatie van alle bestaande beleidsregels en initiatieven controleert. Hoewel ze kunnen worden geconfigureerd als **enforcementMode** _uitgeschakeld_ in een dergelijke omgeving, is het handig om vroeg te weten of een toepassing of infrastructuur implementatie in strijd is met beleidsdefinities vroeg. Deze beleidsevaluatie moet daarom een stap zijn in deze werkstromen en implementaties mislukken die niet-conforme resources maken.

## <a name="review"></a>Beoordelen

Dit artikel behandelt de algemene werkstroom voor Beleid als Code en ook waar beleidsevaluatie deel moet uitmaken van andere implementatiewerkstromen. Deze werkstroom kan worden gebruikt in elke omgeving die gescripte stappen en automatisering ondersteunt op basis van triggers.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [beleidsdefinitiestructuur](./definition-structure.md).
- Meer informatie over de [beleidstoewijzingsstructuur](./assignment-structure.md).
- Begrijpen hoe [u programmatisch beleid maken.](../how-to/programmatically-create.md)
- Meer informatie over het [verzamelen van nalevingsgegevens](../how-to/get-compliance-data.md).
- Meer informatie over het [herstellen van niet-conforme resources.](../how-to/remediate-resources.md)
- Bekijk wat een beheergroep is met [Uw resources organiseren met Azure-beheergroepen.](../../management-groups/overview.md)