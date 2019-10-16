---
title: Azure Updatebeheer update-evaluaties weer geven
description: In dit artikel wordt beschreven hoe u update-evaluaties weergeeft voor update-implementaties
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e88622ede6437086b86a33081d6ec9b9ea50ef65
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377718"
---
# <a name="view-azure-update-management-update-assessments"></a>Azure Updatebeheer update-evaluaties weer geven

Selecteer in uw Automation-account **updatebeheer** om de status van uw computers weer te geven.

Deze weer gave bevat informatie over uw computers, ontbrekende updates, update-implementaties en geplande update-implementaties. In de **kolom compatibiliteit**ziet u de laatste keer dat de computer is geëvalueerd. In de **gereedheids** kolom van de Update-Agent kunt u zien of de status van de Update Agent is. Als er een probleem is, selecteert u de koppeling om naar probleemoplossings documentatie te gaan en leert u welke stappen u moet ondernemen om het probleem te verhelpen.

Als u een zoek opdracht in het logboek wilt uitvoeren die informatie over de computer, update of implementatie retourneert, selecteert u het item in de lijst. Het deel venster **Zoeken in Logboeken** wordt geopend met een query voor het geselecteerde item:

![Standaard weergave Updatebeheer](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Ontbrekende updates weer geven

Selecteer **ontbrekende updates** om de lijst met updates weer te geven die ontbreken op uw computers. Elke update wordt weer gegeven en kan worden geselecteerd. Informatie over het aantal machines dat moet worden bijgewerkt, het besturings systeem en een koppeling voor meer informatie wordt weer gegeven. In het deel venster **Zoeken in Logboeken** ziet u meer informatie over de updates.

![Ontbrekende updates](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Update classificaties

De volgende tabellen geven een lijst van de update classificaties in Updatebeheer, met een definitie voor elke classificatie.

### <a name="windows"></a>Windows

|Classificatie  |Beschrijving  |
|---------|---------|
|Essentiële updates     | Een update voor een specifiek probleem dat betrekking heeft op een kritieke bug die niet aan beveiliging voldoet.        |
|Beveiligingsupdates     | Een update voor een productspecifiek, beveiligings probleem.        |
|Updatepakketten     | Een cumulatieve set met hotfixes die samen zijn verpakt voor een eenvoudige implementatie.        |
|Functiepakketten     | Nieuwe product functies die worden gedistribueerd buiten een product release.        |
|Servicepacks     | Een cumulatieve set met hotfixes die op een toepassing worden toegepast.        |
|Definitie-updates     | Een update van virus-of andere definitie bestanden.        |
|Tools     | Een hulp programma of functie waarmee u een of meer taken kunt volt ooien.        |
|Updates     | Een update voor een toepassing of bestand dat momenteel is geïnstalleerd.        |

### <a name="linux-2"></a>Spreek

|Classificatie  |Beschrijving  |
|---------|---------|
|Essentiële en beveiligingsupdates     | Updates voor een specifiek probleem of een productspecifiek beveiligings probleem.         |
|Andere Updates     | Alle andere updates die niet kritiek zijn of geen beveiligings updates zijn.        |

Voor Linux kan Updatebeheer een onderscheid maken tussen essentiële updates en beveiliging in de Cloud, terwijl evaluatie gegevens worden weer gegeven vanwege gegevens verrijking in de Cloud. Voor patching is Updatebeheer afhankelijk van de classificatie gegevens die op de computer beschikbaar zijn. In tegens telling tot andere distributies is deze informatie niet beschikbaar in CentOS. Als er CentOS-machines zijn geconfigureerd op een manier om beveiligings gegevens te retour neren voor de volgende opdracht, kan Updatebeheer patch op basis van classificaties.

```bash
sudo yum -q --security check-update
```

Er is momenteel geen methode die wordt ondersteund voor het inschakelen van systeem eigen classificatie-gegevens beschikbaarheid op CentOS. Op dit moment wordt alleen ondersteuning voor de beste werk belasting gegeven aan klanten die deze zelf kunnen hebben ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

Nadat u de update-evaluaties hebt bekeken, kunt u een update-implementatie plannen door de stappen onder [updates en patches voor uw virtuele Azure-machines beheren](automation-tutorial-update-management.md)te volgen.