---
title: Problemen met queryprestaties oplossen - Azure Database voor MariaDB
description: Meer informatie over het gebruik van EXPLAIN om problemen met queryprestaties in Azure Database voor MariaDB op te lossen.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: b06fe37b63494eb4ee0ca680733a801c26415d67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530050"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mariadb"></a>Explain gebruiken om queryprestaties te profileren in Azure Database voor MariaDB
**EXPLAIN** is een handig hulpmiddel om query's te optimaliseren. Explain-instructie kan worden gebruikt om informatie te krijgen over hoe SQL-instructies worden uitgevoerd. De volgende uitvoer toont een voorbeeld van de uitvoering van een EXPLAIN-instructie.

```sql
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 10.00
        Extra: Using where
```

Zoals uit dit voorbeeld blijkt, is de waarde van *de sleutel* NULL. Deze uitvoer betekent dat MariaDB geen indexen kan vinden die zijn geoptimaliseerd voor de query en een volledige tabelscan uitvoert. Laten we deze query optimaliseren door een index toe te voegen aan de **kolom ID.**

```sql
mysql> ALTER TABLE tb1 ADD KEY (id);
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ref
possible_keys: id
          key: id
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
```

De nieuwe EXPLAIN laat zien dat MariaDB nu een index gebruikt om het aantal rijen te beperken tot 1, wat op zijn beurt de zoektijd drastisch verkortte.
 
## <a name="covering-index"></a>Dekkingsindex
Een dekkingsindex bestaat uit alle kolommen van een query in de index om het ophalen van waarde uit gegevenstabellen te verminderen. Hier is een illustratie in de volgende **VERKLARING VAN GROEP DOOR.**
 
```sql
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Zoals uit de output blijkt, gebruikt MariaDB geen indexen omdat er geen goede indexen beschikbaar zijn. Het toont ook *het gebruik van tijdelijke; Met behulp van bestandssortering*maakt MariaDB een tijdelijke tabel om te voldoen aan de **GROUP BY-clausule.**
 
Het maken van een index op kolom **c2** alleen maakt geen verschil, en MariaDB moet nog steeds een tijdelijke tabel maken:

```sql 
mysql> ALTER TABLE tb1 ADD KEY (c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

In dit geval kan een **gedekte index** op zowel **c1** als **c2** worden gemaakt, waarbij de waarde van **c2**" direct in de index wordt toegevoegd om verdere gegevensopzoeken te elimineren.

```sql 
mysql> ALTER TABLE tb1 ADD KEY covered(c1,c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: covered
          key: covered
      key_len: 108
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using index
```

Zoals uit de bovenstaande EXPLAIN blijkt, gebruikt MariaDB nu de gedekte index en vermijdt het maken van een tijdelijke tabel. 

## <a name="combined-index"></a>Gecombineerde index
Een gecombineerde index bestaat uit waarden uit meerdere kolommen en kan worden beschouwd als een reeks rijen die worden gesorteerd op basis van de waarden van de geïndexeerde kolommen.Deze methode kan nuttig zijn in een **instructie GROUP BY.**

```sql
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using filesort
```

MariaDB voert een *bestandssorteerbewerking* uit die vrij traag is, vooral wanneer het veel rijen moet sorteren. Om deze query te optimaliseren, kan een gecombineerde index worden gemaakt op beide kolommen die worden gesorteerd.

```sql 
mysql> ALTER TABLE tb1 ADD KEY my_sort2 (c1, c2);
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: NULL
          key: my_sort2
      key_len: 108
          ref: NULL
         rows: 10
     filtered: 11.11
        Extra: Using where; Using index
```

De EXPLAIN laat nu zien dat MariaDB in staat is om gecombineerde index te gebruiken om extra sortering te voorkomen, omdat de index al is gesorteerd.
 
## <a name="conclusion"></a>Conclusie
 
Het gebruik van EXPLAIN en verschillende soorten indexen kan de prestaties aanzienlijk verhogen. Het hebben van een index op de tafel betekent niet noodzakelijkerwijs MariaDB zou kunnen gebruiken voor uw vragen. Valideer uw aannames altijd met EXPLAIN en optimaliseer uw query's met behulp van indexen.

## <a name="next-steps"></a>Volgende stappen
- Ga naar [het MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDatabaseforMariadb) of [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mariadb)om antwoorden op je meest bezorgde vragen te vinden of een nieuwe vraag/antwoord te plaatsen.
