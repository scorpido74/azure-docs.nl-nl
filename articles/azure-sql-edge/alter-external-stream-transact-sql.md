---
title: EXTERNE stroom wijzigen (Transact-SQL)-Azure SQL Edge (preview)
description: Meer informatie over de instructie ALTER EXTERNAL STREAM in Azure SQL Edge (preview)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 2559c4b4b875403b7c70671e27cb6222a3f1103a
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235199"
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
