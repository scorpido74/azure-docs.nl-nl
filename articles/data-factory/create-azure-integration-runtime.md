---
title: Azure Integration runtime in Azure Data Factory maken
description: Meer informatie over het maken van Azure Integration runtime in Azure Data Factory, die wordt gebruikt voor het kopiëren van gegevens en trans formatie-activiteiten.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 1a9a88f975ca10a63741818dcb88874a8f1bf436
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926388"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Azure Integration Runtime maken en configureren
De Integration Runtime (IR) is de reken infrastructuur die door Azure Data Factory wordt gebruikt om mogelijkheden voor gegevens integratie in verschillende netwerk omgevingen te bieden. Zie [Integration runtime](concepts-integration-runtime.md)(Engelstalig) voor meer informatie over IR.

Azure IR biedt een volledig beheerde Compute voor systeem eigen gegevens verplaatsing en activiteiten voor gegevens transformatie voor het berekenen van services zoals HDInsight. Deze wordt gehost in de Azure-omgeving en ondersteunt het verbinden met bronnen in open bare netwerk omgeving met open bare eind punten.

In dit document wordt uitgelegd hoe u Azure Integration Runtime kunt maken en configureren. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Standaard Azure IR
Standaard bevat elke data factory een Azure IR in de back-end die bewerkingen ondersteunt in de Cloud gegevensopslag en Compute-Services in het open bare netwerk. De locatie van de Azure IR automatisch oplossen. Als de eigenschap **connectVia** niet is opgegeven in de definitie van de gekoppelde service, wordt de standaard Azure IR gebruikt. U hoeft alleen expliciet een Azure IR te maken wanneer u de locatie van de IR expliciet wilt definiëren, of als u de uitvoering van de activiteit op verschillende IRs wilt groeperen voor beheer doeleinden. 

## <a name="create-azure-ir"></a>Azure IR maken
Integration Runtime kunnen worden gemaakt met de Power shell **-cmdlet Set-AzDataFactoryV2IntegrationRuntime** . Als u een Azure IR wilt maken, geeft u de naam, de locatie en het type van de opdracht op. Hier volgt een voor beeld van een opdracht voor het maken van een Azure IR waarbij de locatie is ingesteld op Europa-west:

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Voor Azure IR moet het type worden ingesteld op **beheerd**. U hoeft geen reken gegevens op te geven omdat deze elastisch volledig worden beheerd in de Cloud. Geef reken gegevens, zoals de knooppunt grootte en het aantal knoop punten, op wanneer u Azure-SSIS IR wilt maken. Zie [Azure-SSIS IR maken en configureren](create-azure-ssis-integration-runtime.md)voor meer informatie.

U kunt een bestaande Azure IR configureren om de locatie ervan te wijzigen met behulp van de set-AzDataFactoryV2IntegrationRuntime Power shell-cmdlet. Zie [Inleiding tot Integration runtime](concepts-integration-runtime.md)voor meer informatie over de locatie van een Azure IR.

## <a name="use-azure-ir"></a>Azure IR gebruiken

Zodra een Azure IR is gemaakt, kunt u ernaar verwijzen in de definitie van de gekoppelde service. Hieronder ziet u een voor beeld van hoe u kunt verwijzen naar het Azure Integration Runtime dat hierboven is gemaakt op basis van een Azure Storage gekoppelde service:  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=...",
          "type": "SecureString"
        }
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over het maken van andere typen Integration Runtimes:

- [Zelf-hostende integratie-runtime maken](create-self-hosted-integration-runtime.md)
- [Azure-SSIS-integratie-runtime maken](create-azure-ssis-integration-runtime.md)
 
