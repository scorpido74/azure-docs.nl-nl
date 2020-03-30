---
title: Gegevens importeren/exporteren in webservices
titleSuffix: ML Studio (classic) - Azure
description: Meer informatie over het gebruik van de modules Gegevens importeren en gegevens exporteren om gegevens van een webservice te verzenden en te ontvangen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204052"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>Azure Machine Learning Studio (klassieke) webservices implementeren die gebruikmaken van modules voor gegevensimporteren en gegevensexporteren

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Wanneer u een voorspellend experiment maakt, voegt u doorgaans een webservice-invoer en -uitvoer toe. Wanneer u het experiment implementeert, kunnen consumenten gegevens van de webservice verzenden en ontvangen via de ingangen en uitvoer. Voor sommige toepassingen zijn de gegevens van een consument mogelijk beschikbaar in een gegevensfeed of bevinden ze zich al in een externe gegevensbron, zoals Azure Blob-opslag. In deze gevallen hebben ze geen lees- en schrijfgegevens nodig met behulp van webservice-ingangen en uitvoer. In plaats daarvan kunnen ze de Batch Execution Service (BES) gebruiken om gegevens uit de gegevensbron te lezen met behulp van een module Importgegevens en de scoreresultaten naar een andere gegevenslocatie te schrijven met behulp van een exportgegevensmodule.

De importgegevens- en exportgegevensmodules kunnen lezen en schrijven naar verschillende gegevenslocaties, zoals een web-URL via HTTP, een Hive Query, een Azure SQL-database, Azure Table-opslag, Azure Blob-opslag, een gegevensfeed bieden of een on-premises SQL-database.

In dit onderwerp wordt het voorbeeld "Voorbeeld 5: trainen, testen, evalueren voor binaire classificatie: gegevensset voor volwassenen" gebruikt en wordt ervan uitgegaan dat de gegevensset al is geladen in een Azure SQL-tabel met de naam censusdata.

## <a name="create-the-training-experiment"></a>Het trainingsexperiment maken
Wanneer u de steekproef "Voorbeeld 5: Trein, Test, Evalueren voor binaire classificatie: volwassen gegevensset" opent, wordt de gegevensset "Adult Census Income Binary Classification" gebruikt. En het experiment in het canvas ziet er hetzelfde uit als de volgende afbeelding:

![Eerste configuratie van het experiment.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Ga als volgt te werk om de gegevens uit de Azure SQL-tabel te lezen:

1. Verwijder de gegevenssetmodule.
2. Typ importeren in het zoekvak componenten.
3. Voeg in de lijst met resultaten een module *Gegevens importeren* toe aan het experimentcanvas.
4. Verbind de uitvoer van de module *Gegevens importeren* de invoer van de module *Ontbrekende gegevens opschonen.*
5. Selecteer In het deelvenster Eigenschappen de optie **Azure SQL Database** in de vervolgkeuzelijst **Gegevensbron.**
6. Voer in de **velden Databaseservernaam**, **Databasenaam**, **Gebruikersnaam**en **Wachtwoord** de juiste gegevens voor uw database in.
7. Voer in het veld Databasequery de volgende query in.

     selecteer [leeftijd],

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
8. Klik onder aan het experimentcanvas op **Uitvoeren**.

## <a name="create-the-predictive-experiment"></a>Het voorspellende experiment maken
Vervolgens stelt u het voorspellende experiment in van waaruit u uw webservice implementeert.

1. Klik onder aan het experimentcanvas op **Webservice instellen** en selecteer **Predictive Web Service [Aanbevolen]**.
2. Verwijder de modules *WebService-invoer* en *Webservice-uitvoer* uit het voorspellende experiment.
3. Typ exporteren in het zoekvak componenten.
4. Voeg in de lijst met resultaten een module *Gegevens exporteren* toe aan het experimentcanvas.
5. Verbind de uitvoer van de module *Scoremodel* de invoer van de module *Gegevens exporteren.*
6. Selecteer Azure **SQL Database** in het deelvenster Eigenschappen van eigenschappen in de vervolgkeuzelijst gegevensbestemming.
7. Voer in de velden **Databaseservernaam**, **Databasenaam**, **Servergebruikersnaam**en **Server-gebruikersaccountwachtwoord** de juiste gegevens voor uw database in.
8. Typ Labels in **de lijst met gescheiden kolommen met komma's.**
9. Typ dbo in het veld Naam van de **tabel Gegevens.** ScoredLabels. Als de tabel niet bestaat, wordt deze gemaakt wanneer het experiment wordt uitgevoerd of de webservice wordt aangeroepen.
10. Typ ScoredLabels in de lijst met gescheiden gegevenstabelkolommen in de lijst **met gegevenstabelkolommen.**

Wanneer u een toepassing schrijft die de uiteindelijke webservice aanroept, u bij het uitvoeren van de invoerquery of de doeltabel een andere invoerquery of doeltabel opgeven. Als u deze ingangen en uitvoer wilt configureren, gebruikt u de functie Webserviceparameters om de eigenschap *Gegevensbron* *importeren* en de eigenschap *Gegevensstatus exporteren.*  Zie het item Azure Machine [Learning Studio Web Service Parameters](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) op het webserviceblog Cortana Intelligence en Machine Learning voor meer informatie over webserviceparameters.

Ga als u de parameters van de webservice voor de importquery en de doeltabel wilt configureren:

1. Klik in het eigenschappenvenster voor de module *Gegevens importeren* op het pictogram rechtsboven in het veld **Databasequery** en selecteer **Instellen als parameter voor webservice**.
2. Klik in het deelvenster Eigenschappen voor de module *Gegevens exporteren* op het pictogram rechtsboven in het **veld Gegevenstabelnaam** en selecteer **Instellen als parameter voor webservice**.
3. Klik onder aan het *eigenschappenvenster van* de module Exporteren in de sectie **Webserviceparameters** op Databasequery en wijzig de naam Query.
4. Klik **op de naam van de tabel Gegevens** en wijzig de naam **tabel**.

Wanneer u klaar bent, moet uw experiment er hetzelfde uitzien als de volgende afbeelding:

![Laatste blik van experiment.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Nu u het experiment implementeren als een webservice.

## <a name="deploy-the-web-service"></a>De webservice implementeren
U implementeren op een klassieke of nieuwe webservice.

### <a name="deploy-a-classic-web-service"></a>Een klassieke webservice implementeren
Ga als een klassieke webservice implementeren en maak een toepassing om deze te gebruiken:

1. Klik onder aan het experimentcanvas op Uitvoeren.
2. Wanneer de run is voltooid, klikt u op **Webservice implementeren** en selecteert u **Webservice implementeren [Klassiek].**
3. Zoek in het dashboard van de webservice uw API-sleutel. Kopieer en sla het op om het later te gebruiken.
4. Klik in de **tabel Standaardeindpunt** op de koppeling **Batch-uitvoering** om de HELP-pagina voor API te openen.
5. Maak in Visual Studio een C#-consoletoepassing: **Nieuwe** > **Project** > **Visual C#** > **Windows Classic Desktop** > **Console App (.NET Framework).**
6. Zoek op de helppagina van api's de sectie **Voorbeeldcode** onder aan de pagina.
7. Kopieer en plak de C#-voorbeeldcode in uw Program.cs bestand en verwijder alle verwijzingen naar de blobopslag.
8. Werk de waarde van de *apiKey-variabele* bij met de API-sleutel die eerder is opgeslagen.
9. Zoek de aanvraagdeclaratie en werk de waarden van WebService Parameters bij die worden doorgegeven aan de modules *Gegevens importeren* en *Exporteren.* In dit geval gebruikt u de oorspronkelijke query, maar definieert u een nieuwe tabelnaam.

        var request = new BatchExecutionRequest()
        {
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Voer de toepassing uit.

Na voltooiing van de run wordt een nieuwe tabel toegevoegd aan de database met de scoreresultaten.

### <a name="deploy-a-new-web-service"></a>Een nieuwe webservice implementeren

> [!NOTE]
> Als u een nieuwe webservice wilt implementeren, moet u over voldoende machtigingen beschikken in het abonnement waarvoor u de webservice implementeert. Zie [Een webservice beheren met de Azure Machine Learning Web Services-portal](manage-new-webservice.md)voor meer informatie.

Ga als nieuwe webservice implementeren en maak een toepassing om deze te gebruiken:

1. Klik onder aan het experimentcanvas op **Uitvoeren**.
2. Wanneer de run is voltooid, klikt u op **Webservice implementeren** en selecteert u **Webservice implementeren [Nieuw].**
3. Voer op de pagina Experiment implementeren een naam voor uw webservice in en selecteer een prijsplan en klik vervolgens op **Implementeren**.
4. Klik op de pagina **Snelstart** op **Consumeren**.
5. Klik in de sectie **Voorbeeldcode** op **Batch**.
6. Maak in Visual Studio een C#-consoletoepassing: **Nieuwe** > **Project** > **Visual C#** > **Windows Classic Desktop** > **Console App (.NET Framework).**
7. Kopieer en plak de C#-voorbeeldcode in uw Program.cs bestand.
8. Werk de waarde van de *apiKey-variabele* bij met de **primaire sleutel** in de sectie **Basisverbruiksinformatie.**
9. Zoek de *scoreVraagdeclaratie* en werk de waarden van WebService Parameters bij die worden doorgegeven aan de modules *Gegevens importeren* en *Exporteren.* In dit geval gebruikt u de oorspronkelijke query, maar definieert u een nieuwe tabelnaam.

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

