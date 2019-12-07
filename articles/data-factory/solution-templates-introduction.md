---
title: Overzicht van sjablonen
description: Meer informatie over het gebruik van een vooraf gedefinieerde sjabloon om snel aan de slag te gaan met Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/04/2019
author: djpmsft
ms.author: daperlov
manager: craigg
ms.openlocfilehash: 24da737ea017319eafeb014c2695a147909093f1
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74890630"
---
# <a name="templates"></a>Sjablonen

Sjablonen zijn vooraf gedefinieerde Azure Data Factory pijp lijnen waarmee u snel aan de slag kunt met Data Factory. Sjablonen zijn handig wanneer u geen ervaring hebt met Data Factory en snel aan de slag wilt. Deze sjablonen verminderen de ontwikkelings tijd voor het samen stellen van gegevens integratie projecten, waardoor de productiviteit van ontwikkel aars wordt verbeterd.

## <a name="create-data-factory-pipelines-from-templates"></a>Data Factory pijp lijnen maken op basis van sjablonen

U kunt op de volgende twee manieren aan de slag gaan met het maken van een Data Factory pijplijn op basis van een sjabloon:

1.  Selecteer **pijp lijn maken op basis van de sjabloon** op de pagina overzicht om de sjabloon galerie te openen.

    ![Open de sjabloon galerie op de pagina overzicht](media/solution-templates-introduction/templates-intro-image1.png)

1.  Selecteer **+** op het tabblad auteur in resource Explorer en klik vervolgens op **pijp lijn van sjabloon** om de sjabloon galerie te openen.

    ![Open de sjabloon galerie op het tabblad Auteur](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Sjabloon galerie

![De sjabloon galerie](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Data Factory sjablonen uit het vak

Data Factory gebruikt Azure Resource Manager sjablonen voor het opslaan van data factory pijplijn sjablonen. U kunt alle Resource Manager-sjablonen bekijken, samen met het manifest bestand dat wordt gebruikt voor out-of-Box Data Factory sjablonen, in de [officiële Azure Data Factory github opslag plaats](https://github.com/Azure/Azure-DataFactory/tree/master/templates). De vooraf gedefinieerde sjablonen die door micro soft worden geleverd, zijn niet beperkt tot de volgende items:

-   Sjablonen kopiëren:

    -   [Bulksgewijs kopiëren uit data base](solution-template-bulk-copy-with-control-table.md)
    
    -   [Nieuwe bestanden kopiëren op LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Meerdere bestands containers kopiëren tussen archieven op basis van bestanden](solution-template-copy-files-multiple-containers.md)

    -   [Bestanden verplaatsen](solution-template-move-files.md)

    -   [Delta kopie van data base](solution-template-delta-copy-with-control-table.md)

    -   Kopiëren van \<bron\> naar \<doel\>

        -   [Van Amazon S3 naar Azure Data Lake Store gen 2](solution-template-migration-s3-azure.md)

        -   Van Google Big query tot Azure Data Lake Store gen 2

        -   Van HDF tot Azure Data Lake Store gen 2

        -   Van Netezza tot Azure Data Lake Store gen 1

        -   Van SQL Server on-premises naar Azure SQL Database

        -   Van SQL Server on-premises naar Azure SQL Data Warehouse

        -   Van Oracle on premises tot Azure SQL Data Warehouse

-   SSIS-sjablonen

    -   Azure-SSIS Integration Runtime plannen voor het uitvoeren van SSIS-pakketten

-   Sjablonen transformeren

    -   [ETL met Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Mijn sjablonen

U kunt een pijp lijn ook als sjabloon opslaan door **Opslaan als sjabloon** te selecteren op het tabblad pijp lijn.

![Een pijp lijn opslaan als een sjabloon](media/solution-templates-introduction/templates-intro-image4.png)

U kunt pijp lijnen weer geven die zijn opgeslagen als sjablonen in het gedeelte **Mijn sjablonen** van de sjabloon galerie. U kunt ze ook bekijken in de sectie **sjablonen** in de resource Explorer.

![Mijn sjablonen](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Als u de functie Mijn sjablonen wilt gebruiken, moet u GIT-integratie inschakelen. Zowel Azure DevOps GIT als GitHub worden ondersteund.
