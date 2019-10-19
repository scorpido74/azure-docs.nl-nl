---
title: Installeer Visual Studio 2019 voor SQL Data Warehouse | Microsoft Docs
description: Visual Studio en SQL Server Development Tools (SSDT) voor Azure SQL Data Warehouse installeren
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/17/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 81c709e7705e16484438ab684a6b1591e5e624ba
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553511"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>Aan de slag met Visual Studio 2019 voor SQL Data Warehouse
Visual Studio **2019** SQL Server Data tools (SSDT) is één hulp programma waarmee u het volgende kunt doen:

- Toepassingen voor SQL Data Warehouse verbinden, query's uitvoeren en ontwikkelen 
- Maak gebruik van een object Verkenner om alle objecten in uw gegevens model visueel te verkennen, met inbegrip van tabellen, weer gaven, opgeslagen procedures en etc.
- Een DDL-script (T-SQL Data Definition Language) voor uw objecten genereren
- Ontwikkel uw data warehouse met behulp van een op status gebaseerde benadering met SSDT-database projecten
- Integreer uw database project met broncode beheer systemen zoals Git met Azure DevOps opslag plaatsen
- Continue integratie en implementatie pijplijnen instellen met automatiserings servers zoals Azure DevOps

> [!NOTE]
> Momenteel is Visual Studio SSDT-database projecten beschikbaar als preview-versie. Als u periodieke updates voor deze functie wilt ontvangen, kunt u stemmen op [UserVoice].

## <a name="install-visual-studio-2019-preview"></a>Visual Studio 2019 Preview installeren
Zie [Down load Visual Studio 2019][] om visual studio **16,3 en hoger**te downloaden en te installeren. Selecteer tijdens de installatie de werk belasting gegevens opslag en-verwerking. Zelfstandige installatie van SSDT is niet meer vereist in Visual Studio 2019.

## <a name="reporting-issues-with-ssdt-visual-studio-2019-preview"></a>Problemen melden met SSDT Visual Studio 2019 (preview-versie)
Als u problemen wilt rapporteren wanneer u SSDT gebruikt met SQL Data Warehouse, moet u de volgende e-mail distributie lijst per e-mail verzenden: <sqldwssdtpreview@service.microsoft.com>

## <a name="next-steps"></a>Volgende stappen
Nu u de nieuwste versie van SSDT hebt, bent u klaar om [verbinding te maken][connect] met uw SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Down load Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
