---
title: Gegevensbronnen documenteren in Azure-gegevenscatalogus
description: In het artikel waarin wordt uitgelegd hoe gegevenselementen in Azure-gegevenscatalogus kunnen worden documenteren.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: e9e9013d354585d04f205feb93a84d94c0f05905
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68950189"
---
# <a name="how-to-document-data-sources-in-azure-data-catalog"></a>Gegevensbronnen documenteren in Azure-gegevenscatalogus

## <a name="introduction"></a>Inleiding
**Microsoft Azure Data Catalog** is een volledig beheerde cloudservice die dient als een systeem van registratie en detectiesysteem voor bedrijfsgegevensbronnen. Met andere woorden, **Azure Data Catalog** is alles over het helpen van mensen ontdekken, *begrijpen*en gebruiken van gegevensbronnen, en het helpen van organisaties om meer waarde te halen uit hun bestaande gegevens.

Wanneer een gegevensbron is geregistreerd bij **Azure Data Catalog,** worden de metagegevens gekopieerd en geïndexeerd door de service, maar het verhaal eindigt daar niet. **Azure Data Catalog** stelt gebruikers ook in staat om hun eigen volledige documentatie te verstrekken die het gebruik en de algemene scenario's voor de gegevensbron kan beschrijven.

In [Hoe gegevensbronnen te annoteren,](data-catalog-how-to-annotate.md)leert u dat experts die de gegevensbron kennen deze kunnen annoteren met tags en een beschrijving. De **Azure Data Catalog-portal** bevat een rich text editor, zodat gebruikers gegevenselementen en containers volledig kunnen documenteren. De editor bevat alineaopmaak, zoals koppen, tekstopmaak, lijsten met opsommingstekens, genummerde lijsten en tabellen.

Tags en beschrijvingen zijn ideaal voor eenvoudige annotaties. Om gegevensconsumenten echter beter inzicht te geven in het gebruik van een gegevensbron en bedrijfsscenario's voor een gegevensbron, kan een expert volledige, gedetailleerde documentatie verstrekken. Het is eenvoudig om een gegevensbron te documenteren. Selecteer een gegevenselement of -container en kies **Documentatie**.

![Tabblad Documentatie in een gegevenscatalogus](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Gegevenselementen documenteren
Met het voordeel van **azure datacatalogusdocumentatie** u uw gegevenscatalogus gebruiken als een inhoudsopslagplaats om een volledig verhaal van uw gegevenselementen te maken. U gedetailleerde inhoud verkennen die containers en tabellen beschrijft. Als u al inhoud hebt in een andere inhoudsopslagplaats, zoals SharePoint of een bestandsshare, u toevoegen aan de koppelingen naar de assetdocumentatie om naar deze bestaande inhoud te verwijzen. Deze functie maakt uw bestaande documenten beter vindbaar.

> [!NOTE]
> Documentatie is niet opgenomen in de zoekindex.
>

![Tabblad Documentatie en hyperlink naar webkoppeling](media/data-catalog-documentation/data-catalog-documentation2.png)

Het niveau van de documentatie kan variëren van het beschrijven van de kenmerken en de waarde van een gegevensassetcontainer tot een gedetailleerde beschrijving van het tabelschema in een container. Het niveau van de verstrekte documentatie moet worden bepaald door uw zakelijke behoeften. Maar in het algemeen, hier zijn een paar voors en tegens van het documenteren van gegevens activa:

* Document slechts een container: Alle inhoud is op één plaats, maar kan ontbreken noodzakelijke details voor gebruikers om een weloverwogen beslissing te nemen.
* Document alleen de tabellen: inhoud is specifiek voor dat object, maar uw gebruikers hebben meerdere plaatsen voor documenten.
* Documentcontainers en -tabellen: meest uitgebreide aanpak, maar kan leiden tot meer onderhoud van de documenten.

## <a name="summary"></a>Samenvatting
Het documenteren van gegevensbronnen met **Azure Data Catalog** kan een verhaal over uw gegevenselementen tot in de kleinste details maken als u nodig hebt.  Door koppelingen te gebruiken, u een koppeling maken naar inhoud die is opgeslagen in een bestaande inhoudsopslagplaats, waardoor uw bestaande documenten en gegevenselementen bij elkaar worden gebracht. Zodra uw gebruikers de juiste gegevenselementen hebben ontdekt, kunnen ze een volledige set documentatie hebben.
