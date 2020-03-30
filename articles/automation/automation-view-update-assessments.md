---
title: Updatebeoordelingen van Azure Update Management weergeven
description: In dit artikel wordt beschreven hoe updatebeoordelingen voor update-implementaties kunnen worden bekeken.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 58d3cf6261456c09195ad6dafaeb781b55d9e5ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278413"
---
# <a name="view-azure-update-management-update-assessments"></a>Updatebeoordelingen van Azure Update Management weergeven

Selecteer In uw Azure Automation-account **Updatebeheer** om de status van uw machines weer te geven.

Deze weergave bevat informatie over uw machines, ontbrekende updates, update-implementaties en geplande update-implementaties. In de kolom **NALEVING** u zien wanneer de machine voor het laatst is beoordeeld. In de kolom **UPDATE AGENT READINESS** u de status van de update-agent zien. Als er een probleem is, selecteert u de koppeling om naar documentatie voor probleemoplossing te gaan waarmee u het probleem verhelpen.

Als u een logboekzoekopdracht wilt uitvoeren waarin informatie over de machine, bijwerken of implementatie wordt geretourneerd, selecteert u het bijbehorende item in de lijst. Het deelvenster **Logboekzoeken** wordt geopend met een query voor het geselecteerde item:

![Standaardweergave Beheer bijwerken](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Ontbrekende updates weergeven

Selecteer **Ontbrekende updates** om de lijst met updates weer te geven die ontbreken op uw machines. Elke update wordt weergegeven en kan worden geselecteerd. Informatie over het aantal machines waarvoor de update nodig is, de details van het besturingssysteem en een koppeling voor meer informatie worden allemaal weergegeven. In **het zoekvenster Logboek** worden ook meer details over de updates weergegeven.

![Ontbrekende updates](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Update classifications

In de volgende tabellen worden de ondersteunde updateclassificaties weergegeven in Updatebeheer, met een definitie voor elke classificatie.

### <a name="windows"></a>Windows

|Classificatie  |Beschrijving  |
|---------|---------|
|Essentiële updates     | Een update voor een specifiek probleem dat een kritieke, niet-beveiligingsgerelateerde bug aanpakt.        |
|Beveiligingsupdates     | Een update voor een productspecifiek beveiligingsprobleem.        |
|Updatepakketten     | Een cumulatieve set hotfixes die samen worden verpakt voor eenvoudige implementatie.        |
|Functiepakketten     | Nieuwe productfuncties die buiten een productrelease worden gedistribueerd.        |
|Servicepacks     | Een cumulatieve set hotfixes die op een toepassing worden toegepast.        |
|Definitie-updates     | Een update voor virus- of andere definitiebestanden.        |
|Hulpprogramma's     | Een hulpprogramma of functie die helpt bij het voltooien van een of meer taken.        |
|Updates     | Een update voor een toepassing of bestand dat momenteel is geïnstalleerd.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|Classificatie  |Beschrijving  |
|---------|---------|
|Essentiële en beveiligingsupdates     | Updates voor een specifiek probleem of een productspecifiek beveiligingsprobleem.         |
|Andere Updates     | Alle andere updates die niet van cruciaal belang zijn of die geen beveiligingsupdates zijn.        |

Voor Linux kan Update Management onderscheid maken tussen kritieke updates en beveiligingsupdates in de cloud terwijl beoordelingsgegevens worden weergegeven. (Deze granulariteit is mogelijk vanwege gegevensverrijking in de cloud.) Voor het patchen is Update Management afhankelijk van classificatiegegevens die beschikbaar zijn op de machine. In tegenstelling tot andere distributies beschikt CentOS niet over deze informatie in de RTM-versies van het product. Als CentOS-machines zijn geconfigureerd om beveiligingsgegevens terug te sturen voor de volgende opdracht, kan Updatemanagement patchen op basis van classificaties:

```bash
sudo yum -q --security check-update
```

Er is momenteel geen ondersteunde methode om de beschikbaarheid van native classificatiegegevens op CentOS mogelijk te maken. Op dit moment wordt alleen ondersteuning voor de beste inspanning geboden aan klanten die deze functionaliteit zelf hebben ingeschakeld.

Om updates op Red Hat Enterprise versie 6 te classificeren, moet u de plug-in yum-security installeren. Op Red Hat Enterprise Linux 7, de plugin is al een deel van yum zelf, is er geen noodzaak om iets te installeren. Zie voor meer informatie het volgende Red Hat [kennisartikel.](https://access.redhat.com/solutions/10021)

## <a name="next-steps"></a>Volgende stappen

Nadat u updatebeoordelingen hebt bekeken, u een update-implementatie plannen door de stappen te volgen bij [Updates en patches beheren voor uw Azure VM's.](automation-tutorial-update-management.md)
