---
title: Sortering
description: Collatietypen die worden ondersteund in Azure Synapse SQL
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 4270677f8f5f77e1ada0b1d9385163dad762bbda
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431538"
---
# <a name="database-collation-support-for-synapse-sql"></a>Ondersteuning voor databasecollatie voor Synapse SQL

Collaties bieden de landvermelding, codepagina, sorteervolgorde- en tekengevoeligheidsregels voor op tekens gebaseerde gegevenstypen. Eenmaal gekozen, erven alle kolommen en expressies die collatie-informatie vereisen de gekozen collatie van de database-instelling. De standaardovererving kan worden overschreven door expliciet een andere collatie voor een op tekens gebaseerd gegevenstype te vermelden.

U de standaarddatabaseverzameling van de Azure-portal wijzigen wanneer u een nieuwe SQL-pooldatabase maakt. Deze mogelijkheid maakt het nog eenvoudiger om een nieuwe database te maken met behulp van een van de 3800 ondersteunde databasecollaties.

U de standaard Synapse SQL on-demand database collatie op geeft bij het maken van de tijd met behulp van CREATE DATABASE-instructie.

## <a name="changing-collation"></a>Veranderende collatie
Als u de standaardcollatie voor SQL-pooldatabase wilt wijzigen, wordt u eenvoudig bijgewerkt naar het veld Collatie in de inrichtingservaring. Als u bijvoorbeeld de standaardcollatie wilt wijzigen in hoofdlettergevoelig, wijzigt u de naam van de Collatie eenvoudig van SQL_Latin1_General_CP1_CI_AS naar SQL_Latin1_General_CP1_CS_AS. 

Als u de standaardcollatie voor SQL on-demand database wilt wijzigen, u de instructie ALTER DATABASE gebruiken.

## <a name="list-of-unsupported-collation-types"></a>Lijst met niet-ondersteunde collatietypen
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

Bovendien biedt SQL-groep geen ondersteuning voor volgende collatietypen:

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS

## <a name="checking-the-current-collation"></a>De huidige collatie controleren
Als u de huidige collatie voor de database wilt controleren, u het volgende T-SQL-fragment uitvoeren:
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
Wanneer de eigenschapparameter 'Collation' wordt doorgegeven, retourneert de functie DatabasePropertyEx de huidige collatie voor de opgegeven database. Meer informatie over de functie DatabasePropertyEx vindt u op MSDN.

## <a name="next-steps"></a>Volgende stappen

Aanvullende informatie over best practices voor SQL-pool en SQL on-demand vindt u in de volgende artikelen:

- [Aanbevolen procedures voor SQL-pool](best-practices-sql-pool.md)
- [Aanbevolen procedures voor SQL on-demand](best-practices-sql-on-demand.md)


