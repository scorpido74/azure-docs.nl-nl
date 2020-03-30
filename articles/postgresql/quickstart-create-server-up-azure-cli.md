---
title: 'Snelstart: Server maken - az postgres up - Azure Database voor PostgreSQL - Single Server'
description: Quickstart-handleiding voor het maken van Azure Database voor PostgreSQL - Single Server met azure CLI (command-line interface) up command command.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: fe15c02286223ec0829b31664811b7f589cf16aa
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74774829"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Snelstart: gebruik een Azure CLI-opdracht, az postgres up (preview), om een Azure Database voor PostgreSQL - Single Server te maken

> [!IMPORTANT]
> De [opdracht AZ postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI is in preview.

Azure Database voor PostgreSQL is een beheerde service waarmee u PostgreSQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. In deze quickstart ziet u hoe u de opdracht [az-postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) gebruiken om een Azure Database voor PostgreSQL-server te maken met behulp van de Azure CLI. Naast het maken van `az postgres up` de server maakt de opdracht een voorbeelddatabase, een hoofdgebruiker in de database, opent de firewall voor Azure-services en worden standaardfirewallregels voor de clientcomputer gemaakt. Deze standaardinstellingen helpen om het ontwikkelingsproces te versnellen.

## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

In dit artikel moet u de Azure CLI-versie 2.0 of hoger lokaal uitvoeren. Voer de opdracht `az --version` uit om de geïnstalleerde versie te zien. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

Je moet inloggen met je account via de [inlogopdracht AZ.](/cli/azure/authenticate-azure-cli?view=interactive-log-in) Let op de eigenschap **ID** in de opdrachtuitvoer voor de bijbehorende abonnementsnaam.

```azurecli
az login
```

Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer de specifieke abonnements-id in uw account met de opdracht [az account set](/cli/azure/account). Vervang de **eigenschap subscription ID** van de **az-inloguitvoer** voor uw abonnement naar de tijdelijke aanduiding voor de gebruikersnaam.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Een Azure-database voor PostgreSQL-server maken

Als u de opdrachten wilt gebruiken, installeert u de [db-up-extensie.](/cli/azure/ext/db-up) Als er een fout wordt geretourneerd, moet u ervoor zorgen dat u de nieuwste versie van de Azure CLI hebt geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Maak een Azure Database voor PostgreSQL-server met de volgende opdracht:

```azurecli
az postgres up
```

De server wordt gemaakt met de volgende standaardwaarden (tenzij u deze handmatig overschrijft):

**Instelling** | **Standaardwaarde** | **Beschrijving**
---|---|---
servernaam | Systeem gegenereerd | Een unieke naam ter identificatie van uw Azure Database for PostgreSQL-server.
resource-group | Systeem gegenereerd | Een nieuwe Azure-brongroep.
sku-name | GP_Gen5_2 | De naam van de SKU. Volgt de verkorte notatie voor conventie {prijscategorie}\_{compute-generatie}\_{vCores}. De standaard is een General Purpose Gen5-server met 2 vCores. Zie onze [prijspagina](https://azure.microsoft.com/pricing/details/postgresql/) voor meer informatie over de lagen.
backup-retention | 7 | Hoe lang een back-up wordt bewaard. De eenheid is dagen.
geo-redundant-backup | Uitgeschakeld | Of geografisch redundante back-ups moeten worden ingeschakeld voor deze server.
location | westus2 | De Azure-locatie voor de server.
ssl-enforcement | Uitgeschakeld | Of ssl moet worden ingeschakeld voor deze server.
storage-size | 5120 | De opslagcapaciteit van de server (eenheid is MB).
versie | 10 | De primaire versie van PostgreSQL.
admin-user | Systeem gegenereerd | De gebruikersnaam voor de beheerder.
admin-password | Systeem gegenereerd | Het wachtwoord van het beheerdersaccount.

> [!NOTE]
> Zie de Azure `az postgres up` [CLI-documentatie](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up)voor meer informatie over de opdracht en de bijbehorende parameters.

Zodra uw server is gemaakt, wordt deze geleverd met de volgende instellingen:

- Er wordt een firewallregel gemaakt met de naam "devbox". De Azure CLI probeert het IP-adres `az postgres up` van de machine te detecteren waarvan de opdracht wordt uitgevoerd en whitelists dat IP-adres.
- 'Toegang tot Azure-services toestaan' is ingesteld op AAN. Met deze instelling configureert u de firewall van de server om verbindingen van alle Azure-bronnen te accepteren, inclusief resources die niet in uw abonnement zijn.
- Er wordt een lege database met de naam "sampledb" gemaakt
- Er wordt een nieuwe gebruiker met de naam "root" gemaakt met bevoegdheden voor "sampledb"

> [!NOTE]
> Azure Database voor PostgreSQL communiceert via poort 5432. Als u verbinding maakt vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 5432 mogelijk niet toegestaan door de firewall van uw netwerk. Laat uw IT-afdeling poort 5432 openen om verbinding te maken met uw server.

## <a name="get-the-connection-information"></a>De verbindingsgegevens ophalen

Nadat `az postgres up` de opdracht is voltooid, wordt een lijst met verbindingstekenreeksen voor populaire programmeertalen aan u geretourneerd. Deze verbindingstekenreeksen zijn vooraf geconfigureerd met de specifieke kenmerken van uw nieuw gemaakte Azure Database voor PostgreSQL-server.

U de opdracht [az postgres show-connection-string](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-show-connection-string) gebruiken om deze verbindingstekenreeksen opnieuw weer op te sommen.

## <a name="clean-up-resources"></a>Resources opschonen

Ruim alle resources op die u in de snelstart hebt gemaakt met de volgende opdracht. Met deze opdracht worden de Azure Database voor PostgreSQL-server en de brongroep verwijderd.

```azurecli
az postgres down --delete-group
```

Als je gewoon de nieuw gemaakte server wilt verwijderen, kun je [de opdracht AZ Postgres Down](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-down) uitvoeren.

```azurecli
az postgres down
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een database migreren met behulp van Exporteren en importeren](./howto-migrate-using-export-and-import.md)
