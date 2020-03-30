---
title: Inleiding tot de ondersteuning voor de ingebouwde Jupyter-notitieblokken in Azure Cosmos DB (Preview)
description: Lees hoe u de ingebouwde Jupyter-notitieblokken ondersteuning in Azure Cosmos DB gebruiken om query's interactief uit te voeren.
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/22/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 5b320485001d6cbc457d39ef193ed8c57f7161df
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760280"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Ondersteuning voor ingebouwde Jupyter-laptops in Azure Cosmos DB (voorbeeld)

Jupyter notebook is een open-source webapplicatie waarmee u documenten maken en delen met live code, vergelijkingen, visualisaties en verhalende tekst. Azure Cosmos DB ondersteunt ingebouwde Jupyter-notebooks voor alle API's zoals Cassandra, MongoDB, SQL, Gremlin en Table. Met de ingebouwde notebookondersteuning voor alle Azure Cosmos DB-API's en gegevensmodellen u query's interactief uitvoeren. De Jupyter-notitieblokken worden uitgevoerd binnen de Azure Cosmos-accounts en stellen ontwikkelaars in staat om gegevensverkenning, gegevensreiniging, gegevenstransformaties, numerieke simulaties, statistische modellering, gegevensvisualisatie en machine learning uit te voeren.

![Visualisaties van Jupyter-notitieblokken in Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png)

De Jupyter-notebooks ondersteunen magische functies die de mogelijkheden van de kernel uitbreiden door extra opdrachten te ondersteunen. Cosmos-magie is een opdracht die de mogelijkheden van de Python-kernel in Jupyter-notitieblok uitbreidt, zodat u naast Apache Spark ook Azure Cosmos SQL API-query's uitvoeren. U Python- en SQL API-query's eenvoudig combineren om gegevens op te vragen en te visualiseren met behulp van uitgebreide visualisatiebibliotheken die zijn geïntegreerd met renderopdrachten.
Azure portal integreert native Jupyter-notebookervaring in Azure Cosmos-accounts, zoals in de volgende afbeelding wordt weergegeven:

![Ondersteuning voor Jupyter-laptops in Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png)

## <a name="benefits-of-jupyter-notebooks"></a>Voordelen van Jupyter-laptops

Jupyter notebooks werden oorspronkelijk ontwikkeld voor data science applicaties geschreven in Python, R. Ze kunnen echter op verschillende manieren worden gebruikt voor verschillende soorten projecten, zoals:

* ***Gegevensvisualisaties:** Met Jupyter-notitieblokken u gegevens visualiseren in de vorm van een gedeeld notitieblok dat bepaalde gegevensset als een afbeelding weergeeft. Met Jupyter-notitieblok u visualisaties maken, deze delen en interactieve wijzigingen in de gedeelde code en gegevensset toestaan.

* **Code delen:** Services zoals GitHub bieden manieren om code te delen, maar ze zijn grotendeels niet-interactief. Met een Jupyter-notitieblok u code bekijken, uitvoeren en de resultaten rechtstreeks weergeven in de Azure-portal.

* **Live interacties met code:** Jupyter notebook code is dynamisch; het kan stapsgewijs worden bewerkt en stapsgewijs worden uitgevoerd in realtime. Notitieblokken kunnen ook gebruikersbesturingselementen (bijvoorbeeld schuifregelaars of tekstinvoervelden) insluiten die kunnen worden gebruikt als invoerbronnen voor code, demo's of Proof of Concepts (POCs).

* **Documentatie van codemonsters en resultaten van gegevensverkenning:** Als u een stukje code hebt en u wilt regel voor regel uitleggen hoe het werkt in Azure Cosmos DB, met realtime uitvoer onderweg, u het insluiten in een Jupyter-notitieblok. De code blijft volledig functioneel. U tegelijkertijd interactiviteit toevoegen met de documentatie.

* **Cosmos magische commando's:** In Jupyter-notitieblokken u aangepaste magische opdrachten voor Azure Cosmos DB gebruiken om interactief computergebruik gemakkelijker te maken. Bijvoorbeeld de %%sql-magie waarmee je een Cosmos-container met SQL API rechtstreeks in een notitieblok opvragen.

* **Alles op één plek:** Jupyter-notebooks combineren code, rijke tekst, afbeeldingen, video's, animaties, wiskundige vergelijkingen, plots, kaarten, interactieve figuren, widgets en grafische gebruikersinterfaces tot één document.

## <a name="components-of-a-jupyter-notebook"></a>Onderdelen van een Jupyter-laptop

Jupyter-notebooks kunnen verschillende soorten componenten bevatten, elk ingedeeld in afzonderlijke blokken:

* **Tekst en HTML:** Platte tekst, of tekst geannoteerd in de markdown syntaxis om HTML te genereren, kan op elk moment in het document worden ingevoegd. CSS-styling kan ook inline worden opgenomen of worden toegevoegd aan de sjabloon die wordt gebruikt om het notitieblok te genereren.

* **Code en uitvoer:** Jupyter-notebooks ondersteunen Python-code. De resultaten van de uitgevoerde code worden onmiddellijk na de codeblokken weergegeven en de codeblokken kunnen meerdere keren worden uitgevoerd in elke gewenste volgorde.

* **Visualisaties:** Afbeeldingen en grafieken kunnen worden gegenereerd uit de code, met behulp van modules zoals Matplotlib, Plotly of Bokeh. Net als bij de uitvoer worden deze visualisaties inline weergegeven naast de code die ze genereert.

* **Multimedia:** Omdat Jupyter notebook is gebouwd op de webtechnologie, kan het alle soorten multimedia weergeven die op een webpagina worden ondersteund. U ze in een notitieblok opnemen als HTML-elementen, of `IPython.display` u ze programmatisch genereren met behulp van de module.

* **Gegevens:** Gegevens uit Azure Cosmos-containers en resultaten van de query's kunnen programmatisch worden geïmporteerd in een Jupyter-notitieblok. Bijvoorbeeld door code op te nemen in het notitieblok om de gegevens op te vragen met behulp van een van de Cosmos DB API's of native ingebouwde Apache Spark.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen om aan de slag te gaan met ingebouwde Jupyter-notitieblokken in Azure Cosmos DB:

* [Notitieblokken inschakelen in een Azure Cosmos-account](enable-notebooks.md)
* [Notitieblokfuncties en -opdrachten gebruiken](use-notebook-features-and-commands.md)



