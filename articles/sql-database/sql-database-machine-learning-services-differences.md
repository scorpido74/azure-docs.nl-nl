---
title: Belangrijkste verschillen voor Machine Learning Services (preview)
description: In dit onderwerp worden belangrijke verschillen beschreven tussen Azure SQL Database Machine Learning Services (met R) en SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 34ba75b6126024c9cd43d6fe474f7c1b62dd990f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453146"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Belangrijkste verschillen tussen Machine Learning Services in Azure SQL Database (preview) en SQL Server

De functionaliteit van Azure SQL Database Machine Learning Services (met R) in (preview) is vergelijkbaar met [SQL Server Machine Learning Services.](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning) Hieronder staan enkele belangrijke verschillen.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>Taalondersteuning

SQL Server heeft ondersteuning voor R en Python via het [extensibiliteitsframework.](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework) SQL Database biedt geen ondersteuning voor beide talen. De belangrijkste verschillen zijn:

- R is de enige ondersteunde taal in SQL Database. Er is op dit moment geen ondersteuning voor Python.
- De R-versie is 3.4.4.
- Het is niet `external scripts enabled` nodig `sp_configure`om te configureren via .

## <a name="package-management"></a>Pakketbeheer

R-pakketbeheer en -installatie werken verschillend tussen SQL Database en SQL Server. Deze verschillen zijn:

- R-pakketten worden geïnstalleerd via [sqlmlutils](https://github.com/Microsoft/sqlmlutils) of [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Pakketten kunnen geen uitgaande netwerkoproepen uitvoeren. Deze beperking is vergelijkbaar met de [standaardfirewallregels voor Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) in SQL Server, maar kan niet worden gewijzigd in SQL Database.
- Er is geen ondersteuning voor pakketten die afhankelijk zijn van externe runtimes (zoals Java) of toegang tot OS API's nodig hebben voor installatie of gebruik.

## <a name="writing-to-a-temporary-table"></a>Schrijven naar een tijdelijke tabel

Als u RODBC gebruikt in Azure SQL Database, u niet naar een tijdelijke tabel schrijven, of deze nu binnen of buiten de `sp_execute_external_script` sessie is gemaakt. De tijdelijke oplossing is om [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) en [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (met overschrijven=FALSE en aanhangsel="rijen") `sp_execute_external_script` te gebruiken om te schrijven naar een globale tijdelijke tabel die vóór de query is gemaakt.

## <a name="resource-governance"></a>Resourcebeheer

Het is niet mogelijk om R-resources te beperken via [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) en externe resourcepools.

Tijdens de openbare preview worden R-resources ingesteld op maximaal 20% van de SQL Database-resources en zijn ze afhankelijk van welke servicelaag u kiest. Zie [Azure SQL Database-inkoopmodellen voor](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers)meer informatie.
### <a name="insufficient-memory-error"></a>Onvoldoende geheugenfout

Als er onvoldoende geheugen beschikbaar is voor R, krijgt u een foutmelding. Veelvoorkomende foutmeldingen zijn:

- Kan niet communiceren met de runtime voor 'R'-script voor aanvraag-id: *******. Controleer de vereisten van 'R' runtime
- 'R' script fout opgetreden tijdens de uitvoering van 'sp_execute_external_script' met HRESULT 0x80004004. ... een externe scriptfout is opgetreden: ".. kan geen geheugen (0 Mb) toewijzen in de C-functie 'R_AllocStringBuffer'"
- Er is een externe scriptfout opgetreden: Fout: kan geen vector van grootte toewijzen.

Het geheugengebruik is afhankelijk van hoeveel er wordt gebruikt in uw R-scripts en het aantal parallelle query's dat wordt uitgevoerd. Als u de bovenstaande fouten ontvangt, u uw database schalen naar een hogere servicelaag om dit op te lossen.

## <a name="next-steps"></a>Volgende stappen

- Zie het overzicht, [Azure SQL Database Machine Learning Services with R (preview)](sql-database-machine-learning-services-overview.md).
- Zie de [quickstart-handleiding](sql-database-connect-query-r.md)voor meer informatie over het gebruik van R voor het opvragen van Azure SQL Database Machine Learning Services (preview.
- Zie [Eenvoudige R-scripts maken en uitvoeren in Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-create-script.md)om aan de slag te gaan met een aantal eenvoudige R-scripts.
