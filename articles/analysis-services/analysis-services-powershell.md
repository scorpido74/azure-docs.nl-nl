---
title: Azure Analysis Services beheren met Power shell | Microsoft Docs
description: Beschrijft Azure Analysis Services Power shell-cmdlets voor veelvoorkomende beheer taken, zoals het maken van servers, het onderbreken van bewerkingen of het wijzigen van het service niveau.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: dfa9de94665c0ee98d0252be6de807f18e39b087
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83697971"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Azure Analysis Services beheren met PowerShell

In dit artikel worden de Power shell-cmdlets beschreven die worden gebruikt voor het uitvoeren van Azure Analysis Services server-en database beheer taken. 

Server Resource beheer taken zoals het maken of verwijderen van een server, het onderbreken of hervatten van Server bewerkingen of het wijzigen van de service niveau (laag) gebruik Azure Analysis Services-cmdlets. Andere taken voor het beheren van data bases, zoals het toevoegen of verwijderen van Rolgroepen, het verwerken of het partitioneren van cmdlets die zijn opgenomen in dezelfde SqlServer-module als SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Machtigingen

Voor de meeste Power shell-taken moet u beheerders bevoegdheden hebben op de Analysis Services-server die u beheert. Geplande power shell-taken zijn bewerkingen zonder toezicht. De account of Service-Principal waarop de scheduler wordt uitgevoerd, moet beheerders bevoegdheden hebben op de Analysis Services-server. 

Voor Server bewerkingen die gebruikmaken van Azure PowerShell-cmdlets, moet uw account of het account dat scheduler uitvoert ook behoren tot de rol van eigenaar van de resource in [Azure op rollen gebaseerde Access Control (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-and-server-operations"></a>Resource-en Server bewerkingen 

Module installeren- [AZ. AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Documentatie- [AZ. AnalysisServices Reference](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Database bewerkingen

Azure Analysis Services database bewerkingen gebruiken dezelfde SqlServer-module als SQL Server Analysis Services. Niet alle cmdlets worden echter ondersteund voor Azure Analysis Services. 

De SqlServer-module bevat gebruikersspecifieke data base management-cmdlets en de cmdlet invoke-ASCmd die een TMSL-query (Tabular model scripting language) of script accepteert. De volgende cmdlets in de SqlServer-module worden ondersteund voor Azure Analysis Services.

Module installeren- [sqlserver](https://www.powershellgallery.com/packages/SqlServer)   
Documentatie- [sqlserver-referentie](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Ondersteunde cmdlets

|Cmdlet|Description|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Een lid toevoegen aan een databaserol.| 
|[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Back-up maken van een Analysis Services-Data Base.|  
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Een lid uit een databaserol verwijderen.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Voer een TMSL-script uit.|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Een Data Base verwerken.|  
|[Invoke-ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Een partitie verwerken.| 
|[Invoke-ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Een tabel verwerken.|  
|[Samen voegen-partitie](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Een partitie samen voegen.|  
|[Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Een Analysis Services-Data Base herstellen.| 
  

## <a name="related-information"></a>Gerelateerde informatie

* [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [SQL Server Power shell-module downloaden](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [SSMS downloaden](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [SqlServer-module in PowerShell Gallery](https://www.powershellgallery.com/packages/SqlServer)    
* [Program meren in tabellair model voor compatibiliteits niveau 1200 en hoger](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
