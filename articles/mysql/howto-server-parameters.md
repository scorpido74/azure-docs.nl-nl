---
title: Server parameters configureren-Azure Portal-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u MySQL-serverparameters configureren in Azure Database voor MySQL met behulp van de Azure-portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 8ec6f32d7db0161cef00330aa38601ba9bdb309d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78392704"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Parameters van de server configureren in Azure Database voor MySQL met behulp van Azure portal

Azure Database for MySQL ondersteunt de configuratie van bepaalde parameters van de server. In dit artikel wordt beschreven hoe u deze parameters configureren met behulp van de Azure-portal. Niet alle parameters van de server kunnen worden aangepast.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigeer naar de Parameters van de Server in Azure portal

1. Meld u aan bij Azure portal en zoek uw Azure Database voor MySQL-server.
2. Klik onder de sectie **instellingen** op **server parameters** om de pagina server parameters voor de Azure database for mysql-server te openen.
![pagina Azure Portal Server parameters](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Zoek alle instellingen die u nodig hebt om aan te passen. Bekijk de kolom **Beschrijving** om inzicht te krijgen in het doel en de toegestane waarden.
![vervolg keuzelijst opsommen](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klik op **Opslaan** om uw wijzigingen op te slaan.
wijzigingen ![opslaan of negeren](./media/howto-server-parameters/4-save_parameters.png)
5. Als u nieuwe waarden voor de para meters hebt opgeslagen, kunt u altijd terugkeren naar de standaard waarden door **alles opnieuw instellen op de standaard**waarde te selecteren.
![alles opnieuw instellen op de standaard](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lijst met parameters van de server kunnen worden geconfigureerd

De lijst met ondersteunde serverparameters groeit voortdurend. Gebruik het tabblad van de parameters-server in Azure portal naar de definitie en parameters van de server op basis van de toepassingsvereisten van uw configureren.

## <a name="non-configurable-server-parameters"></a>Niet-configureerbare serverparameters

De grootte van de InnoDB-buffer groep kan niet worden geconfigureerd en zijn gekoppeld aan uw [prijs categorie](concepts-service-tiers.md).

|**Prijscategorie**|**vCore (s)**|**Grootte van InnoDB-buffer groep in MB <br>(servers die ondersteuning bieden voor Maxi maal 4 TB opslag)**| **Grootte van InnoDB-buffer groep in MB <br>(servers die ondersteuning bieden voor Maxi maal 16 TB opslag)**|
|:---|---:|---:|---:|
|Basic| 1| 832| |
|Basic| 2| 2560| |
|Algemeen gebruik| 2| 3584| 7168|
|Algemeen gebruik| 4| 7680| 15360|
|Algemeen gebruik| 8| 15360| 30720|
|Algemeen gebruik| 16| 31232| 62464|
|Algemeen gebruik| 32| 62976| 125952|
|Algemeen gebruik| 64| 125952| 251904|
|Geoptimaliseerd geheugen| 2| 7168| 14336|
|Geoptimaliseerd geheugen| 4| 15360| 30720|
|Geoptimaliseerd geheugen| 8| 30720| 61440|
|Geoptimaliseerd geheugen| 16| 62464| 124928|
|Geoptimaliseerd geheugen| 32| 125952| 251904|

Deze extra server-parameters zijn niet kunnen worden geconfigureerd in het systeem:

|**Bepaalde**|**Vaste waarde**|
| :------------------------ | :-------- |
|innodb_file_per_table in Basic-laag|UIT|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Andere server parameters die hier niet worden vermeld, worden ingesteld op de standaard waarden van de MySQL-out-of-Box voor versie [5,7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) en [5,6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="working-with-the-time-zone-parameter"></a>Werken met de parameter tijdzone

### <a name="populating-the-time-zone-tables"></a>Invullen van de tijdzone-tabellen

De tijd zone tabellen op uw server kunnen worden gevuld door de `az_load_timezone` opgeslagen procedure aan te roepen vanuit een hulp programma zoals de MySQL-opdracht regel of MySQL Workbench.

> [!NOTE]
> Als u de `az_load_timezone`-opdracht uit MySQL Workbench uitvoert, moet u de veilige update modus mogelijk eerst uitschakelen met behulp van `SET SQL_SAFE_UPDATES=0;`.

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

De tijd zone op het globale niveau kan worden ingesteld op de pagina **server parameters** in de Azure Portal. De onderstaande stelt de globale tijdzone op de waarde "VS / Stille Oceaan '.

![De tijdzoneparameter instellen](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>De sessie niveau tijdzone instellen

De tijd zone op sessie niveau kan worden ingesteld door de `SET time_zone` opdracht uit te voeren vanuit een hulp programma zoals de MySQL-opdracht regel of MySQL Workbench. In het volgende voor beeld wordt de tijd zone ingesteld op de **Amerikaanse/Pacific-** tijd zone.

```sql
SET time_zone = 'US/Pacific';
```

Raadpleeg de MySQL-documentatie voor [datum-en tijd functies](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Volgende stappen

- [Verbindings bibliotheken voor Azure database for MySQL](concepts-connection-libraries.md).
