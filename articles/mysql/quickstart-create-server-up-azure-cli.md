---
title: 'Snelstartgids: Azure Database for MySQL maken met AZ mysql up'
description: Snelstartgids om Azure Database for MySQL server te maken met behulp van Azure CLI (opdracht regel Interface).
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc
ms.openlocfilehash: 7b81e88fe6f658fdf4c1857c6082100894c6f2f6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80067721"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Snelstartgids: een Azure Database for MySQL maken met een eenvoudige Azure CLI-opdracht-AZ mysql up (preview)

> [!IMPORTANT]
> De opdracht [AZ mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure cli is beschikbaar als preview-versie.

Azure Database voor MySQL is een beheerde service waarmee u MySQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdracht regel of in scripts. In deze Quick start ziet u hoe u de [AZ mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) -opdracht kunt gebruiken om een Azure database for mysql server te maken met behulp van de Azure cli. Naast het maken van de-server, `az mysql up` maakt de opdracht een voorbeeld database, een hoofd gebruiker in de data base, opent de firewall voor Azure-Services en worden standaard firewall regels gemaakt voor de client computer. Zo kunt u het ontwikkelings proces versnellen.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis](https://azure.microsoft.com/free/) account aan voordat u begint.

Voor dit artikel moet u Azure CLI versie 2,0 of hoger lokaal uitvoeren. Voer de opdracht `az --version` uit om de geïnstalleerde versie te zien. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

U moet zich aanmelden bij uw account met de opdracht [AZ login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) . Let op de **id**-eigenschap van de opdrachtuitvoer voor de naam van het desbetreffende abonnement.

```azurecli
az login
```

Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer de specifieke abonnements-id in uw account met de opdracht [az account set](/cli/azure/account). Vervang de **abonnements-ID-** eigenschap van de **AZ-aanmeld** uitvoer voor uw abonnement in de tijdelijke aanduiding voor de abonnements-id.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Een Azure-database voor MySQL-server maken

Als u de opdrachten wilt gebruiken, installeert u de [db-up-](/cli/azure/ext/db-up) extensie. Als er een fout wordt geretourneerd, controleert u of u de nieuwste versie van de Azure CLI hebt geïnstalleerd. Zie [Azure cli installeren](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Maak een Azure Database for MySQL-server met behulp van de volgende opdracht:

```azurecli
az mysql up
```

De server wordt gemaakt met de volgende standaard waarden (tenzij u deze hand matig overschrijft):

**Instelling** | **Standaardwaarde** | **Beschrijving**
---|---|---
servernaam | Systeem gegenereerd | Een unieke naam ter identificatie van de Azure Database voor MySQL-server.
resource-group | Systeem gegenereerd | Een nieuwe Azure-resource groep.
sku-name | GP_Gen5_2 | De naam van de SKU. Volgt de verkorte notatie voor conventie {prijscategorie}\_{compute-generatie}\_{vCores}. De standaard waarde is een Algemeen GEN5-server met 2 vCores. Bekijk onze [pagina met prijzen](https://azure.microsoft.com/pricing/details/mysql/) voor meer informatie over de lagen.
backup-retention | 7 | Hoe lang een back-up moet worden bewaard. De eenheid is dagen.
geo-redundant-backup | Uitgeschakeld | Of geografisch redundante back-ups moeten worden ingeschakeld voor deze server.
location | westus2 | De Azure-locatie voor de server.
ssl-enforcement | Uitgeschakeld | Hiermee wordt aangegeven of SSL moet worden ingeschakeld of niet voor deze server.
storage-size | 5120 | De opslagcapaciteit van de server (eenheid is MB).
versie | 5.7 | De hoofdversie van MySQL.
admin-user | Systeem gegenereerd | De gebruikersnaam voor aanmelding als beheerder.
admin-password | Systeem gegenereerd | Het wachtwoord van het beheerdersaccount.

> [!NOTE]
> Zie de [documentatie van Azure cli](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up)voor meer informatie over de `az mysql up` opdracht en de aanvullende para meters.

Zodra de server is gemaakt, worden de volgende instellingen uitgevoerd:

- Er wordt een firewall regel met de naam ' devbox ' gemaakt. De Azure CLI probeert het IP-adres van de computer te detecteren `az mysql up` waarop de opdracht wordt uitgevoerd en whitelists dat IP-adres.
- ' Toegang tot Azure-Services toestaan ' is ingesteld op aan. Met deze instelling configureert u de firewall van de server voor het accepteren van verbindingen van alle Azure-resources, inclusief bronnen die niet in uw abonnement zijn.
- De `wait_timeout` para meter is ingesteld op 8 uur
- Er is een lege data base gemaakt met de naam ' SampleDb '
- Er is een nieuwe gebruiker gemaakt met de naam ' root ' met bevoegdheden voor ' SampleDb '

> [!NOTE]
> Azure Database for MySQL communiceert via poort 3306. Wanneer u verbinding maakt vanuit een bedrijfs netwerk, wordt uitgaand verkeer via poort 3306 mogelijk niet toegestaan door de firewall van uw netwerk. Laat uw IT-afdeling poort 3306 openen om verbinding te maken met uw server.

## <a name="get-the-connection-information"></a>De verbindingsgegevens ophalen

Nadat de `az mysql up` opdracht is voltooid, wordt er een lijst met verbindings reeksen voor populaire programmeer talen naar u geretourneerd. Deze verbindings reeksen worden vooraf geconfigureerd met de specifieke kenmerken van uw nieuw gemaakte Azure Database for MySQL-server.

U kunt de opdracht [AZ mysql show-Connection-String](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) gebruiken om deze verbindingsteken reeksen weer te geven.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt alle resources die u hebt gemaakt in de Quick Start opschonen met behulp van de volgende opdracht. Met deze opdracht worden de Azure Database for MySQL-server en de resource groep verwijderd.

```azurecli
az mysql down --delete-group
```

Als u alleen de zojuist gemaakte server wilt verwijderen, kunt u de opdracht [AZ mysql down](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down) uitvoeren.

```azurecli
az mysql down
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een MySQL-database ontwerpen met Azure CLI](./tutorial-design-database-using-cli.md)
