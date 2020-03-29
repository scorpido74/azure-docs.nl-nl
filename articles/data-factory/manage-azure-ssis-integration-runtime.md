---
title: De Azure-SSIS-integratieruntime opnieuw configureren
description: Meer informatie over het opnieuw configureren van een runtime van Azure-SSIS-integratie in Azure Data Factory nadat u deze al hebt ingericht.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: fbac52d65433f2137d565ca60fcf754e49199640
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931401"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>De Azure-SSIS-integratieruntime opnieuw configureren
In dit artikel wordt beschreven hoe u een bestaande runtime voor Azure-SSIS-integratie opnieuw configureert. Zie [Runtime (Azure-SSIS-integratie) maken](create-azure-ssis-integration-runtime.md)als u een runtime (Azure-SSIS-integratie) wilt maken in Azure-SSIS-integratie.  

## <a name="data-factory-ui"></a>Gebruikersinterface van Data Factory 
U de gebruikersinterface van Gegevensfabriek gebruiken om een Azure-SSIS IR te stoppen, bewerken/opnieuw configureren of verwijderen. 

1. Ga in de **gebruikersinterface van de gegevensfabriek**over naar het tabblad **Bewerken.** Als u de gebruikersinterface van Gegevensfabriek wilt starten, klikt u op **Auteur & Monitor** op de startpagina van uw gegevensfabriek.
2. Klik in het linkerdeelvenster op **Verbindingen**.
3. Schakel in het rechterdeelvenster over naar de **runtimes integratie**. 
4. U knoppen in de kolom Acties gebruiken om de runtime van de integratie te **stoppen,** **bewerken**of **verwijderen.** Met de knop **Code** in de kolom **Acties** u de JSON-definitie weergeven die is gekoppeld aan de runtime van de integratie.  
    
    ![Acties voor Azure SSIS IR](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Een Azure-SSIS IR opnieuw configureren
1. Stop de uitvoeringstijd voor integratie door op **Stoppen** te klikken in de kolom **Acties.** Als u de lijstweergave wilt vernieuwen, klikt u op **Vernieuwen** op de werkbalk. Nadat de IR is gestopt, ziet u dat u met de eerste actie de IR starten. 

    ![Acties voor Azure SSIS IR - na gestopt](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Bewerk/configureer IR door op de knop **Bewerken** te klikken in de kolom **Handelingen.** Wijzig in het venster Instellingen voor **uitvoeringstijd integratie** (bijvoorbeeld grootte van het knooppunt, aantal knooppunten of maximale parallelle uitvoeringen per knooppunt). 
3. Als u de IR opnieuw wilt starten, klikt u op De knop **Start** in de kolom **Acties.**     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nadat u een exemplaar van azure-SSIS-integratieruntime hebt in- en `Stop`  -  `Set`  -  `Start` gestart, u deze opnieuw configureren door een reeks PowerShell-cmdlets achter elkaar uit te voeren. Met het volgende PowerShell-script wordt bijvoorbeeld het aantal knooppunten dat is toegewezen voor de runtime-instantie Azure-SSIS-integratie gewijzigd in vijf.

### <a name="reconfigure-an-azure-ssis-ir"></a>Een Azure-SSIS IR opnieuw configureren

1. Stop eerst de runtime van Azure-SSIS-integratie met de [cmdlet Stop-AzDataFactoryV2IntegrationRuntime.](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) Met deze opdracht worden alle knooppunten vrijgegeven en wordt facturering gestopt.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Configureer vervolgens de Azure-SSIS IR met de cmdlet [Set-AzDataFactoryV2IntegrationRuntime.](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) Met de volgende voorbeeldopdracht wordt de runtime van Azure-SSIS-integratie geschaald naar vijf knooppunten.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Start vervolgens de runtime van Azure-SSIS-integratie met de [runtime Start-AzDataFactoryV2IntegrationRuntime.](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) Met deze opdracht worden alle knooppunten toegewezen voor het uitvoeren van SSIS-pakketten.   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Een Azure-SSIS IR verwijderen
1. Vermeld eerst alle bestaande Azure SSIS IRs onder uw gegevensfabriek.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Stop vervolgens alle bestaande Azure SSIS IRs in uw gegevensfabriek.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Verwijder vervolgens alle bestaande Azure SSIS IRs in uw gegevensfabriek één voor één.

    ```powershell
    Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Verwijder ten slotte uw gegevensfabriek.

    ```powershell
    Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Als u een nieuwe resourcegroep hebt gemaakt, verwijdert u de brongroep.

    ```powershell
    Remove-AzResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Volgende stappen
Zie de volgende onderwerpen voor meer informatie over de runtime van Azure-SSIS: 

- [Runtime azure-SSIS-integratie](concepts-integration-runtime.md#azure-ssis-integration-runtime). In dit artikel vindt u conceptuele informatie over de runtimes voor integratie in het algemeen, waaronder azure-SSIS IR. 
- [Zelfstudie: SSIS-pakketten implementeren in Azure](tutorial-create-azure-ssis-runtime-portal.md). Dit artikel biedt stapsgewijze instructies voor het maken van een Azure-SSIS IR en maakt gebruik van een Azure SQL-database voor het hosten van de SSIS-catalogus. 
- [Procedure: Een Azure SSIS Integration Runtime maken](create-azure-ssis-integration-runtime.md). In dit artikel wordt de zelfstudie uitgebreid en worden instructies gegeven over het gebruik van Azure SQL Database Managed Instance en het samenvoegen van de IR bij een virtueel netwerk. 
- [Een Azure-SSIS-integratieruntime toevoegen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md). Dit artikel bevat algemene informatie over het toevoegen van een Azure-SSIS IR aan een virtueel netwerk van Azure. Er wordt ook beschreven hoe u Azure Portal gebruikt om een virtueel netwerk te configureren voor het deelnemen van Azure-SSIS IR aan het virtueel netwerk. 
- [Een Azure-SSIS IR controleren](monitor-integration-runtime.md#azure-ssis-integration-runtime). In dit artikel leest u hoe u informatie over een Azure-SSIS IR ophaalt. Daarnaast bevat het artikel beschrijvingen van statuswaarden die worden gebruikt in de geretourneerde informatie. 
 
