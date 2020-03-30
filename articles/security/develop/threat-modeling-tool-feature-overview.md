---
title: Overzicht van de functie van Microsoft Threat Modeling Tool - Azure
description: Meer informatie over alle functies die beschikbaar zijn in de Threat Modeling Tool
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 08/17/2017
ms.openlocfilehash: 88eea0eb2bceb0f6322381a94d4339cbdfa662c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552114"
---
# <a name="threat-modeling-tool-feature-overview"></a>Overzicht van de functie Threat Modeling Tool

De Threat Modeling Tool kan u helpen met uw behoeften op het modelleren van bedreigingen. Zie [Aan de slag met de tool voor bedreigingsmodellering](threat-modeling-tool-getting-started.md)voor een basisintroductie tot de tool .

> [!NOTE]
>De Threat Modeling Tool wordt regelmatig bijgewerkt, dus controleer deze gids vaak om onze nieuwste functies en verbeteringen te zien.

Als u een lege pagina wilt openen, selecteert **u Een model maken**.

![Lege pagina](./media/threat-modeling-tool-feature-overview/tmtstart.png)

Als u de functies wilt zien die momenteel beschikbaar zijn in de tool, gebruikt u het bedreigingsmodel dat door ons team is gemaakt in het voorbeeld Aan de [slag.](threat-modeling-tool-getting-started.md)

![Basisbedreigingsmodel](./media/threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Navigatie

Voordat we de ingebouwde functies bespreken, laten we de belangrijkste onderdelen in de tool bekijken.

### <a name="menu-items"></a>Menu-items

De ervaring is vergelijkbaar met andere Microsoft-producten. Laten we de menu-items op het hoogste niveau bekijken.

![Menu-items](./media/threat-modeling-tool-feature-overview/menuitems.png)

| Label                               | Details      |
| --------------------------------------- | ------------ |
| **Bestand** | <ul><li>Bestanden openen, opslaan en sluiten</li><li>Meld u aan en meld u af bij OneDrive-accounts.</li><li>Koppelingen delen (bekijken en bewerken).</li><li>Bestandsinformatie weergeven.</li><li>Een nieuwe sjabloon toepassen op bestaande modellen.</li></ul> |
| **Bewerken** | Acties ongedaan maken en opnieuw uitvoeren, evenals kopiëren, plakken en verwijderen. |
| **Weergave** | <ul><li>Schakelen tussen **analyse-** en **ontwerpweergaven.**</li><li>Open gesloten vensters (bijvoorbeeld stencils, elementeigenschappen en berichten).</li><li>De lay-out opnieuw instellen naar standaardinstellingen.</li></ul> |
| **Diagram** | Diagrammen toevoegen en verwijderen en door tabbladen met diagrammen gaan. |
| **Rapporten** | HTML-rapporten maken om met anderen te delen. |
| **Help** | Zoek handleidingen om u te helpen de tool te gebruiken. |

De symbolen zijn sneltoetsen voor de menu's op het hoogste niveau:

| Symbool                               | Details      |
| --------------------------------------- | ------------ |
| **Openen** | Hiermee opent u een nieuw bestand. |
| **Opslaan** | Hiermee slaat u het huidige bestand op. |
| **Ontwerp** | Hiermee opent u de **ontwerpweergave,** waar u modellen maken. |
| **Analyseren** | Toont gegenereerde bedreigingen en hun eigenschappen. |
| **Diagram toevoegen** | Hiermee voegt u een nieuw diagram toe (vergelijkbaar met nieuwe tabbladen in Excel). |
| **Diagram verwijderen** | Hiermee verwijdert u het huidige diagram. |
| **Kopiëren/knippen/plakken** | Kopieën, sneden en plakken elementen. |
| **Ongedaan maken/opnieuw doen** | Maakt acties ongedaan en doet deze opnieuw. |
| **In- en uitzoomen** | Zoomt in en uit het diagram voor een betere weergave. |
| **Feedback** | Opent het MSDN-forum. |

### <a name="canvas"></a>Canvas

Het canvas is de ruimte waar u elementen sleept en neerzet. Slepen en neerzetten is de snelste en meest efficiënte manier om modellen te bouwen. U ook met de rechtermuisknop klikken en items selecteren in het menu om algemene versies van elementen toe te voegen, zoals weergegeven:

#### <a name="drop-the-stencil-on-the-canvas"></a>Het stencil op het canvas neerzetten

![Canvas-neerzetten](./media/threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Het stencil selecteren

![Elementeigenschappen](./media/threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Stencils

Op basis van de sjabloon die u selecteert, u alle stencils vinden die beschikbaar zijn voor gebruik. Als u de juiste elementen niet vinden, gebruikt u een andere sjabloon. U een sjabloon aanpassen aan uw behoeften. Over het algemeen u een combinatie van categorieën zoals deze vinden:

| Stencilnaam                               | Details      |
| --------------------------------------- | ------------ |
| **Proces** | Toepassingen, browserplug-ins, threads, virtuele machines |
| **Externe interactie** | Verificatieproviders, browsers, gebruikers, webapplicaties |
| **Gegevensarchief** | Cache, opslag, configuratiebestanden, databases, register |
| **Gegevensstroom** | Binair, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, benoemde pijp, RPC/DCOM, SMB, UDP |
| **Vertrouwenslijn/randgrens** | Bedrijfsnetwerken, internet, machine, sandbox, user/kernel mode |

### <a name="notesmessages"></a>Notities/berichten

| Onderdeel                               | Details      |
| --------------------------------------- | ------------ |
| **Berichten** | Interne gereedschapslogica die gebruikers waarschuwt wanneer er een fout optreedt, zoals geen gegevensstromen tussen elementen. |
| **Opmerkingen** | Handmatige notities worden toegevoegd aan het bestand door engineering teams tijdens het ontwerp-en beoordelingsproces. |

### <a name="element-properties"></a>Elementeigenschappen

Elementeigenschappen variëren afhankelijk van de elementen die u selecteert. Naast vertrouwensgrenzen bevatten alle andere elementen drie algemene selecties:

| Element, eigenschap                               | Details      |
| --------------------------------------- | ------------ |
| **Naam** | Handig voor het benoemen van uw processen, winkels, interactors en stromen, zodat ze gemakkelijk worden herkend. |
| **Buiten het toepassingsgebied** | Als dit is geselecteerd, wordt het element uit de matrix voor bedreigingsgeneratie gehaald (niet aanbevolen). |
| **Reden voor buiten het bereik** | Het veld Uitvullen om gebruikers te laten weten waarom buiten het bereik is geselecteerd. |

Eigenschappen worden gewijzigd onder elke elementcategorie. Selecteer elk element om de beschikbare opties te inspecteren. U de sjabloon openen voor meer informatie. Laten we eens kijken naar de functies.

## <a name="welcome-screen"></a>Welkomstscherm

Wanneer u de app opent, ziet u het **welkomstscherm.**

### <a name="open-a-model"></a>Een model openen

Plaats de muisaanwijzer op **Een model openen** om twee opties te onthullen: Open vanaf deze **computer** en open **vanaf OneDrive**. De eerste optie opent het scherm **Bestand openen.** Met de tweede optie voert u het aanmeldingsproces voor OneDrive. Na succesvolle verificatie u mappen en bestanden selecteren.

![Model openen](./media/threat-modeling-tool-feature-overview/openmodel.png)

![Openen vanaf de computer of OneDrive](./media/threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Feedback, suggesties en problemen

Wanneer u **feedback, suggesties en problemen selecteert,** gaat u naar het MSDN-forum voor SDL-hulpprogramma's. U lezen wat andere mensen zeggen over de tool, inclusief oplossingen en nieuwe ideeën.

![Feedback](./media/threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>Ontwerpweergave

Wanneer u een nieuw model opent of maakt, wordt de **ontwerpweergave** geopend.

### <a name="add-elements"></a>Elementen toevoegen

U elementen op het raster op twee manieren toevoegen:

- **Slepen en neerzetten:** Sleep het gewenste element naar het raster. Gebruik vervolgens de elementeigenschappen om aanvullende informatie te verstrekken.
- **Klik met de rechtermuisknop:** klik met de rechtermuisknop ergens in het raster en selecteer items in het vervolgkeuzemenu. Er verschijnt een algemene weergave van het element dat u selecteert op het scherm.

### <a name="connect-elements"></a>Elementen verbinden

U elementen op twee manieren verbinden:

- **Slepen en neerzetten:** Sleep de gewenste gegevensstroom naar het raster en sluit beide uiteinden aan op de juiste elementen.
- **Klik op + Shift:** Klik op het eerste element (gegevens verzenden), houd shift ingedrukt en selecteer het tweede element (gegevens ontvangen). Klik met de rechtermuisknop en selecteer **Verbinding maken**. Als u een bidirectionele gegevensstroom gebruikt, is de volgorde niet zo belangrijk.

### <a name="properties"></a>Eigenschappen

 Als u de eigenschappen wilt zien die op de stencils kunnen worden gewijzigd, selecteert u het stencil en wordt de informatie dienovereenkomstig ingevuld. In het volgende voorbeeld wordt voor en na het slepen van een **databasestencil** naar het diagram gesleept:

#### <a name="before"></a>Voor

![Voor](./media/threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Na

![Na](./media/threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Berichten

Als u een bedreigingsmodel maakt en vergeet gegevensstromen met elementen te verbinden, ontvangt u een melding. U het bericht negeren of u de instructies volgen om het probleem op te lossen. 

![Berichten](./media/threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Opmerkingen

Als u notities aan uw diagram wilt toevoegen, schakelt u van het tabblad **Berichten** naar het tabblad **Notities.**

## <a name="analysis-view"></a>Analyseweergave

Nadat u het diagram hebt gemaakt, selecteert u het **symbool Analyse** (het vergrootglas) op de werkbalk sneltoetsen om over te schakelen naar de **analyseweergave.**

![Analyseweergave](./media/threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Selectie van gegenereerde bedreigingen

Wanneer u een bedreiging selecteert, u drie verschillende functies gebruiken:

| Functie                               | Informatie      |
| --------------------------------------- | ------------ |
| **Lees-indicator** | <p>De bedreiging is gemarkeerd als gelezen, waardoor u de items bijhouden die u hebt beoordeeld.</p><p>![Lees/ongelezen indicator](./media/threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Interactiefocus** | <p>Interactie in het diagram dat bij een bedreiging hoort, wordt gemarkeerd.</p><p>![Interactiefocus](./media/threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Bedreigingseigenschappen** | <p>Aanvullende informatie over de bedreiging wordt weergegeven in het venster **Bedreigingseigenschappen.**</p><p>![Bedreigingseigenschappen](./media/threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Prioriteitswijziging

U het prioriteitsniveau van elke gegenereerde bedreiging wijzigen. Verschillende kleuren maken het gemakkelijk om bedreigingen met hoge, gemiddelde en lage prioriteit te identificeren.

![Prioriteitswijziging](./media/threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Bewerkbare velden voor bedreigingseigenschappen

Zoals te zien in de vorige afbeelding, u de informatie die door het gereedschap wordt gegenereerd wijzigen. U ook informatie toevoegen aan bepaalde velden, zoals een rechtvaardiging. Deze velden worden gegenereerd door de sjabloon. Als u meer informatie nodig hebt voor elke bedreiging, u wijzigingen aanbrengen.

![Bedreigingseigenschappen](./media/threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Rapporten

Nadat u bent klaar met het wijzigen van prioriteiten en het bijwerken van de status van elke gegenereerde bedreiging, u het bestand opslaan en/of een rapport afdrukken. Ga naar **Volledig** > **rapport maken melden**. Geef het rapport een naam en u ziet iets dat lijkt op de volgende afbeelding:

![Rapport](./media/threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Volgende stappen

- Stuur uw vragen, opmerkingen tmtextsupport@microsoft.comen zorgen naar . **[Download](https://aka.ms/threatmodelingtool)** de Threat Modeling Tool om aan de slag te gaan.
- Ga naar onze [GitHub-pagina](https://github.com/Microsoft/threat-modeling-templates) om een sjabloon voor de community bij te dragen.