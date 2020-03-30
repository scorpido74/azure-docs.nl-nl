---
title: Overgangshandleiding voor azure-monitorweergave ontwerper naar werkmappen
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 234da921b4f0d1243ca8cfdb12ba2d851db2b43f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658690"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Overgangshandleiding voor azure-monitorweergave ontwerper naar werkmappen
[View designer](view-designer.md) is een functie van Azure Monitor waarmee u aangepaste weergaven maken om gegevens in uw Log Analytics-werkruimte te visualiseren, met grafieken, lijsten en tijdlijnen. Ze worden uitgefaseerd en vervangen door werkmappen die extra functionaliteit bieden. In dit artikel vindt u een overzicht van het proces voor het converteren van uw bestaande weergaven naar werkmappen.

## <a name="workbooks-overview"></a>Overzicht van werkmappen
[Werkmappen](../insights/vminsights-workbooks.md) combineren tekst, [logboekquery's,](../log-query/query-language.md)statistieken en parameters in uitgebreide interactieve rapporten. Teamleden met dezelfde toegang tot Azure-bronnen kunnen ook werkmappen bewerken.

Werkmappen zijn handig voor scenario's zoals:

-   Het verkennen van het gebruik van uw virtuele machine wanneer u niet weet wat de statistieken van belang op voorhand: CPU-gebruik, schijfruimte, geheugen, netwerk afhankelijkheden, enz. In tegenstelling tot andere hulpprogramma's voor gebruiksanalyse, u met werkmappen meerdere soorten visualisaties en analyses combineren, waardoor ze ideaal zijn voor dit soort verkenning en verkenning in vrije vorm.
-   Uw team uitleggen hoe een recent ingerichte VM presteert, door statistieken weer te geven voor belangrijke tellers en andere logboekgebeurtenissen.
-   Het delen van de resultaten van een formaat experiment van uw VM met andere leden van uw team. U de doelen voor het experiment met tekst uitleggen en vervolgens elke gebruiksmetrische en analysequery's weergeven die worden gebruikt om het experiment te evalueren, samen met duidelijke call-outs voor de vraag of elke statistiek boven of onder het doel lag.
-   Het rapporteren van de impact van een storing op het gebruik van uw VM, het combineren van gegevens, tekstuitleg en een discussie over de volgende stappen om uitval in de toekomst te voorkomen.


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>Waarom weergavedashboards converteren naar werkmappen?

View designer biedt de mogelijkheid om verschillende query-gebaseerde weergaven en visualisaties te genereren. Aanpassingen op hoog niveau blijven echter beperkt, zoals bijvoorbeeld het formatteren van rasters en tegelindelingen of het selecteren van alternatieve graphics om uw gegevens weer te geven. View designer is beperkt tot een totaal van negen verschillende tegels om uw gegevens weer te geven.

Workbooks is een platform dat het volledige potentieel van uw gegevens benut. werkmappen behouden niet alleen alle mogelijkheden, maar ondersteunen ook extra functionaliteit via tekst, statistieken, parameters en nog veel meer. Met werkmappen kunnen gebruikers bijvoorbeeld dichte rasters consolideren en zoekbalken toevoegen om de gegevens eenvoudig te filteren en te analyseren. 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>Voordelen van het gebruik van werkmappen boven View Designer

* Ondersteunt zowel logboeken als statistieken.
* Hiermee u zowel persoonlijke weergaven voor individuele toegangscontrole als gedeelde werkmappenweergaven weergeven.
* Aangepaste indelingsopties met tabbladen, grootte en besturingselementen voor schalen.
* Ondersteuning voor query's in meerdere Log Analytics-werkruimten, Application Insights-toepassingen en abonnementen.
* Hiermee worden aangepaste parameters inschakelt die gekoppelde grafieken en visualisaties dynamisch bijwerken.
* Ondersteuning voor sjabloongalerie seinen van openbare GitHub.

Hoewel deze handleiding eenvoudige stappen biedt om een aantal van de veelgebruikte weergave-designerweergaven rechtstreeks opnieuw te maken, kunnen gebruikers met werkmappen de vrijheid hebben om een van hun eigen aangepaste visualisaties en statistieken te maken en te ontwerpen. De volgende schermafbeelding is te zien in de [sjabloon voor het gebruik](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook) van werkruimte en toont een voorbeeld van welke werkmappen kunnen worden gemaakt:


![Voorbeeld van werkmappentoepassing](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>Werken met werkmappen gaan gebruiken
Geopende werkmappen uit de werkmappen zijn ingeschakeld in Log Analytics-werkruimten als item in de navigatiebalk aan de zijkant, direct onder de locatie van de weergaveontwerper.

![Navigatie in werkmappen](media/view-designer-conversion-overview/workbooks-nav.png)

Zodra deze optie is geselecteerd, wordt een galerie weergegeven met alle opgeslagen werkmappen en sjablonen voor uw werkruimte.

![Werkmappengalerie](media/view-designer-conversion-overview/workbooks-gallery.png)

Als u een nieuwe werkmap wilt starten, u de sjabloon **Leeg** selecteren onder **Snel starten**of het pictogram **Nieuw** in de bovenste navigatiebalk. Als u sjablonen wilt weergeven of wilt terugkeren naar opgeslagen werkmappen, selecteert u het item in de galerie of zoekt u naar de naam in de zoekbalk.

Als u een werkmap wilt opslaan, moet u het rapport opslaan met een specifieke titel, abonnement, resourcegroep en locatie.
De werkmap wordt automatisch ingevuld naar dezelfde instellingen als de LA-werkruimte, met hetzelfde abonnement, resourcegroep, maar gebruikers kunnen deze rapportinstellingen wijzigen. Werkmappen worden standaard opgeslagen in *Mijn rapporten,* alleen toegankelijk voor de individuele gebruiker. Ze kunnen ook rechtstreeks worden opgeslagen in gedeelde rapporten of later worden gedeeld.

![Werkmappen opslaan](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>Volgende stappen

- [Conversieopties](view-designer-conversion-options.md)
