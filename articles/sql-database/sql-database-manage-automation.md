---
title: Databases beheren met Azure Automation
description: Meer informatie over hoe de Azure Automation-service kan worden gebruikt om Azure SQL-databases op schaal te beheren.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 9d826a75f05cf2031565f89e21d7f3667ecc8f17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822806"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Azure SQL-databases beheren met Azure Automation

In deze handleiding wordt u kennis laten maken met de Azure Automation-service en hoe deze kan worden gebruikt om het beheer van uw Azure SQL-databases te vereenvoudigen.

## <a name="what-is-azure-automation"></a>Wat is Azure Automation?

[Azure Automation](https://azure.microsoft.com/services/automation/) is een Azure-service voor het vereenvoudigen van cloudbeheer door procesautomatisering. Met Azure Automation kunnen langlopende, handmatige, foutgevoelige en vaak herhaalde taken worden geautomatiseerd om de betrouwbaarheid, efficiëntie en tijd tot waarde voor uw organisatie te verhogen.

Azure Automation biedt een workflow-uitvoeringsengine met hoge betrouwbaarheid en hoge beschikbaarheid en die wordt geschaald om aan uw behoeften te voldoen naarmate uw organisatie groeit. In Azure Automation kunnen processen handmatig, door systemen van derden of met geplande intervallen worden gestart, zodat taken precies plaatsvinden wanneer dat nodig is.

Verlaag de operationele overhead en maak IT/DevOps-medewerkers vrij om zich te concentreren op werk dat bedrijfswaarde toevoegt door uw cloudbeheertaken te verplaatsen om automatisch te worden uitgevoerd door Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Hoe kan Azure Automation helpen bij het beheren van Azure SQL-databases?

Azure SQL Database kan worden beheerd in Azure Automation met behulp van de [Azure SQL Database PowerShell-cmdlets](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) die beschikbaar zijn in de [Azure PowerShell-hulpprogramma's.](/powershell/azure/overview) Azure Automation heeft deze Azure SQL Database PowerShell-cmdlets uit de doos beschikbaar, zodat u al uw SQL DB-beheertaken binnen de service uitvoeren. U deze cmdlets ook koppelen in Azure Automation met de cmdlets voor andere Azure-services, om complexe taken te automatiseren in Azure-services en voor systemen van derden.

Azure Automation heeft ook de mogelijkheid om rechtstreeks met SQL-servers te communiceren, door SQL-opdrachten uit te geven met PowerShell.

De [runbook-galerie azure automation](https://azure.microsoft.com/blog/20../../introducing-the-azure-automation-runbook-gallery/) bevat een verscheidenheid aan productteam- en communityrunbooks om aan de slag te gaan met het automatiseren van beheer van Azure SQL-databases, andere Azure-services en systemen van derden. Galerierunboeken zijn:

- [SQL-query's uitvoeren op een SQL Server-database](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
- [Een Azure SQL-database verticaal schalen (omhoog of omlaag) volgens een planning](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
- [Een SQL-tabel afkappen als de database de maximale grootte nadert](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
- [Indextabellen in een Azure SQL-database als ze sterk gefragmenteerd zijn](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Volgende stappen

Nu u de basisbeginselen van Azure Automation hebt geleerd en hoe deze kan worden gebruikt om Azure SQL-databases te beheren, volgt u deze koppelingen voor meer informatie over Azure Automation.

- [Overzicht van Azure Automation](../automation/automation-intro.md)
- [Mijn eerste runbook](../automation/automation-first-runbook-graphical.md)
- [Azure Automation: uw SQL-agent in de cloud](https://azure.microsoft.com/blog/20../../azure-automation-your-sql-agent-in-the-cloud/) 