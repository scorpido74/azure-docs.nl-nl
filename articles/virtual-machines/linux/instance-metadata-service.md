---
title: Azure-instantiemetagegevensservice
description: RESTful-interface om informatie te krijgen over Linux VM's compute, netwerk en aankomende onderhoudsgebeurtenissen.
services: virtual-machines-linux
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines-linux
ms.subservice: monitoring
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: 3281b4dafa5436c9df760ac8aa3fc82f535b4286
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944872"
---
# <a name="azure-instance-metadata-service"></a>Azure-instantiemetagegevensservice

De ImdS (Azure Instance Metadata Service) biedt informatie over het momenteel uitvoeren van virtuele machine-exemplaren en kan worden gebruikt om uw virtuele machines te beheren en te configureren.
De verstrekte informatie omvat de SKU, netwerkconfiguratie en aankomende onderhoudsgebeurtenissen. Zie [api's voor metagegevens](#metadata-apis)voor een volledige lijst met beschikbare gegevens.

Azure's Instance Metadata Service is een REST Endpoint dat toegankelijk is voor alle IaaS VM's die zijn gemaakt via Azure [Resource Manager.](https://docs.microsoft.com/rest/api/resources/)
Het eindpunt is beschikbaar op een bekend niet-routable IP-adres (`169.254.169.254`) dat alleen toegankelijk is vanuit de VM.

> [!IMPORTANT]
> Deze service is **algemeen beschikbaar** in alle Azure-regio's.  Het ontvangt regelmatig updates om nieuwe informatie over virtuele machine-exemplaren bloot te leggen. Deze pagina geeft de up-to-date [metadata API's](#metadata-apis) weer.

## <a name="service-availability"></a>Beschikbaarheid van service

De service is beschikbaar in algemeen beschikbare Azure-regio's. Mogelijk is niet alle API-versie beschikbaar in alle Azure-regio's.

Regio's                                        | Beschikbaarheid?                                 | Ondersteunde versies
-----------------------------------------------|-----------------------------------------------|-----------------
[Alle algemeen beschikbare Globale Azure-regio's](https://azure.microsoft.com/regions/)     | Algemeen beschikbaar | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Algemeen beschikbaar | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure China 21Vianet](https://www.azure.cn/)                                            | Algemeen beschikbaar | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure Duitsland](https://azure.microsoft.com/overview/clouds/germany/)                    | Algemeen beschikbaar | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15

De versie 2019-11-01 wordt momenteel geïmplementeerd en is mogelijk niet in alle regio's beschikbaar.

Deze tabel wordt bijgewerkt wanneer er service-updates zijn en/of er nieuwe ondersteunde versies beschikbaar zijn.

Als u de instantiemetagegevensservice wilt uitproberen, maakt u een VM van [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) of de [Azure-portal](https://portal.azure.com) in de bovenstaande regio's en volgt u de onderstaande voorbeelden.
Andere voorbeelden van het opvragen van IMDS zijn te vinden in [Azure Instance Metadata Samples](https://github.com/microsoft/azureimds)

## <a name="usage"></a>Gebruik

### <a name="versioning"></a>Versiebeheer

De instantiemetagegevensservice is versieed en het opgeven van de API-versie in de HTTP-aanvraag is verplicht.

U de nieuwste versies in deze [beschikbaarheidstabel](#service-availability)bekijken.

Als nieuwere versies worden toegevoegd, kunnen oudere versies nog steeds worden benaderd voor compatibiliteit als uw scripts afhankelijk zijn van specifieke gegevensindelingen.

Wanneer er geen versie is opgegeven, wordt een fout geretourneerd met een lijst met de nieuwste ondersteunde versies.

> [!NOTE]
> Het antwoord is een JSON-tekenreeks. Het volgende voorbeeld antwoord is vrij gedrukt voor leesbaarheid.

**Aanvraag**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**Reactie**

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

### <a name="using-headers"></a>Kopteksten gebruiken

Wanneer u de instantiemetagegevensservice opvraagt, moet u de koptekst `Metadata: true` opgeven om ervoor te zorgen dat de aanvraag niet onbedoeld is doorgestuurd.

### <a name="retrieving-metadata"></a>Metagegevens ophalen

Metagegevens van instantie zijn beschikbaar voor het uitvoeren van VM's die zijn gemaakt/beheerd met [Azure Resource Manager.](https://docs.microsoft.com/rest/api/resources/) Toegang tot alle gegevenscategorieën voor een virtuele machine-instantie met behulp van de volgende aanvraag:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> Alle metagegevensquery's van bijvoorbeeld zijn hoofdlettergevoelig.

### <a name="data-output"></a>Gegevensuitvoer

Standaard retourneert de Instantie Metagegevensservice`Content-Type: application/json`gegevens in JSON-indeling ( ). Verschillende API's retourneren echter gegevens in verschillende indelingen indien daarom wordt gevraagd.
De volgende tabel is een verwijzing naar andere gegevensindelingen die API's kunnen ondersteunen.

API | Standaardgegevensindeling | Andere indelingen
--------|---------------------|--------------
/Instance | json | tekst
/geplande evenementen | json | geen
/atest | json | geen

Als u toegang wilt krijgen tot een niet-standaardantwoordindeling, geeft u de opgevraagde indeling op als parameter voor querytekenreeksen in de aanvraag. Bijvoorbeeld:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Voor bladknooppunten `format=json` werkt het niet. Voor deze `format=text` query's moet expliciet worden opgegeven als de standaardindeling json is.

### <a name="security"></a>Beveiliging

Het eindpunt van de instance metadata service is alleen toegankelijk vanuit de gebruikte virtuele machine-instantie op een niet-routeerbaar IP-adres. Bovendien wordt elke aanvraag `X-Forwarded-For` met een header afgewezen door de service.
Aanvragen moeten ook `Metadata: true` een header bevatten om ervoor te zorgen dat de werkelijke aanvraag direct was bedoeld en geen onderdeel van onbedoelde omleiding.

### <a name="error"></a>Fout

Als er een gegevenselement niet is gevonden of als een verkeerd vormgevende aanvraag, retourneert de instantiemetagegevensservice standaard HTTP-fouten. Bijvoorbeeld:

HTTP-statuscode | Reden
----------------|-------
200 OK |
400 Slecht verzoek | De `Metadata: true` indeling ontbreken of ontbreken bij het opvragen van een bladknooppunt
404 Niet gevonden | Het gevraagde element bestaat niet
405 Methode niet toegestaan | Alleen `GET` aanvragen worden ondersteund
410 Weg | Opnieuw proberen na enige tijd voor een max van 70 seconden
429 Te veel aanvragen | De API ondersteunt momenteel maximaal 5 query's per seconde
500 Servicefout     | Opnieuw proberen na enige tijd

### <a name="examples"></a>Voorbeelden

> [!NOTE]
> Alle API-antwoorden zijn JSON-tekenreeksen. Alle volgende voorbeeldreacties zijn mooi afgedrukt voor leesbaarheid.

#### <a name="retrieving-network-information"></a>Netwerkgegevens ophalen

**Aanvraag**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Reactie**

> [!NOTE]
> Het antwoord is een JSON-tekenreeks. Het volgende voorbeeld antwoord is vrij gedrukt voor leesbaarheid.

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

#### <a name="retrieving-public-ip-address"></a>Openbare IP-adres ophalen

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Alle metagegevens voor een instantie ophalen

**Aanvraag**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-06-01"
```

**Reactie**

> [!NOTE]
> Het antwoord is een JSON-tekenreeks. Het volgende voorbeeld antwoord is vrij gedrukt voor leesbaarheid.

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

## <a name="metadata-apis"></a>API's voor metagegevens

De volgende API's zijn beschikbaar via het eindpunt metagegevens:

Gegevens | Beschrijving | Versie geïntroduceerd
-----|-------------|-----------------------
getest | Zie [Getuigde gegevens](#attested-data) | 2018-10-01
identity | Beheerde identiteiten voor Azure-bronnen. Zie [een toegangstoken verwerven](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
Exemplaar | Zie [Instantie API](#instance-api) | 2017-04-02
geplande evenementen | Geplande [gebeurtenissen bekijken](scheduled-events.md) | 2017-08-01

### <a name="instance-api"></a>Instantie-API

De volgende compute-categorieën zijn beschikbaar via de Instance API:

> [!NOTE]
> Via het eindpunt met metagegevens worden de volgende categorieën geopend via instance/compute

Gegevens | Beschrijving | Versie geïntroduceerd
-----|-------------|-----------------------
azMilieu | Azure-omgeving waarin de VM wordt uitgevoerd | 2018-10-01
customData | Deze functie is momenteel uitgeschakeld en we werken deze documentatie bij wanneer deze beschikbaar is | 2019-02-01
location | Azure-regio waarin de VM wordt uitgevoerd | 2017-04-02
name | Naam van de VM | 2017-04-02
offer | Informatie aanbieden voor de VM-afbeelding en is alleen aanwezig voor afbeeldingen die zijn geïmplementeerd vanuit azure-afbeeldingsgalerie | 2017-04-02
osType | Linux of Windows | 2017-04-02
plaatsingGroupId | [Plaatsingsgroep](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) van uw virtuele machineschaalset | 2017-08-01
plannen | [Plan](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) met naam, product en uitgever voor een vm als het een Azure Marketplace-afbeelding is | 2018-04-02
platformUpdateDomein |  [Domein bijwerken waarin](manage-availability.md) de VIRTUELE VVM wordt uitgevoerd | 2017-04-02
platformFaultDomain | [Foutdomein](manage-availability.md) waarin de VM wordt uitgevoerd | 2017-04-02
Provider | Aanbieder van de VM | 2018-10-01
publicKeys | [Verzameling van openbare sleutels](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) die zijn toegewezen aan de VM en paden | 2018-04-02
uitgever | Uitgever van de VM-afbeelding | 2017-04-02
resourceGroupName | [Resourcegroep](../../azure-resource-manager/management/overview.md) voor uw virtuele machine | 2017-08-01
resourceId | De [volledig gekwalificeerde](https://docs.microsoft.com/rest/api/resources/resources/getbyid) ID van de resource | 2019-03-11
sku | Specifieke SKU voor de VM-afbeelding | 2017-04-02
storageProfiel | Zie [Opslagprofiel](#storage-profile) | 2019-06-01
subscriptionId | Azure-abonnement voor de virtuele machine | 2017-08-01
tags | [Tags](../../azure-resource-manager/management/tag-resources.md) voor uw virtuele machine  | 2017-08-01
tagsLijst | Tags opgemaakt als een JSON-array voor eenvoudiger programmatisch ontleden  | 2019-06-04
versie | Versie van de VM-afbeelding | 2017-04-02
vmId (vmId) | [Unieke id](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) voor de VM | 2017-04-02
vmScaleSetName | [Virtuele machineschaalset Naam](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) van uw virtuele machineschaalset | 2017-12-01
vmSize | [VM-grootte](sizes.md) | 2017-04-02
zone | [Beschikbaarheidszone](../../availability-zones/az-overview.md) van uw virtuele machine | 2017-12-01

De volgende netwerkcategorieën zijn beschikbaar via de Instance API:

> [!NOTE]
> Via het metadata eindpunt worden de volgende categorieën geopend via instance/network/interface

Gegevens | Beschrijving | Versie geïntroduceerd
-----|-------------|-----------------------
ipv4/privateIpAddress | Lokaal IPv4-adres van de VM | 2017-04-02
ipv4/publicIpAddress | Openbaar IPv4-adres van de VM | 2017-04-02
subnet/adres | Subnetadres van de VM | 2017-04-02
subnet/voorvoegsel | Subnetvoorvoegsel, voorbeeld 24 | 2017-04-02
ipv6/ipAddress | Lokaal IPv6-adres van de VM | 2017-04-02
macAddress | VM-mac-adres | 2017-04-02

## <a name="attested-data"></a>Getuigde gegevens

Een deel van het scenario dat wordt weergegeven door Instance Metadata Service is om garanties te bieden dat de verstrekte gegevens afkomstig zijn van Azure. We ondertekenen een deel van deze informatie, zodat marketplace-afbeeldingen er zeker van kunnen zijn dat het hun afbeelding is die op Azure wordt uitgevoerd.

### <a name="example-attested-data"></a>Voorbeeld Van atestgegevens

> [!NOTE]
> Alle API-antwoorden zijn JSON-tekenreeksen. De volgende voorbeeldreacties zijn vrij gedrukt voor leesbaarheid.

 **Aanvraag**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

Api-versie is een verplicht veld. Raadpleeg de [sectie servicebeschikbaarheid](#service-availability) voor ondersteunde API-versies.
Nonce is een optionele tekenreeks van 10 cijfers. Als dit niet is verstrekt, retourneert IMDS de huidige UTC-tijdstempel op zijn plaats. Als gevolg van imds's caching mechanisme, een eerder in de cache opgeslagen nonce waarde kan worden geretourneerd.

 **Reactie**

> [!NOTE]
> Het antwoord is een JSON-tekenreeks. Het volgende voorbeeld antwoord is vrij gedrukt voor leesbaarheid.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

De handtekeningblob is een [door pkcs7](https://aka.ms/pkcs7) ondertekende versie van het document. Het bevat het certificaat dat wordt gebruikt voor het ondertekenen, samen met de VM-details zoals vmId, sku, nonce, subscriptionId, timeStamp voor het maken en verlopen van het document en de planinformatie over de afbeelding. De plangegevens worden alleen ingevuld voor afbeeldingen op Azure Market Place. Het certificaat kan uit het antwoord worden gehaald en worden gebruikt om te valideren dat het antwoord geldig is en afkomstig is van Azure.

## <a name="example-scenarios-for-usage"></a>Voorbeeldscenario's voor het gebruik  

### <a name="tracking-vm-running-on-azure"></a>VM bijhouden waarop Azure wordt uitgevoerd

Als serviceprovider moet u mogelijk het aantal VM's bijhouden waarop uw software wordt uitgevoerd of dat u agents hebt die de uniciteit van de VM moeten bijhouden. Gebruik het `vmId` veld van Instance Metadata Service om een unieke ID voor een VM te kunnen krijgen.

**Aanvraag**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Reactie**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Plaatsen van containers, fout-/updatedomein op basis van gegevenspartities

Voor bepaalde scenario's is het plaatsen van verschillende gegevensreplica's van groot belang. [HDFS-replicaplaatsing](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) of containerplaatsing via een [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) kan `platformFaultDomain` bijvoorbeeld `platformUpdateDomain` nodig zijn om de VM te kennen en waarop de VM wordt uitgevoerd.
U [beschikbaarheidszones](../../availability-zones/az-overview.md) ook gebruiken voor de instanties om deze beslissingen te nemen.
U deze gegevens rechtstreeks opvragen via de Instance Metadata Service.

**Aanvraag**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Reactie**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Meer informatie krijgen over de VM tijdens de ondersteuningsaanvraag

Als serviceprovider u een ondersteuningsgesprek krijgen waarbij u meer informatie over de VM wilt weten. De klant vragen om de compute metadata te delen kan basisinformatie geven voor de ondersteuningsprofessional om te weten te komen over het soort VM op Azure.

**Aanvraag**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
```

**Reactie**

> [!NOTE]
> Het antwoord is een JSON-tekenreeks. Het volgende voorbeeld antwoord is vrij gedrukt voor leesbaarheid.

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

Azure heeft verschillende soevereine clouds zoals [Azure Government.](https://azure.microsoft.com/overview/clouds/government/) Soms hebt u de Azure-omgeving nodig om een aantal runtime-beslissingen te nemen. In het volgende voorbeeld ziet u hoe u dit gedrag bereiken.

**Aanvraag**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Reactie**

```bash
AzurePublicCloud
```

De cloud en de waarden van de Azure-omgeving worden hieronder weergegeven.

 Cloud   | Azure-omgeving
---------|-----------------
[Alle algemeen beschikbare Globale Azure-regio's](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Duitsland](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

### <a name="getting-the-tags-for-the-vm"></a>De tags voor de VM zoeken

Tags zijn mogelijk toegepast op uw Azure VM om ze logisch te ordenen in een taxonomie. De tags die aan een VM zijn toegewezen, kunnen worden opgehaald met behulp van de onderstaande aanvraag.

**Aanvraag**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Reactie**

```text
Department:IT;Environment:Test;Role:WebRole
```

Het `tags` veld is een tekenreeks met de tags die worden afgebakend door puntkomma's. Dit kan een probleem zijn als puntkomma's worden gebruikt in de tags zelf. Als een parser is geschreven om programmatisch de tags `tagsList` te extraheren, moet u vertrouwen op het veld dat een JSON-array is zonder scheidingstekens en bijgevolg gemakkelijker te ontleden is.

**Aanvraag**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04&format=json"
```

**Reactie**

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

Marketplace-leveranciers willen ervoor zorgen dat hun software een licentie heeft om alleen in Azure te worden uitgevoerd. Als iemand kopieën van de VHD naar on-premises, dan moeten ze de mogelijkheid hebben om dat te detecteren. Door in te schakelen bij Instance Metadata Service kunnen Marketplace-leveranciers ondertekende gegevens krijgen die alleen respons van Azure garanderen.

> [!NOTE]
> Vereist jq worden geïnstalleerd.

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

 **Reactie**

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
Nonce | Gebruiker heeft optionele tekenreeks bij het verzoek geleverd. Als er geen nonce is geleverd in de aanvraag, wordt de huidige UTC-tijdstempel geretourneerd
plannen | [Plan](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) voor een VM in een Azure Marketplace-afbeelding, bevat naam, product en uitgever
tijdstempel/gemaaktOp | De UTC-tijdstempel waarop het eerste ondertekende document is gemaakt
timestamp/verlooptOp | De UTC-tijdstempel waarop het ondertekende document verloopt
vmId (vmId) |  [Unieke id](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) voor de VM
subscriptionId | Azure-abonnement voor de Virtuele Machine, geïntroduceerd in`2019-04-30`
sku | Specifieke SKU voor de VM-afbeelding, geïntroduceerd in`2019-11-01`

#### <a name="verifying-the-signature"></a>De handtekening verifiëren

Zodra u de bovenstaande handtekening hebt, u controleren of de handtekening van Microsoft afkomstig is. Ook u het tussencertificaat en de certificaatketen verifiëren. Ten slotte u controleren of de abonnements-ID correct is.

> [!NOTE]
> Het certificaat voor Public cloud en sovereign cloud zal anders zijn.

 Cloud | Certificaat
---------|-----------------
[Alle algemeen beschikbare Globale Azure-regio's](https://azure.microsoft.com/regions/)     | *.metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | *.metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | *.metadata.azure.cn
[Azure Duitsland](https://azure.microsoft.com/overview/clouds/germany/)                    | *.metadata.microsoftazure.de

Er is een bekend probleem rond het certificaat dat wordt gebruikt voor ondertekening. De certificaten hebben mogelijk geen `metadata.azure.com` exacte overeenkomst met de openbare cloud. Daarom moet de certificeringsvalidatie een `.metadata.azure.com` gemeenschappelijke naam van elk subdomein mogelijk maken.

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

In gevallen waarin het tussencertificaat niet kan worden gedownload vanwege netwerkbeperkingen tijdens de validatie, kan het tussencertificaat worden vastgemaakt. Azure rolt de certificaten echter over volgens de standaard PKI-praktijk. De vastgemaakte certificaten moeten worden bijgewerkt wanneer de roll over plaatsvindt. Wanneer een wijziging om het tussencertificaat bij te werken is gepland, wordt de Azure-blog bijgewerkt en worden Azure-klanten op de hoogte gebracht. De tussenliggende certificaten zijn [hier](https://www.microsoft.com/pki/mscorp/cps/default.htm)te vinden. De tussenliggende certificaten voor elk van de regio's kunnen verschillend zijn.

### <a name="storage-profile"></a>Opslagprofiel

Instance Metadata Service kan details geven over de opslagschijven die aan de VM zijn gekoppeld. Deze gegevens zijn te vinden op het instance/compute/storageProfile eindpunt.

Het opslagprofiel van een VM is onderverdeeld in drie categorieën: beeldverwijzing, OS-schijf en gegevensschijven.

Het referentieobject voor afbeeldingen bevat de volgende informatie over de os-afbeelding:

Gegevens    | Beschrijving
--------|-----------------
id      | Resource-id
offer   | Aanbieding van het platform of marktplaatsafbeelding
uitgever | Afbeeldingsuitgever
sku     | Afbeelding sku
versie | Versie van het platform of marktplaatsafbeelding

Het object OS-schijf bevat de volgende informatie over de osschijf die door de vm wordt gebruikt:

Gegevens    | Beschrijving
--------|-----------------
Caching | Caching-vereisten
makenOption | Informatie over hoe de VM is gemaakt
diffDiskSettings | Kortstondige schijfinstellingen
diskSizeGB | Grootte van de schijf in GB
installatiekopie   | Virtuele harde schijf brongebruikersafbeelding
Lun     | Logisch eenheidsnummer van de schijf
managedDisk | Beheerde schijfparameters
name    | Schijfnaam
Vhd     | Virtuele harde schijf
writeAcceleratorEnabled | Of writeAccelerator is ingeschakeld op de schijf

De array gegevensschijven bevat een lijst met gegevensschijven die aan de vm zijn gekoppeld. Elk object met gegevensschijf bevat de volgende informatie:

Gegevens    | Beschrijving
--------|-----------------
Caching | Caching-vereisten
makenOption | Informatie over hoe de VM is gemaakt
diffDiskSettings | Kortstondige schijfinstellingen
diskSizeGB | Grootte van de schijf in GB
versleutelingInstellingen | Versleutelingsinstellingen voor de schijf
installatiekopie   | Virtuele harde schijf brongebruikersafbeelding
managedDisk | Beheerde schijfparameters
name    | Schijfnaam
osType  | Type besturingssysteem dat in de schijf is opgenomen
Vhd     | Virtuele harde schijf
writeAcceleratorEnabled | Of writeAccelerator is ingeschakeld op de schijf

Het volgende is een voorbeeld van het opvragen van de opslaggegevens van de VM.

**Aanvraag**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
```

**Reactie**

> [!NOTE]
> Het antwoord is een JSON-tekenreeks. Het volgende voorbeeld antwoord is vrij gedrukt voor leesbaarheid.

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

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Voorbeelden van het aanroepen van metagegevensservice met verschillende talen in de VM

Taal | Voorbeeld
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Aan de slag  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata

## <a name="faq"></a>Veelgestelde vragen

1. Ik krijg de `400 Bad Request, Required metadata header not specified`fout . Wat betekent dit?
   * De instantiemetagegevensservice `Metadata: true` vereist dat de koptekst wordt doorgegeven in de aanvraag. Als u deze koptekst doorgeeft in de REST-aanroep, heeft u toegang tot de Instantie Metadata Service.
2. Waarom krijg ik geen rekengegevens voor mijn vm?
   * Momenteel ondersteunt de Instantie Metagegevensservice alleen instanties die zijn gemaakt met Azure Resource Manager. In de toekomst kan ondersteuning voor Cloud Service VM's worden toegevoegd.
3. Ik heb mijn virtuele machine een tijdje geleden gemaakt via Azure Resource Manager. Waarom zie ik geen gegevensmetagegevens voor gegevensgegevens?
   * Voor alle VM's die na september 2016 zijn gemaakt, voegt u een [tag](../../azure-resource-manager/management/tag-resources.md) toe om rekengegevensopagegevens te zien. Voor oudere VM's (gemaakt vóór september 2016) voegt u extensies of gegevensschijven toe aan de VM om metagegevens te vernieuwen.
4. Ik zie niet alle gegevens ingevuld voor nieuwe versie
   * Voor alle VM's die na september 2016 zijn gemaakt, voegt u een [tag](../../azure-resource-manager/management/tag-resources.md) toe om rekengegevensopagegevens te zien. Voor oudere VM's (gemaakt vóór september 2016) voegt u extensies of gegevensschijven toe aan de VM om metagegevens te vernieuwen.
5. Waarom krijg ik `500 Internal Server Error`de fout?
   * Probeer uw verzoek opnieuw op basis van exponentiële back-off systeem. Als het probleem blijft bestaan, neemt u contact op met Azure-ondersteuning.
6. Waar kan ik aanvullende vragen/opmerkingen delen?
   * Stuur uw https://feedback.azure.comopmerkingen op .
7. Zou dit werken voor Virtual Machine Scale Set Instance?
   * Ja-metagegevensservice is beschikbaar voor instanties voor het schalen van sets.
8. Hoe krijg ik ondersteuning voor de service?
   * Als u ondersteuning voor de service wilt krijgen, maakt u een ondersteuningsprobleem in azure-portal voor de VM, waar u na lange pogingen geen metagegevenskunt ontvangen.
9. Ik krijg verzoek time-out voor mijn oproep naar de dienst?
   * Metagegevens moeten worden aangesproken vanaf het primaire IP-adres dat is toegewezen aan de primaire netwerkkaart van de VM, bovendien moet er een route zijn voor het adres van 169.254.0.0/16 van uw netwerkkaart.
10. Ik heb mijn tags bijgewerkt in de virtuele machine schaal set, maar ze niet worden weergegeven in de exemplaren in tegenstelling tot VM's?
    * Momenteel worden voor ScaleSets-tags alleen weergegeven aan de VM bij een herstart/reimage/of een schijfwijziging in de instantie.

    ![Ondersteuning voor metagegevens voor instance](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [geplande evenementen](scheduled-events.md)
