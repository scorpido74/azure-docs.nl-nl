---
title: 'Snelstartgids: server maken-AZ post gres up-Azure Database for PostgreSQL-Single Server'
description: Snelstartgids om Azure Database for PostgreSQL-één server te maken met behulp van Azure CLI (opdracht regel Interface).
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: 1dd20d94737df3e799876323d2aeb555dcb9a381
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146815"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Snelstartgids: een Azure CLI-opdracht gebruiken, AZ post gres up (preview) om een Azure Database for PostgreSQL-één server te maken

> [!IMPORTANT]
> De opdracht [AZ post gres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure cli is beschikbaar als preview-versie.

Azure Database voor PostgreSQL is een beheerde service waarmee u PostgreSQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. In deze Quick start ziet u hoe u de opdracht [AZ post gres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) gebruikt om een Azure database for postgresql server te maken met behulp van de Azure cli. Naast het maken van de-server, `az postgres up` maakt de opdracht een voorbeeld database, een hoofd gebruiker in de data base, opent de firewall voor Azure-Services en worden standaard firewall regels gemaakt voor de client computer. Deze standaard waarden helpen bij het versnellen van het ontwikkelings proces.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis](https://azure.microsoft.com/free/) account aan voordat u begint.

Voor dit artikel moet u Azure CLI versie 2,0 of hoger lokaal uitvoeren. Voer de opdracht `az --version` uit om de geïnstalleerde versie te zien. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

U moet zich aanmelden bij uw account met de opdracht [AZ login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) . Noteer de **id-** eigenschap van de uitvoer van de opdracht voor de bijbehorende abonnements naam.

```azurecli
az login
```

Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer de specifieke abonnements-id in uw account met de opdracht [az account set](/cli/azure/account). Vervang de **abonnements-ID-** eigenschap van de **AZ-aanmeld** uitvoer voor uw abonnement in de tijdelijke aanduiding voor de abonnements-id.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Een Azure-database voor PostgreSQL-server maken

Als u de opdrachten wilt gebruiken, installeert u de [db-up-](/cli/azure/ext/db-up) extensie. Als er een fout wordt geretourneerd, controleert u of u de nieuwste versie van de Azure CLI hebt geïnstalleerd. Zie [Azure cli installeren](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Maak een Azure Database for PostgreSQL-server met behulp van de volgende opdracht:

```azurecli
az postgres up
```

De server wordt gemaakt met de volgende standaard waarden (tenzij u deze hand matig overschrijft):

**Instelling** | **Standaardwaarde** | **Beschrijving**
---|---|---
servernaam | Systeem gegenereerd | Een unieke naam ter identificatie van uw Azure Database for PostgreSQL-server.
resource-group | Systeem gegenereerd | Een nieuwe Azure-resource groep.
sku-name | GP_Gen5_2 | De naam van de SKU. Volgt de verkorte notatie voor conventie {prijscategorie}\_{compute-generatie}\_{vCores}. De standaard waarde is een Algemeen GEN5-server met 2 vCores. Bekijk onze [pagina met prijzen](https://azure.microsoft.com/pricing/details/postgresql/) voor meer informatie over de lagen.
backup-retention | 7 | Hoe lang een back-up wordt bewaard. De eenheid is dagen.
geo-redundant-backup | Uitgeschakeld | Of geografisch redundante back-ups moeten worden ingeschakeld voor deze server.
location | westus2 | De Azure-locatie voor de server.
ssl-enforcement | Uitgeschakeld | Hiermee wordt aangegeven of TLS/SSL moet worden ingeschakeld of niet voor deze server.
storage-size | 5120 | De opslagcapaciteit van de server (eenheid is MB).
versie | 10 | De primaire versie van PostgreSQL.
admin-user | Systeem gegenereerd | De gebruikers naam voor de beheerder.
admin-password | Systeem gegenereerd | Het wachtwoord van het beheerdersaccount.

> [!NOTE]
> Zie de [documentatie van Azure cli](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up)voor meer informatie over de `az postgres up` opdracht en de aanvullende para meters.

Zodra de server is gemaakt, worden de volgende instellingen uitgevoerd:

- Er wordt een firewall regel met de naam ' devbox ' gemaakt. De Azure CLI probeert het IP-adres van de computer te detecteren `az postgres up` waarop de opdracht wordt uitgevoerd en whitelists dat IP-adres.
- ' Toegang tot Azure-Services toestaan ' is ingesteld op aan. Met deze instelling configureert u de firewall van de server voor het accepteren van verbindingen van alle Azure-resources, inclusief bronnen die niet in uw abonnement zijn.
- Er is een lege data base gemaakt met de naam ' SampleDb '
- Er is een nieuwe gebruiker gemaakt met de naam ' root ' met bevoegdheden voor ' SampleDb '

> [!NOTE]
> Azure Database for PostgreSQL communiceert via poort 5432. Als u verbinding maakt vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 5432 mogelijk niet toegestaan door de firewall van uw netwerk. Laat uw IT-afdeling poort 5432 openen om verbinding te maken met uw server.

## <a name="get-the-connection-information"></a>De verbindingsgegevens ophalen

Nadat de `az postgres up` opdracht is voltooid, wordt er een lijst met verbindings reeksen voor populaire programmeer talen naar u geretourneerd. Deze verbindings reeksen worden vooraf geconfigureerd met de specifieke kenmerken van uw nieuw gemaakte Azure Database for PostgreSQL-server.

U kunt de opdracht [AZ post gres show-Connection-String](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-show-connection-string) gebruiken om deze verbindingsteken reeksen weer te geven.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt alle resources die u hebt gemaakt in de Quick Start opschonen met behulp van de volgende opdracht. Met deze opdracht worden de Azure Database for PostgreSQL-server en de resource groep verwijderd.

```azurecli
az postgres down --delete-group
```

Als u alleen de zojuist gemaakte server wilt verwijderen, kunt u de opdracht [AZ post gres down](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-down) uitvoeren.

```azurecli
az postgres down
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een database migreren met behulp van Exporteren en importeren](./howto-migrate-using-export-and-import.md)
