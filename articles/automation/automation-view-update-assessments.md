---
title: Azure Automation Updatebeheer update-evaluaties weer geven
description: In dit artikel wordt beschreven hoe update-evaluaties voor update-implementaties worden weer gegeven.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 75762afc9ae69da81e89ce320f454d9764f82914
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617384"
---
# <a name="view-azure-automation-update-management-update-assessments"></a>Azure Automation Updatebeheer update-evaluaties weer geven

Selecteer in uw Azure Automation-account **updatebeheer** om de status van uw computers weer te geven.

Deze weer gave bevat informatie over uw computers, ontbrekende updates, update-implementaties en geplande update-implementaties. In de kolom **compatibiliteit** ziet u de laatste keer dat de computer is geëvalueerd. In de **gereedheids** kolom van de Update-Agent kunt u de status van de Update Agent bekijken. Als er een probleem is, selecteert u de koppeling om naar probleemoplossings documentatie te gaan waarmee u het probleem kunt verhelpen.

Als u een zoek opdracht in het logboek wilt uitvoeren die informatie over de computer, update of implementatie retourneert, selecteert u het bijbehorende item in de lijst. Het deel venster zoeken in Logboeken wordt geopend met een query voor het geselecteerde item.

![Standaard weergave Updatebeheer](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Ontbrekende updates weer geven

Selecteer **ontbrekende updates** om de lijst met updates weer te geven die ontbreken op uw computers. Elke update wordt weer gegeven en kan worden geselecteerd. Informatie over het aantal machines dat moet worden bijgewerkt, Details van het besturings systeem en een koppeling voor meer informatie, worden weer gegeven. Het deel venster zoeken in Logboeken bevat ook meer informatie over de updates.

![Ontbrekende updates](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Update classifications

De volgende tabellen geven een lijst van de ondersteunde update classificaties in Updatebeheer, met een definitie voor elke classificatie.

### <a name="windows"></a>Windows

|Classificatie  |Beschrijving  |
|---------|---------|
|Essentiële updates     | Updates voor specifieke problemen die kritiek zijn, niet-beveiligings fouten.        |
|Beveiligingsupdates     | Updates voor productspecifieke, beveiligings problemen.        |
|Updatepakketten     | Sets van hotfixes die samen zijn verpakt voor een eenvoudige implementatie.        |
|Functiepakketten     | Nieuwe product functies die worden gedistribueerd buiten een product release.        |
|Servicepacks     | Sets van hotfixes die op een toepassing worden toegepast.        |
|Definitie-updates     | Updates van virus-of andere definitie bestanden.        |
|Hulpprogramma's     | Hulpprogram ma's of functies die helpen bij het volt ooien van een of meer taken.        |
|Updates     | Updates voor toepassingen of bestanden die momenteel worden geïnstalleerd.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|Classificatie  |Beschrijving  |
|---------|---------|
|Essentiële en beveiligingsupdates     | Updates voor een specifiek probleem of een productspecifiek beveiligings probleem.         |
|Andere Updates     | Alle andere updates die niet kritiek zijn of die geen beveiligings updates zijn.        |

Voor Linux kan Updatebeheer een onderscheid maken tussen essentiële updates en beveiligings updates in de Cloud, terwijl beoordelings gegevens worden weer gegeven. (Deze granulatie is mogelijk vanwege gegevens verrijking in de Cloud.) Voor patching is Updatebeheer afhankelijk van de classificatie gegevens die op de computer beschikbaar zijn. In tegens telling tot andere distributies is CentOS deze informatie niet beschikbaar in de RTM-versies van het product. Als er CentOS machines zijn geconfigureerd voor het retour neren van beveiligings gegevens voor de volgende opdracht, kunt Updatebeheer patch op basis van classificaties uitvoeren:

```bash
sudo yum -q --security check-update
```

Er is momenteel geen ondersteunde methode voor het inschakelen van systeem eigen classificatie-gegevens beschikbaarheid op CentOS. Op dit moment wordt alleen ondersteuning voor de beste werk belasting gegeven aan klanten die hun eigen functionaliteit hebben ingeschakeld.

Als u updates wilt classificeren voor Red Hat Enter prise versie 6, moet u de yum-beveiligings-invoeg toepassing installeren. Op Red Hat Enterprise Linux 7 maakt de invoeg toepassing al deel uit van yum. u hoeft niets te installeren. Zie het volgende Red Hat [Knowledge-artikel](https://access.redhat.com/solutions/10021)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Nadat u de update-evaluaties hebt bekeken, kunt u een update-implementatie plannen door de stappen te volgen op [updates en patches voor uw virtuele Azure-machines beheren](automation-tutorial-update-management.md).
