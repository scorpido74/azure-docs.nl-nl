---
title: De data base oplossen Azure Database for MySQL beschadigd
description: Meer informatie over het oplossen van beschadigde database problemen voor Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 815b24d25e674e5460cc50d7eb6871f740994893
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935936"
---
# <a name="troubleshoot-database-corruption-on-azure-database-for-mysql"></a>Problemen met de data base oplossen op Azure Database for MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Het gebruik van een Data Base kan uitval tijd voor uw toepassing veroorzaken. het is ook cruciaal om het probleem op tijd op te lossen om gegevens verlies te voor komen. Wanneer de data base is beschadigd, ziet u in de server logboeken deze fout **InnoDB: de database pagina is beschadigd op de schijf of een mislukt**.

In deze hand leiding wordt beschreven hoe u kunt oplossen of de data base of tabel is beschadigd. Azure Database for MySQL maakt gebruik van de InnoDB-engine en de IT-functies automatische beschadigings controle en herstel bewerkingen. InnoDB controleert op beschadigde pagina's door controle sommen uit te voeren op elke pagina die wordt gelezen en als er een controlesom verschil wordt gevonden, wordt de MySQL-server automatisch gestopt.

Voer een van deze opties hieronder uit om snel problemen met uw Data Base op te lossen.

## <a name="restart-your-mysql-server"></a>De MySQL-server opnieuw opstarten

Normaal gesp roken ziet u dat een Data Base of tabel is beschadigd wanneer uw toepassing toegang heeft tot de tabel ro-data base. Omdat InnoDB een mechanisme voor crash herstel bevat dat de meeste problemen kan oplossen wanneer de server opnieuw wordt opgestart. Daarom moet de server opnieuw worden opgestart om de server te helpen herstellen van een crash waardoor de status van de data base niet is beschadigd.

##  <a name="resolve-data-corruption-with-dump-and-restore-method"></a>Gegevens beschadiging oplossen met de methode dump en herstel

Het is raadzaam om het beschadigings probleem met een **dump-en herstel methode**op te lossen. Dit betekent dat u toegang hebt tot de beschadigde tabel met behulp van het hulp programma **mysqldump** om een logische back-up te maken van de tabel, waardoor de tabel structuur en de gegevens erin worden bewaard en de tabel vervolgens opnieuw wordt geladen in de data base.

### <a name="backup-your-database-or-tables"></a>Back-up maken van uw data base of tabellen

> [!Important]
> - Zorg ervoor dat u een firewall regel hebt geconfigureerd om toegang te krijgen tot de server vanaf uw client computer. Zie [firewall regel configureren op één server](howto-manage-firewall-using-portal.md) en [firewall regel op een flexibele server](flexible-server/how-to-connect-tls-ssl.md).
> - SSL-optie gebruiken ```--ssl-cert``` voor **MYSQLDUMP** als SSL is ingeschakeld

Maak een back-upbestand van de opdracht regel met behulp van mysqldump met behulp van deze opdracht

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

De para meters die u moet opgeven, zijn:
- [SSL-cert =/Path/to/PEM] Down load het SSL-certificaat op uw client computer en stel het pad in de opdracht in. Gebruik niet dit is SSL is uitgeschakeld.
- [host] is uw Azure Database for MySQL-server
- [uname] is de gebruikers naam van de server beheerder
- [Pass] is het wacht woord voor de gebruiker met beheerders rechten
- [dbname] is de naam van uw data base
- [upbestand. SQL] als de bestands naam van de back-up van uw data base

> [!Important]
> - Voor een enkele server gebruikt u de indeling ```admin-user@servername``` om ```myserveradmin``` de volgende opdrachten te vervangen.
> - Voor een flexibele server gebruikt u de indeling ```admin-user``` om ```myserveradmin``` de volgende opdrachten te vervangen.

Als een specifieke tabel is beschadigd, selecteert u specifieke tabellen in uw data base om een back-up te maken met dit voor beeld
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

Als u een back-up wilt maken van een of meer data bases, gebruikt u de--database switch en geeft u de database namen op, gescheiden door spaties.

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

###  <a name="restore-your-database-or-tables"></a>Uw data base of tabellen herstellen

In de volgende stappen ziet u hoe TP uw data base of tabellen herstelt. Zodra het back-upbestand is gemaakt, kunt u de tabel of data bases herstellen met het hulp programma ***MySQL** . Voer de opdracht uit zoals hieronder wordt weer gegeven:

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Hier volgt een voor beeld van het terugzetten ```testdb``` van een back-upbestand dat is gemaakt met **mysqldump**. 

> [!Important]
> - Voor een enkele server gebruikt u de indeling ```admin-user@servername``` die u ```myserveradmin``` in de onderstaande opdracht wilt vervangen.
> - Voor een flexibele server gebruikt u de indeling ```admin-user``` die u ```myserveradmin``` in de onderstaande opdracht wilt vervangen. 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>Volgende stappen
Als de bovenstaande stappen niet helpen om het probleem op te lossen, kunt u altijd de volledige server herstellen.
- [Een Azure Database for MySQL één server herstellen](howto-restore-server-portal.md)
- [Een Azure Database for MySQL flexibele server herstellen](flexible-server/how-to-restore-server-portal.md)



