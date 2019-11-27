---
title: Belangrijkste verschillen voor Machine Learning Services (preview-versie)
description: In dit onderwerp worden de belangrijkste verschillen tussen de Azure SQL Database-Machine Learning Services (met R) en SQL Server Machine Learning Services beschreven.
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
ms.openlocfilehash: 533e2b9e50a92cce1419da521d8cebc4955e4df6
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74462106"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>De belangrijkste verschillen tussen Machine Learning Services in Azure SQL Database (preview) en SQL Server

De functionaliteit van Azure SQL Database Machine Learning Services (met R) in (preview) is vergelijkbaar met [SQL Server machine learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Hieronder vindt u enkele belang rijke verschillen.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>Taalondersteuning

SQL Server biedt ondersteuning voor R en python via het [Framework voor uitbreid baarheid](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). SQL Database biedt geen ondersteuning voor beide talen. De belangrijkste verschillen zijn:

- R is de enige ondersteunde taal in SQL Database. Er is op dit moment geen ondersteuning voor Python.
- De R-versie is 3.4.4.
- Het is niet nodig om `external scripts enabled` te configureren via `sp_configure`. Zodra u zich hebt [aangemeld](sql-database-machine-learning-services-overview.md#signup), wordt machine learning voor uw SQL database ingeschakeld.

## <a name="package-management"></a>Pakketbeheer

R-pakket beheer en installatie verschillen tussen SQL Database en SQL Server. Deze verschillen zijn:

- R-pakketten worden geïnstalleerd via [sqlmlutils](https://github.com/Microsoft/sqlmlutils) of u [maakt een externe bibliotheek](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Pakketten kunnen geen uitgaande netwerk aanroepen uitvoeren. Deze beperking is vergelijkbaar met de [standaard firewall regels voor Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) in SQL Server, maar kan niet worden gewijzigd in SQL database.
- Er wordt geen ondersteuning geboden voor pakketten die afhankelijk zijn van externe Runtimes (zoals Java) of waarvoor u toegang nodig hebt tot OS Api's voor installatie of gebruik.

## <a name="writing-to-a-temporary-table"></a>Schrijven naar een tijdelijke tabel

Als u RODBC gebruikt in Azure SQL Database, kunt u niet naar een tijdelijke tabel schrijven, ongeacht of deze binnen of buiten de `sp_execute_external_script` sessie is gemaakt. De tijdelijke oplossing is om [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) en [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (met de opdracht Write = False en append = "rows") te gebruiken om te schrijven naar een globale tijdelijke tabel die is gemaakt voor de `sp_execute_external_script`-query.

## <a name="resource-governance"></a>Resourcebeheer

Het is niet mogelijk om R-resources te beperken via [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) en externe resource groepen.

Tijdens de open bare preview worden R-resources ingesteld op Maxi maal 20% van de SQL Database resources, afhankelijk van welke servicelaag u kiest. Zie [Azure SQL database-aankoop modellen](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers)voor meer informatie.
### <a name="insufficient-memory-error"></a>Onvoldoende geheugen fout

Als er onvoldoende geheugen beschikbaar is voor R, wordt een fout bericht weer gegeven. Veelvoorkomende fout berichten zijn:

- Kan niet communiceren met de runtime voor het script ' R ' voor aanvraag-id: * * * * * * *. Raadpleeg de vereisten van ' R ' runtime
- ' R ' er is een script fout opgetreden tijdens het uitvoeren van ' sp_execute_external_script ' met HRESULT 0x80004004. ... Er is een externe script fout opgetreden: ".. kan geen geheugen toewijzen (0 MB) in de C-functie R_AllocStringBuffer
- Er is een externe script fout opgetreden: fout: kan de vector grootte niet toewijzen.

Het geheugen gebruik is afhankelijk van de hoeveelheid die wordt gebruikt in uw R-scripts en het aantal parallelle query's dat wordt uitgevoerd. Als u de bovenstaande fouten ontvangt, kunt u uw data base schalen naar een hogere servicelaag om dit op te lossen.

## <a name="next-steps"></a>Volgende stappen

- Zie het overzicht [Azure SQL Database Machine Learning Services met R (preview)](sql-database-machine-learning-services-overview.md).
- Zie de [Snelstartgids](sql-database-connect-query-r.md)voor meer informatie over het gebruik van R voor het opvragen van Azure SQL database machine learning Services (preview).
- Zie [eenvoudige r-scripts maken en uitvoeren in Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-create-script.md)om aan de slag te gaan met een aantal eenvoudige r-scripts.
