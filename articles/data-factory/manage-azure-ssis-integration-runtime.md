---
title: De Azure-SSIS Integration runtime opnieuw configureren
description: Meer informatie over het opnieuw configureren van een Azure SSIS Integration runtime in Azure Data Factory nadat u deze al hebt ingericht.
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
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931401"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>De Azure-SSIS Integration runtime opnieuw configureren
In dit artikel wordt beschreven hoe u een bestaande Azure SSIS Integration runtime opnieuw kunt configureren. Zie [een Azure SSIS Integration runtime maken](create-azure-ssis-integration-runtime.md)voor het maken van een Azure-SSIS Integration runtime (IR) in azure Data Factory.  

## <a name="data-factory-ui"></a>Gebruikersinterface van Data Factory 
U kunt Data Factory gebruikers interface gebruiken om een Azure-SSIS IR te stoppen, te bewerken/opnieuw te configureren of te verwijderen. 

1. Ga in de **Data Factory-gebruikers interface**naar het tabblad **bewerken** . Als u Data Factory gebruikers interface wilt starten, klikt u op de start pagina van uw data factory op **& controle** maken.
2. Klik in het linkerdeel venster op **verbindingen**.
3. Schakel in het rechterdeel venster over naar de **Integration Runtimes**. 
4. U kunt de knoppen in de kolom acties gebruiken om de Integration runtime te **stoppen**, te **bewerken**of te **verwijderen** . Met de **code** knop in de kolom **acties** kunt u de JSON-definitie weer geven die aan de Integration runtime is gekoppeld.  
    
    ![Acties voor Azure SSIS IR](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Een Azure-SSIS IR opnieuw configureren
1. Stop de Integration runtime door te klikken op **stoppen** in de kolom **acties** . Als u de lijst weergave wilt vernieuwen, klikt u op **vernieuwen** op de werk balk. Nadat de IR is gestopt, ziet u dat u met de eerste actie de IR kunt starten. 

    ![Acties voor Azure SSIS IR-na het stoppen](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Bewerk/Wijzig IR door te klikken op de knop **bewerken** in de kolom **acties** . Wijzig in het venster **Integration runtime Setup** de instellingen (bijvoorbeeld grootte van het knoop punt, het aantal knoop punten of het maximum aantal parallelle uitvoeringen per knoop punt). 
3. Als u de IR opnieuw wilt opstarten, klikt u op de knop **Start** in de kolom **acties** .     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nadat u een exemplaar van Azure SSIS Integration runtime hebt ingericht en gestart, kunt u dit opnieuw configureren door een reeks `Stop` - `Set` - `Start` Power shell-cmdlets uit te voeren. Het volgende Power shell-script wijzigt bijvoorbeeld het aantal knoop punten dat voor het Azure-SSIS Integration runtime-exemplaar is toegewezen aan vijf.

### <a name="reconfigure-an-azure-ssis-ir"></a>Een Azure-SSIS IR opnieuw configureren

1. Stop eerst de Azure SSIS Integration runtime met behulp van de cmdlet [Stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) . Met deze opdracht worden alle knoop punten vrijgegeven en wordt de facturering gestopt.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Configureer vervolgens de Azure-SSIS IR opnieuw met behulp van de cmdlet [set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) . Met de volgende voorbeeld opdracht wordt een Azure-SSIS-integratie-runtime geschaald naar vijf knoop punten.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Start vervolgens de Azure SSIS Integration runtime met behulp van de cmdlet [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) . Met deze opdracht worden alle knoop punten toegewezen voor het uitvoeren van SSIS-pakketten.   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Een Azure-SSIS IR verwijderen
1. Eerst een lijst van alle bestaande Azure SSIS-IRs onder uw data factory.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Stop vervolgens alle bestaande Azure SSIS IRs in uw data factory.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Verwijder vervolgens alle bestaande Azure SSIS-IRs in uw data factory een voor een.

    ```powershell
    Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Verwijder ten slotte de data factory.

    ```powershell
    Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Als u een nieuwe resource groep hebt gemaakt, verwijdert u de resource groep.

    ```powershell
    Remove-AzResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Volgende stappen
Zie de volgende onderwerpen voor meer informatie over Azure-SSIS runtime: 

- [Azure-SSIS Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dit artikel bevat conceptuele informatie over integratie-Runtimes in het algemeen, met inbegrip van de Azure-SSIS IR. 
- [Zelfstudie: SSIS-pakketten implementeren in Azure](tutorial-create-azure-ssis-runtime-portal.md). Dit artikel biedt stapsgewijze instructies voor het maken van een Azure-SSIS IR en maakt gebruik van een Azure SQL-database voor het hosten van de SSIS-catalogus. 
- [Procedure: Een Azure SSIS Integration Runtime maken](create-azure-ssis-integration-runtime.md). In dit artikel wordt de zelf studie uitgebreid en vindt u instructies voor het gebruik van Azure SQL Database beheerde instantie en het toevoegen van de IR aan een virtueel netwerk. 
- [Een Azure-SSIS-integratieruntime toevoegen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md). Dit artikel bevat algemene informatie over het toevoegen van een Azure-SSIS IR aan een virtueel netwerk van Azure. Er wordt ook beschreven hoe u Azure Portal gebruikt om een virtueel netwerk te configureren voor het deelnemen van Azure-SSIS IR aan het virtueel netwerk. 
- [Een Azure-SSIS IR controleren](monitor-integration-runtime.md#azure-ssis-integration-runtime). In dit artikel leest u hoe u informatie over een Azure-SSIS IR ophaalt. Daarnaast bevat het artikel beschrijvingen van statuswaarden die worden gebruikt in de geretourneerde informatie. 
 
