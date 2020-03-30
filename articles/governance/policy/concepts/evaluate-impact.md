---
title: De impact van een nieuw Azure-beleid evalueren
description: Inzicht in het te volgen proces bij de invoering van een nieuwe beleidsdefinitie in uw Azure-omgeving.
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: 562fa2378356ddc1eac48b6ea5c160ebf655d525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74463521"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy"></a>De impact van een nieuw Azure-beleid evalueren

Azure Policy is een krachtig hulpmiddel voor het beheren van uw Azure-resources volgens bedrijfsstandaarden en om te voldoen aan de nalevingsbehoeften. Wanneer personen, processen of pijplijnen resources maken of bijwerken, beoordeelt Azure Policy de aanvraag. Wanneer het effect beleidsdefinitie [Append](./effects.md#deny) of [DeployIfNotExists](./effects.md#deployifnotexists)is, wijzigt Beleid de aanvraag of wordt deze toegevoegd. Wanneer het effect beleidsdefinitie [Audit](./effects.md#audit) of [AuditIfNotExists](./effects.md#auditifnotexists)is, zorgt beleid ervoor dat een activiteitslogboekvermelding wordt gemaakt. En wanneer het effect van de beleidsdefinitie [Weigeren](./effects.md#deny)is, stopt beleid het maken of wijzigen van de aanvraag.

Deze resultaten zijn precies zoals gewenst wanneer u weet dat het beleid correct is gedefinieerd. Het is echter belangrijk om te valideren dat een nieuw beleid werkt zoals bedoeld voordat het werk kan wijzigen of blokkeren. De validatie moet ervoor zorgen dat alleen de beoogde resources niet-compatibel zijn en dat er geen compatibele resources onjuist zijn opgenomen (ook wel _fout-positief genoemd)_ in de resultaten.

De aanbevolen benadering van het valideren van een nieuwe beleidsdefinitie is door de volgende stappen te volgen:

- Definieer uw beleid strak
- Uw bestaande resources controleren
- Nieuwe of bijgewerkte resourceaanvragen controleren
- Uw beleid implementeren op resources
- Doorlopende bewaking

## <a name="tightly-define-your-policy"></a>Definieer uw beleid strak

Het is belangrijk om te begrijpen hoe het bedrijfsbeleid wordt geïmplementeerd als beleidsdefinitie en de relatie tussen Azure-resources en andere Azure-services. Deze stap wordt uitgevoerd door [de vereisten te identificeren](../tutorials/create-custom-policy-definition.md#identify-requirements) en de [resourceeigenschappen te bepalen.](../tutorials/create-custom-policy-definition.md#determine-resource-properties)
Maar het is ook belangrijk om verder te kijken dan de enge definitie van uw bedrijfsbeleid. Staat uw beleid bijvoorbeeld "Alle virtuele machines moeten..."? Hoe zit het met andere Azure-services die gebruik maken van VM's, zoals HDInsight of AKS? Bij het definiëren van een beleid moeten we overwegen hoe dit beleid invloed heeft op resources die door andere diensten worden gebruikt.

Daarom moeten uw beleidsdefinities zo strak worden gedefinieerd en gericht op de resources en de eigenschappen die u moet evalueren op naleving mogelijk.

## <a name="audit-existing-resources"></a>Bestaande resources controleren

Voordat u nieuwe of bijgewerkte resources wilt beheren met uw nieuwe beleidsdefinitie, u het beste zien hoe een beperkte subset van bestaande resources, zoals een testbrongroep, wordt geëvalueerd. Gebruik de [handhavingsmodus](./assignment-structure.md#enforcement-mode)
_Uitgeschakeld_ (DoNotEnforce) op uw beleidstoewijzing om te voorkomen dat het [effect](./effects.md) triggering of activity log items worden gemaakt.

Deze stap geeft u de kans om de nalevingsresultaten van het nieuwe beleid voor bestaande resources te evalueren zonder dat dit gevolgen heeft voor de werkstroom. Controleer of er geen compatibele resources zijn gemarkeerd als niet-compatibel _(false positive)_ en of alle resources waarvan u verwacht dat ze niet-compatibel zijn, correct zijn gemarkeerd.
Nadat de eerste subset van resources is gevalideerd zoals verwacht, wordt de evaluatie langzaam uitgebreid naar alle bestaande resources.

Het op deze manier evalueren van bestaande middelen biedt ook de mogelijkheid om niet-conforme middelen te herstellen voordat het nieuwe beleid volledig wordt uitgevoerd. Deze opschoning kan handmatig of via een [hersteltaak](../how-to/remediate-resources.md) worden uitgevoerd als het effect van de beleidsdefinitie _DeployIfNotExists is._

## <a name="audit-new-or-updated-resources"></a>Nieuwe of bijgewerkte resources controleren

Zodra u hebt gevalideerd dat uw nieuwe beleidsdefinitie correct wordt rapportage over bestaande resources, is het tijd om te kijken naar de impact van het beleid wanneer resources worden gemaakt of bijgewerkt. Als de beleidsdefinitie effectparameterisatie ondersteunt, gebruikt u [Audit](./effects.md#audit). Met deze configuratie u het maken en bijwerken van resources controleren om te zien of de nieuwe beleidsdefinitie een vermelding in het Azure-activiteitenlogboek activeert voor een bron die niet-compatibel is zonder dat dit gevolgen heeft voor bestaande werk of aanvragen.

Het wordt aanbevolen om zowel nieuwe bronnen bij te werken als te maken die overeenkomen met uw beleidsdefinitie om te zien dat het _controle-effect_ correct wordt geactiveerd wanneer verwacht. Wees op zoek naar resourceaanvragen die niet mogen worden beïnvloed door de nieuwe beleidsdefinitie die het _controle-effect activeert._
Deze beïnvloede middelen zijn een ander voorbeeld van _false positives_ en moeten in de beleidsdefinitie worden vastgelegd voordat de volledige uitvoering wordt uitgevoerd.

In het geval dat de beleidsdefinitie in dit stadium van het testen wordt gewijzigd, wordt aanbevolen om het validatieproces opnieuw te beginnen met de controle van bestaande resources. Een wijziging van de beleidsdefinitie voor een _false positive_ op nieuwe of bijgewerkte resources zal waarschijnlijk ook gevolgen hebben voor bestaande bronnen.

## <a name="deploy-your-policy-to-resources"></a>Uw beleid implementeren op resources

Nadat u de validatie van uw nieuwe beleidsdefinitie met zowel bestaande resources als nieuwe of bijgewerkte resourceaanvragen hebt voltooid, begint u met het implementeren van het beleid. Het wordt aanbevolen om eerst de beleidstoewijzing voor de nieuwe beleidsdefinitie te maken voor een subset van alle resources, zoals een resourcegroep. Nadat u de eerste implementatie hebt gevaliderd, breidt u het toepassingsgebied van het beleid uit tot bredere en bredere niveaus, zoals abonnementen en beheergroepen. Deze uitbreiding wordt bereikt door de toewijzing te verwijderen en een nieuwe te maken op de doelscopes totdat deze is toegewezen aan het volledige bereik van resources die bedoeld zijn om onder uw nieuwe beleidsdefinitie te vallen.

Als resources zich tijdens de implementatie bevinden die moeten worden vrijgesteld van uw nieuwe beleidsdefinitie, moet u deze op een van de volgende manieren aanpakken:

- De beleidsdefinitie bijwerken om explicieter te zijn om onbedoelde impact te verminderen
- Het bereik van de beleidstoewijzing wijzigen (door een nieuwe toewijzing te verwijderen en te maken)
- De groep resources toevoegen aan de uitsluitingslijst voor de beleidstoewijzing

Eventuele wijzigingen in het bereik (niveau of uitsluitingen) moeten volledig worden gevalideerd en gecommuniceerd met uw beveiligings- en nalevingsorganisaties om ervoor te zorgen dat er geen hiaten in de dekking zijn.

## <a name="monitor-your-policy-and-compliance"></a>Controleer uw beleid en naleving

Het implementeren en toewijzen van uw beleidsdefinitie is niet de laatste stap. Controleer continu het [nalevingsniveau](../how-to/get-compliance-data.md) van resources voor uw nieuwe beleidsdefinitie en stel de juiste [Azure Monitor-waarschuwingen en meldingen](../../../azure-monitor/platform/alerts-overview.md) in voor wanneer niet-compatibele apparaten worden geïdentificeerd. Het wordt ook aanbevolen om de beleidsdefinitie en gerelateerde toewijzingen op een geplande basis te evalueren om de beleidsdefinitie te valideren, om te voldoen aan de behoeften aan bedrijfsbeleid en naleving. Beleid moet worden verwijderd als dat niet langer nodig is. Beleidsregels moeten ook van tijd tot tijd worden bijgewerkt naarmate de onderliggende Azure-resources evolueren en nieuwe eigenschappen en mogelijkheden toevoegen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [beleidsdefinitiestructuur](./definition-structure.md).
- Meer informatie over de [beleidstoewijzingsstructuur](./assignment-structure.md).
- Begrijpen hoe [u programmatisch beleid maken.](../how-to/programmatically-create.md)
- Meer informatie over het [verzamelen van nalevingsgegevens](../how-to/get-compliance-data.md).
- Meer informatie over het [herstellen van niet-conforme resources.](../how-to/remediate-resources.md)
- Bekijk wat een beheergroep is met [Uw resources organiseren met Azure-beheergroepen.](../../management-groups/overview.md)