---
title: Overzicht van sjablonen
description: Meer informatie over het gebruik van een vooraf gedefinieerde sjabloon om snel aan de slag te gaan met Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 01/04/2019
ms.openlocfilehash: 14f1d3b58996d438279bfa2a234c754a6d840c79
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414738"
---
# <a name="templates"></a>Sjablonen
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Sjablonen zijn vooraf gedefinieerde Azure Data Factory-pijplijnen waarmee u snel aan de slag met Data Factory. Sjablonen zijn handig als u nieuw bent in Data Factory en snel aan de slag wilt. Deze sjablonen verkorten de ontwikkeltijd voor het bouwen van data-integratieprojecten en verbeteren zo de productiviteit van ontwikkelaars.

## <a name="create-data-factory-pipelines-from-templates"></a>Data Factory-pijplijnen maken op basis van sjablonen

U op de volgende twee manieren aan de slag met het maken van een datafabriekpijplijn op basis van een sjabloon:

1.  Selecteer **Pijplijn maken in de sjabloon** op de pagina Overzicht om de sjabloongalerie te openen.

    ![De sjabloongalerie openen op de pagina Overzicht](media/solution-templates-introduction/templates-intro-image1.png)

1.  Selecteer op het tabblad Auteur **+** in Resource Explorer de optie **vervolgens Pijplijn van sjabloon** om de sjabloongalerie te openen.

    ![De sjabloongalerie openen op het tabblad Auteur](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Sjabloongalerie

![De sjabloongalerie](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Out of the box Gegevensfabrieksjablonen

Data Factory gebruikt Azure Resource Manager-sjablonen voor het opslaan van pijplijnsjablonen voor gegevensfabrieken. U alle Resource Manager-sjablonen, samen met het manifestbestand dat wordt gebruikt voor kant-en-klare Gegevensfabrieksjablonen, bekijken in de [officiële Azure Data Factory GitHub-repo.](https://github.com/Azure/Azure-DataFactory/tree/master/templates) De vooraf gedefinieerde sjablonen van Microsoft bevatten, maar zijn niet beperkt tot de volgende items:

-   Sjablonen kopiëren:

    -   [Bulkkopie uit database](solution-template-bulk-copy-with-control-table.md)
    
    -   [Nieuwe bestanden kopiëren op basis van LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Meerdere bestandscontainers kopiëren tussen bestanden](solution-template-copy-files-multiple-containers.md)

    -   [Bestanden verplaatsen](solution-template-move-files.md)

    -   [Delta-kopie uit database](solution-template-delta-copy-with-control-table.md)

    -   Kopiëren \<van\> \<bron naar bestemming\>

        -   [Van Amazon S3 tot Azure Data Lake Store Gen 2](solution-template-migration-s3-azure.md)

        -   Van Google Big Query naar Azure Data Lake Store Gen 2

        -   Van HDF naar Azure Data Lake Store Gen 2

        -   Van Netezza naar Azure Data Lake Store Gen 1

        -   Van SQL Server on premises naar Azure SQL Database

        -   Van SQL Server on premises naar Azure SQL Data Warehouse

        -   Van Oracle on-premises naar Azure SQL Data Warehouse

-   SSIS-sjablonen

    -   Runtime azure-SSIS-integratie plannen om SSIS-pakketten uit te voeren

-   Sjablonen transformeren

    -   [ETL met Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Mijn sjablonen

U een pijplijn ook opslaan als sjabloon door **Opslaan als sjabloon** te selecteren op het tabblad Pijplijn.

![Een pijplijn opslaan als sjabloon](media/solution-templates-introduction/templates-intro-image4.png)

U pijplijnen weergeven die zijn opgeslagen als sjablonen in de sectie **Mijn sjablonen** van de sjabloongalerie. U ze ook zien in de sectie **Sjablonen** in de Bronverkenner.

![Mijn sjablonen](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Als u de functie Mijn sjablonen wilt gebruiken, moet u GIT-integratie inschakelen. Zowel Azure DevOps GIT als GitHub worden ondersteund.
