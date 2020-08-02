---
title: Ondersteuning voor sorteringen
description: Sorterings typen die worden ondersteund in azure Synapse SQL
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 1099c4e4dd69a8dc8caee96ec5dda633ce8b9d12
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496199"
---
# <a name="database-collation-support-for-synapse-sql"></a>Ondersteuning voor database sortering voor Synapse SQL

Met sorteringen kunt u de land instelling, de code pagina, de sorteer volgorde en de teken gevoeligheids regels voor gegevens typen op basis van tekens opgeven. Nadat u hebt gekozen, nemen alle kolommen en expressies die sorteer gegevens vereisen de gekozen sortering van de data base-instelling over. De standaard overname kan worden overschreven door expliciet een andere sortering voor een gegevens type op basis van een teken te vermelden.

U kunt de standaard sortering van de data base wijzigen van de Azure Portal wanneer u een nieuwe SQL-groeps database maakt. Hierdoor is het nog eenvoudiger om een nieuwe Data Base te maken met behulp van een van de ondersteunde database sorteringen van 3800.

U kunt de standaard Synapse SQL-database sortering op aanvraag opgeven tijdens de aanmaak tijd met behulp van de instructie CREATE data base.

## <a name="change-collation"></a>Sortering wijzigen
Als u de standaard sortering voor de SQL-groeps database wilt wijzigen, kunt u eenvoudig bijwerken naar het veld sortering in de inrichtings ervaring. Als u de standaard sortering bijvoorbeeld wilt wijzigen in hoofdletter gevoelig, wijzigt u de naam van de sortering van SQL_Latin1_General_CP1_CI_AS in SQL_Latin1_General_CP1_CS_AS. 

Als u de standaard sortering voor een SQL-Data Base op aanvraag wilt wijzigen, kunt u de instructie ALTER Data Base gebruiken.

## <a name="list-of-unsupported-collation-types"></a>Lijst met niet-ondersteunde sorterings typen
*    Japanese_Bushu_Kakusu_140_BIN
*    Japanese_Bushu_Kakusu_140_BIN2
*    Japanese_Bushu_Kakusu_140_CI_AI_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI
*    Japanese_Bushu_Kakusu_140_CI_AI_WS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AI
*    Japanese_Bushu_Kakusu_140_CS_AI_WS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
*    Japanese_XJIS_140_BIN
*    Japanese_XJIS_140_BIN2
*    Japanese_XJIS_140_CI_AI_VSS
*    Japanese_XJIS_140_CI_AI_WS_VSS
*    Japanese_XJIS_140_CI_AI_KS_VSS
*    Japanese_XJIS_140_CI_AI_KS_WS_VSS
*    Japanese_XJIS_140_CI_AS_VSS
*    Japanese_XJIS_140_CI_AS_WS_VSS
*    Japanese_XJIS_140_CI_AS_KS_VSS
*    Japanese_XJIS_140_CI_AS_KS_WS_VSS
*    Japanese_XJIS_140_CS_AI_VSS
*    Japanese_XJIS_140_CS_AI_WS_VSS
*    Japanese_XJIS_140_CS_AI_KS_VSS
*    Japanese_XJIS_140_CS_AI_KS_WS_VSS
*    Japanese_XJIS_140_CS_AS_VSS
*    Japanese_XJIS_140_CS_AS_WS_VSS
*    Japanese_XJIS_140_CS_AS_KS_VSS
*    Japanese_XJIS_140_CS_AS_KS_WS_VSS
*    Japanese_XJIS_140_CI_AI
*    Japanese_XJIS_140_CI_AI_WS
*    Japanese_XJIS_140_CI_AI_KS
*    Japanese_XJIS_140_CI_AI_KS_WS
*    Japanese_XJIS_140_CI_AS
*    Japanese_XJIS_140_CI_AS_WS
*    Japanese_XJIS_140_CI_AS_KS
*    Japanese_XJIS_140_CI_AS_KS_WS
*    Japanese_XJIS_140_CS_AI
*    Japanese_XJIS_140_CS_AI_WS
*    Japanese_XJIS_140_CS_AI_KS
*    Japanese_XJIS_140_CS_AI_KS_WS
*    Japanese_XJIS_140_CS_AS
*    Japanese_XJIS_140_CS_AS_WS
*    Japanese_XJIS_140_CS_AS_KS
*    Japanese_XJIS_140_CS_AS_KS_WS

Daarnaast biedt SQL-pool geen ondersteuning voor de volgende sorterings typen:

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS
*    UTF-8

## <a name="check-the-current-collation"></a>De huidige sortering controleren
Als u de huidige sortering voor de Data Base wilt controleren, kunt u het volgende T-SQL-fragment uitvoeren:
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
Als Sortation is door gegeven als eigenschaps parameter, retourneert de functie DatabasePropertyEx de huidige sortering voor de opgegeven Data Base. Meer informatie over de functie DatabasePropertyEx vindt u op MSDN.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over best practices voor SQL-pool en SQL on-demand vindt u in de volgende artikelen:

- [Best practices voor SQL-pool](best-practices-sql-pool.md)
- [Best practices voor SQL on-demand](best-practices-sql-on-demand.md)


