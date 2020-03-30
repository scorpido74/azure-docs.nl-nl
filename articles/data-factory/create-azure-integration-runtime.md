---
title: Runtime voor Azure-integratie maken in Azure Data Factory
description: Meer informatie over het maken van Azure-integratieruntime in Azure Data Factory, die wordt gebruikt voor het kopiëren van gegevens en het verzenden van transformatieactiviteiten.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 87633abaaae1f6034709c6e552be6647533115ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260759"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Runtime voor Azure-integratie maken en configureren
De Integration Runtime (IR) is de rekeninfrastructuur die wordt gebruikt door Azure Data Factory om gegevensintegratiemogelijkheden te bieden voor verschillende netwerkomgevingen. Zie [Runtime integratie voor](concepts-integration-runtime.md)meer informatie over IR.

Azure IR biedt een volledig beheerde compute om gegevensverplaatsings- en verzendgegevenstransformatieactiviteiten native uit te voeren naar compute services zoals HDInsight. Het wordt gehost in de Azure-omgeving en ondersteunt het verbinden met bronnen in een openbare netwerkomgeving met openbare toegankelijke eindpunten.

In dit document wordt uitgelegd hoe u Azure Integration Runtime maken en configureren. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Standaard Azure IR
Standaard heeft elke gegevensfabriek een Azure IR in de back-end die bewerkingen ondersteunt op cloudgegevensopslag en compute services in het openbare netwerk. De locatie van die Azure IR wordt automatisch opgelost. Als de eigenschap **connectVia** niet is opgegeven in de definitie van gekoppelde service, wordt de standaard Azure IR gebruikt. U hoeft alleen expliciet een Azure IR te maken wanneer u de locatie van de IR expliciet wilt definiëren of als u de activiteitsuitvoeringen op verschillende IRs voor beheerdoeleinden virtueel wilt groeperen. 

## <a name="create-azure-ir"></a>Azure IR maken
Integratie Runtime kan worden gemaakt met behulp van de **Set-AzDataFactoryV2IntegrationRuntime** PowerShell cmdlet. Als u een Azure IR wilt maken, geeft u de naam, locatie en het type op aan de opdracht. Hier vindt u een voorbeeldopdracht voor het maken van een Azure IR met locatie ingesteld op 'West-Europa':

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Voor Azure IR moet het type worden ingesteld op **Beheerd**. U hoeft geen rekengegevens op te geven omdat deze volledig elastisch worden beheerd in de cloud. Geef rekengegevens op, zoals knooppuntgrootte en knooppunttelling wanneer u Azure-SSIS IR wilt maken. Zie [Azure-SSIS IR maken en configureren](create-azure-ssis-integration-runtime.md)voor meer informatie.

U een bestaande Azure IR configureren om de locatie te wijzigen met de cmdlet Set-AzDataFactoryV2IntegrationRuntime PowerShell. Zie [Inleiding tot de runtime van integratie voor](concepts-integration-runtime.md)meer informatie over de locatie van een Azure IR.

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
 
