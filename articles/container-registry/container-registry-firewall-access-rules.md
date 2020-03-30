---
title: Firewalltoegangsregels
description: Configureer regels om toegang te krijgen tot een Azure-containerregister van achter een firewall, door toegang te verlenen tot REST API- en opslageindpuntdomeinnamen of servicespecifieke IP-adresbereiken ("whitelisting").
ms.topic: article
ms.date: 02/11/2020
ms.openlocfilehash: 06fedea2adf5e73929f5752279f2bd7e7227e570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77168024"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Regels configureren om toegang te krijgen tot een Azure-containerregister achter een firewall

In dit artikel wordt uitgelegd hoe u regels op uw firewall configureert om toegang te verlenen tot een Azure-containerregister. Een Azure IoT Edge-apparaat achter een firewall of proxyserver moet bijvoorbeeld toegang krijgen tot een containerregister om een containerafbeelding op te halen. Of een vergrendelde server in een on-premises netwerk heeft mogelijk toegang nodig om een afbeelding te pushen.

Zie Toegang tot [een Azure-containerregister](container-registry-vnet.md)beperken vanuit een virtueel netwerk als u inplaats wilt stellen voor een containerregister in een containerregister.

## <a name="about-registry-endpoints"></a>Informatie over registereindpunten

Als u afbeeldingen of andere artefacten wilt trekken of pushen naar een Azure-containerregister, moet een client zoals een Docker-daemon via HTTPS communiceren met twee verschillende eindpunten.

* **Register REST API-eindpunt** - Verificatie- en registerbeheerbewerkingen worden afgehandeld via het openbare REST API-eindpunt van het register. Dit eindpunt is de naam van de inlogserver van het register of een bijbehorend IP-adresbereik. 

* **Opslageindpunt** - Azure [wijst blob-opslag toe](container-registry-storage.md) in Azure Storage-accounts namens elk register om de gegevens voor containerafbeeldingen en andere artefacten te beheren. Wanneer een client toegang heeft tot afbeeldingslagen in een Azure-containerregister, worden aanvragen ingediend met behulp van een eindpunt voor opslagaccount dat door het register wordt verstrekt.

Als uw register [geo-gerepliceerd](container-registry-geo-replication.md)is, moet een client mogelijk communiceren met RUST- en opslageindpunten in een specifieke regio of in meerdere gerepliceerde regio's.

## <a name="allow-access-to-rest-and-storage-domain-names"></a>Toegang verlenen tot REST- en opslagdomeinnamen

* **REST-eindpunt** - Geef toegang tot de volledig gekwalificeerde registerregistratieservernaam, zoals`myregistry.azurecr.io`
* **Eindpunt van opslag (gegevens)** - Toegang verlenen tot alle Azure blob-opslagaccounts met de wildcard`*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Toegang per IP-adresbereik toestaan

Als uw organisatie beleid heeft om alleen toegang toe te staan tot specifieke IP-adressen of adresbereiken, downloadt u [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519).

Als u de IP-bereiken van ACR REST-eindpunt wilt vinden waarvoor u toegang moet toestaan, zoekt u naar **AzureContainerRegistry** in het JSON-bestand.

> [!IMPORTANT]
> IP-adresbereiken voor Azure-services kunnen worden gewijzigd en updates worden wekelijks gepubliceerd. Download het JSON-bestand regelmatig en breng de nodige updates uit in uw toegangsregels. Als uw scenario bestaat uit het configureren van netwerkbeveiligingsgroepregels in een virtueel Azure-netwerk om toegang te krijgen tot Azure Container Registry, gebruikt u in plaats daarvan de [AzureContainerRegistry-servicetag.](#allow-access-by-service-tag) **AzureContainerRegistry**
>

### <a name="rest-ip-addresses-for-all-regions"></a>REST IP-adressen voor alle regio's

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>REST IP-adressen voor een specifieke regio

Zoek naar de specifieke regio, zoals **AzureContainerRegistry.AustraliaEast**.

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>Ip-adressen voor opslag voor alle regio's

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>Ip-adressen voor opslag voor specifieke regio's

Zoek naar de specifieke regio, zoals **Storage.AustraliaCentral**.

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>Toegang toestaan via servicetag

Gebruik in een virtueel Azure-netwerk netwerkbeveiligingsregels om verkeer van een bron zoals een virtuele machine naar een containerregister te filteren. Als u de creatie van de Azure-netwerkregels wilt vereenvoudigen, gebruikt u de **AzureContainerRegistry-servicetag** [service tag](../virtual-network/security-overview.md#service-tags). Een servicetag vertegenwoordigt een groep IP-adresvoorvoegsels om toegang te krijgen tot een Azure-service wereldwijd of per Azure-regio. De tag wordt automatisch bijgewerkt wanneer adressen worden gewijzigd. 

Maak bijvoorbeeld een regel voor uitgaande netwerkbeveiligingsgroepen met doel **AzureContainerRegistry** om verkeer toe te staan naar een Azure-containerregister. Als u alleen toegang tot de servicetag in een bepaald gebied wilt toestaan, geeft u het gebied op in de volgende indeling: **AzureContainerRegistry**. [*regionaam*].

## <a name="configure-client-firewall-rules-for-mcr"></a>Clientfirewallregels configureren voor MCR

Als u microsoft containerregistry (MCR) van achter een firewall wilt openen, raadpleegt u de richtlijnen voor het configureren van [FIREWALL-regels voor MCR-client.](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md) MCR is het primaire register voor alle door Microsoft gepubliceerde dockerafbeeldingen, zoals Windows Server-afbeeldingen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [azure-aanbevolen procedures voor netwerkbeveiliging](../security/fundamentals/network-best-practices.md)

* Meer informatie over [beveiligingsgroepen](/azure/virtual-network/security-overview) in een virtueel Azure-netwerk



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

