---
title: Overzicht van Azure Monitor Workbooks
description: Complexe rapportage vereenvoudigen met vooraf gebouwde en aangepaste geparametereerde werkmappen
author: mrbullwinkle
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 29e675f3ae35df9211f58d45ad8450566d67a588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658231"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor-werkmappen

Werkmappen bieden een flexibel canvas voor gegevensanalyse en het maken van uitgebreide visuele rapporten binnen de Azure-portal. Hiermee u meerdere gegevensbronnen uit heel Azure gebruiken en deze combineren tot uniforme interactieve ervaringen. 

## <a name="data-sources"></a>Gegevensbronnen

Werkmappen kunnen gegevens uit meerdere bronnen binnen Azure opvragen. Auteurs van werkmappen kunnen deze gegevens omzetten om inzicht te geven in de beschikbaarheid, prestaties, het gebruik en de algehele status van de onderliggende componenten. Bijvoorbeeld het analyseren van prestatielogboeken van virtuele machines om hoge CPU- of lage geheugenexemplaren te identificeren en de resultaten weer te geven als een raster in een interactief rapport.
  
Maar de echte kracht van werkmappen is de mogelijkheid om gegevens uit verschillende bronnen te combineren in één rapport. Dit maakt het mogelijk samengestelde resourceweergaven te maken of voegt zich bij bronnen toe, zodat rijkere gegevens en inzichten die anders onmogelijk zouden zijn.

Werkmappen zijn momenteel compatibel met de volgende gegevensbronnen:

* [Logs](workbooks-data-sources.md#logs)
* [Statistieken](workbooks-data-sources.md#metrics)
* [Azure Resource Graph](workbooks-data-sources.md#azure-resource-graph)
* [Waarschuwingen (preview)](workbooks-data-sources.md#alerts-preview)
* [Werkbelastingstatus (voorbeeld)](workbooks-data-sources.md#workload-health-preview)
* [Azure-bronstatus (voorbeeld)](workbooks-data-sources.md#azure-resource-health)
* [Azure Data Explorer (voorbeeld)](workbooks-data-sources.md#azure-data-explorer-preview)

## <a name="visualizations"></a>Visualisaties

Werkmappen bieden een uitgebreide set mogelijkheden voor het visualiseren van uw gegevens. Voor gedetailleerde voorbeelden van elk visualisatietype u onderstaande voorbeeldkoppelingen raadplegen:

* [Tekst](workbooks-visualizations.md#text)
* [Grafieken](workbooks-visualizations.md#charts)
* [Rasters](workbooks-visualizations.md#grids)
* [Tegels](workbooks-visualizations.md#tiles)
* [Bomen](workbooks-visualizations.md#trees)
* [Grafieken](workbooks-visualizations.md#graphs)

![Voorbeeld van werkmapvisualisaties](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>Aan de slag

Als u de werkmappenervaring wilt verkennen, navigeert u eerst naar de Azure Monitor-service. Dit kan door **Monitor** in het zoekvak in de Azure-portal te typen.

Selecteer vervolgens **Werkmappen (voorbeeld)**.

![Schermafbeelding van de knop Voorbeeld van werkmappen die is gemarkeerd in een rood vak](./media/workbooks-overview/workbooks-preview.png)

### <a name="gallery"></a>Galerie

Dit brengt u naar de werkmappengalerie:

![Schermafbeelding van de galerieweergave Azure Monitor-werkmappen](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>Werkmappen versus werkmapsjablonen

U een _werkmap_ in het groen en een aantal _werkmapsjablonen_ in paars weergeven. Sjablonen dienen als samengestelde rapporten die zijn ontworpen voor flexibel hergebruik door meerdere gebruikers en teams. Als u een sjabloon opent, wordt een tijdelijke werkmap gemaakt die is gevuld met de inhoud van de sjabloon. 

U de parameters van de op een sjabloon gebaseerde werkmap aanpassen en analyses uitvoeren zonder bang te zijn voor het doorbreken van de toekomstige rapportage-ervaring voor collega's. Als u een sjabloon opent, voert u enkele aanpassingen uit en selecteert u het pictogram Opslaan dat u de sjabloon opslaat als een werkmap die vervolgens in het groen wordt weergegeven en de oorspronkelijke sjabloon onaangeroerd laat. 

Onder de motorkap verschillen sjablonen ook van opgeslagen werkmappen. Als u een werkmap opslaat, wordt een gekoppelde Azure Resource Manager-bron gemaakt, terwijl in de tijdelijke werkmap die is gemaakt bij het openen van een sjabloon geen unieke resource is gekoppeld. Raadpleeg het artikel voor het [besturingselement voor werkmappen](workbooks-access-control.md)voor meer informatie over hoe toegangsbeheer wordt beheerd in werkmappen.

### <a name="exploring-a-workbook-template"></a>Een werkmapsjabloon verkennen

Selecteer **Analyse van toepassingsfouten** om een van de standaardwerkmapsjablonen voor toepassingen te bekijken.

![Schermafbeelding van de analysesjabloon voor toepassingsfouten](./media/workbooks-overview/failure-analysis.png)

Zoals eerder vermeld, maakt het openen van de sjabloon een tijdelijke werkmap waarmee u communiceren. Standaard wordt de werkmap geopend in de leesmodus, waarin alleen de informatie wordt weergegeven voor de beoogde analyse-ervaring die is gemaakt door de oorspronkelijke sjabloonauteur.

In het geval van deze specifieke werkmap is de ervaring interactief. U het abonnement, de gerichte apps en het tijdsbereik van de gegevens die u wilt weergeven, aanpassen. Zodra u deze selecties hebt gemaakt, is het raster van HTTP-aanvragen ook interactief, waarbij het selecteren van een afzonderlijke rij de gegevens in de twee grafieken onder aan het rapport wijzigt.

### <a name="editing-mode"></a>Bewerkingsmodus

Als u wilt begrijpen hoe deze werkmapsjabloon is samengesteld, moet u wisselen naar de bewerkingsmodus door **Bewerken te selecteren.** 

![Schermafbeelding van de analysesjabloon voor toepassingsfouten](./media/workbooks-overview/edit.png)

Zodra u bent overgestapt naar de bewerkingsmodus, ziet u dat er een aantal **bewerkingsvakken** rechts lijken die overeenkomen met elk afzonderlijk aspect van uw werkmap.

![Schermafbeelding van de knop Bewerken](./media/workbooks-overview/edit-mode.png)

Als we de bewerkingsknop direct onder het raster van aanvraaggegevens selecteren, kunnen we zien dat dit deel van onze werkmap bestaat uit een Kusto-query tegen gegevens uit een Application Insights-bron.

![Schermafbeelding van onderliggende Kusto-query](./media/workbooks-overview/kusto.png)

Als u klikt op de andere **knoppen bewerken** aan de rechterkant, worden een aantal kerncomponenten weergegeven die werkmappen vormen, zoals op markdown gebaseerde [tekstvakken,](workbooks-visualizations.md#text)gebruikersinterface-elementen [voor parameterselectie](workbooks-parameters.md) en andere [grafiek-/visualisatietypen](workbooks-visualizations.md). 

Het verkennen van de vooraf gebouwde sjablonen in de bewerkingsmodus en deze vervolgens aanpassen aan uw behoeften en het opslaan van uw eigen aangepaste werkmap is een uitstekende manier om te beginnen met meer informatie over wat er mogelijk is met Azure Monitor-werkmappen.

## <a name="pinning-visualizations"></a>Visualisaties vastmaken

Stappen voor tekst, query's en statistieken in een werkmap kunnen worden vastgemaakt met de pinknop op die items terwijl de werkmap in de pinmodus staat, of als de auteur van de werkmap instellingen voor dat element heeft ingeschakeld om het pinpictogram zichtbaar te maken. 

Als u de pinmodus wilt openen, klikt u op **Bewerken** om de bewerkingsmodus in te voeren en selecteert u het blauwe pictogram voor de pin in de bovenste balk. Er verschijnt dan een afzonderlijk pinpictogram boven het *vak Bewerken* van elk overeenkomstig werkmaponderdeel aan de rechterkant van het scherm.

![Pin-ervaring](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> De status van de werkmap wordt opgeslagen op het moment van de pin en vastgemaakte werkmappen op een dashboard worden niet bijgewerkt als de onderliggende werkmap wordt gewijzigd. Als u een vastgemaakt werkmaponderdeel wilt bijwerken, moet u dat onderdeel verwijderen en opnieuw vastmaken.

## <a name="dashboard-time-ranges"></a>Dashboardtijdbereiken

Vastgemaakte werkmapquerydelen respecteren het tijdsbereik van het dashboard als het vastgemaakte item is geconfigureerd om een parameter *Tijdbereik* te gebruiken. De tijdbereikwaarde van het dashboard wordt gebruikt als de waarde van de parameter tijdbereik en elke wijziging van het dashboardtijdbereik zorgt ervoor dat het vastgemaakte item wordt bijgewerkt. Als een vastgemaakt onderdeel het tijdsbereik van het dashboard gebruikt, ziet u de ondertitel van de vastgemaakte onderdeelupdate om het tijdsbereik van het dashboard weer te geven wanneer het tijdsbereik verandert. 

Bovendien worden vastgemaakte werkmaponderdelen met behulp van een parameter voor tijdbereik automatisch vernieuwd met een snelheid die wordt bepaald door het tijdsbereik van het dashboard. De laatste keer dat de query is uitgevoerd, wordt weergegeven in de ondertitel van het vastgemaakte onderdeel.

Als een vastgemaakte stap een expliciet ingesteld tijdsbereik heeft (geen parameter voor tijdbereik gebruikt), wordt dat tijdsbereik altijd gebruikt voor het dashboard, ongeacht de instellingen van het dashboard. De ondertitel van het vastgemaakte onderdeel geeft het tijdsbereik van het dashboard niet weer en de query wordt niet automatisch vernieuwd op het dashboard. De ondertitel wordt weergegeven wanneer de query voor het laatst is uitgevoerd.

> [!NOTE]
> Query's met de *gegevensbron samenvoegen* worden momenteel niet ondersteund bij het vastmaken aan dashboards.

## <a name="sharing-workbook-templates"></a>Werkmapsjablonen delen

Zodra u begint met het maken van uw eigen werkmapsjablonen, wilt u deze misschien delen met de bredere community. Ga naar onze [GitHub-repository](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)voor meer informatie en voor het verkennen van andere sjablonen die geen deel uitmaken van de standaardweergave van de Azure Monitor-galerie. Ga naar de [Werkmapbibliotheek](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) op GitHub om door bestaande werkmappen te bladeren.

## <a name="next-step"></a>Volgende stap

* [Ga aan de slag met](workbooks-visualizations.md) het leren van meer over werkmappen met veel uitgebreide visualisatiesopties.
* [Beheer](workbooks-access-control.md) en deel de toegang tot uw werkmapbronnen.
