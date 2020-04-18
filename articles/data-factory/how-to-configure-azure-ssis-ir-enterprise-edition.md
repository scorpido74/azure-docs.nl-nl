---
title: Enterprise Edition inrichten voor de runtime azure-SSIS-integratie
description: In dit artikel worden de functies van Enterprise Edition beschreven voor de runtime Azure-SSIS-integratie en hoe u deze indient
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: a0e35a309fd30a36263b498b6a8e74a312004a99
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606162"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Enterprise Edition inrichten voor de runtime azure-SSIS-integratie

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met de Enterprise Edition van de Runtime Azure-SSIS-integratie u de volgende geavanceerde en premium functies gebruiken:
-   CDC-componenten (Data Capture) wijzigen
-   Oracle-, Teradata- en SAP BW-connectoren
-   Sql Server Analysis Services (SSAS) en Azure Analysis Services (AAS) connectors en transformaties
-   Fuzzy Grouping en Fuzzy Lookup transformaties
-   Term Extraction and Term Lookup transformations

Voor sommige van deze functies moet u extra onderdelen installeren om de Azure-SSIS IR aan te passen. Zie [Aangepaste installatie voor de runtime van Azure-SSIS-integratie voor](how-to-configure-azure-ssis-ir-custom-setup.md)meer informatie over het installeren van extra componenten.

## <a name="enterprise-features"></a>Enterprise-functies

| **Bedrijfsfuncties** | **Beschrijvingen** |
|---|---|
| CDC-componenten | De CDC Source, Control Task en Splitter Transformation zijn vooraf geïnstalleerd op de Azure-SSIS IR Enterprise Edition. Om verbinding te maken met Oracle, moet u de CDC Designer and Service ook op een andere computer installeren. |
| Oracle-connectors | De Oracle Connection Manager, Source en Destination zijn vooraf geïnstalleerd op de Azure-SSIS IR Enterprise Edition. U moet ook het OCI-stuurprogramma (Oracle Call Interface) installeren en indien nodig het Oracle Transport Network Substrate (TNS) configureren op azure-SSIS IR. Zie [Aangepaste instelling voor de Azure-SSIS-integratieruntime](how-to-configure-azure-ssis-ir-custom-setup.md) voor meer informatie. |
| Teradata-connectoren | U moet de Teradata Connection Manager, Bron en Bestemming, evenals de Teradata Parallel Transporter (TPT) API en Teradata ODBC-driver, installeren op de Azure-SSIS IR Enterprise Edition. Zie [Aangepaste instelling voor de Azure-SSIS-integratieruntime](how-to-configure-azure-ssis-ir-custom-setup.md) voor meer informatie. |
| SAP BW-connectoren | De SAP BW Connection Manager, Source en Destination zijn vooraf geïnstalleerd op de Azure-SSIS IR Enterprise Edition. U moet ook het SAP BW-stuurprogramma installeren op de Azure-SSIS IR. Deze connectors ondersteunen SAP BW 7.0 of eerdere versies. Als u verbinding wilt maken met latere versies van SAP BW of andere SAP-producten, u SAP-connectors van externe ISV's kopen en installeren op de Azure-SSIS IR. Zie [Aangepaste installatie voor de runtime van Azure-SSIS-integratie voor](how-to-configure-azure-ssis-ir-custom-setup.md)meer informatie over het installeren van extra componenten. |
| Analysis Services-componenten               | De trainingsbestemming voor gegevensminingmodellen, de bestemming voor dimensionprocessing en de bestemming voor partitionprocessing en de transformatie van gegevensminingquery's zijn vooraf geïnstalleerd op de Azure-SSIS IR Enterprise Edition. Al deze componenten ondersteunen SQL Server Analysis Services (SSAS), maar alleen de bestemming voor partitieverwerking ondersteunt Azure Analysis Services (AAS). Als u verbinding wilt maken met SSAS, moet u ook [Windows-verificatiereferenties configureren in SSISDB.](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) Naast deze componenten zijn de Analysis Services Execute DDL-taak, de analysis services processing task en de Data Mining Query Task ook vooraf geïnstalleerd op de Azure-SSIS IR Standard/Enterprise Edition. |
| Fuzzy Grouping en Fuzzy Lookup transformaties  | De fuzzy grouping- en fuzzy lookup-transformaties zijn vooraf geïnstalleerd op de Azure-SSIS IR Enterprise Edition. Deze componenten ondersteunen zowel SQL Server als Azure SQL Database voor het opslaan van referentiegegevens. |
| Term Extraction and Term Lookup transformations | De transformaties term extraction en term lookup zijn vooraf geïnstalleerd op de Azure-SSIS IR Enterprise Edition. Deze componenten ondersteunen zowel SQL Server als Azure SQL Database voor het opslaan van referentiegegevens. |

## <a name="instructions"></a>Instructies

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  Azure [PowerShell](/powershell/azure/install-az-ps)downloaden en installeren .

2.  Wanneer u azure-SSIS IR indient of opnieuw `Set-AzDataFactoryV2IntegrationRuntime` configureert met PowerShell, voert u met **Enterprise** uit als waarde voor de parameter **Edition** voordat u de Azure-SSIS IR start. Hier is een voorbeeld script:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Volgende stappen

-   [Aangepaste installatie voor de runtime van Azure-SSIS-integratie](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Betaalde of gelicentieerde aangepaste componenten ontwikkelen voor de runtime van Azure-SSIS-integratie](how-to-develop-azure-ssis-ir-licensed-components.md)
