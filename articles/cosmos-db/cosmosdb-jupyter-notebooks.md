---
title: Inleiding tot de ingebouwde Jupyter-notebook ondersteuning in Azure Cosmos DB (preview-versie)
description: Meer informatie over hoe u de ingebouwde ondersteuning voor Jupyter-notebooks in Azure Cosmos DB kunt gebruiken om query's interactief uit te voeren.
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/22/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 5b320485001d6cbc457d39ef193ed8c57f7161df
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760280"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Ingebouwde Jupyter-notebooks ondersteunen in Azure Cosmos DB (preview-versie)

Jupyter notebook is een open-source webtoepassing waarmee u documenten kunt maken en delen met Live code, vergelijkingen, visualisaties en tekst. Azure Cosmos DB ondersteunt ingebouwde Jupyter-notebooks voor alle Api's, zoals Cassandra, MongoDB, SQL, Gremlin en Table. Met de ingebouwde ondersteuning voor notebooks voor alle Azure Cosmos DB Api's en gegevens modellen kunt u interactief query's uitvoeren. De Jupyter-notebooks worden uitgevoerd binnen de Azure Cosmos-accounts en ze stellen ontwikkel aars in staat om gegevens te verkennen, gegevens te reinigen, gegevens trans formaties, numerieke simulaties, statistische model lering, gegevens visualisatie en machine learning.

![Visualisaties van Jupyter-notebooks in Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png)

De Jupyter-notebooks ondersteunen Magic-functies die de mogelijkheden van de kernel uitbreiden door extra opdrachten te ondersteunen. Cosmos Magic is een opdracht die de mogelijkheden van de python-kernel in Jupyter notebook uitbreidt, zodat u Azure Cosmos SQL API-query's kunt uitvoeren naast Apache Spark. U kunt python-en SQL-API-query's eenvoudig combi neren om gegevens op te vragen en te visualiseren door gebruik te maken van uitgebreide visualisatie bibliotheken die zijn geïntegreerd met render-opdrachten.
Azure Portal Jupyter Notebook-ervaring in systeem eigen integreert in azure Cosmos-accounts, zoals wordt weer gegeven in de volgende afbeelding:

![Ondersteuning voor Jupyter-notebooks in Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png)

## <a name="benefits-of-jupyter-notebooks"></a>Voor delen van Jupyter-notebooks

Jupyter-notebooks werden oorspronkelijk ontwikkeld voor data Science-toepassingen die zijn geschreven in Python, R. Ze kunnen echter op verschillende manieren worden gebruikt voor verschillende soorten projecten, zoals:

* ***gegevens visualisaties:** Jupyter-notebooks bieden u de mogelijkheid om gegevens in de vorm van een gedeeld notitie blok te visualiseren waarmee een aantal gegevens sets als een afbeelding wordt weer gegeven. Met Jupyter notebook kunt u visualisaties ontwerpen, ze delen en interactieve wijzigingen in de gedeelde code en gegevensset toestaan.

* **Code delen:** Services zoals GitHub bieden manieren om code te delen, maar ze zijn grotendeels niet-interactief. Met een Jupyter-notebook kunt u code weer geven, deze uitvoeren en de resultaten rechtstreeks in het Azure Portal weer geven.

* **Live interacties met code:** Jupyter-notebook code is dynamisch; het kan worden bewerkt en stapsgewijs in realtime worden uitgevoerd. Notebooks kunnen ook gebruikers besturings elementen (bijvoorbeeld schuif regelaars of tekst invoer velden) insluiten die kunnen worden gebruikt als invoer bronnen voor code, demo's of concepten (POCs).

* **Documentatie voor code voorbeelden en resultaten van het verkennen van gegevens:** Als u een stukje code hebt en u regel-voor-line wilt uitleggen hoe het werkt in Azure Cosmos DB, kunt u de informatie in realtime-uitvoer insluiten in een Jupyter Notebook. De code blijft volledig functioneel. U kunt gelijktijdig interactief samen met de documentatie toevoegen.

* **Cosmos Magic-opdrachten:** In Jupyter-notebooks kunt u aangepaste Magic-opdrachten voor Azure Cosmos DB gebruiken om interactief computer gemak te maken. Bijvoorbeeld:%% SQL Magic waarmee een Cosmos-container kan worden doorzocht met behulp van SQL API rechtstreeks in een notitie blok.

* **Alle in één locatie omgeving:** Jupyter-notebooks combi neren code, RTF-tekst, afbeeldingen, Video's, animaties, wiskundige vergelijkingen, grafieken, kaarten, interactieve cijfers, widgets en grafische gebruikers interfaces tot één document.

## <a name="components-of-a-jupyter-notebook"></a>Onderdelen van een Jupyter-notebook

Jupyter-notebooks kunnen diverse soorten onderdelen bevatten, die zijn ingedeeld in discrete blokken:

* **Tekst en HTML:** Tekst zonder opmaak of tekst die in de syntaxis van de prijs opmaakt om HTML te genereren, kan op elk gewenst moment in het document worden ingevoegd. CSS-opmaak kan ook inline worden opgenomen of worden toegevoegd aan de sjabloon die wordt gebruikt voor het genereren van het notitie blok.

* **Code en uitvoer:** Jupyter-notebooks ondersteunen python-code. De resultaten van de uitgevoerde code worden direct na de code blokken weer gegeven en de code blokken kunnen in elke gewenste volg orde meerdere keren worden uitgevoerd.

* **Visualisaties:** Afbeeldingen en grafieken kunnen worden gegenereerd op basis van de code, met behulp van modules als matplotlib, plots of bokeh. Net als bij de uitvoer worden deze visualisaties weer gegeven naast de code die ze genereert.

* **Multi media:** Omdat Jupyter notebook is gebaseerd op de webtechnologie, kunnen alle typen multi media worden weer gegeven die op een webpagina worden ondersteund. U kunt deze toevoegen aan een notitie blok als HTML-elementen of u kunt deze via een programma genereren met behulp van de module `IPython.display`.

* **Gegevens:** Gegevens uit Azure Cosmos-containers en resultaten van de query's kunnen programmatisch in een Jupyter-notebook worden geïmporteerd. Bijvoorbeeld door code in het notitie blok op te nemen om de gegevens op te vragen met behulp van een van de Cosmos DB Api's of de systeem eigen ingebouwde Apache Spark.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen om aan de slag te gaan met ingebouwde Jupyter-notebooks in Azure Cosmos DB:

* [Notitie blokken in een Azure Cosmos-account inschakelen](enable-notebooks.md)
* [Functies en opdrachten van notitie blok gebruiken](use-notebook-features-and-commands.md)



