---
title: Gegevens kopiëren van Blob Storage naar SQL Database - Azure
description: In deze zelfstudie ziet u hoe u Activiteit kopiëren in een Azure Data Factory-pijplijn gebruiken om gegevens van Blob-opslag naar SQL-database te kopiëren.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: cc2f0a513219a671dd8a75ee00af4fc9d4c6a68a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979731"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Zelfstudie: Gegevens kopiëren van Blob-opslag naar SQL-database met gegevensfabriek
> [!div class="op_single_selector"]
> * [Overzicht en vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [De wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [Powershell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-sjabloon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u de [zelfstudie over kopieeractiviteiten](../quickstart-create-data-factory-dot-net.md).

In deze zelfstudie maakt u een gegevensfabriek met een pijplijn om gegevens van Blob-opslag naar SQL-database te kopiëren.

Met Copy Activity wordt de gegevensverplaatsing in Azure Data Factory uitgevoerd. De activiteit wordt mogelijk gemaakt door een wereldwijd beschikbare service waarmee gegevens veilig, betrouwbaar en schaalbaar kunnen worden gekopieerd tussen verschillende gegevensarchieven. Zie [Activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) voor meer informatie over Copy Activity.  

> [!NOTE]
> Zie het artikel Inleiding tot Azure [Data Factory](data-factory-introduction.md) voor een gedetailleerd overzicht van de gegevensfabriekservice.
>
>

## <a name="prerequisites-for-the-tutorial"></a>Vereisten voor de zelfstudie
Voordat u met deze zelfstudie begint, moet u aan de volgende vereisten voldoen:

* **Azure-abonnement**.  Als u geen abonnement hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie het [gratis proefversie-artikel](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.
* **Azure Storage-account**. U gebruikt de blob-opslag als **brongegevensarchief** in deze zelfstudie. Als u geen Azure Storage-account hebt, raadpleegt u het artikel [Een opslagaccount maken](../../storage/common/storage-account-create.md) voor de stappen voor het maken van een account.
* **Azure SQL-database**. U gebruikt een Azure SQL-database als **doelgegevensarchief** in deze zelfstudie. Als u geen Azure SQL-database hebt die u in de zelfstudie gebruiken, raadpleegt u [Hoe u een Azure SQL-database maakt en configureert](../../sql-database/sql-database-get-started.md) om er een te maken.
* **SQL Server 2012/2014 of Visual Studio 2013**. U gebruikt SQL Server Management Studio of Visual Studio om een voorbeelddatabase te maken en de resultaatgegevens in de database weer te geven.  

## <a name="collect-blob-storage-account-name-and-key"></a>Blob-opslagaccountnaam en -sleutel verzamelen
U hebt de accountnaam en de accountsleutel van uw Azure-opslagaccount nodig om deze zelfstudie uit te kunnen. Noteer **accountnaam** en **accountsleutel** voor uw Azure-opslagaccount.

1. Log in bij de [Azure-portal](https://portal.azure.com/).
2. Klik op **Alle services** in het linkermenu en selecteer **Opslagaccounts**.

    ![Bladeren - Opslagaccounts](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. Selecteer in het blade **Opslagaccounts** het **Azure-opslagaccount** dat u in deze zelfstudie wilt gebruiken.
4. Selecteer **koppeling Toegangssleutels** onder **INSTELLINGEN**.
5. Klik **op de** knop Kopiëren (afbeelding) naast het tekstvak **Accountnaam opslag** en sla het ergens op (bijvoorbeeld: in een tekstbestand).
6. Herhaal de vorige stap om de toets te kopiëren of op te **schrijven1**.

    ![Opslagtoegangssleutel](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Sluit alle messen door op **X**te klikken.

## <a name="collect-sql-server-database-user-names"></a>SQL-server, database, gebruikersnamen verzamelen
U hebt de namen van Azure SQL-server, database en gebruiker nodig om deze zelfstudie uit te kunnen. Noteer namen van **server,** **database**en **gebruiker** voor uw Azure SQL-database.

1. Klik in de **Azure-portal**op **Alle services** aan de linkerkant en selecteer **SQL-databases**.
2. Selecteer in het **SQL-databasesblad**de **database** die u in deze zelfstudie wilt gebruiken. Noteer de **databasenaam**.  
3. Klik in het **SQL-databaseblad** op **Eigenschappen** onder **INSTELLINGEN**.
4. Noteer de waarden voor **SERVERNAAM** en **SERVER ADMIN LOGIN**.
5. Sluit alle messen door op **X**te klikken.

## <a name="allow-azure-services-to-access-sql-server"></a>Azure-services toegang geven tot SQL-server
Controleer of de instelling **Toegang tot Azure-services** **ingeschakeld** is voor uw Azure SQL-server, zodat de Service Gegevensfabriek toegang heeft tot uw Azure SQL-server. Voer de volgende stappen uit om dit te controleren en de instelling in te schakelen:

1. Klik op **De hub Alle services** aan de linkerkant en klik op **SQL-servers**.
2. Selecteer uw server en klik op **Firewall** onder **INSTELLINGEN**.
3. In de blade **Firewallinstellingen**schakelt u **Toegang tot Azure-services toestaan****in**.
4. Sluit alle messen door op **X**te klikken.

## <a name="prepare-blob-storage-and-sql-database"></a>Blob-opslag en SQL-database voorbereiden
Bereid nu uw Azure blob-opslag en Azure SQL-database voor op de zelfstudie door de volgende stappen uit te voeren:  

1. Start Kladblok. Kopieer de volgende tekst en sla deze op als **emp.txt** naar de map **C:\ADFGetStarted** op uw harde schijf.

    ```
    John, Doe
    Jane, Doe
    ```
2. Gebruik hulpprogramma's zoals [Azure Storage Explorer](https://storageexplorer.com/) om de **adftutorial-container** te maken en het **emp.txt-bestand** naar de container te uploaden.

3. Gebruik het volgende SQL-script om de tabel **emp** te maken in uw Azure SQL Database.  

    ```SQL
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    **Als SQL Server 2012/2014 op uw computer is geïnstalleerd:** volg instructies van [Azure SQL Database beheren met SQL Server Management Studio](../../sql-database/sql-database-manage-azure-ssms.md) om verbinding te maken met uw Azure SQL-server en voer het SQL-script uit.

    Als de client geen toegang heeft tot de Azure SQL-server, moet u de firewall configureren voor uw Azure SQL-server zodat toegang vanaf uw apparaat (IP-adres) wordt toegestaan. Raadpleeg [dit artikel](../../sql-database/sql-database-configure-firewall-settings.md) voor stappen waarmee u uw firewall kunt configureren voor uw Azure SQL-server.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken
U hebt de voorwaarden voltooid. U een gegevensfabriek maken op een van de volgende manieren. Klik op een van de opties in de vervolgkeuzelijst bovenaan of op de volgende koppelingen om de zelfstudie uit te voeren.     

* [De wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [Powershell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Azure Resource Manager-sjabloon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> In de gegevenspijplijn in deze zelfstudie worden gegevens van een brongegevensarchief gekopieerd naar een doelgegevensarchief. Er worden geen invoergegevens mee getransformeerd in uitvoergegevens. Zie [Zelfstudie: uw eerste pijplijn maken om gegevens te transformeren met een Hadoop-cluster](data-factory-build-your-first-pipeline.md) voor meer informatie over het transformeren van gegevens met Azure Data Factory.
>
> U kunt twee activiteiten koppelen (de ene activiteit na de andere laten uitvoeren) door de uitvoergegevensset van één activiteit in te stellen als invoergegevensset voor een andere activiteit. Zie [Planning en uitvoering in Data Factory](data-factory-scheduling-and-execution.md) voor gedetailleerde informatie.
