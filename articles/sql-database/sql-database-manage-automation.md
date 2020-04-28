---
title: Data bases beheren met Azure Automation
description: Meer informatie over hoe de Azure Automation-Service kan worden gebruikt om Azure SQL-data bases op schaal te beheren.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73822806"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Azure SQL-data bases beheren met Azure Automation

In deze hand leiding vindt u informatie over de Azure Automation-Service en hoe u deze kunt gebruiken om het beheer van uw Azure SQL-data bases te vereenvoudigen.

## <a name="what-is-azure-automation"></a>Wat is Azure Automation?

[Azure Automation](https://azure.microsoft.com/services/automation/) is een Azure-service voor het vereenvoudigen van het beheer van Clouds met behulp van proces automatisering. Het gebruik van Azure Automation, langlopende, hand matige, fout gevoelige en regel matig herhaalde taken kunnen automatisch worden uitgevoerd om de betrouw baarheid, efficiëntie en tijd voor uw organisatie te verg Roten.

Azure Automation biedt een engine voor het uitvoeren van werk stromen met hoge betrouw baarheid en hoge Beschik baarheid, die wordt geschaald om te voldoen aan uw behoeften naarmate uw organisatie groeit. In Azure Automation kunnen processen hand matig worden gestart door systemen van derden of op geplande intervallen zodat taken precies worden uitgevoerd wanneer dit nodig is.

Verlaag operationele overhead en maak IT/DevOps-personeel meer werk dat bedrijfs waarde kan toevoegen door te verplaatsen van uw cloud Beheer taken zodat deze automatisch door Azure Automation worden uitgevoerd.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Hoe kan Azure Automation helpen bij het beheren van Azure SQL-data bases?

Azure SQL Database kunnen worden beheerd in Azure Automation met behulp van de [Azure SQL database Power shell-cmdlets](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) die beschikbaar zijn in de [Azure PowerShell-hulpprogram ma's](/powershell/azure/overview). Azure Automation bevat deze Azure SQL Database Power shell-cmdlets die beschikbaar zijn in het vak, zodat u al uw SQL DB-beheer taken binnen de service kunt uitvoeren. U kunt deze cmdlets ook in Azure Automation koppelen met de cmdlets voor andere Azure-Services, om complexe taken te automatiseren in Azure-Services en op systemen van derden.

Azure Automation heeft ook de mogelijkheid om rechtstreeks te communiceren met SQL-servers door SQL-opdrachten uit te geven met behulp van Power shell.

De [Azure Automation runbook Gallery](https://azure.microsoft.com/blog/20../../introducing-the-azure-automation-runbook-gallery/) bevat diverse product teams en community-runbooks om aan de slag te gaan met het automatiseren van het beheer van Azure SQL-data bases, andere Azure-Services en systemen van derden. Galerie-runbooks zijn onder andere:

- [SQL-query's uitvoeren op een SQL Server Data Base](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
- [Een Azure SQL Database op een planning verticaal schalen (omhoog of omlaag)](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
- [Een SQL-tabel afkappen als de data base de maximale grootte nadert](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
- [Index tabellen in een Azure SQL Database als deze sterk gefragmenteerd zijn](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes van Azure Automation hebt geleerd en hoe u deze kunt gebruiken voor het beheren van Azure SQL-data bases, volgt u deze koppelingen voor meer informatie over Azure Automation.

- [Overzicht van Azure Automation](../automation/automation-intro.md)
- [Mijn eerste runbook](../automation/automation-first-runbook-graphical.md)
- [Azure Automation: uw SQL-Agent in de Cloud](https://azure.microsoft.com/blog/20../../azure-automation-your-sql-agent-in-the-cloud/) 