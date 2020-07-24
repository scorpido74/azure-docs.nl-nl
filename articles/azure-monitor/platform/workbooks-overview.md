---
title: Overzicht van Azure Monitor Workbooks
description: Vereenvoudig complexe rapportage met vooraf samengestelde en aangepaste werkmappen met para meters
author: mrbullwinkle
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.author: mbullwin
ms.openlocfilehash: 044786d6a6aa4f9f3d163090c0edfa3832023b10
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87116955"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor werkmappen

Werkmappen bieden een flexibel canvas voor gegevens analyse en het maken van uitgebreide visuele rapporten in de Azure Portal. Hiermee kunt u in meerdere gegevens bronnen in azure tikken en deze combi neren in Unified Interactive-ervaringen. 

## <a name="data-sources"></a>Gegevensbronnen

Werkmappen kunnen gegevens uit meerdere bronnen in azure opvragen. Auteurs van werkmappen kunnen deze gegevens transformeren om inzicht te krijgen in de beschik baarheid, de prestaties, het gebruik en de algemene status van de onderliggende onderdelen. U kunt bijvoorbeeld prestatie logboeken analyseren van virtuele machines om een hoog CPU-of laag geheugen te identificeren en de resultaten weer te geven als een raster in een interactief rapport.
  
Maar de kracht van werkmappen is de mogelijkheid om gegevens uit verschillende bronnen in één rapport te combi neren. Dit maakt het mogelijk om samengestelde resource weergaven te maken of samen te voegen over resources, waardoor rijkere gegevens en inzichten worden ingeschakeld die anders niet mogelijk zouden zijn.

Werkmappen zijn momenteel compatibel met de volgende gegevens bronnen:

* [Logboeken](workbooks-data-sources.md#logs)
* [Metrische gegevens](workbooks-data-sources.md#metrics)
* [Azure Resource Graph](workbooks-data-sources.md#azure-resource-graph)
* [Waarschuwingen (preview)](workbooks-data-sources.md#alerts-preview)
* [Werk belasting status](workbooks-data-sources.md#workload-health)
* [Azure Resource Health](workbooks-data-sources.md#azure-resource-health)
* [Azure Data Explorer](workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>Visualisaties

Werkmappen bieden een uitgebreide set mogelijkheden voor het visualiseren van uw gegevens. Raadpleeg de onderstaande voorbeeld koppelingen voor gedetailleerde voor beelden van elk type visualisatie:

* [Tekst](workbooks-visualizations.md#text)
* [Grafieken](workbooks-visualizations.md#charts)
* [Rasters](workbooks-visualizations.md#grids)
* [Tegels](workbooks-visualizations.md#tiles)
* [Mapstructuren](workbooks-visualizations.md#trees)
* [Grafieken](workbooks-visualizations.md#graphs)

![Voor beelden van werkmap visualisaties](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>Aan de slag

Als u de werkmappen-ervaring wilt verkennen, gaat u eerst naar de Azure Monitor-service. U kunt dit doen door **monitor** te typen in het zoekvak in de Azure Portal.

Selecteer vervolgens **werkmappen**.

![Scherm afbeelding van de knop werkmappen gemarkeerd in een rood vak](./media/workbooks-overview/workbooks.png)

### <a name="gallery"></a>Galerie

Hiermee gaat u naar de galerie met werkmappen:

![Scherm afbeelding van de galerie met Azure Monitor werkmappen](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>Werkmappen versus werkmap sjablonen

U kunt een _werkmap_ in een groen en een aantal _werkmap sjablonen_ in paars weer geven. Sjablonen fungeren als gerapporteerde rapporten die zijn ontworpen voor flexibel gebruik door meerdere gebruikers en teams. Als u een sjabloon opent, wordt een tijdelijke werkmap gemaakt die is gevuld met de inhoud van de sjabloon. 

U kunt de para meters van de werkmap op basis van een sjabloon aanpassen en analyses uitvoeren zonder bang te zijn voor het verbreken van de toekomstige rapportage ervaring voor collega's. Als u een sjabloon opent, wijzigingen aanbrengt en vervolgens het pictogram opslaan selecteert, wordt de sjabloon opgeslagen als een werkmap die vervolgens groen wordt weer gegeven, zodat de oorspronkelijke sjabloon ongewijzigd blijft. 

Onder de motorkap verschillen sjablonen ook van opgeslagen werkmappen. Als u een werkmap opslaat, wordt een gekoppelde Azure Resource Manager resource gemaakt, terwijl de tijdelijke werkmap die wordt gemaakt wanneer alleen een sjabloon wordt geopend, geen unieke resource is gekoppeld. Raadpleeg het [artikel werkmappen Access Control](workbooks-access-control.md)voor meer informatie over het beheren van toegangs beheer in werkmappen.

### <a name="exploring-a-workbook-template"></a>Een werkmap sjabloon verkennen

Selecteer **analyse van toepassings fouten** om een van de standaard sjablonen voor toepassings werkmappen te bekijken.

![Scherm opname van sjabloon voor toepassings fout analyse](./media/workbooks-overview/failure-analysis.png)

Zoals eerder is aangegeven, maakt het openen van de sjabloon een tijdelijke werkmap waarmee u kunt werken. De werkmap wordt standaard geopend in de Lees modus, waarin alleen de informatie wordt weer gegeven voor de beoogde analyse-ervaring die is gemaakt door de oorspronkelijke auteur van de sjabloon.

In het geval van deze bepaalde werkmap is de ervaring interactief. U kunt het abonnement, de beoogde apps en het tijds bereik van de gegevens die u wilt weer geven aanpassen. Wanneer u deze selecties hebt gemaakt, wordt het raster van HTTP-aanvragen ook interactief weer gegeven. Als u een afzonderlijke rij selecteert, worden de gegevens in de twee grafieken onder aan het rapport gewijzigd.

### <a name="editing-mode"></a>Bewerkings modus

Als u wilt weten hoe deze werkmap sjabloon samen wordt geplaatst, moet u de bewerkings modus **wijzigen door bewerken**te selecteren. 

![Scherm opname van sjabloon voor toepassings fout analyse](./media/workbooks-overview/edit.png)

Wanneer u overschakelt naar de bewerkings modus, ziet u dat er een aantal **bewerkings** vakken worden weer gegeven aan de rechter kant die overeenkomt met elk afzonderlijk aspect van uw werkmap.

![Scherm afbeelding van de knop bewerken](./media/workbooks-overview/edit-mode.png)

Als we de knop bewerken direct onder het raster van aanvraag gegevens selecteren, kunnen we zien dat dit deel van onze werkmap bestaat uit een Kusto-query op basis van gegevens uit een Application Insights bron.

![Scherm opname van onderliggende Kusto-query](./media/workbooks-overview/kusto.png)

Als u op de andere **bewerkings** knoppen aan de rechter kant klikt, wordt een aantal van de belangrijkste onderdelen voor werkmappen, zoals op [prijs op basis](workbooks-visualizations.md#text)van korting, UI-elementen voor [parameter selectie](workbooks-parameters.md) en andere [grafiek/visualisatie typen](workbooks-visualizations.md)weer geven. 

Het verkennen van de vooraf gemaakte sjablonen in de Bewerk modus en deze vervolgens aanpassen aan uw behoeften en uw eigen aangepaste werkmap opslaan is een uitstekende manier om te leren wat er mogelijk is met Azure Monitor-werkmappen.

## <a name="pinning-visualizations"></a>Visualisaties vastmaken

De stappen voor tekst, query's en metrische gegevens in een werkmap kunnen worden vastgemaakt met behulp van de knop vastmaken op deze items terwijl de werkmap zich in de pincode modus bevindt, of als de auteur van de werkmap instellingen heeft ingeschakeld voor dat element om het speld pictogram zichtbaar te maken. 

Als u toegang wilt krijgen tot de modus pincode, klikt u op **bewerken** om de bewerkings modus in te voeren en selecteert u het pictogram blauwe pincode in de bovenste balk. Er wordt dan een afzonderlijk speld pictogram weer gegeven boven elk *invoervak* van het werkmap onderdeel aan de rechter kant van het scherm.

![Ervaring vastmaken](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> De status van de werkmap wordt opgeslagen op het moment van de pincode en vastgemaakte werkmappen op een dash board worden niet bijgewerkt als de onderliggende werkmap wordt gewijzigd. Als u een vastgemaakte werkmap onderdeel wilt bijwerken, moet u dat onderdeel verwijderen en opnieuw vastmaken.

## <a name="dashboard-time-ranges"></a>Tijd bereik van dash board

Met de vastgemaakte werkmap-onderdelen wordt het tijds bereik van het dash board in acht genomen als het vastgemaakte item is geconfigureerd voor het gebruik van een *tijds bereik* parameter. De waarde van het tijds bereik van het dash board wordt gebruikt als de waarde van de tijds bereik parameter en elke wijziging van het tijds bereik van het dash board zorgt ervoor dat het vastgemaakte item wordt bijgewerkt. Als een vastgemaakt deel het tijds bereik van het dash board gebruikt, ziet u de ondertitel van de bijgewerkte onderdeel Update om het tijds bereik van het dash board weer te geven wanneer het tijds bereik verandert. 

Vastgemaakte werkmap onderdelen die gebruikmaken van een tijds bereik parameter, worden automatisch vernieuwd tegen een snelheid die wordt bepaald door het tijds bereik van het dash board. De laatste keer dat de query is uitgevoerd, wordt deze weer gegeven in de ondertitel van het vastgemaakte gedeelte.

Als een vastgemaakte stap een expliciet ingesteld tijds bereik heeft (geen tijds bereik parameter gebruikt), wordt dat tijds bereik altijd gebruikt voor het dash board, ongeacht de instellingen van het dash board. De ondertitel van het vastgemaakte deel geeft niet het tijds bereik van het dash board weer en de query wordt niet automatisch vernieuwd op het dash board. De ondertitel toont de laatste keer dat de query wordt uitgevoerd.

> [!NOTE]
> Query's die de gegevens bron voor *samen voegen* gebruiken, worden momenteel niet ondersteund bij het vastmaken aan dash boards.

## <a name="sharing-workbook-templates"></a>Werkmap Sjablonen delen

Wanneer u begint met het maken van uw eigen werkmap sjablonen, wilt u deze mogelijk delen met de bredere community. Ga naar onze [github-opslag plaats](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)voor meer informatie en om andere sjablonen te verkennen die geen deel uitmaken van de standaard Azure monitor galerie weergave. Als u door bestaande werkmappen wilt bladeren, gaat u naar de [werkmap bibliotheek](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) op github.

## <a name="next-step"></a>Volgende stap

* [Ga](workbooks-visualizations.md) voor meer informatie over werkmappen veel uitgebreide visualisaties opties.
* De toegang tot uw werkmap resources [beheren](workbooks-access-control.md) en delen.
