---
title: Toegangs regels voor Firewall
description: Configureer regels voor toegang tot een Azure container Registry van achter een firewall door toegang toe te staan (white list) REST API en domein namen van het opslag eindpunt of servicespecifieke IP-adresbereiken.
ms.topic: article
ms.date: 02/11/2020
ms.openlocfilehash: 06fedea2adf5e73929f5752279f2bd7e7227e570
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168024"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Regels configureren voor toegang tot een Azure container Registry achter een firewall

In dit artikel wordt uitgelegd hoe u regels kunt configureren op uw firewall om toegang te krijgen tot een Azure container Registry. Bijvoorbeeld, een Azure IoT Edge apparaat achter een firewall of proxy server moet mogelijk toegang hebben tot een container register om een container installatie kopie te halen. Het is ook mogelijk dat een vergrendelde server in een on-premises netwerk toegang nodig heeft tot het pushen van een installatie kopie.

Zie [toegang tot een Azure-container register beperken vanuit een virtueel netwerk](container-registry-vnet.md)als u in plaats daarvan binnenkomende regels voor netwerk toegang wilt configureren in een container register, alleen binnen een virtueel Azure-netwerk of een openbaar IP-adres bereik.

## <a name="about-registry-endpoints"></a>Over register eindpunten

Om installatie kopieën of andere artefacten te halen of pushen naar een Azure container Registry, moet een client, zoals een docker-daemon, communiceren via HTTPS met twee verschillende eind punten.

* **Register-rest API** -verificatie-en register beheer bewerkingen worden verwerkt via het open bare rest API eindpunt van het REGI ster. Dit eind punt is de naam van de aanmeldings server van het REGI ster of een gekoppeld IP-adres bereik. 

* **Opslag eindpunt** : Azure [wijst de Blob-opslag](container-registry-storage.md) in azure Storage accounts uit naam van elk REGI ster toe om de gegevens voor container installatie kopieën en andere artefacten te beheren. Wanneer een client afbeeldings lagen in een Azure container Registry opent, worden er aanvragen gemaakt met behulp van een eind punt voor het opslag account dat wordt meegeleverd met het REGI ster.

Als uw REGI ster [geo-gerepliceerd](container-registry-geo-replication.md)is, moet een client mogelijk communiceren met rest-en opslag eindpunten in een bepaalde regio of in meerdere gerepliceerde regio's.

## <a name="allow-access-to-rest-and-storage-domain-names"></a>Toegang tot REST-en opslag domein namen toestaan

* **Rest-eind punt** : toegang tot de volledig gekwalificeerde registratie server naam van het REGI ster toestaan, zoals `myregistry.azurecr.io`
* **Opslag eindpunt (gegevens)** : toegang tot alle Azure Blob Storage-accounts toestaan met het Joker `*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Toegang toestaan op basis van IP-adres bereik

Als uw organisatie beleids regels heeft die alleen toegang tot specifieke IP-adressen of adresbereiken toestaan, down load dan [Azure IP-bereiken en service Tags – open bare Cloud](https://www.microsoft.com/download/details.aspx?id=56519).

Zoek naar **AzureContainerRegistry** in het JSON-bestand om de IP-bereiken van het ACR rest endpoint te vinden waarvoor u toegang wilt toestaan.

> [!IMPORTANT]
> IP-adresbereiken voor Azure-Services kunnen worden gewijzigd en updates worden wekelijks gepubliceerd. Down load het JSON-bestand regel matig en breng de benodigde updates in uw toegangs regels. Als uw scenario bestaat uit het configureren van regels voor netwerk beveiligings groepen in een virtueel Azure-netwerk om toegang te krijgen tot Azure Container Registry, gebruikt u in plaats daarvan de **AzureContainerRegistry** - [service label](#allow-access-by-service-tag) .
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

Zoek naar de specifieke regio, bijvoorbeeld **AzureContainerRegistry. AustraliaEast**.

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

### <a name="storage-ip-addresses-for-all-regions"></a>IP-adressen voor opslag voor alle regio's

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

### <a name="storage-ip-addresses-for-specific-regions"></a>IP-adressen van opslag voor specifieke regio's

Zoek naar de specifieke regio, zoals **Storage. AustraliaCentral**.

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

## <a name="allow-access-by-service-tag"></a>Toegang per service-tag toestaan

Gebruik in een virtueel Azure-netwerk netwerk beveiligings regels voor het filteren van verkeer van een bron, zoals een virtuele machine, naar een container register. Gebruik het **AzureContainerRegistry** - [service label](../virtual-network/security-overview.md#service-tags)om het maken van de regels van het Azure-netwerk te vereenvoudigen. Een servicetag vertegenwoordigt een groep IP-adres voorvoegsels voor de toegang tot een Azure-service wereld wijd of per Azure-regio. Het label wordt automatisch bijgewerkt wanneer adressen worden gewijzigd. 

Maak bijvoorbeeld een regel voor een uitgaande netwerk beveiligings groep met het doel **AzureContainerRegistry** om verkeer naar een Azure container Registry toe te staan. Als u alleen toegang tot de servicetag wilt toestaan in een specifieke regio, geeft u de regio op in de volgende indeling: **AzureContainerRegistry**. [*regio naam*].

## <a name="configure-client-firewall-rules-for-mcr"></a>Firewall regels voor clients configureren voor MCR

Zie de richt lijnen voor het configureren van [MCR-firewall regels](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)voor micro soft container Registry (MCR) van achter een firewall. MCR is het primaire REGI ster voor alle door micro soft gepubliceerde docker-installatie kopieën, zoals Windows Server-installatie kopieën.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Aanbevolen procedures voor Azure voor netwerk beveiliging](../security/fundamentals/network-best-practices.md)

* Meer informatie over [beveiligings groepen](/azure/virtual-network/security-overview) in een virtueel Azure-netwerk



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

