---
title: Taalelementen T-SQL
description: Koppelingen naar de documentatie voor T-SQL-instructies die worden ondersteund in synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 06/13/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f27a0a351e4a446dc950ac13f850bd14b2b3c65a
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586402"
---
# <a name="t-sql-language-elements-supported-in-synapse-sql-pool"></a>T-SQL-taalelementen ondersteund in Synapse SQL-pool

Koppelingen naar de documentatie voor T-SQL-taalelementen die worden ondersteund in synapse SQL-pool.

## <a name="core-elements"></a>Kernelementen

* [syntaxisconventies](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)
* [objectnaamgevingsregels](https://msdn.microsoft.com/library/ms175874.aspx)
* [gereserveerde trefwoorden](https://msdn.microsoft.com/library/ms189822.aspx)
* [Sorteringen](https://msdn.microsoft.com/library/ff848763.aspx)
* [Opmerkingen](https://msdn.microsoft.com/library/ms181627.aspx)
* [Constanten](https://msdn.microsoft.com/library/ms179899.aspx)
* [gegevenstypen](https://msdn.microsoft.com/library/ms187752.aspx)
* [Uitvoeren](https://msdn.microsoft.com/library/ms188332.aspx)
* [Expressies](https://msdn.microsoft.com/library/ms190286.aspx)
* [Doden](https://msdn.microsoft.com/library/ms173730.aspx)
* [Tijdelijke oplossing voor identiteitseigenschappen](https://msdn.microsoft.com/library/ms186775.aspx)
* [Afdrukken](https://msdn.microsoft.com/library/ms176047.aspx)
* [Gebruiken](https://msdn.microsoft.com/library/ms188366.aspx)

## <a name="batches-control-of-flow-and-variables"></a>Batches, controle-van-stroom en variabelen

* [Beginnen... Einde](https://msdn.microsoft.com/library/ms190487.aspx)
* [Breken](https://msdn.microsoft.com/library/ms181271.aspx)
* [Verklaren@local_variable](https://msdn.microsoft.com/library/ms188927.aspx)
* [Als... Anders](https://msdn.microsoft.com/library/ms182717.aspx)
* [RAISFout](https://msdn.microsoft.com/library/ms178592.aspx)
* [SET@local_variable](https://msdn.microsoft.com/library/ms189484.aspx)
* [Gooien](https://msdn.microsoft.com/library/ee677615.aspx)
* [Proberen... Vangen](https://msdn.microsoft.com/library/ms175976.aspx)
* [Terwijl](https://msdn.microsoft.com/library/ms178642.aspx)

## <a name="operators"></a>Operators

* [+ (Toevoegen)](https://msdn.microsoft.com/library/ms178565.aspx)
* [+ (String Concatenation)](https://msdn.microsoft.com/library/ms177561.aspx)
* [- (Negatief)](https://msdn.microsoft.com/library/ms189480.aspx)
* [- (Aftrekken)](https://msdn.microsoft.com/library/ms189518.aspx)
* [* (Vermenigvuldigen)](https://msdn.microsoft.com/library/ms176019.aspx)
* [/ (Verdeel)](https://msdn.microsoft.com/library/ms175009.aspx)
* [Modulo](https://msdn.microsoft.com/library/ms190279.aspx)

## <a name="wildcard-characters-to-match"></a>Jokerteken(en) dat overeenkomt

* [= (Gelijken)](https://msdn.microsoft.com/library/ms175118.aspx)
* [> (Groter dan)](https://msdn.microsoft.com/library/ms178590.aspx)
* [< (Minder dan)](https://msdn.microsoft.com/library/ms179873.aspx)
* [>= (Groot dan of gelijk aan)](https://msdn.microsoft.com/library/ms181567.aspx)
* [<= (Minder dan of gelijk aan)](https://msdn.microsoft.com/library/ms174978.aspx)
* [<> (Niet gelijk aan)](https://msdn.microsoft.com/library/ms176020.aspx)
* [!= (Niet gelijk aan)](https://msdn.microsoft.com/library/ms190296.aspx)
* [En](https://msdn.microsoft.com/library/ms188372.aspx)
* [Tussen](https://msdn.microsoft.com/library/ms187922.aspx)
* [Bestaat](https://msdn.microsoft.com/library/ms188336.aspx)
* [IN](https://msdn.microsoft.com/library/ms177682.aspx)
* [IS [NIET] NULL](https://msdn.microsoft.com/library/ms188795.aspx)
* [Als](https://msdn.microsoft.com/library/ms179859.aspx)
* [Niet](https://msdn.microsoft.com/library/ms189455.aspx)
* [Of](https://msdn.microsoft.com/library/ms188361.aspx)

### <a name="bitwise-operators"></a>Bitwise operators

* [& (Bitwise AND)](https://msdn.microsoft.com/library/ms174965.aspx)
* [| (Bitwise OR)](https://msdn.microsoft.com/library/ms186714.aspx)
* [^ (Exclusieve OR Bitwise)](https://msdn.microsoft.com/library/ms190277.aspx)
* [~ (Bitwise NIET)](https://msdn.microsoft.com/library/ms173468.aspx)
* [^= (Bitwise Exclusive OF EQUALS)](https://msdn.microsoft.com/library/cc627413.aspx)
* [|= (Bitwise OF EQUALS)](https://msdn.microsoft.com/library/cc627409.aspx)
* [&= (Bitwise AND EQUALS)](https://msdn.microsoft.com/library/cc627427.aspx)

## <a name="functions"></a>Functions

* [@@DATEFIRST](https://msdn.microsoft.com/library/ms187766.aspx)
* [@@ERROR](https://msdn.microsoft.com/library/ms188790.aspx)
* [@@LANGUAGE](https://msdn.microsoft.com/library/ms177557.aspx)
* [@@SPID](https://msdn.microsoft.com/library/ms189535.aspx)
* [@@TRANCOUNT](https://msdn.microsoft.com/library/ms187967.aspx)
* [@@VERSION](https://msdn.microsoft.com/library/ms177512.aspx)
* [Abs](https://msdn.microsoft.com/library/ms189800.aspx)
* [ACOS](https://msdn.microsoft.com/library/ms178627.aspx)
* [Ascii](https://msdn.microsoft.com/library/ms177545.aspx)
* [ASIN](https://msdn.microsoft.com/library/ms181581.aspx)
* [ATAN](https://msdn.microsoft.com/library/ms181746.aspx)
* [ATN2](https://msdn.microsoft.com/library/ms173854.aspx)
* [BINARY_CHECKSUM](https://msdn.microsoft.com/library/ms173784.aspx)
* [Geval](https://msdn.microsoft.com/library/ms181765.aspx)
* [CAST en CONVERT](https://msdn.microsoft.com/library/ms187928.aspx)
* [Plafond](https://msdn.microsoft.com/library/ms189818.aspx)
* [Char](https://msdn.microsoft.com/library/ms187323.aspx)
* [CHARINDEX](https://msdn.microsoft.com/library/ms186323.aspx)
* [Checksum](https://msdn.microsoft.com/library/ms189788.aspx)
* [COALESCE](https://msdn.microsoft.com/library/ms190349.aspx)
* [COL_NAME](https://msdn.microsoft.com/library/ms174974.aspx)
* [COLLATIONPROPERTY](https://msdn.microsoft.com/library/ms190305.aspx)
* [Concat](https://msdn.microsoft.com/library/hh231515.aspx)
* [COS](https://msdn.microsoft.com/library/ms188919.aspx)
* [COT](https://msdn.microsoft.com/library/ms188921.aspx)
* [Tellen](https://msdn.microsoft.com/library/ms175997.aspx)
* [COUNT_BIG](https://msdn.microsoft.com/library/ms190317.aspx)
* [CUME_DIST](https://msdn.microsoft.com/library/hh231078.aspx)
* [CURRENT_TIMESTAMP](https://msdn.microsoft.com/library/ms188751.aspx)
* [CURRENT_USER](https://msdn.microsoft.com/library/ms176050.aspx)
* [DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx)
* [GEGEVENSLENGTE](https://msdn.microsoft.com/library/ms173486.aspx)
* [DATEADD](https://msdn.microsoft.com/library/ms186819.aspx)
* [DATEDIFF](https://msdn.microsoft.com/library/ms189794.aspx)
* [DATUMUITDELEN](https://msdn.microsoft.com/library/hh213228.aspx)
* [DATENAME](https://msdn.microsoft.com/library/ms174395.aspx)
* [DATEPART](https://msdn.microsoft.com/library/ms174420.aspx)
* [DATUMTIJD2UITONDERDELEN](https://msdn.microsoft.com/library/hh213312.aspx)
* [DATETIMEFROMPARTS](https://msdn.microsoft.com/library/hh213233.aspx)
* [DATUMTIJDVERSCHUIVINGVAN ONDERDELEN](https://msdn.microsoft.com/library/hh231077.aspx)
* [Dag](https://msdn.microsoft.com/library/ms176052.aspx)
* [DB_ID](https://msdn.microsoft.com/library/ms186274.aspx)
* [DB_NAME](https://msdn.microsoft.com/library/ms189753.aspx)
* [DEGREES](https://msdn.microsoft.com/library/ms178566.aspx)
* [DENSE_RANK](https://msdn.microsoft.com/library/ms173825.aspx)
* [Verschil](https://msdn.microsoft.com/library/ms188753.aspx)
* [EOMONTH](https://msdn.microsoft.com/library/hh213020.aspx)
* [ERROR_MESSAGE](https://msdn.microsoft.com/library/ms190358.aspx)
* [ERROR_NUMBER](https://msdn.microsoft.com/library/ms175069.aspx)
* [ERROR_PROCEDURE](https://msdn.microsoft.com/library/ms188398.aspx)
* [ERROR_SEVERITY](https://msdn.microsoft.com/library/ms178567.aspx)
* [ERROR_STATE](https://msdn.microsoft.com/library/ms180031.aspx)
* [Exp](https://msdn.microsoft.com/library/ms179857.aspx)
* [FIRST_VALUE](https://msdn.microsoft.com/library/hh213018.aspx)
* [Verdieping](https://msdn.microsoft.com/library/ms178531.aspx)
* [GETDATE](https://msdn.microsoft.com/library/ms188383.aspx)
* [GETUTCDATE](https://msdn.microsoft.com/library/ms178635.aspx)
* [HAS_DBACCESS](https://msdn.microsoft.com/library/ms187718.aspx)
* [HASHBYTES](https://msdn.microsoft.com/library/ms174415.aspx)
* [INDEXEIGENSCHAP](https://msdn.microsoft.com/library/ms187729.aspx)
* [ISDATE](https://msdn.microsoft.com/library/ms187347.aspx)
* [Isnull](https://msdn.microsoft.com/library/ms184325.aspx)
* [ISNUMERIEK](https://msdn.microsoft.com/library/ms186272.aspx)
* [LAG](https://msdn.microsoft.com/library/hh231256.aspx)
* [LAST_VALUE](https://msdn.microsoft.com/library/hh231517.aspx)
* [Leiden](https://msdn.microsoft.com/library/hh213125.aspx)
* [LEFT](https://msdn.microsoft.com/library/ms177601.aspx)
* [LEN](https://msdn.microsoft.com/library/ms190329.aspx)
* [Log](https://msdn.microsoft.com/library/ms190319.aspx)
* [LOG10](https://msdn.microsoft.com/library/ms175121.aspx)
* [LOWER](https://msdn.microsoft.com/library/ms174400.aspx)
* [LTRIM](https://msdn.microsoft.com/library/ms177827.aspx)
* [Max](https://msdn.microsoft.com/library/ms187751.aspx)
* [Min](https://msdn.microsoft.com/library/ms179916.aspx)
* [Maand](https://msdn.microsoft.com/library/ms187813.aspx)
* [Nchar](https://msdn.microsoft.com/library/ms182673.aspx)
* [NTILE (NTILE)](https://msdn.microsoft.com/library/ms175126.aspx)
* [NULLIF (NULLIF)](https://msdn.microsoft.com/library/ms177562.aspx)
* [OBJECT_ID](https://msdn.microsoft.com/library/ms190328.aspx)
* [OBJECT_NAME](https://msdn.microsoft.com/library/ms186301.aspx)
* [OBJECTEIGENSCHAP OBJECT](https://msdn.microsoft.com/library/ms176105.aspx)
* [OIBJECTPROPERTYEX](https://msdn.microsoft.com/library/ms188390.aspx)
* [ODBCS-scalaire functies](https://msdn.microsoft.com/library/bb630290.aspx)
* [OVER-clausule](https://msdn.microsoft.com/library/ms189461.aspx)
* [PARSENAME](https://msdn.microsoft.com/library/ms188006.aspx)
* [PATINDEX](https://msdn.microsoft.com/library/ms188395.aspx)
* [PERCENTILE_CONT](https://msdn.microsoft.com/library/hh231473.aspx)
* [PERCENTILE_DISC](https://msdn.microsoft.com/library/hh231327.aspx)
* [PERCENT_RANK](https://msdn.microsoft.com/library/hh213573.aspx)
* [PI](https://msdn.microsoft.com/library/ms189512.aspx)
* [POWER](https://msdn.microsoft.com/library/ms174276.aspx)
* [QUOTENAAM](https://msdn.microsoft.com/library/ms176114.aspx)
* [RADIANS](https://msdn.microsoft.com/library/ms189742.aspx)
* [RAND](https://msdn.microsoft.com/library/ms177610.aspx)
* [Rang](https://msdn.microsoft.com/library/ms176102.aspx)
* [Vervangen](https://msdn.microsoft.com/library/ms186862.aspx)
* [REPLICEREN](https://msdn.microsoft.com/library/ms174383.aspx)
* [REVERSE](https://msdn.microsoft.com/library/ms180040.aspx)
* [RIGHT](https://msdn.microsoft.com/library/ms177532.aspx)
* [Ronde](https://msdn.microsoft.com/library/ms175003.aspx)
* [ROW_NUMBER](https://msdn.microsoft.com/library/ms186734.aspx)
* [RTRIM](https://msdn.microsoft.com/library/ms178660.aspx)
* [SCHEMA_ID](https://msdn.microsoft.com/library/ms188797.aspx)
* [SCHEMA_NAME](https://msdn.microsoft.com/library/ms175068.aspx)
* [SERVEREIGENSCHAP SERVEREIGENSCHAP](https://msdn.microsoft.com/library/ms174396.aspx)
* [SESSION_USER](https://msdn.microsoft.com/library/ms177587.aspx)
* [SIGN](https://msdn.microsoft.com/library/ms188420.aspx)
* [Zonde](https://msdn.microsoft.com/library/ms188377.aspx)
* [SMALLDATETIMEFROMPARTS](https://msdn.microsoft.com/library/hh213396.aspx)
* [SOUNDEX (SOUNDEX)](https://msdn.microsoft.com/library/ms187384.aspx)
* [Ruimte](https://msdn.microsoft.com/library/ms187950.aspx)
* [SQL_VARIANT_PROPERTY](https://msdn.microsoft.com/library/ms178550.aspx)
* [Sqrt](https://msdn.microsoft.com/library/ms176108.aspx)
* [SQUARE](https://msdn.microsoft.com/library/ms173569.aspx)
* [STATS_DATE](https://msdn.microsoft.com/library/ms190330.aspx)
* [STDEV](https://msdn.microsoft.com/library/ms190474.aspx)
* [STDEVP](https://msdn.microsoft.com/library/ms176080.aspx)
* [Str](https://msdn.microsoft.com/library/ms189527.aspx)
* [Spullen](https://msdn.microsoft.com/library/ms188043.aspx)
* [SUBSTRING](https://msdn.microsoft.com/library/ms187748.aspx)
* [Som](https://msdn.microsoft.com/library/ms187810.aspx)
* [SUSER_SNAME](https://msdn.microsoft.com/library/ms174427.aspx)
* [SWITCHOFFSET](https://msdn.microsoft.com/library/bb677244.aspx)
* [SYSDATETIME (SYSDATETIME)](https://msdn.microsoft.com/library/bb630353.aspx)
* [SYSDATETIMEOFFSET](https://msdn.microsoft.com/library/bb677334.aspx)
* [SYSTEM_USER](https://msdn.microsoft.com/library/ms179930.aspx)
* [SYSUTCDATETIME](https://msdn.microsoft.com/library/bb630387.aspx)
* [TAN](https://msdn.microsoft.com/library/ms190338.aspx)
* [TERTIARY_WEIGHTS](https://msdn.microsoft.com/library/ms186881.aspx)
* [TIMEFROMPARTS](https://msdn.microsoft.com/library/hh213398.aspx)
* [TOT HEDENTIJDCOMPENSATIE](https://msdn.microsoft.com/library/bb630335.aspx)
* [TYPE_ID](https://msdn.microsoft.com/library/ms181628.aspx)
* [TYPE_NAME](https://msdn.microsoft.com/library/ms189750.aspx)
* [TYPEEIGENSCHAP](https://msdn.microsoft.com/library/ms188419.aspx)
* [Unicode](https://msdn.microsoft.com/library/ms180059.aspx)
* [UPPER](https://msdn.microsoft.com/library/ms180055.aspx)
* [GEBRUIKER](https://msdn.microsoft.com/library/ms186738.aspx)
* [USER_NAME](https://msdn.microsoft.com/library/ms188014.aspx)
* [VAR](https://msdn.microsoft.com/library/ms186290.aspx)
* [VARP](https://msdn.microsoft.com/library/ms188735.aspx)
* [Jaar](https://msdn.microsoft.com/library/ms186313.aspx)
* [XACT_STATE](https://msdn.microsoft.com/library/ms189797.aspx)

## <a name="transactions"></a>Transacties

* [Transacties](https://msdn.microsoft.com/library/mt204031.aspx)

## <a name="diagnostic-sessions"></a>Diagnostische sessies

* [DIAGNOSTISCHE SESSIE MAKEN](https://msdn.microsoft.com/library/mt204029.aspx)

## <a name="procedures"></a>Procedures

* [sp_addrolemember](https://msdn.microsoft.com/library/ms187750.aspx)
* [sp_columns](https://msdn.microsoft.com/library/ms176077.aspx)
* [sp_configure](https://msdn.microsoft.com/library/ms188787.aspx)
* [sp_datatype_info_90](https://msdn.microsoft.com/library/mt204014.aspx)
* [sp_droprolemember](https://msdn.microsoft.com/library/ms188369.aspx)
* [sp_execute](https://msdn.microsoft.com/library/ff848746.aspx)
* [sp_executesql](https://msdn.microsoft.com/library/ms188001.aspx)
* [sp_fkeys](https://msdn.microsoft.com/library/ms175090.aspx)
* [sp_pdw_add_network_credentials](https://msdn.microsoft.com/library/mt204011.aspx)
* [sp_pdw_database_encryption](https://msdn.microsoft.com/library/mt219360.aspx)
* [sp_pdw_database_encryption_regenerate_system_keys](https://msdn.microsoft.com/library/mt204033.aspx)
* [sp_pdw_log_user_data_masking](https://msdn.microsoft.com/library/mt204023.aspx)
* [sp_pdw_remove_network_credentials](https://msdn.microsoft.com/library/mt204038.aspx)
* [sp_pkeys](https://msdn.microsoft.com/library/ms189813.aspx)
* [sp_prepare](https://msdn.microsoft.com/library/ff848808.aspx)
* [sp_spaceused](https://msdn.microsoft.com/library/ms188776.aspx)
* [sp_special_columns_100](https://msdn.microsoft.com/library/mt204025.aspx)
* [sp_sproc_columns](https://msdn.microsoft.com/library/ms182705.aspx)
* [sp_statistics](https://msdn.microsoft.com/library/ms173842.aspx)
* [sp_tables](https://msdn.microsoft.com/library/ms186250.aspx)
* [sp_unprepare](https://msdn.microsoft.com/library/ff848735.aspx)

## <a name="set-statements"></a>SET-instructies

* [STEL ANSI_DEFAULTS IN](https://msdn.microsoft.com/library/ms188340.aspx)
* [SET ANSI_NULL_DFLT_OFF](https://msdn.microsoft.com/library/ms187356.aspx)
* [Stel ANSI_NULL_DFLT_ON IN](https://msdn.microsoft.com/library/ms187375.aspx)
* [STEL ANSI_NULLS IN](https://msdn.microsoft.com/library/ms188048.aspx)
* [SET ANSI_PADDING](https://msdn.microsoft.com/library/ms187403.aspx)
* [Stel ANSI_WARNINGS in](https://msdn.microsoft.com/library/ms190368.aspx)
* [ARITHABORT INSTELLEN](https://msdn.microsoft.com/library/ms190306.aspx)
* [ARITHIGNORE INSTELLEN](https://msdn.microsoft.com/library/ms184341.aspx)
* [SET CONCAT_NULL_YIELDS_NULL](https://msdn.microsoft.com/library/ms176056.aspx)
* [DATUM EERST INSTELLEN](https://msdn.microsoft.com/library/ms181598.aspx)
* [DATUMNOTATIE INSTELLEN](https://msdn.microsoft.com/library/ms189491.aspx)
* [FMTONLY INSTELLEN](https://msdn.microsoft.com/library/ms173839.aspx)
* [Stel IMPLICIT_TRANSACITONS](https://msdn.microsoft.com/library/ms187807.aspx)
* [Stel LOCK_TIMEOUT in](https://msdn.microsoft.com/library/ms189470.aspx)
* [NUMBERIC_ROUNDABORT INSTELLEN](https://msdn.microsoft.com/library/ms188791.aspx)
* [Stel QUOTED_IDENTIFIER](https://msdn.microsoft.com/library/ms174393.aspx)
* [RIJNUMMER INSTELLEN](https://msdn.microsoft.com/library/ms188774.aspx)
* [TEKSTGROOTTE INSTELLEN](https://msdn.microsoft.com/library/ms186238.aspx)
* [TRANSACTIEISOLATIENIVEAU INSTELLEN](https://msdn.microsoft.com/library/ms173763.aspx)
* [SET XACT_ABORT](https://msdn.microsoft.com/library/ms188792.aspx)

## <a name="next-steps"></a>Volgende stappen

Zie [T-SQL-instructies in Synapse SQL-pool](sql-data-warehouse-reference-tsql-statements.md)en [Systeemweergaven in Synapse SQL-pool](sql-data-warehouse-reference-tsql-system-views.md)voor meer referentiegegevens.

