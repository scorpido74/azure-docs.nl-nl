---
title: Installeer Visual Studio 2019
description: Visual Studio en SQL Server Development tools (SSDT) voor SQL Analytics installeren
services: sql-data-warehouse
ms.custom: azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b9ad0f4aedc5a095bfa70dec1b67e52cd4d152e0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195336"
---
# <a name="getting-started-with-visual-studio-2019"></a>Aan de slag met Visual Studio 2019
Visual Studio **2019** SQL Server Data tools (SSDT) is één hulp programma waarmee u het volgende kunt doen:

- Toepassingen verbinden, query's uitvoeren en ontwikkelen
- Maak gebruik van een object Verkenner om alle objecten in uw gegevens model visueel te verkennen, met inbegrip van tabellen, weer gaven, opgeslagen procedures en etc.
- Een DDL-script (T-SQL Data Definition Language) voor uw objecten genereren
- Ontwikkel uw data warehouse met behulp van een op status gebaseerde benadering met SSDT-database projecten
- Integreer uw database project met broncode beheer systemen zoals Git met Azure opslag plaatsen
- Continue integratie en implementatie pijplijnen instellen met automatiserings servers zoals Azure DevOps

## <a name="install-visual-studio-2019"></a>Installeer Visual Studio 2019
Zie [Down load Visual Studio 2019][] om visual studio **16,3 en hoger**te downloaden en te installeren. Selecteer tijdens de installatie de werk belasting gegevens opslag en-verwerking. Zelfstandige installatie van SSDT is niet meer vereist in Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Niet-ondersteunde functies in SSDT

De functie releases voor SQL Analytics bevatten mogelijk geen ondersteuning voor SSDT. De volgende functies worden momenteel niet ondersteund:

- [Gerealiseerde weer gaven](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (wordt uitgevoerd)
- [Geordende geclusterde column Store-indexen](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-) (wordt uitgevoerd)
- [Instructie kopiëren](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (wordt uitgevoerd)
- [Workload Management](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management) : werkbelasting groepen en classificaties (wordt uitgevoerd)
- [Beveiliging op rijniveau](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - Verzend [hier](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) een ondersteunings ticket om de functie op te halen.
- [Dynamische gegevensmaskering](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - Verzend [hier](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) een ondersteunings ticket om de functie op te halen. 
- Functie voors [pellen](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest) 
- [Tabellen met beperkingen](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-table-constraints#table-constraints) worden niet ondersteund. Stel voor deze tabel objecten de actie build in op ' geen '.

## <a name="next-steps"></a>Volgende stappen

Nu u de nieuwste versie van SSDT hebt, bent u klaar om [verbinding te maken][connect] met uw SQL-groep.

<!--Anchors-->

<!--Image references-->

<!--Articles-->

[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->

[Down load Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
