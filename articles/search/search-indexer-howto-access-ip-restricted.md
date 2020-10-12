---
title: Toegang tot de IP-bereiken van de Indexeer functie toestaan
titleSuffix: Azure Cognitive Search
description: Instructies voor het instellen van IP-firewall regels zodat Indexeer functies toegang kunnen hebben.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: c80462707d3dccbb8fccff244017053c25ad46e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89463583"
---
# <a name="setting-up-ip-firewall-rules-to-enable-indexer-access"></a>IP-firewall regels instellen om toegang tot de Indexeer functie in te scha kelen

IP-firewall regels voor Azure-resources, zoals opslag accounts, Cosmos DB accounts en Azure SQL-servers, staan alleen verkeer toe die afkomstig zijn van specifieke IP-bereiken tot toegang tot gegevens.

In dit artikel wordt beschreven hoe u de IP-regels via Azure Portal kunt configureren voor een opslag account, zodat Azure Cognitive Search Indexeer functies de gegevens veilig kunnen benaderen. Hoewel deze hand leiding specifiek is voor opslag, kan deze rechtstreeks worden vertaald naar andere Azure-resources die ook IP-firewall regels bieden voor het beveiligen van de toegang tot gegevens.

> [!NOTE]
> De IP-firewall regels voor het opslag account zijn alleen effectief als het opslag account en de zoek service zich in verschillende regio's bevinden. Als uw installatie dit niet toestaat, raden we u aan de [optie voor uitzonde ringen voor vertrouwde services](search-indexer-howto-access-trusted-service-exception.md)te gebruiken.

## <a name="get-the-ip-address-of-the-search-service"></a>Het IP-adres van de zoek service ophalen

Verkrijg de Fully Qualified Domain Name (FQDN) van uw zoek service. Dit ziet er als volgt uit `<search-service-name>.search.windows.net` . U kunt de FQDN-namen achterhalen door uw zoek service op de Azure Portal op te zoeken.

   ![FQDN van service verkrijgen](media\search-indexer-howto-secure-access\search-service-portal.png "FQDN van service verkrijgen")

Het IP-adres van de zoek service kan worden verkregen door een `nslookup` (of een `ping` ) van de FQDN uit te voeren. Dit is een van de IP-adressen die moeten worden toegevoegd aan de firewall regels.

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>De IP-adresbereiken ophalen voor de servicetag ' AzureCognitiveSearch '

De IP-adresbereiken voor de servicetag `AzureCognitiveSearch` kunnen worden verkregen via de [detectie-API (preview)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) of het [JSON-bestand](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)dat kan worden gedownload.

Voor dit scenario wordt ervan uitgegaan dat de Search-service de open bare Azure-Cloud is, dan moet het [open bare Azure-JSON-bestand](https://www.microsoft.com/download/details.aspx?id=56519) worden gedownload.

   ![JSON-bestand downloaden](media\search-indexer-howto-secure-access\service-tag.png "JSON-bestand downloaden")

In het JSON-bestand, ervan uitgaande dat de Search-service zich in West-Centraal VS bevindt, wordt de lijst met IP-adressen voor de uitvoerings omgeving van de multi tenant-indexer hieronder weer gegeven.

```json
    {
      "name": "AzureCognitiveSearch.WestCentralUS",
      "id": "AzureCognitiveSearch.WestCentralUS",
      "properties": {
        "changeNumber": 1,
        "region": "westcentralus",
        "platform": "Azure",
        "systemService": "AzureCognitiveSearch",
        "addressPrefixes": [
          "52.150.139.0/26",
          "52.253.133.74/32"
        ]
      }
    }
```

Voor/32 IP-adressen verwijdert u de '/32 ' (52.253.133.74/32-> 52.253.133.74), kunnen anderen worden gebruikt Verbatim.

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>De IP-adresbereiken toevoegen aan IP-firewall regels

De eenvoudigste manier om IP-adresbereiken toe te voegen aan de firewall regel van een opslag account is via de Azure Portal. Zoek het opslag account op de portal en ga naar het tabblad**firewalls en virtuele netwerken**.

   ![Firewall en virtuele netwerken](media\search-indexer-howto-secure-access\storage-firewall.png "Firewall en virtuele netwerken")

Voeg de drie IP-adressen toe die eerder zijn verkregen (1 voor de Search-service-IP, 2 voor de servicetag `AzureCognitiveSearch` ) in het adres bereik en klik op**Opslaan**.

   ![Firewall-IP-regels](media\search-indexer-howto-secure-access\storage-firewall-ip.png "Firewall-IP-regels")

De firewall regels nemen 5-10 minuten in beslag, waarna de Indexeer functies de gegevens in het opslag account kunnen openen.

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u de twee sets met IP-adressen kunt ophalen voor het toestaan van toegang voor indexen, gebruikt u de volgende koppelingen om de IP-firewall regels voor een aantal algemene gegevens bronnen bij te werken.

- [Azure Storage firewalls configureren](https://docs.microsoft.com/azure/storage/common/storage-network-security)
- [IP-Firewall configureren voor CosmosDB](https://docs.microsoft.com/azure/cosmos-db/firewall-support)
- [IP-Firewall configureren voor Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)