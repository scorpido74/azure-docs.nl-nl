---
title: Back-up en herstel - Azure CLI - Azure Database voor MariaDB
description: Meer informatie over het maken van back-ups en het herstellen van een server in Azure Database voor MariaDB met behulp van de Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: 6faae80c78fe07d33579cc3fb7c76ce668969992
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369275"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mariadb-using-the-azure-cli"></a>Een back-up maken en een server herstellen in Azure Database voor MariaDB met behulp van de Azure CLI

Er wordt periodiek een back-up gemaakt van Azure Database voor MariaDB-servers om herstelfuncties in te schakelen. Met deze functie u de server en al zijn databases herstellen naar een eerder point-in-time, op een nieuwe server.

## <a name="prerequisites"></a>Vereisten

Om deze handleiding te voltooien, moet u het:

- Een [Azure-database voor MariaDB-server en -database](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Deze handleiding vereist dat u Azure CLI-versie 2.0 of hoger gebruikt. Als u de versie wilt bevestigen, `az --version`voert u bij de opdrachtprompt azure CLI de opdrachtprompt in. Zie Azure CLI [installeren]( /cli/azure/install-azure-cli).

## <a name="set-backup-configuration"></a>Back-upconfiguratie instellen

U maakt de keuze tussen het configureren van uw server voor lokaal redundante back-ups of geografisch redundante back-ups bij het maken van servers.

> [!NOTE]
> Nadat een server is gemaakt, kan het soort redundantie dat het heeft, geografisch redundant vs lokaal redundant, niet worden geschakeld.
>

Tijdens het maken `az mariadb server create` van een `--geo-redundant-backup` server via de opdracht bepaalt de parameter de optie Back-upredundantie. Als `Enabled`, geo redundante back-ups worden genomen. Of `Disabled` als lokaal redundante back-ups worden genomen.

De back-upbewaarperiode `--backup-retention`wordt ingesteld door de parameter .

Zie de [Azure Database voor MariaDB-server CLI Quickstart voor](quickstart-create-mariadb-server-database-using-azure-cli.md)meer informatie over het instellen van deze waarden tijdens het maken.

De back-upbewaarperiode van een server kan als volgt worden gewijzigd:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

In het voorgaande voorbeeld wordt de back-upbewaarperiode van mydemoserver gewijzigd in 10 dagen.

De back-upbewaarperiode bepaalt hoe ver terug in de tijd een point-in-time herstel kan worden opgehaald, omdat deze is gebaseerd op beschikbare back-ups. Point-in-time restore wordt verder beschreven in het volgende gedeelte.

## <a name="server-point-in-time-restore"></a>Serverpoint-in-time herstellen

U de server herstellen naar een eerder tijdstip. De herstelde gegevens worden gekopieerd naar een nieuwe server en de bestaande server blijft zoals het is. Als een tabel bijvoorbeeld vandaag om 12.00 uur per ongeluk wordt gedropt, u de tijd net voor het middaguur herstellen. Vervolgens u de ontbrekende tabel en gegevens ophalen uit de herstelde kopie van de server.

Als u de server wilt herstellen, gebruikt u de opdracht Voor het herstellen van de Azure CLI [az mariadb-server.](/cli/azure/mariadb/server#az-mariadb-server-restore)

### <a name="run-the-restore-command"></a>De opdracht Herstellen uitvoeren

Voer bij de opdrachtprompt Azure CLI de volgende opdracht in:

```azurecli-interactive
az mariadb server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

De `az mariadb server restore` opdracht vereist de volgende parameters:

| Instelling | Voorgestelde waarde | Beschrijving  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  De brongroep waarin de bronserver bestaat.  |
| name | mydemoserver-restored | De naam van de nieuwe server die door de opdracht restore is gemaakt. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Selecteer een punt in de tijd om naar te herstellen. Deze datum en tijd moet binnen de back-upretentieperiode van de bronserver vallen. Gebruik de datum- en tijdnotatie ISO8601. U bijvoorbeeld uw eigen lokale tijdzone `2018-03-13T05:59:00-08:00`gebruiken, zoals. U bijvoorbeeld ook de UTC Zulu-indeling `2018-03-13T13:59:00Z`gebruiken. |
| source-server | mydemoserver | De naam of ID van de bronserver voor het herstellen. |

WWanneer u een server herstelt naar een eerder tijdstip, wordt een nieuwe server gemaakt. De oorspronkelijke server en de bijbehorende databases vanaf het opgegeven tijdstip worden gekopieerd naar de nieuwe server.

De locatie- en prijsniveauwaarden voor de herstelde server blijven hetzelfde als de oorspronkelijke server. 

Nadat het herstelproces is voltooid, zoekt u de nieuwe server en controleert u of de gegevens zijn hersteld zoals verwacht. De nieuwe server heeft dezelfde serveradmin-inlognaam en -wachtwoord die geldig was voor de bestaande server op het moment dat het herstel werd gestart. Het wachtwoord kan worden gewijzigd vanaf de **pagina Overzicht** van de nieuwe server.

De nieuwe server die tijdens een herstel is gemaakt, heeft niet de VNet-serviceeindpunten die op de oorspronkelijke server bestonden. Deze regels moeten afzonderlijk worden ingesteld voor deze nieuwe server. Firewallregels van de oorspronkelijke server worden hersteld.

## <a name="geo-restore"></a>Geo-herstel

Als u uw server hebt geconfigureerd voor geografisch redundante back-ups, kan een nieuwe server worden gemaakt met de back-up van die bestaande server. Deze nieuwe server kan worden gemaakt in elke regio waar Azure Database voor MariaDB beschikbaar is.  

Als u een server wilt maken met `az mariadb server georestore` een georedundante back-up, gebruikt u de opdracht Azure CLI.

> [!NOTE]
> Wanneer een server voor het eerst wordt gemaakt, is deze mogelijk niet onmiddellijk beschikbaar voor geoherstel. Het kan enkele uren duren voordat de benodigde metagegevens zijn ingevuld.
>

Voer bij de opdrachtprompt Azure CLI de volgende opdracht in om de server te geoherstellen:

```azurecli-interactive
az mariadb server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen5_8
```

Met deze opdracht wordt een nieuwe server gemaakt die *mydemoserver-georestored in Oost-VS* wordt genoemd en die tot *myresourcegroup*zal behoren. Het is een General Purpose, Gen 5 server met 8 vCores. De server is gemaakt van de geo-redundante back-up van *mydemoserver*, die ook in de brongroep *myresourcegroup*

Als u de nieuwe server wilt maken in een andere brongroep dan de bestaande server, kwalificeert u in de `--source-server` parameter de servernaam als in het volgende voorbeeld:

```azurecli-interactive
az mariadb server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMariaDB/servers/mydemoserver" --location eastus --sku-name GP_Gen5_8

```

De `az mariadb server georestore` opdracht vereist de volgende parameters:

| Instelling | Voorgestelde waarde | Beschrijving  |
| --- | --- | --- |
|resource-group| myResourceGroup | De naam van de brongroep waartoe de nieuwe server behoort.|
|name | mydemoserver-georestored | De naam van de nieuwe server. |
|source-server | mydemoserver | De naam van de bestaande server waarvan de georedundante back-ups worden gebruikt. |
|location | eastus | De locatie van de nieuwe server. |
|sku-name| GP_Gen5_8 | Met deze parameter worden de prijscategorie, de rekengeneratie en het aantal vCores van de nieuwe server ingesteld. GP_Gen5_8 kaarten naar een General Purpose, Gen 5-server met 8 vCores.|

Bij het maken van een nieuwe server met een geoherstel, neemt deze dezelfde opslaggrootte en prijscategorie over als de bronserver. Deze waarden kunnen niet worden gewijzigd tijdens het maken. Nadat de nieuwe server is gemaakt, kan de opslaggrootte worden opgeschaald.

Nadat het herstelproces is voltooid, zoekt u de nieuwe server en controleert u of de gegevens zijn hersteld zoals verwacht. De nieuwe server heeft dezelfde serveradmin-inlognaam en -wachtwoord die geldig was voor de bestaande server op het moment dat het herstel werd gestart. Het wachtwoord kan worden gewijzigd vanaf de **pagina Overzicht** van de nieuwe server.

De nieuwe server die tijdens een herstel is gemaakt, heeft niet de VNet-serviceeindpunten die op de oorspronkelijke server bestonden. Deze regels moeten afzonderlijk worden ingesteld voor deze nieuwe server. Firewallregels van de oorspronkelijke server worden hersteld.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [back-ups](concepts-backup.md) van de service
- Meer informatie over [replica's](concepts-read-replicas.md)
- Meer informatie over opties voor [bedrijfscontinuïteit](concepts-business-continuity.md)
