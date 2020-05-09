---
title: Toegangs regels voor Firewall
description: Configureer regels voor toegang tot een Azure container Registry van achter een firewall, door toegang toe te staan (' white list ') REST API en domein namen van gegevens eindpunten of servicespecifieke IP-adresbereiken.
ms.topic: article
ms.date: 05/07/2020
ms.openlocfilehash: b3560fe769a97c8d3a4e5a3580d42d7c0b3a3f08
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82978345"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Regels configureren voor toegang tot een Azure container Registry achter een firewall

In dit artikel wordt uitgelegd hoe u regels kunt configureren op uw firewall om toegang te krijgen tot een Azure container Registry. Bijvoorbeeld, een Azure IoT Edge apparaat achter een firewall of proxy server moet mogelijk toegang hebben tot een container register om een container installatie kopie te halen. Het is ook mogelijk dat een vergrendelde server in een on-premises netwerk toegang nodig heeft tot het pushen van een installatie kopie.

Zie [Azure private link configureren voor een Azure-container register](container-registry-private-link.md)als u in plaats daarvan binnenkomende netwerk toegang wilt configureren voor een container register in een virtueel Azure-netwerk.

## <a name="about-registry-endpoints"></a>Over register eindpunten

Om installatie kopieën of andere artefacten te halen of pushen naar een Azure container Registry, moet een client, zoals een docker-daemon, communiceren via HTTPS met twee verschillende eind punten. Voor clients die toegang hebben tot een REGI ster van achter een firewall, moet u toegangs regels voor beide eind punten configureren.

* **Register-rest API** -verificatie-en register beheer bewerkingen worden verwerkt via het open bare rest API eindpunt van het REGI ster. Dit eind punt is de naam van de aanmeldings server van het REGI ster. Voorbeeld: `myregistry.azurecr.io`

* **Opslag (gegevens)-eind punt** : Azure [wijst de Blob-opslag](container-registry-storage.md) in azure Storage accounts uit naam van elk REGI ster toe om de gegevens voor container installatie kopieën en andere artefacten te beheren. Wanneer een client afbeeldings lagen in een Azure container Registry opent, worden er aanvragen gemaakt met behulp van een eind punt voor het opslag account dat wordt meegeleverd met het REGI ster.

Als uw REGI ster [geo-gerepliceerd](container-registry-geo-replication.md)is, moet een client mogelijk communiceren met het gegevens eindpunt in een bepaalde regio of in meerdere gerepliceerde regio's.

## <a name="allow-access-to-rest-and-data-endpoints"></a>Toegang tot REST-en gegevens eindpunten toestaan

* **Rest-eind punt** : toegang tot de volledige gekwalificeerde naam van het REGI `<registry-name>.azurecr.io`ster-server, of een gekoppeld IP-adres bereik toestaan
* **Storage-eind punt (Data)** : Hiermee staat u toegang toe tot alle Azure Blob `*.blob.core.windows.net`Storage-accounts met behulp van het Joker teken of een gekoppeld IP-adres bereik.
> [!NOTE]
> Azure Container Registry introduceert [speciale gegevens eindpunten](#enable-dedicated-data-endpoints-preview) (preview), zodat u de firewall regels van de client voor uw register opslag nauw keurig kunt bepalen. Schakel eventueel gegevens eindpunten in in alle regio's waar het REGI ster zich bevindt of repliceert met behulp van het formulier `<registry-name>.<region>.data.azurecr.io`.

## <a name="allow-access-by-ip-address-range"></a>Toegang toestaan op basis van IP-adres bereik

Als uw organisatie beleids regels heeft die alleen toegang tot specifieke IP-adressen of adresbereiken toestaan, down load dan [Azure IP-bereiken en service Tags – open bare Cloud](https://www.microsoft.com/download/details.aspx?id=56519).

Zoek naar **AzureContainerRegistry** in het JSON-bestand om de IP-bereiken van het ACR rest endpoint te vinden waarvoor u toegang wilt toestaan.

> [!IMPORTANT]
> IP-adresbereiken voor Azure-Services kunnen worden gewijzigd en updates worden wekelijks gepubliceerd. Down load het JSON-bestand regel matig en breng de benodigde updates in uw toegangs regels. Als uw scenario bestaat uit het configureren van regels voor netwerk beveiligings groepen in een Azure Virtual Network of als u Azure Firewall gebruikt, gebruikt u in plaats daarvan de **AzureContainerRegistry** - [service label](#allow-access-by-service-tag) .
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

## <a name="enable-dedicated-data-endpoints-preview"></a>Dedicated data-eind punten inschakelen (preview-versie)

> [!WARNING]
> Als u eerder client firewall toegang tot de bestaande `*.blob.core.windows.net` eind punten hebt geconfigureerd, is het overschakelen naar toegewezen gegevens eindpunten van invloed op client connectiviteit, waardoor pull-fouten optreden. Om ervoor te zorgen dat clients consistente toegang hebben, voegt u de regels voor het nieuwe gegevens eindpunt toe aan de firewall regels van de client. Wanneer u klaar bent, kunt u speciale gegevens eindpunten voor uw registers inschakelen met behulp van Azure CLI of andere hulpprogram ma's.

Toegewezen gegevens eindpunten is een optionele functie van de service-laag van het **Premium** -container register. Zie [Azure container Registry-lagen](container-registry-skus.md)voor meer informatie over de service lagen en limieten voor het REGI ster. Gebruik Azure CLI-versie 2.4.0 of hoger om gegevens eindpunten in te scha kelen met behulp van de Azure CLI. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

Met de volgende opdracht [AZ ACR update][az-acr-update] kunt u speciale gegevens eindpunten in een REGI ster *myregistry*. Voor demonstratie doeleinden moet u ervan uitgaan dat het REGI ster wordt gerepliceerd in twee regio's:

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

De gegevens eindpunten gebruiken een regionaal patroon `<registry-name>.<region>.data.azurecr.io`,. Als u de gegevens eindpunten wilt weer geven, gebruikt u de opdracht [AZ ACR show-endpoints][az-acr-show-endpoints] :

```azurecli
az acr show-endpoints --name myregistry
```

Uitvoer:

```
{
    "loginServer": "myregistry.azurecr.io",
    "dataEndpoints": [
        {
            "region": "eastus",
            "endpoint": "myregistry.eastus.data.azurecr.io",
        },
        {
            "region": "westus",
            "endpoint": "myregistry.westus.data.azurecr.io",
        }
    ]
}
```

Nadat u toegewezen gegevens eindpunten hebt ingesteld voor uw REGI ster, kunt u de firewall regels voor client toegang inschakelen voor de gegevens eindpunten. Toegangs regels voor gegevens eindpunt inschakelen voor alle vereiste register regio's.

## <a name="configure-client-firewall-rules-for-mcr"></a>Firewall regels voor clients configureren voor MCR

Zie de richt lijnen voor het configureren van [MCR-firewall regels](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)voor micro soft container Registry (MCR) van achter een firewall. MCR is het primaire REGI ster voor alle door micro soft gepubliceerde docker-installatie kopieën, zoals Windows Server-installatie kopieën.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Aanbevolen procedures voor Azure voor netwerk beveiliging](../security/fundamentals/network-best-practices.md)

* Meer informatie over [beveiligings groepen](/azure/virtual-network/security-overview) in een virtueel Azure-netwerk

* Meer informatie over [toegewezen gegevens eindpunten](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/) voor Azure container Registry



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az-acr-update
[az-acr-show-endpoints]: /cli/azure/acr#az-acr-show-endpoints

