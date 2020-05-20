---
title: Beleid ontwerpen als codewerkstromen
description: Meer informatie over het ontwerpen van werk stromen om uw Azure Policy definities als code te implementeren en om resources automatisch te valideren.
ms.date: 05/20/2020
ms.topic: conceptual
ms.openlocfilehash: 972ec40609c340b159d21dde2bf18ab3330bf8cd
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684263"
---
# <a name="design-policy-as-code-workflows"></a>Beleid ontwerpen als codewerkstromen

Als u de voortgang van uw reis met Cloud governance wilt, kunt u de beleids definitie hand matig beheren in de Azure Portal of via de diverse Sdk's, zodat u ze beter kunt beheren en herhalen op ondernemings schaal. Twee van de meest voorkomende benaderingen voor het beheer van systemen op schaal in de Cloud zijn:

- Infra structuur als code: de manier waarop de inhoud wordt behandeld waarmee uw omgevingen worden gedefinieerd, met alles uit Resource Manager-sjablonen voor het Azure Policy van definities aan Azure-blauw drukken, als bron code.
- DevOps: de samen voeging van personen, processen en producten om een continue levering van de waarde aan onze eind gebruikers mogelijk te maken.

Beleid als code is een combi natie van deze ideeën. Behoud in wezen uw beleids definities in broncode beheer en telkens wanneer een wijziging wordt aangebracht, test en valideer deze wijziging. Dit geldt echter niet voor de mate van beleids betrokkenheid bij de infra structuur als code of DevOps.

De validatie stap moet ook een onderdeel zijn van andere workflows voor continue integratie of continue implementatie. Voor beelden hiervan zijn het implementeren van een toepassings omgeving of virtuele infra structuur. Door Azure Policy een vroegtijdige component van het build-en implementatie proces te valideren, ontdekken de toepassings-en Operations-teams of hun wijzigingen niet-klacht zijn, lang voordat deze te laat zijn en er wordt geprobeerd om te implementeren in de productie omgeving.

## <a name="workflow-overview"></a>Overzicht werk stroom

De aanbevolen werk stroom van het beleid als code ziet er als volgt uit:

:::image type="content" source="../media/policy-as-code/policy-as-code-workflow.png" alt-text="Overzicht van beleid voor code werk stroom" border="false":::

### <a name="create-and-update-policy-definitions"></a>Beleids definities maken en bijwerken

De beleids definities worden gemaakt met JSON en opgeslagen in broncode beheer. Elk beleid heeft een eigen set bestanden, zoals de para meters, regels en omgevings parameters, die moeten worden opgeslagen in dezelfde map. De volgende structuur is een aanbevolen manier om uw beleids definities in broncode beheer te houden.

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

Wanneer een nieuw beleid wordt toegevoegd of een bestaande beleids regel is bijgewerkt, moet de werk stroom de beleids definitie automatisch bijwerken in Azure. Het testen van de nieuwe of bijgewerkte beleids definitie wordt in een latere stap geleverd.

### <a name="create-and-update-initiative-definitions"></a>Initiatief definities maken en bijwerken

Evenzo hebben initiatieven een eigen JSON-bestand en gerelateerde bestanden die moeten worden opgeslagen in dezelfde map. Voor de initiatief definitie moet de beleids definitie al bestaan, dus kan daarom niet worden gemaakt of bijgewerkt totdat de bron voor het beleid is bijgewerkt in broncode beheer en vervolgens wordt bijgewerkt in Azure. De volgende structuur is een aanbevolen manier om uw initiatief definities in broncode beheer te houden:

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

Net als bij het toevoegen of bijwerken van een bestaand initiatief, moet de werk stroom, zoals beleids definities, de initiatief definitie in azure automatisch bijwerken. Het testen van de nieuwe of bijgewerkte initiatief definitie wordt in een latere stap geleverd.

### <a name="test-and-validate-the-updated-definition"></a>De bijgewerkte definitie testen en valideren

Zodra Automation uw nieuw gemaakte of bijgewerkte beleids-of initiatief definities heeft uitgevoerd en de update heeft aangebracht in het object in azure, is het tijd om de wijzigingen te testen die zijn aangebracht. Het beleid of het initiatief (en) waarvan het deel uitmaakt, moet vervolgens worden toegewezen aan resources in de omgeving die het verst van de productie. Deze omgeving is doorgaans _ontwikkel aars_.

De toewijzing moet [enforcementMode](./assignment-structure.md#enforcement-mode) van _uitgeschakeld_ gebruiken zodat het maken en bijwerken van resources niet wordt geblokkeerd, maar dat bestaande resources nog steeds worden gecontroleerd op naleving van de bijgewerkte beleids definitie. Zelfs met enforcementMode is het raadzaam dat het toewijzings bereik een resource groep is of een abonnement dat specifiek is voor het valideren van beleid.

> [!NOTE]
> Hoewel de afdwingings modus nuttig is, is het geen vervanging voor het grondig testen van een beleids definitie onder verschillende omstandigheden. De beleids definitie moet worden getest met `PUT` en `PATCH` rest API-aanroepen, compatibele en niet-compatibele resources en rand cases, zoals een eigenschap die van de resource ontbreekt.

Nadat de toewijzing is geïmplementeerd, gebruikt u de SDK van het beleid om de [compatibiliteits gegevens](../how-to/get-compliance-data.md) voor de nieuwe toewijzing op te halen. De omgeving die wordt gebruikt om het beleid en de toewijzingen te testen, moet zowel compatibele als niet-compatibele resources hebben. Net als bij een goede eenheids test voor code, wilt u testen of de resources naar verwachting zijn en dat u ook geen valse positieven of onwaar-negatieven hebt. Als u alleen test en valideert voor wat u verwacht, is er mogelijk een onverwachte en niet-geïdentificeerde impact van het beleid. Zie [de impact van een nieuwe Azure Policy definitie evalueren](./evaluate-impact.md)voor meer informatie.

### <a name="enable-remediation-tasks"></a>Herstel taken inschakelen

Als de validatie van de toewijzing voldoet aan verwachtingen, is de volgende stap het valideren van herstel.
Beleids regels die gebruikmaken van [deployIfNotExists](./effects.md#deployifnotexists) of [Modify](./effects.md#modify) , kunnen worden omgezet in een herstel taak en resources van een niet-compatibele status.

De eerste stap voor het oplossen van resources is het toewijzen van de toewijzing van het beleid aan de roltoewijzing die is gedefinieerd in de beleids definitie. Met deze roltoewijzing krijgt de beheerde identiteit van de beleids toewijzing voldoende rechten om de benodigde wijzigingen door te voeren om de resource compatibel te maken.

Zodra de beleids toewijzing de juiste rechten heeft, gebruikt u de SDK van het beleid om een herstel taak te activeren op basis van een set resources waarvan bekend is dat deze niet compatibel is. Er moeten drie tests worden uitgevoerd voor deze herstelde taken voordat u doorgaat:

- Controleren of de herstel taak is voltooid
- Beleids evaluatie uitvoeren om te zien dat de resultaten van beleids naleving worden bijgewerkt zoals verwacht
- Een omgevings eenheid testen op basis van de resources om de eigenschappen ervan direct te valideren

Het testen van de resultaten van de bijgewerkte beleids evaluatie en de omgeving bieden direct een bevestiging dat de herstel taken hebben gewijzigd wat er werd verwacht en dat de beleids definitie de wijziging van de naleving had gezien als verwacht.

### <a name="update-to-enforced-assignments"></a>Bijwerken naar afgedwongen toewijzingen

Nadat alle validatie-Gates zijn voltooid, werkt u de toewijzing bij om **enforcementMode** van _ingeschakeld_te gebruiken. Het is raadzaam deze wijziging in eerste instantie in dezelfde omgeving te maken. Zodra deze omgeving is gevalideerd terwijl deze werkt zoals verwacht, moet de wijziging de scope hebben om de volgende omgeving op te nemen, enzovoort, totdat het beleid is geïmplementeerd voor productie resources.

## <a name="process-integrated-evaluations"></a>Geïntegreerde evaluaties verwerken

De algemene werk stroom voor beleid als code is voor het ontwikkelen en implementeren van beleid en initiatieven voor een omgeving op schaal. Beleids evaluatie moet echter deel uitmaken van het implementatie proces voor elke werk stroom die resources implementeert of maakt in azure, zoals het implementeren van toepassingen of het uitvoeren van Resource Manager-sjablonen voor het maken van een infra structuur.

In deze gevallen moet er na de implementatie van de toepassing of infra structuur een test abonnement of resource groep worden uitgevoerd om de validatie van alle bestaande beleids regels en initiatieven te controleren. Hoewel ze kunnen worden geconfigureerd als **enforcementMode** die in een dergelijke omgeving worden _uitgeschakeld_ , is het nuttig om te weten te komen als een toepassing of infrastructuur implementatie in een vroeg stadium wordt geschonden door beleids definities. Deze beleids evaluatie moet daarom een stap zijn in deze werk stromen en er kunnen geen implementaties worden uitgevoerd die niet-compatibele resources maken.

## <a name="review"></a>Beoordelen

In dit artikel wordt de algemene werk stroom voor het beleid behandeld als code en ook de beleids evaluatie moet deel uitmaken van andere implementatie werk stromen. Deze werk stroom kan worden gebruikt in elke omgeving die script stappen en automatisering ondersteunt op basis van triggers.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de structuur van de [beleids definitie](./definition-structure.md).
- Meer informatie over de structuur van de [beleids toewijzing](./assignment-structure.md).
- Meer informatie over het [programmatisch maken van beleids regels](../how-to/programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](../how-to/get-compliance-data.md).
- Meer informatie over het [oplossen van niet-compatibele resources](../how-to/remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).