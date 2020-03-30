---
title: Filters in Azure Monitor-weergaven | Microsoft Documenten
description: Met een filter in een Azure Monitor-weergave kunnen gebruikers de gegevens in de weergave filteren op de waarde van een bepaalde eigenschap zonder de weergave zelf te wijzigen.  In dit artikel wordt beschreven hoe u een filter gebruikt en een filter toevoegen aan een aangepaste weergave.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: b4840ed30eb1f6dc8d6e6cef47da17807f9644d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658571"
---
# <a name="filters-in-azure-monitor-views"></a>Filters in Azure Monitor-weergaven
Met een **filter** in een [Azure Monitor-weergave](view-designer.md) kunnen gebruikers de gegevens in de weergave filteren op de waarde van een bepaalde eigenschap zonder de weergave zelf te wijzigen.  U gebruikers van uw weergave bijvoorbeeld toestaan de weergave alleen vanaf een bepaalde computer of een bepaalde computer te filteren.  U meerdere filters maken in één weergave, zodat gebruikers op meerdere eigenschappen kunnen filteren.  In dit artikel wordt beschreven hoe u een filter gebruikt en een filter toevoegen aan een aangepaste weergave.

## <a name="using-a-filter"></a>Een filter gebruiken
Klik op het datumtijdbereik boven aan een weergave om de vervolgkeuzelijst te openen waarin u het datumtijdbereik voor de weergave wijzigen.

![Voorbeeld van filter](media/view-designer-filters/filters-example-time.png)

Klik **+** op de optie om een filter toe te voegen met aangepaste filters die zijn gedefinieerd voor de weergave. Selecteer een waarde voor het filter in de vervolgkeuzelijst of typ een waarde. Doorgaan met het toevoegen **+** van filters door op de . 


![Voorbeeld van filter](media/view-designer-filters/filters-example-custom.png)

Als u alle waarden voor een filter verwijdert, wordt dat filter niet meer toegepast.


## <a name="creating-a-filter"></a>Een filter maken

Maak een filter op het tabblad **Filters** bij [het bewerken van een weergave](view-designer.md).  Het filter is globaal voor de weergave en is van toepassing op alle onderdelen in de weergave.  

![Filterinstellingen](media/view-designer-filters/filters-settings.png)

In de volgende tabel worden de instellingen voor een filter beschreven.

| Instelling | Beschrijving |
|:---|:---|
| Veldnaam | Naam van het veld dat wordt gebruikt voor filtering.  Dit veld moet overeenkomen met het veld Samenvatten in **Query voor Waarden**. |
| Query voor waarden | Query die moet worden uitgevoerd om de vervolgkeuzelijst voor de gebruiker in te vullen.  Deze query moet [samenvatten](/azure/kusto/query/summarizeoperator) of [onderscheiden](/azure/kusto/query/distinctoperator) gebruiken om unieke waarden voor een bepaald veld te bieden en moet overeenkomen met de **veldnaam**.  U [sorteren](/azure/kusto/query/sortoperator) gebruiken om de waarden te sorteren die aan de gebruiker worden weergegeven. |
| Label | Naam voor het veld dat wordt gebruikt in query's die het filter ondersteunen en wordt ook weergegeven aan de gebruiker. |

### <a name="examples"></a>Voorbeelden

De volgende tabel bevat een paar voorbeelden van algemene filters.  

| Veldnaam | Query voor waarden | Label |
|:--|:--|:--|
| Computer   | Heartbeat &#124; verschillende computer &#124; sorteren op computer asc | Computers |
| EventLevelName | Gebeurtenis &#124; afzonderlijke EventLevelName | Severity |
| ErnstNiveau | Syslog &#124; verschillende ernstniveau | Severity |
| SvcChangeType | ConfigurationChange &#124; afzonderlijke svcChangeType | ChangeType (ChangeType) |


## <a name="modify-view-queries"></a>Weergavequery's wijzigen

Als een filter enig effect heeft, moet u eventuele query's in de weergave wijzigen om op de geselecteerde waarden te filteren.  Als u geen query's in de weergave wijzigt, hebben alle waarden die de gebruiker selecteert geen effect.

De syntaxis voor het gebruik van een filterwaarde in een query is: 

    where ${filter name}  

Als in uw weergave bijvoorbeeld een query is weergegeven die gebeurtenissen retourneert en een filter met de naam _Computers_gebruikt, u de volgende query gebruiken.

    Event | where ${Computers} | summarize count() by EventLevelName

Als u een ander filter met de naam Ernst hebt toegevoegd, u de volgende query gebruiken om beide filters te gebruiken.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [visualisatieonderdelen die](view-designer-parts.md) u toevoegen aan uw aangepaste weergave.
