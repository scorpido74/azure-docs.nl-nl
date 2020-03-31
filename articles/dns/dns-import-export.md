---
title: Een domeinzonebestand importeren en exporteren - Azure CLI
titleSuffix: Azure DNS
description: Meer informatie over het importeren en exporteren van een DNS-zonebestand naar Azure DNS met Azure CLI
services: dns
author: rohinkoul
ms.service: dns
ms.date: 4/3/2019
ms.author: rohink
ms.topic: conceptual
ms.openlocfilehash: a5c2fdde564eba2d95e7f14f4d47e4d381739d5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365165"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Een DNS-zonebestand importeren en exporteren met de Azure CLI

In dit artikel u met behulp van de Azure CLI DNS-zonebestanden voor Azure DNS importeren en exporteren.

## <a name="introduction-to-dns-zone-migration"></a>Inleiding tot DNS-zonemigratie

Een DNS-zonebestand is een tekstbestand dat details bevat van elke DNS-record (Domain Name System) in de zone. Het volgt een standaardformaat, waardoor het geschikt is voor het overbrengen van DNS-records tussen DNS-systemen. Het gebruik van een zonebestand is een snelle, betrouwbare en handige manier om een DNS-zone over te zetten naar of uit Azure DNS.

Azure DNS ondersteunt het importeren en exporteren van zonebestanden met behulp van de Azure command-line interface (CLI). Het importeren van zonebestanden wordt momenteel **niet** ondersteund via Azure PowerShell of de Azure-portal.

De Azure CLI is een cross-platform command-line tool die wordt gebruikt voor het beheer van Azure-services. Het is beschikbaar voor de Windows-, Mac- en Linux-platforms vanaf de [pagina Azure-downloads.](https://azure.microsoft.com/downloads/) Ondersteuning voor cross-platform is belangrijk voor het importeren en exporteren van zonebestanden, omdat de meest voorkomende naamserversoftware, [BIND,](https://www.isc.org/downloads/bind/)meestal op Linux draait.

## <a name="obtain-your-existing-dns-zone-file"></a>Uw bestaande DNS-zonebestand verkrijgen

Voordat u een DNS-zonebestand importeert in Azure DNS, moet u een kopie van het zonebestand verkrijgen. De bron van dit bestand is afhankelijk van waar de DNS-zone momenteel wordt gehost.

* Als uw DNS-zone wordt gehost door een partnerservice (zoals een domeinregistrar, speciale DNS-hostingprovider of alternatieve cloudprovider), moet die service de mogelijkheid bieden om het DNS-zonebestand te downloaden.
* Als uw DNS-zone wordt gehost op Windows DNS, is de standaardmap voor de zonebestanden **%systemroot%\system32\dns**. Het volledige pad naar elk zonebestand wordt ook weergegeven op het tabblad **Algemeen** van de DNS-console.
* Als uw DNS-zone wordt gehost door BIND te gebruiken, wordt de locatie van het zonebestand voor elke zone opgegeven in het BIND-configuratiebestand **met de naam.conf**.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Een DNS-zonebestand importeren in Azure DNS

Als u een zonebestand importeert, wordt een nieuwe zone in Azure DNS gemaakt als deze nog niet bestaat. Als de zone al bestaat, moeten de recordsets in het zonebestand worden samengevoegd met de bestaande recordsets.

### <a name="merge-behavior"></a>Gedrag samenvoegen

* Bestaande en nieuwe recordsets worden standaard samengevoegd. Identieke records binnen een samengevoegde recordset worden gedupliceerd.
* Wanneer recordsets worden samengevoegd, wordt de time-to-live (TTL) van reeds bestaande recordsets gebruikt.
* De parameters voor het begin `host`van de Autoriteit (SOA) (behalve ) worden altijd uit het geïmporteerde zonebestand gehaald. Ook voor de naamserverrecord die op de zonetop is ingesteld, wordt de TTL altijd uit het geïmporteerde zonebestand gehaald.
* Een geïmporteerde CNAME-record vervangt een bestaande CNAME-record niet met dezelfde naam.  
* Wanneer een conflict ontstaat tussen een CNAME-record en een andere record met dezelfde naam, maar een ander type (ongeacht welke bestaand of nieuw is), blijft de bestaande record behouden. 

### <a name="additional-information-about-importing"></a>Aanvullende informatie over importeren

De volgende notities bevatten aanvullende technische details over het zoneimportproces.

* De `$TTL` richtlijn is facultatief en wordt ondersteund. Wanneer `$TTL` er geen richtlijn wordt gegeven, worden records zonder expliciete TTL geïmporteerd ingesteld op een standaard TTL van 3600 seconden. Wanneer twee records in dezelfde recordset verschillende TTLs opgeven, wordt de lagere waarde gebruikt.
* De `$ORIGIN` richtlijn is facultatief en wordt ondersteund. Wanneer `$ORIGIN` er geen is ingesteld, wordt de standaardwaarde gebruikt, de zonenaam zoals opgegeven op de opdrachtregel (plus de beëindiging ".").
* De `$INCLUDE` `$GENERATE` richtlijnen en richtlijnen worden niet ondersteund.
* Deze recordtypen worden ondersteund: A, AAAA, CAA, CNAME, MX, NS, SOA, SRV en TXT.
* De SOA-record wordt automatisch gemaakt door Azure DNS wanneer een zone wordt gemaakt. Wanneer u een zonebestand importeert, worden alle SOA-parameters uit het zonebestand *verwijderd, behalve* de `host` parameter. Deze parameter gebruikt de waarde van Azure DNS. Deze parameter moet namelijk verwijzen naar de primaire naamserver van Azure DNS.
* De naamserverrecord die is ingesteld bij de zonetop wordt ook automatisch gemaakt door Azure DNS wanneer de zone wordt gemaakt. Alleen de TTL van deze recordset wordt geïmporteerd. Deze records bevatten de namen van de naamserver die door Azure DNS worden opgegeven. De recordgegevens worden niet overschreven door de waarden in het geïmporteerde zonebestand.
* Tijdens Public Preview ondersteunt Azure DNS alleen Single-string TXT-records. Multistring TXT-records worden samengevoegd en afgekapt tot 255 tekens.

### <a name="cli-format-and-values"></a>CLI-indeling en -waarden

De indeling van de opdracht Azure CLI om een DNS-zone te importeren is:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Waarden:

* `<resource group>`is de naam van de brongroep voor de zone in Azure DNS.
* `<zone name>`is de naam van de zone.
* `<zone file name>`is het pad/de naam van het te importeren zonebestand.

Als er geen zone met deze naam bestaat in de resourcegroep, wordt deze voor u gemaakt. Als de zone al bestaat, worden de geïmporteerde recordsets samengevoegd met bestaande recordsets. 

### <a name="step-1-import-a-zone-file"></a>Step 1. Een zonebestand importeren

Een zonebestand importeren voor de zone **contoso.com**.

1. Als u er nog geen hebt, moet u een resourcegroep resourcemanager maken.

    ```azurecli
    az group create --group myresourcegroup -l westeurope
    ```

2. Als u de zone **contoso.com** van het bestand **contoso.com.txt** wilt importeren in een nieuwe `az network dns zone import`DNS-zone in de groep **myresourcegroep**van resources, voert u de opdracht uit .<BR>Deze opdracht laadt het zonebestand en ontneemt het. De opdracht voert een reeks opdrachten uit op de Azure DNS-service om de zone en alle recordsets in de zone te maken. De opdracht rapporteert de voortgang in het consolevenster, samen met eventuele fouten of waarschuwingen. Omdat recordsets in reeksen worden gemaakt, kan het enkele minuten duren voordat een bestand met een grote zone is geïmporteerd.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Stap 2. De zone verifiëren

Als u de DNS-zone wilt verifiëren nadat u het bestand hebt geïmporteerd, u een van de volgende methoden gebruiken:

* U de records aanbieden met de volgende opdracht Azure CLI:

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* U de records aanbieden met `az network dns record-set ns list`de opdracht Azure CLI.
* U kunt `nslookup` de naamomzetting voor de records verifiëren. Omdat de zone nog niet is gedelegeerd, moet u de juiste Azure DNS-naamservers expliciet opgeven. In het volgende voorbeeld ziet u hoe u de namen van de naamserver ophaalt die aan de zone zijn toegewezen. Dit laat ook zien hoe u de `nslookup`"www" record query met behulp van .

    ```azurecli
    az network dns record-set ns list -g myresourcegroup -z contoso.com  --output json 
    ```

    ```json
    [
      {
       .......
       "name": "@",
        "nsRecords": [
          {
            "additionalProperties": {},
            "nsdname": "ns1-03.azure-dns.com."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns2-03.azure-dns.net."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns3-03.azure-dns.org."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns4-03.azure-dns.info."
          }
        ],
        "resourceGroup": "myresourcegroup",
        "ttl": 86400,
        "type": "Microsoft.Network/dnszones/NS"
      }
    ]
    ```

    ```cmd
    nslookup www.contoso.com ns1-03.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221
    ```

### <a name="step-3-update-dns-delegation"></a>Stap 3. DNS-delegatie bijwerken

Nadat u hebt geverifieerd dat de zone correct is geïmporteerd, moet u de DNS-delegatie bijwerken om naar de Azure DNS-naamservers te wijzen. Zie voor meer informatie het artikel [Update the DNS delegation](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Een DNS-zonebestand exporteren vanuit Azure DNS

De indeling van de opdracht Azure CLI om een DNS-zone te exporteren is:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Waarden:

* `<resource group>`is de naam van de brongroep voor de zone in Azure DNS.
* `<zone name>`is de naam van de zone.
* `<zone file name>`is het pad/de naam van het te exporteren zonebestand.

Net als bij het importeren van de zone, moet u zich eerst aanmelden, uw abonnement kiezen en de Azure CLI configureren om de Resource Manager-modus te gebruiken.

### <a name="to-export-a-zone-file"></a>Een zonebestand exporteren

Als u de bestaande Azure DNS-zone **wilt** exporteren contoso.com in **myresourcegroep van** resources in resources naar het bestand **contoso.com.txt** (in de huidige map), voert u . `azure network dns zone export` Met deze opdracht wordt de Azure DNS-service aanroepen om recordsets in de zone op te sommen en de resultaten naar een BIND-compatibel zonebestand te exporteren.

```azurecli
az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [beheren van recordsets en records](dns-getstarted-create-recordset-cli.md) in uw DNS-zone.

* Meer informatie over het [delegeren van uw domein aan Azure DNS](dns-domain-delegation.md).
