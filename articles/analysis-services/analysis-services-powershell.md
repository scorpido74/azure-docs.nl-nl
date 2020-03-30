---
title: Azure Analysis Services beheren met PowerShell | Microsoft Documenten
description: Beschrijft PowerShell-cmdlets van Azure Analysis Services voor veelvoorkomende beheertaken, zoals het maken van servers, het onderbreken van bewerkingen of het wijzigen van serviceniveau.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2c8f4c0541d97a189087af692658cfe794eaaf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572697"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Azure Analysis Services beheren met PowerShell

In dit artikel worden PowerShell-cmdlets beschreven die worden gebruikt om azure analysis services-server- en databasebeheertaken uit te voeren. 

Serverresourcebeheertaken zoals het maken of verwijderen van een server, het onderbreken of hervatten van serverbewerkingen of het wijzigen van de cmdlets van het serviceniveau (tier) gebruiken Azure Analysis Services. Andere taken voor het beheren van databases, zoals het toevoegen of verwijderen van rolleden, het verwerken of partitioneren van cmdlets die zijn opgenomen in dezelfde SqlServer-module als SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Machtigingen

Voor de meeste PowerShell-taken hebt u beheerdersbevoegdheden op de server van Analysis Services die u beheert. Geplande PowerShell-taken zijn onbeheerde bewerkingen. De account- of serviceprincipal die de planner uitvoert, moet beheerdersrechten hebben op de analysis services-server. 

Voor serverbewerkingen met Azure PowerShell-cmdlets moet uw account of de accountplanningplanner ook deel uitmaken van de rol Eigenaar voor de bron in [Azure Role-Based Access Control (RBAC).](../role-based-access-control/overview.md) 

## <a name="resource-and-server-operations"></a>Resource- en serverbewerkingen 

Installatiemodule - [Az.AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Documentatie - [Referentie van AZ.AnalysisServices](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Databasebewerkingen

Azure Analysis Services-databasebewerkingen gebruiken dezelfde SqlServer-module als SQL Server Analysis Services. Niet alle cmdlets worden echter ondersteund voor Azure Analysis Services. 

De SqlServer-module biedt taakspecifieke databasebeheercmdlets en de cmdlet voor algemene doeleinden Invoke-ASCmd die een TMSL-query of -script (Tabular Model Scripting Language) accepteert. De volgende cmdlets in de SqlServer-module worden ondersteund voor Azure Analysis Services.

Installatiemodule - [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Documentatie - [SqlServer-referentie](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Ondersteunde cmdlets

|Cmdlet|Beschrijving|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Een lid toevoegen aan een databaserol.| 
|[Back-up-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Een back-up maken van een analysis services-database.|  
|[Verwijder-Rollid](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Een lid uit een databaserol verwijderen.|   
|[Aanroepen-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Een TMSL-script uitvoeren.|
|[Aanroepen-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Een database verwerken.|  
|[Aanroepen-ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Een partitie verwerken.| 
|[Aanroep-procestabel](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Een tabel verwerken.|  
|[Samenvoeging-partitie](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Een partitie samenvoegen.|  
|[Herstel-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Een Analysis Services-database herstellen.| 
  

## <a name="related-information"></a>Gerelateerde informatie

* [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [SQL Server PowerShell-module downloaden](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [SSMS downloaden](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [SqlServer-module in PowerShell-galerie](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabelmodelprogrammering voor compatibiliteitsniveau 1200 en hoger](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
