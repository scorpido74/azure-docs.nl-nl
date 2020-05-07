---
title: 'Quick Start: een werk ruimte maken'
description: Maak een Azure Synapse Analytics-werk ruimte door de stappen in deze hand leiding te volgen.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 49bd251b7f76a844a0d3fd1b1a09f7e15c5e7ddb
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792204"
---
# <a name="quickstart-create-an-azure-synapse-analytics-workspace-preview"></a>Snelstartgids: een Azure Synapse Analytics-werk ruimte maken (preview)

In deze Quick Start worden de stappen beschreven voor het maken van een Azure Synapse-werk ruimte met behulp van de Azure Portal.

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account aan voordat u begint](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

- [Azure Data Lake Storage Gen2 Storage-account](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure Portal](https://portal.azure.com/)

## <a name="create-an-azure-synapse-workspace-using-the-azure-portal"></a>Een Azure Synapse-werk ruimte maken met behulp van de Azure Portal

1. Voer in het zoek venster Microsoft Azure de **Synapse-werk ruimte** in en selecteer vervolgens deze service.
![Azure Portal zoek balk met Azure Synapse-werk ruimten die zijn](media/quickstart-create-synapse-workspace/workspace-search.png)getypt in..
2. Klik op de pagina **Synapse-werk ruimten** op **+ toevoegen**.
![Opdracht voor het maken van een nieuwe Azure Synapse-werk ruimte gemarkeerd. ](media/quickstart-create-synapse-workspace/create-workspace-02.png).
3. Vul het **Azure Synapse-werkruimte** formulier in met de volgende informatie:

    | Instelling | Voorgestelde waarde | Beschrijving |
    | :------ | :-------------- | :---------- |
    | **Abonnement** | *Uw abonnement* | Zie [Abonnementen](https://account.windowsazure.com/Subscriptions) voor meer informatie over uw abonnementen. |
    | **Resourcegroep** | *Een resource groep* | Zie [Naming conventions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (Naamgevingsconventies) voor geldige namen van resourcegroepen. |
    | **Werkruimtenaam** | mysampleworkspace | Hiermee geeft u de naam van de werk ruimte, die ook wordt gebruikt voor verbindings eindpunten.|
    | **Regio** | VS - oost 2 | Hiermee geeft u de locatie van de werk ruimte op.|
    | **Data Lake Storage Gen2** | Account`storage account name` </br> Bestands systeem:`root file system to use` | Hiermee geeft u de naam van het opslag account van ADLS Gen2 moet worden gebruikt als primaire opslag en het bestands systeem dat moet worden gebruikt.|
    ||||

    ![Werk ruimte-inrichtings stroom-tabblad basis beginselen.](media/quickstart-create-synapse-workspace/create-workspace-03.png).

    Het opslag account kan worden geselecteerd vanuit:
    - Een lijst met ADLS Gen2 accounts die beschikbaar zijn in uw abonnement
    - Hand matig ingevoerd met de account naam

    > [!IMPORTANT]
    > De Azure Synapse-werk ruimte moet kunnen lezen en schrijven naar het geselecteerde ADLS Gen2-account. Bovendien moet u voor elk opslag account dat u als primair opslag account koppelt, een **hiërarchische naam ruimte** hebben ingeschakeld bij het maken van het opslag account.
    >
    > Onder de ADLS Gen2 selectie velden ziet u een opmerking dat de beheerde identiteit van de werk ruimte wordt toegewezen aan de **Storaqe BLOB data contributor** -rol op het geselecteerde data Lake Storage Gen2 bestands systeem waarmee het volledige toegang wordt verleend.

4. Beschrijving Wijzig het tabblad **beveiliging en netwerk standaard instellingen** :
5. Beschrijving Voeg labels toe op het tabblad **labels** .
6. Op het tabblad **samen vatting** worden de benodigde validaties uitgevoerd om er zeker van te zijn dat de werk ruimte kan worden gemaakt. Zodra de validatie is gelukt, drukt u op werk ruimte inrichten stroom **maken** ![-](media/quickstart-create-synapse-workspace/create-workspace-05.png)bevestigings tabblad..
7. Nadat het inrichtings proces voor de resource is voltooid, ziet u een vermelding voor de gemaakte werk ruimte in de lijst met Synapse-werk ruimten. ![Lijst met Synapse-werk ruimten waarin de zojuist ingerichte werk ruimte wordt weer gegeven.](media/quickstart-create-synapse-workspace/create-workspace-07.png).

## <a name="clean-up-resources"></a>Resources opschonen

Volg de onderstaande stappen om de Azure Synapse-werk ruimte te verwijderen.
> [!WARNING]
> Als u een Azure Synapse-werk ruimte verwijdert, worden de analyse-engines en de gegevens opgeslagen in de data base van de Inge sloten SQL-groepen en de meta gegevens van de werk ruimte verwijderd. Het is niet meer mogelijk om verbinding te maken met de SQL-eind punten, Apache Spark-eind punten. Alle code artefacten worden verwijderd (query's, notitie blokken, taak definities en pijp lijnen).
>
> Het verwijderen van de werk ruimte heeft **geen** invloed op de gegevens in de data Lake Store Gen2 die aan de werk ruimte zijn gekoppeld.

Als u de Azure Synapse-werk ruimte wilt verwijderen, voert u de volgende stappen uit:

1. Ga naar de Azure Synapse-werk ruimte die u wilt verwijderen.
1. Druk op **verwijderen** op de opdracht balk.
 ![Overzicht van de Azure Synapse-werk ruimte-opdracht verwijderen gemarkeerd.](media/quickstart-create-synapse-workspace/create-workspace-10.png)
1. Bevestig het verwijderen en druk op de knop **verwijderen** .
 ![Overzicht van de Azure Synapse-werk ruimte-bevestigings dialoogvenster voor werk ruimte verwijderen.](media/quickstart-create-synapse-workspace/create-workspace-11.png)
1. Wanneer het proces is voltooid, wordt de Azure Synapse-werk ruimte niet meer weer gegeven in de lijst met werk ruimten.

## <a name="next-steps"></a>Volgende stappen

Vervolgens kunt u [SQL-groepen maken](quickstart-create-sql-pool.md) of [Apache Spark groepen maken](quickstart-create-apache-spark-pool.md) om uw gegevens te analyseren en te verkennen.
