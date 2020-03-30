---
title: 'Snelstart: Azure Database voor MySQL maken met az mysql up'
description: Quickstart-handleiding voor het maken van Azure Database voor MySQL-server met azure cli (command line interface) up command.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc
ms.openlocfilehash: 7b81e88fe6f658fdf4c1857c6082100894c6f2f6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067721"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Snelstart: een Azure-database voor MySQL maken met een eenvoudige opdracht Azure CLI - az mysql up (preview)

> [!IMPORTANT]
> De opdracht [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI is in preview.

Azure Database voor MySQL is een beheerde service waarmee u MySQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanuit de opdrachtregel of in scripts. In deze quickstart ziet u hoe u de opdracht [az mysql-up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) gebruiken om een Azure Database voor MySQL-server te maken met behulp van de Azure CLI. Naast het maken van `az mysql up` de server maakt de opdracht een voorbeelddatabase, een hoofdgebruiker in de database, opent de firewall voor Azure-services en worden standaardfirewallregels voor de clientcomputer gemaakt. Dit helpt om het ontwikkelingsproces te versnellen.

## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

In dit artikel moet u de Azure CLI-versie 2.0 of hoger lokaal uitvoeren. Voer de opdracht `az --version` uit om de geïnstalleerde versie te zien. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

Je moet inloggen op je account via de [inlogopdracht az.](/cli/azure/authenticate-azure-cli?view=interactive-log-in) Let op de **id**-eigenschap van de opdrachtuitvoer voor de naam van het desbetreffende abonnement.

```azurecli
az login
```

Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer de specifieke abonnements-id in uw account met de opdracht [az account set](/cli/azure/account). Vervang de **eigenschap subscription ID** van de **az-inloguitvoer** voor uw abonnement naar de tijdelijke aanduiding voor de gebruikersnaam.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Een Azure-database voor MySQL-server maken

Als u de opdrachten wilt gebruiken, installeert u de [db-up-extensie.](/cli/azure/ext/db-up) Als er een fout wordt geretourneerd, moet u ervoor zorgen dat u de nieuwste versie van de Azure CLI hebt geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Maak een Azure Database voor MySQL-server met de volgende opdracht:

```azurecli
az mysql up
```

De server wordt gemaakt met de volgende standaardwaarden (tenzij u deze handmatig overschrijft):

**Instelling** | **Standaardwaarde** | **Beschrijving**
---|---|---
servernaam | Systeem gegenereerd | Een unieke naam ter identificatie van de Azure Database voor MySQL-server.
resource-group | Systeem gegenereerd | Een nieuwe Azure-brongroep.
sku-name | GP_Gen5_2 | De naam van de SKU. Volgt de verkorte notatie voor conventie {prijscategorie}\_{compute-generatie}\_{vCores}. De standaard is een General Purpose Gen5-server met 2 vCores. Zie onze [prijspagina](https://azure.microsoft.com/pricing/details/mysql/) voor meer informatie over de lagen.
backup-retention | 7 | Hoe lang een back-up moet worden bewaard. De eenheid is dagen.
geo-redundant-backup | Uitgeschakeld | Of geografisch redundante back-ups moeten worden ingeschakeld voor deze server.
location | westus2 | De Azure-locatie voor de server.
ssl-enforcement | Uitgeschakeld | Of SSL moet worden ingeschakeld of niet voor deze server.
storage-size | 5120 | De opslagcapaciteit van de server (eenheid is MB).
versie | 5.7 | De hoofdversie van MySQL.
admin-user | Systeem gegenereerd | De gebruikersnaam voor aanmelding als beheerder.
admin-password | Systeem gegenereerd | Het wachtwoord van het beheerdersaccount.

> [!NOTE]
> Zie de Azure `az mysql up` [CLI-documentatie](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up)voor meer informatie over de opdracht en de bijbehorende parameters.

Zodra uw server is gemaakt, wordt deze geleverd met de volgende instellingen:

- Er wordt een firewallregel gemaakt met de naam "devbox". De Azure CLI probeert het IP-adres `az mysql up` van de machine te detecteren waarvan de opdracht wordt uitgevoerd en whitelists dat IP-adres.
- 'Toegang tot Azure-services toestaan' is ingesteld op AAN. Met deze instelling configureert u de firewall van de server om verbindingen van alle Azure-bronnen te accepteren, inclusief resources die niet in uw abonnement zijn.
- De `wait_timeout` parameter is ingesteld op 8 uur
- Er wordt een lege database met de naam "sampledb" gemaakt
- Er wordt een nieuwe gebruiker met de naam "root" gemaakt met bevoegdheden voor "sampledb"

> [!NOTE]
> Azure Database voor MySQL communiceert via poort 3306. Wanneer u verbinding maakt vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 3306 mogelijk niet toegestaan door de firewall van uw netwerk. Laat uw IT-afdeling poort 3306 openen om verbinding te maken met uw server.

## <a name="get-the-connection-information"></a>De verbindingsgegevens ophalen

Nadat `az mysql up` de opdracht is voltooid, wordt een lijst met verbindingstekenreeksen voor populaire programmeertalen aan u geretourneerd. Deze verbindingstekenreeksen zijn vooraf geconfigureerd met de specifieke kenmerken van uw nieuw gemaakte Azure Database voor MySQL-server.

U de opdracht [az mysql show-connection-string](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) gebruiken om deze verbindingstekenreeksen opnieuw weer op te sommen.

## <a name="clean-up-resources"></a>Resources opschonen

Ruim alle resources op die u in de snelstart hebt gemaakt met de volgende opdracht. Met deze opdracht worden de Azure Database voor MySQL-server en de brongroep verwijderd.

```azurecli
az mysql down --delete-group
```

Als u alleen de nieuw gemaakte server wilt verwijderen, u [de opdracht AZ Mysql down](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down) uitvoeren.

```azurecli
az mysql down
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een MySQL-database ontwerpen met Azure CLI](./tutorial-design-database-using-cli.md)
