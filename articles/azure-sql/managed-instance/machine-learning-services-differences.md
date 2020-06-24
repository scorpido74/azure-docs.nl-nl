---
title: Belangrijkste verschillen voor Machine Learning Services (preview-versie)
description: In dit onderwerp worden de belangrijkste verschillen tussen Machine Learning Services in Azure SQL Managed instance en SQL Server Machine Learning Services beschreven.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: carlrab, davidph
manager: cgronlun
ms.date: 05/27/2020
ms.openlocfilehash: da97938736e7a3719da9d280e60e6a636b86e0e5
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85254745"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>De belangrijkste verschillen tussen Machine Learning Services in Azure SQL Managed instance en SQL Server

De functionaliteit van [Machine Learning Services in Azure SQL Managed instance (preview)](machine-learning-services-overview.md) is bijna identiek aan [SQL Server machine learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Hier volgen enkele belang rijke verschillen.

> [!IMPORTANT]
> Machine Learning Services in Azure SQL Managed instance is momenteel beschikbaar als open bare preview. Zie [registreren voor de preview](machine-learning-services-overview.md#signup)om u aan te melden.

## <a name="preview-limitations"></a>Preview-beperkingen

Tijdens de preview heeft de service de volgende beperkingen:

- Loop back-verbindingen werken niet (Zie [loop back-verbinding met SQL Server vanuit een python-of R-script](/sql/machine-learning/connect/loopback-connection)).
- Externe resource groepen worden niet ondersteund.
- Alleen python en R worden ondersteund. Externe talen, zoals Java, kunnen niet worden toegevoegd.
- Scenario's die gebruikmaken van de [Message Passing Interface](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) (MPI) worden niet ondersteund.

In het geval van een serviceniveau doelstelling (SLO) update moet u de SLO bijwerken en een ondersteunings ticket genereren om de toegewezen resource limieten voor R/python opnieuw in te scha kelen.

## <a name="language-support"></a>Taalondersteuning

Machine Learning Services in SQL Managed instance en SQL Server ondersteunen zowel python als R [Extensibility Framework](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). De belangrijkste verschillen zijn:

- De eerste versies van python en R verschillen van Machine Learning Services in SQL Managed instance en SQL Server:

  |                      | Python | R     |
  |----------------------|--------|-------|
  | SQL Managed Instance | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- U hoeft niet in te stellen `external scripts enabled` via `sp_configure` . Zodra u zich hebt [geregistreerd](machine-learning-services-overview.md#signup) voor de preview-versie, wordt machine learning ingeschakeld voor Azure SQL Managed instance.

## <a name="packages"></a>Pakketten

Python en R-pakket beheer werken anders tussen SQL Managed instance en SQL Server. Deze verschillen zijn:

- Pakketten kunnen geen uitgaande netwerk aanroepen uitvoeren. Deze beperking is vergelijkbaar met de [standaard firewall regels voor Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) in SQL Server, maar kan niet worden gewijzigd in een SQL Managed instance.
- Er wordt geen ondersteuning geboden voor pakketten die afhankelijk zijn van externe Runtimes (zoals Java) of waarvoor u toegang nodig hebt tot OS Api's voor installatie of gebruik.

Zie voor meer informatie over het beheren van python-en R-pakketten:

- [Python-pakketgegevens ophalen](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)
- [R-pakketgegevens ophalen](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)

## <a name="resource-governance"></a>Resourcebeheer

Het is niet mogelijk om R-resources te beperken via [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) en externe resource groepen.

Tijdens de open bare preview worden R-resources ingesteld op Maxi maal 20% van de SQL Managed instance-resources en zijn deze afhankelijk van welke servicelaag u kiest. Zie [Azure SQL database-aankoop modellen](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers)voor meer informatie.

### <a name="insufficient-memory-error"></a>Onvoldoende geheugen fout

Als er onvoldoende geheugen beschikbaar is voor R, wordt een fout bericht weer gegeven. Veelvoorkomende fout berichten zijn:

- Kan niet communiceren met de runtime voor het script ' R ' voor aanvraag-id: * * * * * * *. Raadpleeg de vereisten van ' R ' runtime
- ' R ' er is een script fout opgetreden tijdens het uitvoeren van ' sp_execute_external_script ' met HRESULT 0x80004004. ... Er is een externe script fout opgetreden: ".. kan geen geheugen toewijzen (0 MB) in de C-functie R_AllocStringBuffer
- Er is een externe script fout opgetreden: fout: kan de vector grootte niet toewijzen.

Het geheugen gebruik is afhankelijk van de hoeveelheid die wordt gebruikt in uw R-scripts en het aantal parallelle query's dat wordt uitgevoerd. Als u de bovenstaande fouten ontvangt, kunt u uw data base schalen naar een hogere servicelaag om dit op te lossen.

## <a name="next-steps"></a>Volgende stappen

- Zie het overzicht [Machine Learning Services in Azure SQL Managed instance](machine-learning-services-overview.md).
- Zie [python-scripts uitvoeren](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)voor meer informatie over het gebruik van python in Machine Learning Services.
- Zie [r-scripts uitvoeren](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)voor meer informatie over het gebruik van r in Machine Learning Services.
