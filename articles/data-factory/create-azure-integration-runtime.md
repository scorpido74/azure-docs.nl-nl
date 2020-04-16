---
title: Runtime voor Azure-integratie maken in Azure Data Factory
description: Meer informatie over het maken van Azure-integratieruntime in Azure Data Factory, die wordt gebruikt voor het kopiëren van gegevens en het verzenden van transformatieactiviteiten.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/13/2020
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: e32530ece3626807b199850a2b4af5461ff51cde
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414064"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Runtime voor Azure-integratie maken en configureren
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

De Integration Runtime (IR) is de rekeninfrastructuur die wordt gebruikt door Azure Data Factory om gegevensintegratiemogelijkheden te bieden voor verschillende netwerkomgevingen. Zie [Runtime integratie voor](concepts-integration-runtime.md)meer informatie over IR.

Azure IR biedt een volledig beheerde compute om gegevensverplaatsings- en verzendgegevenstransformatieactiviteiten native uit te voeren naar compute services zoals HDInsight. Het wordt gehost in de Azure-omgeving en ondersteunt het verbinden met bronnen in een openbare netwerkomgeving met openbare toegankelijke eindpunten.

In dit document wordt uitgelegd hoe u Azure Integration Runtime maken en configureren. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Standaard Azure IR
Standaard heeft elke gegevensfabriek een Azure IR in de back-end die bewerkingen ondersteunt op cloudgegevensopslag en compute services in het openbare netwerk. De locatie van die Azure IR wordt automatisch opgelost. Als de eigenschap **connectVia** niet is opgegeven in de definitie van gekoppelde service, wordt de standaard Azure IR gebruikt. U hoeft alleen expliciet een Azure IR te maken wanneer u de locatie van de IR expliciet wilt definiëren of als u de activiteitsuitvoeringen op verschillende IRs voor beheerdoeleinden virtueel wilt groeperen. 

## <a name="create-azure-ir"></a>Azure IR maken

Als u een Azure IR wilt maken en instellen, u de volgende procedures gebruiken.

### <a name="create-an-azure-ir-via-azure-powershell"></a>Een Azure IR maken via Azure PowerShell
Integratie Runtime kan worden gemaakt met behulp van de **Set-AzDataFactoryV2IntegrationRuntime** PowerShell cmdlet. Als u een Azure IR wilt maken, geeft u de naam, locatie en typt u de opdracht op. Hier vindt u een voorbeeldopdracht voor het maken van een Azure IR met locatie ingesteld op 'West-Europa':

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Voor Azure IR moet het type worden ingesteld op **Beheerd**. U hoeft geen rekengegevens op te geven omdat deze volledig elastisch worden beheerd in de cloud. Geef rekengegevens op, zoals knooppuntgrootte en knooppunttelling wanneer u Azure-SSIS IR wilt maken. Zie [Azure-SSIS IR maken en configureren](create-azure-ssis-integration-runtime.md)voor meer informatie.

U een bestaande Azure IR configureren om de locatie te wijzigen met de cmdlet Set-AzDataFactoryV2IntegrationRuntime PowerShell. Zie [Inleiding tot de runtime van integratie voor](concepts-integration-runtime.md)meer informatie over de locatie van een Azure IR.

### <a name="create-an-azure-ir-via-azure-data-factory-ui"></a>Een Azure IR maken via de gebruikersinterface van Azure Data Factory
Gebruik de volgende stappen om een Azure IR te maken met azure data factory-gebruikersinterface.

1. Selecteer op de pagina **Laten beginnen** van de gebruikersinterface van Azure Data Factory het tabblad **Auteur** in het linkerdeelvenster.

   ![De knop Auteur van startpagina](media/doc-common-process/get-started-page-author-button.png)

1. Selecteer **Verbindingen** onder aan het linkerdeelvenster en selecteer **De werktijd Integratie** in het venster **Verbindingen.** Selecteer **+Nieuw**.

   ![Een Integration Runtime maken](media/create-azure-integration-runtime/new-integration-runtime.png)

1. Selecteer **azure, zelfgehoste**en selecteer **Doorgaan**op de pagina **Runtime-installatie van integratie** . 

1. Selecteer op de volgende pagina **Azure** om een Azure IR te maken en selecteer **Doorgaan**.
   ![Een Integration Runtime maken](media/create-azure-integration-runtime/new-azure-ir.png)

1. Voer een naam in voor uw Azure IR en selecteer **Maken**.
   ![Een Azure IR maken](media/create-azure-integration-runtime/create-azure-ir.png)

1. Je ziet een pop-upmelding wanneer de creatie is voltooid. Controleer op de pagina **Runtimes integratie** of u de nieuw gemaakte IR in de lijst ziet.

## <a name="use-azure-ir"></a>Azure IR gebruiken

Zodra een Azure IR is gemaakt, u ernaar verwijzen in uw Linked Service-definitie. Hieronder vindt u een voorbeeld van hoe u verwijzen naar de hierboven gemaakte Azure Integration Runtime van een Azure Storage Linked Service:

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen over het maken van andere typen integratie-runtimes:

- [Runtime voor zelfgehoste integratie maken](create-self-hosted-integration-runtime.md)
- [Azure-SSIS-integratie-runtime maken](create-azure-ssis-integration-runtime.md)
 
