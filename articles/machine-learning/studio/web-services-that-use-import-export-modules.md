---
title: Gegevens importeren/exporteren in webservices
titleSuffix: ML Studio (classic) - Azure
description: Informatie over het gebruik van de modules importeren en exporteren van gegevens te verzenden en ontvangen van gegevens vanuit een webservice.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 144a3bc0d9e0499a238e4033d37d5e4d3fa61e05
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204052"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>Azure Machine Learning Studio (klassieke) webservices implementeren die gebruikmaken van modules voor het importeren en exporteren van gegevens

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Wanneer u een Voorspellend experiment maakt, toevoegen u gewoonlijk een web-invoer en uitvoer. Wanneer u het experiment implementeert, kunnen gebruikers verzonden en ontvangen van gegevens van de webservice via de invoer en uitvoer. Voor sommige toepassingen van een gebruiker gegevens uit een gegevensfeed beschikbaar of al bevinden zich in een externe gegevensbron, zoals Azure Blob-opslag. In dergelijke gevallen hoeven ze niet lezen en schrijven van gegevens met behulp van web service invoer en uitvoer. Ze kunnen in plaats daarvan de Batch Execution Service (BES) gebruiken om gegevens te lezen uit de gegevensbron met behulp van een module gegevens importeren en de scoring resultaten schrijven naar een andere locatie met behulp van een module gegevens exporteren.

De gegevens importeren en exporteren van gegevens-modules, kunnen lezen en schrijven naar verschillende gegevens locaties, zoals een via HTTP, een Hive-Query, een Azure SQL-database, Azure Table storage, Azure Blob-opslag, een Gegevensfeed-URL opgeven of een on-premises SQL-database.

In dit onderwerp wordt gebruikgemaakt van de "voorbeeld 5: trainen, testen, evalueren voor binaire classificatie: volwassenen gegevensset" voorbeeld en wordt ervan uitgegaan dat de gegevensset is al geladen in een Azure SQL-tabel met de naam censusdata.

## <a name="create-the-training-experiment"></a>De trainingsexperiment maken
Wanneer u opent de "voorbeeld 5: evalueren van de trein, Test, voor binaire classificatie: volwassenen gegevensset ' voorbeeld van de voorbeeldgegevensset van volwassenen telling inkomsten binaire classificatie wordt gebruikt. En het experiment in het canvas ziet eruit als in de volgende afbeelding:

![Eerste configuratie van het experiment.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

De gegevens lezen uit de Azure SQL-tabel:

1. De gegevensset-module verwijderen.
2. Typ in het zoekvak onderdelen importeren.
3. Voeg in de lijst met resultaten een module *import gegevens* toe aan het canvas voor het experiment.
4. Verbinding maken met de uitvoer van de module *import data* de invoer van de module *clean Missing Data* .
5. Selecteer in het deel venster Eigenschappen **Azure SQL database** in de vervolg keuzelijst **gegevens bron** .
6. In de velden **database server naam**, **database naam**, **gebruikers naam**en **wacht woord** voert u de juiste informatie in voor uw data base.
7. Voer de volgende query in het queryveld Database.

     Selecteer [leeftijd]

        [workclass],
        [fnlwgt],
        [education],
        [education-num],
        [marital-status],
        [occupation],
        [relationship],
        [race],
        [sex],
        [capital-gain],
        [capital-loss],
        [hours-per-week],
        [native-country],
        [income]
     van dbo.censusdata;
8. Klik onder aan het canvas op **uitvoeren**.

## <a name="create-the-predictive-experiment"></a>De Voorspellend experiment maken
De volgende instellen van de Voorspellend experiment van waaruit u uw webservice implementeert.

1. Klik aan de onderkant van het canvas op **webservice instellen** en selecteer **Predictive web service (aanbevolen)** .
2. Verwijder de webservices- *invoer* en *webservice-uitvoer modules* uit het voorspellende experiment.
3. Typ in het zoekvak onderdelen exporteren.
4. Voeg in de lijst met resultaten een module *export gegevens* toe aan het canvas van het experiment.
5. Verbinding maken met de uitvoer van de module *score model* de invoer van de module *gegevens exporteren* .
6. Selecteer in het deel venster Eigenschappen **Azure SQL database** in de vervolg keuzelijst gegevens bestemming.
7. Voer in de velden **database server naam**, **database naam**, **Server gebruikers account naam**en **wacht woord voor Server gebruikers account** de juiste informatie in voor uw data base.
8. In het veld **door komma's gescheiden lijst met kolommen die moeten worden opgeslagen** , typt u labels met scores.
9. Typ dbo in het **veld naam van gegevens tabel**. ScoredLabels. Als de tabel niet bestaat, wordt deze gemaakt wanneer het experiment wordt uitgevoerd of de webservice wordt aangeroepen.
10. Typ ScoredLabels in het veld **door komma's gescheiden lijst met DataTable-kolommen** .

Wanneer u een toepassing die de laatste webservice roept schrijft, kunt u een andere invoer voor de query of doeltabel opgeven tijdens de uitvoering. Als u deze invoer en uitvoer wilt configureren, gebruikt u de functie webservice-para meters om de eigenschap *gegevens bron* van de gegevens module *importeren* en de gegevens doel eigenschap *export gegevens* modus in te stellen.  Zie voor meer informatie over para meters voor de webservice de [para meters](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) voor de Azure machine learning Studio-webservice in de blog Cortana Intelligence en machine learning.

Parameters van de webservice voor de import-query en de doeltabel configureren:

1. Klik in het deel venster Eigenschappen voor de module *gegevens importeren* op het pictogram in de rechter bovenhoek van het veld **database query** en selecteer **set as web service para meter**.
2. Klik in het deel venster met eigenschappen voor de module *gegevens exporteren* op het pictogram rechtsboven in het veld **naam van gegevens tabel** en selecteer **set as web service para meter**.
3. Klik onder aan het deel venster Eigenschappen van module *gegevens exporteren* in het gedeelte **para meters** van de webservice op database query en wijzig de naam van de query.
4. Klik op **naam van gegevens tabel** en wijzig deze **tabel**.

Wanneer u klaar bent, is uw experiment ziet die vergelijkbaar is met de volgende afbeelding:

![Laatste uiterlijk van het experiment.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

U kunt nu het experiment implementeren als een webservice.

## <a name="deploy-the-web-service"></a>De webservice implementeren
U kunt implementeren op een klassieke of een nieuwe webservice betreft.

### <a name="deploy-a-classic-web-service"></a>Een klassieke webservice implementeren
Als een klassieke webservice implementeren en maken van een toepassing gebruikt:

1. Klik op uitvoeren aan de onderkant van het experimentcanvas.
2. Wanneer de uitvoering is voltooid, klikt u op **webservice implementeren** en selecteert u **Web-service implementeren [klassiek]** .
3. Zoek op het dashboard van de webservice, uw API-sleutel. Kopiëren en opslaan voor later gebruik.
4. Klik in de tabel **standaard eindpunt** op de koppeling voor **batch uitvoering** om de API Help-pagina te openen.
5. Maak in Visual Studio C# een console toepassing: **Nieuw** > **project** > **Visual C#**  > **Windows Classic Desktop** > console- **app (.NET Framework)** .
6. Op de API Help-pagina, onder aan de pagina, gaat u naar de sectie **voorbeeld code** .
7. Kopieer en plak de C# voorbeeldcode in het bestand Program.cs en verwijder alle verwijzingen naar de blob-opslag.
8. Werk de waarde van de variabele *apiKey* bij met de API-sleutel die u eerder hebt opgeslagen.
9. Zoek de aanvraag declaratie en werk de waarden bij van de para meters van de webservice die worden door gegeven aan de modules *gegevens importeren* en *exporteren* . In dit geval u gebruikt u de oorspronkelijke query, maar de naam van een nieuwe tabel te definiëren.

        var request = new BatchExecutionRequest()
        {
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Voer de toepassing uit.

Na voltooiing van de uitvoering wordt een nieuwe tabel toegevoegd aan de database met de scoring resultaten.

### <a name="deploy-a-new-web-service"></a>Een nieuwe webservice implementeren

> [!NOTE]
> Voor het implementeren van een nieuwe webservice moet u voldoende machtigingen hebben in het abonnement waarvoor u de webservice implementeert. Zie [Manage a web service using the Azure machine learning Web Services portal](manage-new-webservice.md)(Engelstalig) voor meer informatie.

Als een nieuwe webservice implementeren en maken van een toepassing gebruikt:

1. Klik onder aan het canvas op **uitvoeren**.
2. Wanneer de uitvoering is voltooid, klikt u op **webservice implementeren** en selecteert u **Web service implementeren [New]** .
3. Voer op de pagina experiment implementeren een naam in voor uw webservice, selecteer een prijs plan en klik vervolgens op **implementeren**.
4. Klik op de pagina **Quick** start op **verbruikt**.
5. Klik in de sectie **voorbeeld code** op **batch**.
6. Maak in Visual Studio C# een console toepassing: **Nieuw** > **project** > **Visual C#**  > **Windows Classic Desktop** > console- **app (.NET Framework)** .
7. Kopieer en plak de C# voorbeeldcode in het bestand Program.cs.
8. Werk de waarde van de variabele *apiKey* bij met de **primaire sleutel** in de sectie **basis informatie over verbruik** .
9. Zoek de *scoreRequest* -declaratie en werk de waarden bij van de webservice-para meters die worden door gegeven aan de modules *gegevens importeren* en *exporteren* . In dit geval u gebruikt u de oorspronkelijke query, maar de naam van een nieuwe tabel te definiëren.

        var scoreRequest = new
        {
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };
10. Voer de toepassing uit.

