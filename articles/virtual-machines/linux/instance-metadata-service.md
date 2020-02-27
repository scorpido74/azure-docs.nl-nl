---
title: Azure Instance Metadata Service
description: De REST-interface voor het ophalen van informatie over gebeurtenissen voor de berekening, het netwerk en aanstaande onderhouds werkzaamheden van Linux.
services: virtual-machines-linux
documentationcenter: ''
author: KumariSupriya
manager: paulmey
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: 22f50a6d5136eaff457c24864dae71261a20e13e
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77615619"
---
# <a name="azure-instance-metadata-service"></a>Meta gegevens service van Azure-exemplaar

De Azure Instance Metadata Service (IMDS) bevat informatie over actieve exemplaren van virtuele machines en kan worden gebruikt voor het beheren en configureren van uw virtuele machines.
De verstrekte informatie omvat de gebeurtenissen SKU, netwerk configuratie en gepland onderhoud. Zie [meta data api's](#metadata-apis)voor een volledige lijst van de beschik bare gegevens.

De Instance Metadata Service van Azure is een REST-eind punt dat toegankelijk is voor alle virtuele IaaS-machines die via de [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/)zijn gemaakt.
Het eind punt is beschikbaar via een bekend, niet-routeerbaar IP-adres (`169.254.169.254`) dat alleen vanuit de virtuele machine kan worden geopend.

> [!IMPORTANT]
> Deze service is **over het algemeen beschikbaar** in alle Azure-regio's.  Er worden regel matig updates ontvangen om nieuwe informatie over virtuele-machine-instanties beschikbaar te maken. Op deze pagina worden de bijgewerkte [api's voor meta gegevens](#metadata-apis) weer gegeven die beschikbaar zijn.

## <a name="service-availability"></a>Service beschikbaarheid

De service is beschikbaar in de algemeen beschik bare Azure-regio's. Niet alle API-versies zijn mogelijk beschikbaar in alle Azure-regio's.

Regio's                                        | Availability?                                 | Ondersteunde versies
-----------------------------------------------|-----------------------------------------------|-----------------
[Alle algemeen beschik bare wereld wijde Azure-regio's](https://azure.microsoft.com/regions/)     | Algemeen beschikbaar | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Algemeen beschikbaar | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure China 21Vianet](https://www.azure.cn/)                                            | Algemeen beschikbaar | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure Duitsland](https://azure.microsoft.com/overview/clouds/germany/)                    | Algemeen beschikbaar | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15

De versie 2019-11-01 wordt momenteel geïmplementeerd en is mogelijk niet beschikbaar in alle regio's.

Deze tabel wordt bijgewerkt wanneer er service-updates zijn en/of er nieuwe ondersteunde versies beschikbaar zijn.

Als u de Instance Metadata Service wilt uitproberen, maakt u een VM op basis van [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) of de [Azure Portal](https://portal.azure.com) in de bovenstaande regio's en volgt u de voor beelden hieronder.
Meer voor beelden van het uitvoeren van een query op IMDS vindt u voor [beelden van Azure instance meta data](https://github.com/microsoft/azureimds)

## <a name="usage"></a>Gebruik

### <a name="versioning"></a>Versiebeheer

De Instance Metadata Service is versie nummer en het opgeven van de API-versie in de HTTP-aanvraag is verplicht.

U kunt de nieuwste versies zien die worden vermeld in deze [beschikbaarheids tabel](#service-availability).

Als nieuwere versies worden toegevoegd, kunnen oudere versies nog steeds worden gebruikt voor compatibiliteit als uw scripts afhankelijk zijn van specifieke gegevens indelingen.

Als er geen versie is opgegeven, wordt er een fout geretourneerd met een lijst met de nieuwste ondersteunde versies.

> [!NOTE]
> Het antwoord is een JSON-teken reeks. Het volgende voor beeld is een mooie afdruk van de Lees baarheid.

**Aanvraag**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**Antwoord**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
    ]
}
```

### <a name="using-headers"></a>Headers gebruiken

Wanneer u een query uitvoert op de Instance Metadata Service, moet u de header-`Metadata: true` opgeven om ervoor te zorgen dat de aanvraag niet per ongeluk is omgeleid.

### <a name="retrieving-metadata"></a>Meta gegevens ophalen

Meta gegevens van exemplaren zijn beschikbaar voor het uitvoeren van Vm's die zijn gemaakt/beheerd met behulp van [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Toegang tot alle gegevens categorieën voor een exemplaar van een virtuele machine met behulp van de volgende aanvraag:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> Alle meta gegevens query's zijn hoofdletter gevoelig.

### <a name="data-output"></a>Gegevens uitvoer

Standaard worden in de Instance Metadata Service gegevens in JSON-indeling (`Content-Type: application/json`) geretourneerd. Verschillende Api's retour neren gegevens echter in verschillende indelingen als dat wordt gevraagd.
De volgende tabel bevat een verwijzing naar andere Data Format-Api's die mogelijk worden ondersteund.

API | Standaard gegevens indeling | Andere indelingen
--------|---------------------|--------------
/instance | json | tekst
/scheduledevents | json | geen
/attested | json | geen

Om toegang te krijgen tot een niet-standaard antwoord indeling, geeft u de aangevraagde indeling op als een query reeks parameter in de aanvraag. Bijvoorbeeld:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Voor blad knooppunten werkt de `format=json` niet. Voor deze query's moet `format=text` expliciet worden opgegeven als de standaard indeling JSON is.

### <a name="security"></a>Beveiliging

Het Instance Metadata Service-eind punt is alleen toegankelijk vanuit het actieve exemplaar van de virtuele machine op een niet-routeerbaar IP-adres. Daarnaast wordt elke aanvraag met een `X-Forwarded-For`-header afgewezen door de service.
Aanvragen moeten ook een `Metadata: true`-header bevatten om ervoor te zorgen dat de daad werkelijke aanvraag rechtstreeks is bedoeld en geen onderdeel van onbedoelde omleiding.

### <a name="error"></a>Fout

Als er geen gegevens element is gevonden of een ongeldige aanvraag is, retourneert de Instance Metadata Service standaard HTTP-fouten. Bijvoorbeeld:

HTTP-statuscode | Reden
----------------|-------
200 OK |
400 ongeldige aanvraag | `Metadata: true` header ontbreekt of de indeling ontbreekt tijdens het uitvoeren van een query op een Leaf-knoop punt
404 niet gevonden | Het aangevraagde element bestaat niet
methode 405 niet toegestaan | Alleen `GET`-aanvragen worden ondersteund
410 verdwenen | Probeer het na enige tijd opnieuw uit met een maximum van 70 seconden
429 te veel aanvragen | De API ondersteunt momenteel Maxi maal vijf query's per seconde
500-service fout     | Na enige tijd opnieuw proberen

### <a name="examples"></a>Voorbeelden

> [!NOTE]
> Alle API-antwoorden zijn JSON-teken reeksen. Alle volgende voor beelden van antwoorden worden afgedrukt voor de Lees baarheid.

#### <a name="retrieving-network-information"></a>Netwerk gegevens ophalen

**Aanvraag**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Antwoord**

> [!NOTE]
> Het antwoord is een JSON-teken reeks. Het volgende voor beeld is een mooie afdruk van de Lees baarheid.

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="retrieving-public-ip-address"></a>Openbaar IP-adres ophalen

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Alle meta gegevens voor een exemplaar ophalen

**Aanvraag**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-06-01"
```

**Antwoord**

> [!NOTE]
> Het antwoord is een JSON-teken reeks. Het volgende voor beeld is een mooie afdruk van de Lees baarheid.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Meta gegevens ophalen in virtuele Windows-machine

**Aanvraag**

Meta gegevens van exemplaren kunnen worden opgehaald in Windows via het hulp programma Power shell `curl`: 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content
```

Of via de `Invoke-RestMethod`-cmdlet:

```powershell

Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2019-06-01 -Method get 
```

**Antwoord**

> [!NOTE]
> Het antwoord is een JSON-teken reeks. Het volgende voor beeld is een mooie afdruk van de Lees baarheid.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="metadata-apis"></a>Api's voor meta gegevens

De volgende Api's zijn beschikbaar via het eind punt voor meta gegevens:

Gegevens | Beschrijving | Geïntroduceerde versie
-----|-------------|-----------------------
Attestation | Zie [attested data](#attested-data) | 10-01-2018
identity | Beheerde identiteiten voor Azure-resources. Zie [een toegangs Token ophalen](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
instance | Zie [instance API](#instance-api) | 2017-04-02
scheduledevents | Zie [Scheduled Events](scheduled-events.md) | 2017-08-01

### <a name="instance-api"></a>Exemplaar-API

De volgende Compute-categorieën zijn beschikbaar via de instance API:

> [!NOTE]
> Via het eind punt van de meta gegevens zijn de volgende categorieën toegankelijk via instance/compute

Gegevens | Beschrijving | Geïntroduceerde versie
-----|-------------|-----------------------
azEnvironment | Azure-omgeving waarin de virtuele machine wordt uitgevoerd | 10-01-2018
customData | Deze functie is momenteel uitgeschakeld en deze documentatie wordt bijgewerkt zodra deze beschikbaar is | 2019-02-01
locatie | Azure-regio waarin de virtuele machine wordt uitgevoerd | 2017-04-02
naam | Naam van de virtuele machine | 2017-04-02
offer | Informatie over de installatie kopie van de virtuele machine weer geven en die alleen aanwezig is voor installatie kopieën die vanuit de Azure-installatie kopie galerie | 2017-04-02
besturingssysteemtype | Linux of Windows | 2017-04-02
placementGroupId | [Plaatsings groep](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) van de schaalset voor virtuele machines | 2017-08-01
Fonds | [Plan](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) met naam, product en uitgever voor een virtuele machine als dit een Azure Marketplace-installatie kopie is | 2018-04-02
platformUpdateDomain |  [Domein bijwerken](manage-availability.md) waarop de VM wordt uitgevoerd | 2017-04-02
platformFaultDomain | [Fout domein](manage-availability.md) waarop de VM wordt uitgevoerd | 2017-04-02
provider | Provider van de virtuele machine | 10-01-2018
publicKeys | [Verzameling van open bare sleutels](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) die zijn toegewezen aan de virtuele machine en de paden | 2018-04-02
publisher | Uitgever van de VM-installatie kopie | 2017-04-02
resourceGroupName | [Resource groep](../../azure-resource-manager/management/overview.md) voor uw virtuele machine | 2017-08-01
resourceId | De [volledig gekwalificeerde](https://docs.microsoft.com/rest/api/resources/resources/getbyid) id van de resource | 2019-03-11
sku | Specifieke SKU voor de VM-installatie kopie | 2017-04-02
storageProfile | [Opslag profiel](#storage-profile) bekijken | 2019-06-01
subscriptionId | Azure-abonnement voor de virtuele machine | 2017-08-01
tags | [Labels](../../azure-resource-manager/management/tag-resources.md) voor uw virtuele machine  | 2017-08-01
tagsList | Tags die zijn opgemaakt als een JSON-matrix voor eenvoudiger programmatisch parseren  | 2019-06-04
version | Versie van de VM-installatie kopie | 2017-04-02
vmId | De [unieke id](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) voor de virtuele machine | 2017-04-02
vmScaleSetName | [Naam van de schaalset voor virtuele machines](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) van de schaalset voor virtuele machines | 2017-12-01
vmSize | [VM-grootte](sizes.md) | 2017-04-02
zone | [Beschikbaarheids zone](../../availability-zones/az-overview.md) van uw virtuele machine | 2017-12-01

De volgende netwerk categorieën zijn beschikbaar via de exemplaar-API:

> [!NOTE]
> Via het eind punt van de meta gegevens zijn de volgende categorieën toegankelijk via instance/Network/Interface

Gegevens | Beschrijving | Geïntroduceerde versie
-----|-------------|-----------------------
ipv4/privateIpAddress | Lokaal IPv4-adres van de virtuele machine | 2017-04-02
ipv4/publicIpAddress | Openbaar IPv4-adres van de virtuele machine | 2017-04-02
subnet/adres | Subnetadres van de virtuele machine | 2017-04-02
subnet/voor voegsel | Subnetvoorvoegsel, voor beeld 24 | 2017-04-02
ipv6/ipAddress | Lokaal IPv6-adres van de virtuele machine | 2017-04-02
macAddress | Mac-adres van VM | 2017-04-02

## <a name="attested-data"></a>Attestende gegevens

Een deel van het scenario dat wordt verzorgd door Instance Metadata Service is om ervoor te zorgen dat de verstrekte gegevens afkomstig zijn van Azure. We ondertekenen een deel van deze informatie zodat installatie kopieën van Marketplace er zeker van kunnen zijn dat de installatie kopie wordt uitgevoerd op Azure.

### <a name="example-attested-data"></a>Voor beeld van Attestation-gegevens

> [!NOTE]
> Alle API-antwoorden zijn JSON-teken reeksen. De volgende voorbeeld antwoorden zijn tamelijk afgedrukt voor de Lees baarheid.

 **Aanvraag**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

API-Version is een verplicht veld. Raadpleeg de [sectie Service beschikbaarheid](#service-availability) voor ondersteunde API-versies.
Nonce is een optionele teken reeks van tien cijfers. Als u dit niet opgeeft, retourneert IMDS de huidige UTC-tijds tempel. Vanwege het cache mechanisme van IMDS kan een eerder in de cache opgeslagen nonce-waarde worden geretourneerd.

 **Antwoord**

> [!NOTE]
> Het antwoord is een JSON-teken reeks. Het volgende voor beeld is een mooie afdruk van de Lees baarheid.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

De hand tekening-blob is een ondertekende [pkcs7](https://aka.ms/pkcs7) -versie van het document. Het bevat het certificaat dat wordt gebruikt voor het ondertekenen samen met de VM-Details zoals vmId, SKU, nonce, subscriptionId, time stamp voor het maken en verlopen van het document en de plannings informatie over de installatie kopie. De plan gegevens worden alleen ingevuld voor installatie kopieën van Azure-markt plaatsen. Het certificaat kan worden geëxtraheerd uit het antwoord en wordt gebruikt om te valideren dat het antwoord geldig is en afkomstig is van Azure.

#### <a name="retrieving-attested-metadata-in-windows-virtual-machine"></a>De verwerkte meta gegevens worden opgehaald in de virtuele Windows-machine

 **Aanvraag**

Meta gegevens van exemplaren kunnen worden opgehaald in Windows via het hulp programma Power shell `curl`:

 ```powershell
curl -H @{'Metadata'='true'} "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" | select -ExpandProperty Content
```

 Of via de `Invoke-RestMethod`-cmdlet:

 ```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" -Method get
```

API-Version is een verplicht veld. Raadpleeg de sectie Service beschikbaarheid voor ondersteunde API-versies.
Nonce is een optionele teken reeks van tien cijfers. Als u dit niet opgeeft, retourneert IMDS de huidige UTC-tijds tempel. Vanwege het cache mechanisme van IMDS kan een eerder in de cache opgeslagen nonce-waarde worden geretourneerd.

 **Antwoord**

> [!NOTE]
> Het antwoord is een JSON-teken reeks. Het volgende voor beeld is een mooie afdruk van de Lees baarheid.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

De hand tekening-blob is een ondertekende [pkcs7](https://aka.ms/pkcs7) -versie van het document. Het bevat het certificaat dat wordt gebruikt voor het ondertekenen samen met de VM-Details zoals vmId, SKU, nonce, subscriptionId, time stamp voor het maken en verlopen van het document en de plannings informatie over de installatie kopie. De plan gegevens worden alleen ingevuld voor installatie kopieën van Azure-markt plaatsen. Het certificaat kan worden geëxtraheerd uit het antwoord en wordt gebruikt om te valideren dat het antwoord geldig is en afkomstig is van Azure.

## <a name="example-scenarios-for-usage"></a>Voorbeeld scenario's voor gebruik  

### <a name="tracking-vm-running-on-azure"></a>VM bijhouden waarop Azure wordt uitgevoerd

Als service provider kan het nodig zijn om het aantal Vm's waarop de software wordt uitgevoerd bij te houden of om agents te hebben die de uniekheid van de virtuele machine moeten bijhouden. Als u een unieke ID voor een virtuele machine wilt ophalen, gebruikt u het veld `vmId` van Instance Metadata Service.

**Aanvraag**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Antwoord**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Plaatsen van containers, fout-/updatedomein op basis van gegevenspartities

Voor bepaalde scenario's is het plaatsen van verschillende gegevens replica's van groot belang. Het is bijvoorbeeld mogelijk dat u de `platformFaultDomain` van [HDFS replica](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) of container plaatsing via een [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) wilt weten en dat `platformUpdateDomain` de virtuele machine wordt uitgevoerd.
U kunt [Beschikbaarheidszones](../../availability-zones/az-overview.md) ook gebruiken voor de instanties om deze beslissingen te nemen.
U kunt deze gegevens rechtstreeks opvragen via de Instance Metadata Service.

**Aanvraag**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Antwoord**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Meer informatie krijgen over de VM tijdens de ondersteuningsaanvraag

Als service provider kunt u een ondersteunings oproep krijgen waarin u meer informatie over de virtuele machine wilt weten. Als u de klant vraagt om de berekenings-meta gegevens te delen, kan de ondersteunings medewerker basis informatie geven over het type virtuele machine op Azure.

**Aanvraag**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
```

**Antwoord**

> [!NOTE]
> Het antwoord is een JSON-teken reeks. Het volgende voor beeld is een mooie afdruk van de Lees baarheid.

```json
{
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
}
```

### <a name="getting-azure-environment-where-the-vm-is-running"></a>Azure-omgeving ophalen waarin de VM wordt uitgevoerd

Azure heeft verschillende soevereine Clouds, zoals [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Soms hebt u de Azure-omgeving nodig om enkele runtime beslissingen te nemen. In het volgende voor beeld ziet u hoe u dit gedrag kunt behaalt.

**Aanvraag**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Antwoord**

```bash
AzurePublicCloud
```

De Cloud en de waarden van de Azure-omgeving worden hieronder weer gegeven.

 Cloud   | Azure-omgeving
---------|-----------------
[Alle algemeen beschik bare wereld wijde Azure-regio's](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Duitsland](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

### <a name="getting-the-tags-for-the-vm"></a>De labels voor de virtuele machine ophalen

Labels zijn mogelijk toegepast op uw virtuele Azure-machine om ze logisch in een taxonomie te organiseren. De labels die aan een virtuele machine zijn toegewezen, kunnen worden opgehaald met behulp van de onderstaande aanvraag.

**Aanvraag**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Antwoord**

```text
Department:IT;Environment:Test;Role:WebRole
```

Het `tags` veld is een teken reeks waarbij de labels worden gescheiden door punt komma's. Dit kan een probleem zijn als punt komma's worden gebruikt in de labels zelf. Als een parser is geschreven om de Tags programmatisch uit te pakken, moet u vertrouwen op het `tagsList` veld dat een JSON-matrix met geen scheidings tekens is, en dus gemakkelijker te parseren is.

**Aanvraag**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04&format=json"
```

**Antwoord**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

### <a name="validating-that-the-vm-is-running-in-azure"></a>Valideren dat Azure wordt uitgevoerd op de VM

Marketplace-leveranciers willen ervoor zorgen dat hun software alleen in azure wordt uitgevoerd. Als iemand de VHD naar een on-premises kopie kopieert, moeten ze die kunnen detecteren. Bij het aanroepen van Instance Metadata Service kunnen Marketplace-leveranciers ondertekende gegevens ophalen die alleen reageren vanuit Azure.

> [!NOTE]
> Vereist dat JQ wordt geïnstalleerd.

**Aanvraag**

 ```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2019-04-30 | jq -r '.["signature"]' > signature
  # Decode the signature
  base64 -d signature > decodedsignature
  #Get PKCS7 format
  openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
  # Get Public key out of pkc7
  openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
  #Get the intermediate certificate
  wget -q -O intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
  openssl x509 -inform der -in intermediate.cer -out intermediate.pem
  #Verify the contents
  openssl smime -verify -in sign.pk7 -inform pem -noverify
 ```

 **Antwoord**

```json
Verification successful
{"nonce":"20181128-001617",
  "plan":
    {
     "name":"",
     "product":"",
     "publisher":""
    },
"timeStamp":
  {
    "createdOn":"11/28/18 00:16:17 -0000",
    "expiresOn":"11/28/18 06:16:17 -0000"
  },
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34",
"subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
"sku": "RS3-Pro"
}
```

Gegevens | Beschrijving
-----|------------
nonce | Door de gebruiker opgegeven optionele teken reeks met de aanvraag. Als er geen nonce in de aanvraag is opgegeven, wordt de huidige UTC-tijds tempel geretourneerd
Fonds | Een virtuele machine in een Azure Marketplace-installatie kopie [plannen](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) , met naam, product en uitgever
timestamp/createdOn | De UTC-tijds tempel waarop het eerste ondertekende document is gemaakt
timestamp/expiresOn | De UTC-tijds tempel waarop het ondertekende document verloopt
vmId |  De [unieke id](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) voor de virtuele machine
subscriptionId | Azure-abonnement voor de virtuele machine, geïntroduceerd in `2019-04-30`
sku | Specifieke SKU voor de VM-installatie kopie, geïntroduceerd in `2019-11-01`

#### <a name="verifying-the-signature"></a>De hand tekening verifiëren

Nadat u de hand tekening hierboven hebt opgehaald, kunt u controleren of de hand tekening afkomstig is van micro soft. U kunt ook het tussenliggende certificaat en de certificaat keten controleren. Ten slotte kunt u controleren of de abonnements-ID juist is.

> [!NOTE]
> Het certificaat voor open bare Cloud en soevereine Cloud wijkt af.

 Cloud | Certificaat
---------|-----------------
[Alle algemeen beschik bare wereld wijde Azure-regio's](https://azure.microsoft.com/regions/)     | *. metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | *. metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | *. metadata.azure.cn
[Azure Duitsland](https://azure.microsoft.com/overview/clouds/germany/)                    | *. metadata.microsoftazure.de

Er is een bekend probleem rond het certificaat dat wordt gebruikt voor het ondertekenen. De certificaten hebben mogelijk niet exact overeenkomen met de `metadata.azure.com` voor de open bare Cloud. Daarom moet de certificerings validatie een algemene naam van een `.metadata.azure.com` subdomein toestaan.

```bash

# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

In gevallen waarin het tussenliggende certificaat niet kan worden gedownload vanwege netwerk beperkingen tijdens de validatie, kan het tussenliggende certificaat worden vastgemaakt. Azure zal de certificaten echter ook volgens de standaard-PKI-praktijk inrollen. De vastgemaakte certificaten moeten worden bijgewerkt wanneer er wordt gekanteld. Wanneer een wijziging voor het bijwerken van het tussenliggende certificaat is gepland, wordt het Azure-blog bijgewerkt en ontvangen Azure-klanten een melding. De tussenliggende certificaten kunt u [hier](https://www.microsoft.com/pki/mscorp/cps/default.htm)vinden. De tussenliggende certificaten voor elk van de regio's kunnen verschillen.

### <a name="failover-clustering-in-windows-server"></a>Failover Clustering in Windows Server

Bij het uitvoeren van een query op Instance Metadata Service met Failoverclustering, moet u voor bepaalde scenario's een route toevoegen aan de routerings tabel.

1. Open de opdracht prompt met beheerders bevoegdheden.

2. Voer de volgende opdracht uit en noteer het adres van de interface voor de netwerk bestemming (`0.0.0.0`) in de IPv4-route tabel.

```bat
route print
```

> [!NOTE]
> De volgende voorbeeld uitvoer van een Windows Server-VM met failovercluster ingeschakeld bevat alleen de IPv4-route tabel voor eenvoud.

```bat
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         10.0.1.10    266
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

1. Voer de volgende opdracht uit en gebruik het adres van de interface voor de netwerk bestemming (`0.0.0.0`) (`10.0.1.10`) in dit voor beeld.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="storage-profile"></a>Opslagprofiel

Instance Metadata Service kunt details opgeven over de opslag schijven die zijn gekoppeld aan de virtuele machine. Deze gegevens zijn te vinden op het eind punt van de instantie/Compute/storageProfile.

Het opslag profiel van een virtuele machine is onderverdeeld in drie categorieën: verwijzing naar afbeelding, besturingssysteem schijf en gegevens schijven.

Het verwijzings object voor de afbeelding bevat de volgende informatie over de installatie kopie van het besturings systeem:

Gegevens    | Beschrijving
--------|-----------------
id      | Resource-id
offer   | Aanbieding van de installatie kopie van het platform of de Marketplace
publisher | Uitgever van installatie kopie
sku     | Afbeeldings-SKU
version | Versie van de installatie kopie van het platform of de Marketplace

Het object van de besturingssysteem schijf bevat de volgende informatie over de besturingssysteem schijf die wordt gebruikt door de virtuele machine:

Gegevens    | Beschrijving
--------|-----------------
in | Cache vereisten
createOption | Informatie over de manier waarop de virtuele machine is gemaakt
diffDiskSettings | Tijdelijke schijf instellingen
diskSizeGB | Grootte van de schijf in GB
image   | Virtuele harde schijf voor installatie kopie van bron gebruiker
LUN     | Nummer van de logische eenheid van de schijf
managedDisk | Beheerde schijf parameters
naam    | Schijf naam
schijven     | Virtuele harde schijf
writeAcceleratorEnabled | Hiermee wordt aangegeven of Write Accelerator is ingeschakeld op de schijf

De matrix gegevens schijven bevat een lijst met gegevens schijven die zijn gekoppeld aan de VM. Elk gegevens schijf object bevat de volgende informatie:

Gegevens    | Beschrijving
--------|-----------------
in | Cache vereisten
createOption | Informatie over de manier waarop de virtuele machine is gemaakt
diffDiskSettings | Tijdelijke schijf instellingen
diskSizeGB | Grootte van de schijf in GB
encryptionSettings | Versleutelings instellingen voor de schijf
image   | Virtuele harde schijf voor installatie kopie van bron gebruiker
managedDisk | Beheerde schijf parameters
naam    | Schijf naam
besturingssysteemtype  | Type besturings systeem dat is opgenomen in de schijf
schijven     | Virtuele harde schijf
writeAcceleratorEnabled | Hiermee wordt aangegeven of Write Accelerator is ingeschakeld op de schijf

Hier volgt een voor beeld van hoe u een query kunt uitvoeren op de opslag gegevens van de virtuele machine.

**Aanvraag**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
```

**Antwoord**

> [!NOTE]
> Het antwoord is een JSON-teken reeks. Het volgende voor beeld is een mooie afdruk van de Lees baarheid.

```json
{
    "dataDisks": [
      {
        "caching": "None",
        "createOption": "Empty",
        "diskSizeGB": "1024",
        "image": {
          "uri": ""
        },
        "lun": "0",
        "managedDisk": {
          "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
          "storageAccountType": "Standard_LRS"
        },
        "name": "exampledatadiskname",
        "vhd": {
          "uri": ""
        },
        "writeAcceleratorEnabled": "false"
      }
    ],
    "imageReference": {
      "id": "",
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04.0-LTS",
      "version": "latest"
    },
    "osDisk": {
      "caching": "ReadWrite",
      "createOption": "FromImage",
      "diskSizeGB": "30",
      "diffDiskSettings": {
        "option": "Local"
      },
      "encryptionSettings": {
        "enabled": "false"
      },
      "image": {
        "uri": ""
      },
      "managedDisk": {
        "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
        "storageAccountType": "Standard_LRS"
      },
      "name": "exampleosdiskname",
      "osType": "Linux",
      "vhd": {
        "uri": ""
      },
      "writeAcceleratorEnabled": "false"
    }
}
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Voor beelden van het aanroepen van meta gegevens service met verschillende talen in de VM

Taal | Voorbeeld
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Aan de slag  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Javascript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata

## <a name="faq"></a>Veelgestelde vragen

1. Ik krijg de fout `400 Bad Request, Required metadata header not specified`. Wat betekent dit?
   * Het Instance Metadata Service vereist dat de header `Metadata: true` worden door gegeven in de aanvraag. Als deze header wordt door gegeven in de REST-aanroep, is toegang tot de Instance Metadata Service toegestaan.
2. Waarom krijg ik geen reken gegevens voor mijn virtuele machine?
   * Momenteel ondersteunt de Instance Metadata Service alleen instanties die zijn gemaakt met Azure Resource Manager. In de toekomst kan ondersteuning voor Cloud service-Vm's worden toegevoegd.
3. Ik heb mijn virtuele machine door Azure Resource Manager een tijdje gemaakt. Waarom zie ik geen gegevens voor reken gegevens berekenen?
   * Voor virtuele machines die na sep 2016 zijn gemaakt, voegt u een [tag](../../azure-resource-manager/management/tag-resources.md) toe om te beginnen met het bekijken van meta gegevens. Voor oudere Vm's (gemaakt vóór sep 2016) kunt u uitbrei dingen of gegevens schijven aan de virtuele machine toevoegen/verwijderen om meta gegevens te vernieuwen.
4. Ik zie niet alle gegevens die zijn ingevuld voor een nieuwe versie
   * Voor virtuele machines die na sep 2016 zijn gemaakt, voegt u een [tag](../../azure-resource-manager/management/tag-resources.md) toe om te beginnen met het bekijken van meta gegevens. Voor oudere Vm's (gemaakt vóór sep 2016) kunt u uitbrei dingen of gegevens schijven aan de virtuele machine toevoegen/verwijderen om meta gegevens te vernieuwen.
5. Waarom krijg ik de fout `500 Internal Server Error`?
   * Voer de aanvraag opnieuw uit op basis van een exponentieel back-systeem. Neem contact op met de ondersteuning van Azure als het probleem zich blijft voordoen.
6. Waar kan ik extra vragen/opmerkingen delen?
   * Stuur uw opmerkingen over https://feedback.azure.com.
7. Zou dit werken voor de virtuele-machine Scale set-instantie?
   * Ja meta gegevens service is beschikbaar voor instanties van schaal sets.
8. Hoe kan ik ondersteuning voor de service krijgen?
   * Als u ondersteuning voor de service wilt, maakt u een ondersteunings probleem in Azure Portal voor de virtuele machine waar u geen meta gegevens reacties meer kunt krijgen na lange pogingen.
9. Er is een time-out opgetreden voor de aanvraag voor mijn oproep naar de service?
   * Meta gegevens moeten worden gemaakt van het primaire IP-adres dat is toegewezen aan de primaire netwerk kaart van de virtuele machine, naast het geval u de routes hebt gewijzigd, moet u een route voor 169.254.0.0/16-adres uit uw netwerk kaart hebben.
10. Ik heb mijn tags in de virtuele-machine schaalset bijgewerkt, maar ze worden niet weer gegeven in de instanties in tegens telling tot virtuele machines?
    * Momenteel worden alleen ScaleSets-Tags weer gegeven voor de VM op het opnieuw opstarten/terugzetten van de installatie kopie/of een schijf wijziging in het exemplaar.

    ![Ondersteuning van meta gegevens van exemplaren](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Scheduled Events](scheduled-events.md)
