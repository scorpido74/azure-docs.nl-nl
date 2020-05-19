---
title: EXTERNE stroom wijzigen (Transact-SQL)-Azure SQL Edge (preview)
description: Meer informatie over de instructie ALTER EXTERNAL STREAM in Azure SQL Edge (preview)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 0183972b5eb92d3f081b857940609bffc183b331
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597938"
---
# <a name="alter-external-stream-transact-sql"></a>EXTERNE stroom wijzigen (Transact-SQL)

Hiermee wijzigt u de definitie van een externe stroom. Het is niet toegestaan een externe stroom te wijzigen die wordt gebruikt door een streaming-taak in een *actieve* status. 



## <a name="syntax"></a>Syntaxis

```sql
  ALTER EXTERNAL STREAM external_stream_name 
  SET 
    [DATA_SOURCE] = <data_source_name> 
    , LOCATION = <location_name> 
    , EXTERNAL_FILE_FORMAT = <external_file_format_name> 
    , INPUT_OPTIONS = <input_options> 
    , OUTPUT_OPTIONS = <output_options> 
```

## <a name="arguments"></a>Argumenten

Zie [externe stream maken (Transact-SQL)](create-external-stream-transact-sql.md)voor meer informatie over de para meters van de opdracht ALTER External stream.

## <a name="return-code-values"></a>Retour code waarden

ALTER EXTERNAL STREAM retourneert 0 als dit is gelukt. Een geretourneerde waarde die niet gelijk is aan nul duidt op een fout.


## <a name="see-also"></a>Zie ook

- [EXTERNE STREAM maken (Transact-SQL)](create-external-stream-transact-sql.md) 
- [EXTERNE stroom verwijderen (Transact-SQL)](drop-external-stream-transact-sql.md) 
