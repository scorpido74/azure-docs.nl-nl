---
title: 'Quickstart: een werkruimte maken'
description: Maak een Azure Synapse Analytics-werkruimte door de stappen in deze handleiding uit te voeren.
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: df7753983cea2ca2cc285f04cd32da6ed065f415
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656172"
---
# <a name="quickstart-create-an-azure-synapse-analytics-workspace-preview"></a>Quickstart: Een Azure Synapse Analytics-werkruimte maken (preview)

In deze quickstart wordt stapsgewijs beschreven hoe u een Azure Synapse-werkruimte maakt met Azure Portal.

Als u geen Azure-abonnement hebt, [maakt u een gratis account voordat u begint](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

- [Azure Data Lake Storage Gen2-opslagaccount](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="register-azure-synapse-resource-provider"></a>Azure Synapse-resourceprovider registreren
Registreer de Azure Synapse-resourceprovider, als u dit nog niet hebt gedaan. Als u een resourceprovider registreert, wordt uw abonnement zo geconfigureerd dat dit kan worden gebruikt met de resourceprovider. Zie [Azure-resourceproviders en -typen](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types) voor het registreren van de Azure Synapse-resourceprovider. Kies bij de registratie *Microsoft.Synapse* in de lijst met resourceproviders.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/)

## <a name="create-an-azure-synapse-workspace-using-the-azure-portal"></a>Een Azure Synapse-werkruimte maken met Azure Portal

1. Voer in het Microsoft Azure-zoekvenster **Synapse-werkruimte** in en selecteer vervolgens deze service.
![Azure Portal-zoekbalk waarin Azure Synapse-werkruimten zijn ingevoerd.](media/quickstart-create-synapse-workspace/workspace-search.png)
2. Klik op de pagina **Synapse-werkruimten** op **+Toevoegen**.
![De opdracht voor het maken van een nieuwe Azure Synapse-werkruimte is gemarkeerd.](media/quickstart-create-synapse-workspace/create-workspace-02.png)
3. Vul het formulier van de **Azure Synapse-werkruimte** in met de volgende gegevens:

    | Instelling | Voorgestelde waarde | Beschrijving |
    | :------ | :-------------- | :---------- |
    | **Abonnement** | *Uw abonnement* | Zie [Abonnementen](https://account.windowsazure.com/Subscriptions) voor meer informatie over uw abonnementen. |
    | **Resourcegroep** | *Een resourcegroep* | Zie [Naming conventions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (Naamgevingsconventies) voor geldige resourcegroepnamen. |
    | **Werkruimtenaam** | mysampleworkspace | Hiermee geeft u de naam op van de werkruimte die ook wordt gebruikt voor verbindingseindpunten.|
    | **Regio** | VS - oost 2 | Geeft de locatie van de werkruimte aan.|
    | **Data Lake Storage Gen2** | Account: `storage account name` </br> Bestandssysteem: `root file system to use` | Hiermee geeft u de naam op van het ADLS Gen2-opslagaccount dat moet worden gebruikt als primaire opslag en het bestandssysteem dat moet worden gebruikt.|
    ||||

    ![Stroom voor het inrichten van de werkruimte: het tabblad Basisgegevens.](media/quickstart-create-synapse-workspace/create-workspace-03.png)

    Het opslagaccount kan worden geselecteerd in:
    - Een lijst met ADLS Gen2-accounts die beschikbaar zijn in uw abonnement
    - Handmatig ingevoerd met de accountnaam

    > [!IMPORTANT]
    > De Azure Synapse-werkruimte moet kunnen lezen uit en schrijven naar het geselecteerde ADLS Gen2-account. Daarnaast moet u voor elk opslagaccount dat u als primair opslagaccount koppelt **hiërarchische naamruimte** hebben ingeschakeld bij het maken van het opslagaccount.
    >
    > Onder de ADLS Gen2-selectievelden ziet u een opmerking waarin wordt vermeld dat aan de beheerde identiteit van de werkruimte de rol **Storaqe Blob Data Contributor** voor het geselecteerde Data Lake Storage Gen2-bestandssysteem wordt toegewezen, zodat deze volledige toegang heeft.

4. (Optioneel) Wijzig de standaardinstellingen op het tabblad **Beveiliging en netwerken**:
5. (Optioneel) Voeg labels toe op het tabblad **Tags**.
6. Op het tabblad **Overzicht** worden de benodigde validaties uitgevoerd zodat de werkruimte kan worden gemaakt. Wanneer de validatie is voltooid, selecteert u **Maken** ![Stroom voor het inrichten van de werkruimte: bevestiging.](media/quickstart-create-synapse-workspace/create-workspace-05.png)
7. Wanneer de resource eenmaal is ingericht, ziet u een vermelding voor de gemaakte werkruimte in de lijst met Synapse-werkruimten. ![Overzicht van Synapse-werkruimten waarin de zojuist ingerichte werkruimte wordt weergegeven.](media/quickstart-create-synapse-workspace/create-workspace-07.png)

## <a name="clean-up-resources"></a>Resources opschonen

Voer de onderstaande stappen uit om de Azure Synapse-werkruimte te verwijderen.
> [!WARNING]
> Als u een Azure Synapse-werkruimte verwijdert, worden de analyse-engines en de gegevens die zijn opgeslagen in de database van de SQL-pools die het bevat en de metagegevens van de werkruimte verwijderd. Er kan geen verbinding meer worden gemaakt met de SQL-eindpunten en Apache Spark-eindpunten. Alle codeartefacten worden verwijderd (query's, notebooks, taakdefinities en pijplijnen).
>
> Als u de werkruimte verwijdert, heeft dit **geen** invloed op de gegevens in het Data Lake Storage Gen2-account dat aan de werkruimte is gekoppeld.

Als u de Azure Synapse-werkruimte wilt verwijderen, voert u de volgende stappen uit:

1. Ga naar de Azure Synapse-werkruimte die u wilt verwijderen.
1. Selecteer in de opdrachtbalk de optie **Verwijderen**.
 ![Overzicht van de Azure Synapse-werkruimte: de opdracht voor verwijderen is gemarkeerd.](media/quickstart-create-synapse-workspace/create-workspace-10.png)
1. Bevestig dat u de werkruimte wilt verwijderen en selecteer de knop **Verwijderen**.
 ![Overzicht van de Azure Synapse-werkruimte: dialoogvenster voor het bevestigen van de verwijdering van de werkruimte.](media/quickstart-create-synapse-workspace/create-workspace-11.png)
1. Wanneer de werkruimte is verwijderd, wordt de Azure Synapse-werkruimte niet meer weergegeven in de lijst met werkruimten.

## <a name="next-steps"></a>Volgende stappen

Vervolgens kunt u [SQL-pools maken](quickstart-create-sql-pool-studio.md) of [Apache Spark-pools maken](quickstart-create-apache-spark-pool-studio.md) zodat u uw gegevens kunt gaan analyseren en verkennen.
