---
title: Server parameters configureren-Azure Portal-Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u MariaDB-server parameters in Azure Database for MariaDB kunt configureren met behulp van de Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 59d18ea11699ed77763c162e4930b159fcd19fe2
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888662"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Server parameters configureren in Azure Database for MariaDB met behulp van de Azure Portal

Azure Database for MariaDB ondersteunt de configuratie van sommige server parameters. In dit artikel wordt beschreven hoe u deze parameters configureren met behulp van de Azure-portal. Niet alle parameters van de server kunnen worden aangepast.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigeer naar de Parameters van de Server in Azure portal

1. Meld u aan bij de Azure Portal en zoek vervolgens de Azure Database for MariaDB-server.
2. Klik onder de sectie **instellingen** op **server parameters** om de pagina server parameters voor de Azure database for MariaDB-server te openen.
![Pagina van de parameters voor Azure portal-server](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Zoek alle instellingen die u nodig hebt om aan te passen. Controleer de **beschrijving** kolom om te begrijpen van het doel en de toegestane waarden.
![Vervolgkeuzelijst omlaag opsommen](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klik op **opslaan** uw wijzigingen op te slaan.
![Opslaan of wijzigingen negeren](./media/howto-server-parameters/4-save_parameters.png)
5. Als u nieuwe waarden voor de parameters hebt opgeslagen, kunt u altijd terugkeren alles weer op de standaardwaarden hiervoor **alle standaardinstellingen opnieuw instellen**.
![Alle standaardinstellingen opnieuw instellen](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lijst met parameters van de server kunnen worden geconfigureerd

De lijst met ondersteunde serverparameters groeit voortdurend. Gebruik het tabblad van de parameters-server in Azure portal naar de definitie en parameters van de server op basis van de toepassingsvereisten van uw configureren.

## <a name="non-configurable-server-parameters"></a>Niet-configureerbare serverparameters

InnoDB-buffergroep en maximum aantal verbindingen zijn niet kunnen worden geconfigureerd en gekoppeld aan uw [prijscategorie](concepts-pricing-tiers.md).

|**Prijscategorie**| **vCore(s)**|**InnoDB buffergroep (MB)**| **Maximum aantal verbindingen**|
|---|---|---|---|
|Basic| 1| 1024| 50|
|Basic| 2| 2560| 100|
|Algemeen doel| 2| 3584| 300|
|Algemeen doel| 4| 7680| 625|
|Algemeen doel| 8| 15360| 1250|
|Algemeen doel| 16| 31232| 2500|
|Algemeen doel| 32| 62976| 5000|
|Algemeen doel| 64| 125952| 10.000|
|Geoptimaliseerd geheugen| 2| 7168| 600|
|Geoptimaliseerd geheugen| 4| 15360| 1250|
|Geoptimaliseerd geheugen| 8| 30720| 2500|
|Geoptimaliseerd geheugen| 16| 62464| 5000|
|Geoptimaliseerd geheugen| 32| 125952| 10.000|

Deze extra server-parameters zijn niet kunnen worden geconfigureerd in het systeem:

|**Parameter**|**Vaste waarde**|
| :------------------------ | :-------- |
|innodb_file_per_table in Basic-laag|UIT|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Andere server parameters die hier niet worden vermeld, worden ingesteld op hun MariaDB-out-of-Box-standaard waarden voor [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/).

## <a name="working-with-the-time-zone-parameter"></a>Werken met de parameter tijdzone

### <a name="populating-the-time-zone-tables"></a>Invullen van de tijdzone-tabellen

De tijdzone-tabellen op de server kunnen worden gevuld door het aanroepen van de `az_load_timezone` opgeslagen procedure van een hulpprogramma zoals de MySQL-opdrachtregel of MySQL Workbench.

> [!NOTE]
> Als u werkt met de `az_load_timezone` opdracht via MySQL Workbench, moet u de veilige modus eerst uitschakelen met behulp van `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> U moet de server opnieuw opstarten om te controleren of de tabellen van de tijd zone juist zijn ingevuld. Gebruik de [Azure Portal](howto-restart-server-portal.md) of [cli](howto-restart-server-cli.md)om de server opnieuw op te starten.
Als u wilt weergeven van waarden van de beschikbare tijd zone, moet u de volgende opdracht uitvoeren:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>De globale niveau tijdzone instellen

De globale niveau tijdzone kan worden ingesteld van de **serverparameters** pagina in de Azure portal. De onderstaande stelt de globale tijdzone op de waarde "VS / Stille Oceaan '.

![De tijdzoneparameter instellen](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>De sessie niveau tijdzone instellen

De sessie niveau time zone kan worden ingesteld door het uitvoeren van de `SET time_zone` opdracht uit vanaf een hulpprogramma zoals de MySQL-opdrachtregel of MySQL Workbench. In het volgende voorbeeld wordt de tijdzone ingesteld op de **VS / Stille Oceaan** tijdzone.

```sql
SET time_zone = 'US/Pacific';
```

Raadpleeg de MariaDB-documentatie voor [datum-en tijd functies](https://mariadb.com/kb/en/library/convert_tz/).

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->
