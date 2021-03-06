---
title: Back-up en herstel-Azure CLI-Azure Database for PostgreSQL-één server
description: Meer informatie over het instellen van back-upconfiguraties en het herstellen van een server in Azure Database for PostgreSQL-één server met behulp van de Azure CLI.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/25/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: c7e8f0fc3a90a0811d38840004f7ae12a9a225ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708506"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Een back-up maken en herstellen van een server in Azure Database for PostgreSQL-één server met behulp van de Azure CLI

Er wordt regel matig een back-up van Azure Database for PostgreSQL servers gemaakt om herstel functies in te scha kelen. Met deze functie kunt u de server en alle bijbehorende data bases naar een eerder tijdstip herstellen op een nieuwe server.

## <a name="prerequisites"></a>Vereisten
U hebt het volgende nodig om deze hand leiding te volt ooien:
- Een [Azure database for postgresql-server en-data base](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Voor deze hand leiding moet u Azure CLI versie 2,0 of hoger gebruiken. Als u de versie wilt bevestigen, typt u bij de opdracht prompt van Azure CLI `az --version` . Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u CLI wilt installeren of upgraden.

## <a name="set-backup-configuration"></a>Back-upconfiguratie instellen

U kunt kiezen tussen het configureren van uw server voor lokaal redundante back-ups of geografisch redundante back-ups bij het maken van een server. 

> [!NOTE]
> Nadat een server is gemaakt, is het soort redundantie die het heeft, geografisch redundant versus lokaal redundant, niet overgeschakeld.
>

Tijdens het maken van een server via de `az postgres server create` opdracht, `--geo-redundant-backup` bepaalt de para meter de optie voor de redundantie van back-ups. Als er `Enabled` geo redundante back-ups worden gemaakt. Of als er `Disabled` lokaal redundante back-ups worden gemaakt. 

De Bewaar periode voor back-ups is ingesteld met de para meter `--backup-retention-days` . 

Zie de [Snelstartgids voor Azure database for postgresql server cli](quickstart-create-server-database-azure-cli.md)voor meer informatie over het instellen van deze waarden tijdens het maken.

De Bewaar periode van een back-up van een server kan als volgt worden gewijzigd:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

In het vorige voor beeld wordt de Bewaar periode voor back-ups van mydemoserver gewijzigd in 10 dagen.

De Bewaar periode voor back-ups bepaalt hoe ver terug in de tijd een herstel naar een bepaald tijdstip kan worden opgehaald, omdat het is gebaseerd op back-ups die beschikbaar zijn. Herstel naar een bepaald tijdstip wordt verderop in de volgende sectie beschreven.

## <a name="server-point-in-time-restore"></a>Server herstel naar een bepaald tijdstip
U kunt de server herstellen naar een eerder tijdstip. De herstelde gegevens worden gekopieerd naar een nieuwe server en de bestaande server blijft in de huidige staat. Als een tabel bijvoorbeeld per ongeluk is verwijderd om 12:00 uur, kunt u de tijd herstellen naar een periode van meer dan 12 uur 's middags. Vervolgens kunt u de ontbrekende tabel en gegevens ophalen van de herstelde kopie van de server. 

Als u de server wilt herstellen, gebruikt u de opdracht Azure CLI [AZ post gres server Restore](/cli/azure/postgres/server) .

### <a name="run-the-restore-command"></a>De herstelopdracht uitvoeren

Als u de server wilt herstellen, voert u bij de opdracht prompt van Azure CLI de volgende opdracht in:

```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

Voor de `az postgres server restore` opdracht zijn de volgende para meters vereist:

| Instelling | Voorgestelde waarde | Beschrijving  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  De resource groep waar de bron server zich bevindt.  |
| naam | mydemoserver-restored | De naam van de nieuwe server die door de opdracht restore is gemaakt. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Selecteer een punt in de tijd waarnaar u wilt herstellen. Deze datum en tijd moet binnen de back-upretentieperiode van de bronserver vallen. Gebruik de ISO8601 datum-en tijd notatie. U kunt bijvoorbeeld uw eigen lokale tijd zone gebruiken, zoals `2018-03-13T05:59:00-08:00` . U kunt ook de notatie UTC-Zulu gebruiken, bijvoorbeeld `2018-03-13T13:59:00Z` . |
| source-server | mydemoserver | De naam of ID van de bronserver voor het herstellen. |

Wanneer u een server herstelt naar een eerder tijdstip, wordt er een nieuwe server gemaakt. De oorspronkelijke server en de bijbehorende data bases vanaf het opgegeven tijdstip worden gekopieerd naar de nieuwe server.

De locatie en prijscategorie van de herstelde server zijn hetzelfde als die van de oorspronkelijke server. 

Nadat het herstelproces is voltooid, zoekt u de nieuwe server en controleert u of de gegevens correct zijn hersteld. De nieuwe server heeft dezelfde aanmeldings naam en hetzelfde wacht woord voor de server beheerder als die van de bestaande server op het moment dat de herstel bewerking werd gestart. Het wachtwoord kan worden gewijzigd op de pagina **Overzicht** van de nieuwe server.

De nieuwe server die is gemaakt tijdens een herstelbewerking, bevat niet de firewallregels of VNet-service-eindpunten die bestonden op de oorspronkelijke server. Deze regels moeten afzonderlijk worden ingesteld voor deze nieuwe server.

## <a name="geo-restore"></a>Geo-herstel
Als u uw server voor geografisch redundante back-ups hebt geconfigureerd, kan een nieuwe server worden gemaakt op basis van de back-up van die bestaande server. Deze nieuwe server kan worden gemaakt in elke regio die Azure Database for PostgreSQL beschikbaar is.  

Als u een server wilt maken met behulp van een geo-redundante back-up, gebruikt u de Azure CLI- `az postgres server georestore` opdracht.

> [!NOTE]
> Wanneer een server voor het eerst wordt gemaakt, is deze mogelijk niet onmiddellijk beschikbaar voor geo Restore. Het kan enkele uren duren voordat de benodigde meta gegevens zijn gevuld.
>

Als u de server wilt herstellen, voert u bij de opdracht prompt van Azure CLI de volgende opdracht in:

```azurecli-interactive
az postgres server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
Met deze opdracht maakt u een nieuwe server met de naam *mydemoserver-restored* in VS-Oost, die deel uitmaakt van *myresourcegroup*. Het is een Algemeen, gen 4-server met 8 vCores. De server wordt gemaakt op basis van de geo-redundante back-up van *mydemoserver*, die ook in de resource groep *myresourcegroup*

Als u de nieuwe server in een andere resource groep van de bestaande server wilt maken, moet u in de `--source-server` para meter de naam van de server kwalificeren, zoals in het volgende voor beeld:

```azurecli-interactive
az postgres server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

Voor de `az postgres server georestore` opdracht zijn de volgende para meters vereist:

| Instelling | Voorgestelde waarde | Beschrijving  |
| --- | --- | --- |
|resource-group| myResourceGroup | De naam van de resource groep waar de nieuwe server deel van uitmaakt.|
|naam | mydemoserver-geoterugzet bewerking | De naam van de nieuwe server. |
|source-server | mydemoserver | De naam van de bestaande server waarvoor geo redundante back-ups worden gebruikt. |
|location | eastus | De locatie van de nieuwe server. |
|sku-name| GP_Gen4_8 | Met deze para meter worden de prijs categorie, generatie van Compute en het aantal vCores van de nieuwe server ingesteld. GP_Gen4_8 is toegewezen aan een Algemeen, gen 4 server met 8 vCores.|

Wanneer u een nieuwe server maakt door een geo-terugzet bewerking, neemt deze dezelfde opslag grootte en prijs categorie over als de bron server. Deze waarden kunnen niet worden gewijzigd tijdens het maken. Nadat de nieuwe server is gemaakt, kan de opslag grootte worden uitgebreid.

Nadat het herstelproces is voltooid, zoekt u de nieuwe server en controleert u of de gegevens correct zijn hersteld. De nieuwe server heeft dezelfde aanmeldings naam en hetzelfde wacht woord voor de server beheerder als die van de bestaande server op het moment dat de herstel bewerking werd gestart. Het wachtwoord kan worden gewijzigd op de pagina **Overzicht** van de nieuwe server.

De nieuwe server die is gemaakt tijdens een herstelbewerking, bevat niet de firewallregels of VNet-service-eindpunten die bestonden op de oorspronkelijke server. Deze regels moeten afzonderlijk worden ingesteld voor deze nieuwe server.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [back-ups](concepts-backup.md) van de service
- Meer informatie over [replica's](concepts-read-replicas.md)
- Meer informatie over opties voor [bedrijfs continuïteit](concepts-business-continuity.md)
