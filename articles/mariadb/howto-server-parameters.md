---
title: Serverparameters configureren - Azure portal - Azure Database voor MariaDB
description: In dit artikel wordt beschreven hoe u MariaDB-serverparameters in Azure Database voor MariaDB configureert met behulp van de Azure-portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 9d057a4be02d8d93d3ef02ee3153baebe9146ff1
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632716"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Serverparameters configureren in Azure Database voor MariaDB met behulp van de Azure-portal

Azure Database voor MariaDB ondersteunt configuratie van sommige serverparameters. In dit artikel wordt beschreven hoe u deze parameters configureert met behulp van de Azure-portal. Niet alle serverparameters kunnen worden aangepast.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigeren naar serverparameters op Azure-portal

1. Meld u aan bij de Azure-portal en zoek vervolgens uw Azure Database voor MariaDB-server.
2. Klik onder de sectie **INSTELLINGEN** op **Serverparameters** om de pagina serverparameters voor de Azure Database voor MariaDB-server te openen.
![Pagina Parameters azure portalserver](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Zoek alle instellingen die u moet aanpassen. Bekijk de kolom **Beschrijving** om het doel en de toegestane waarden te begrijpen.
![Opsommen daling naar beneden](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klik **op Opslaan** om de wijzigingen op te slaan.
![Wijzigingen opslaan of verwijderen](./media/howto-server-parameters/4-save_parameters.png)
5. Als u nieuwe waarden voor de parameters hebt opgeslagen, u alles altijd terugzetten naar de standaardwaarden door **Alles opnieuw instellen op standaard te selecteren.**
![Alles opnieuw instellen op standaard](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lijst met configureerbare serverparameters

De lijst met ondersteunde serverparameters wordt voortdurend uitgebreid. Gebruik het tabblad serverparameters in azure-portal om de definitie te krijgen en serverparameters te configureren op basis van uw toepassingsvereisten.

## <a name="non-configurable-server-parameters"></a>Niet-configureerbare serverparameters

InnoDB Buffer Pool en Max Connections zijn niet configureerbaar en gekoppeld aan uw [prijscategorie.](concepts-pricing-tiers.md)

|**Prijsniveau**| **vCore(s)**|**InnoDB-buffergroep (MB)**|
|---|---|---|
|Basic| 1| 1024|
|Basic| 2| 2560|
|Algemeen gebruik| 2| 3584|
|Algemeen gebruik| 4| 7680|
|Algemeen gebruik| 8| 15360|
|Algemeen gebruik| 16| 31232|
|Algemeen gebruik| 32| 62976|
|Algemeen gebruik| 64| 125952|
|Geoptimaliseerd geheugen| 2| 7168|
|Geoptimaliseerd geheugen| 4| 15360|
|Geoptimaliseerd geheugen| 8| 30720|
|Geoptimaliseerd geheugen| 16| 62464|
|Geoptimaliseerd geheugen| 32| 125952|

Deze extra serverparameters kunnen niet worden geconfigureerd in het systeem:

|**Parameter**|**Vaste waarde**|
| :------------------------ | :-------- |
|innodb_file_per_table in basislaag|UIT|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Andere serverparameters die hier niet worden vermeld, zijn ingesteld op de standaardwaarden van MariaDB out-of-box voor [MariaDB.](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/)

## <a name="working-with-the-time-zone-parameter"></a>Werken met de parameter tijdzone

### <a name="populating-the-time-zone-tables"></a>De tijdzonetabellen vullen

De tijdzonetabellen op uw server kunnen worden `mysql.az_load_timezone` ingevuld door de opgeslagen procedure aan te roepen vanuit een hulpprogramma zoals de MySQL-opdrachtregel of MySQL Workbench.

> [!NOTE]
> Als u de `mysql.az_load_timezone` opdracht uitvoert vanuit MySQL Workbench, moet u `SET SQL_SAFE_UPDATES=0;`mogelijk eerst de veilige updatemodus uitschakelen met behulp van .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> U moet de server opnieuw starten om ervoor te zorgen dat de tijdzonetabellen goed worden gevuld. Als u de server opnieuw wilt starten, gebruikt u de [Azure-portal](howto-restart-server-portal.md) of [CLI](howto-restart-server-cli.md).
Voer de volgende opdracht uit om beschikbare tijdzonewaarden weer te geven:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>De tijdzone op mondiaal niveau instellen

De tijdzone op globaal niveau kan worden ingesteld vanaf de pagina **Serverparameters** in de Azure-portal. Onderstaand stelt de globale tijdzone in op de waarde "US/Pacific".

![Parameter tijdzone instellen](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>De tijdzone op sessieniveau instellen

De tijdzone op sessieniveau kan `SET time_zone` worden ingesteld door de opdracht uit te voeren vanuit een hulpprogramma zoals de Opdrachtregel MySQL of MySQL Workbench. In het onderstaande voorbeeld wordt de tijdzone ingesteld op de tijdzone **VS/Pacific.**

```sql
SET time_zone = 'US/Pacific';
```

Raadpleeg de MariaDB-documentatie voor [datum- en tijdfuncties.](https://mariadb.com/kb/en/library/convert_tz/)

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->
