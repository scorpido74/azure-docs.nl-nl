---
title: Linux VM-afbeeldingen selecteren met de Azure CLI
description: Meer informatie over het gebruik van de Azure CLI om de uitgever, aanbieding, SKU en versie voor Marketplace VM-afbeeldingen te bepalen.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 01/25/2019
ms.author: cynthn
ms.openlocfilehash: 0026c70a3a1a6b5e635e6b43e74b557d4218e6d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250307"
---
# <a name="find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Linux-VM-installatiekopieën zoeken in de Azure Marketplace met de Azure CLI

In dit onderwerp wordt beschreven hoe u de Azure CLI gebruiken om VM-afbeeldingen in de Azure Marketplace te vinden. Gebruik deze informatie om een Marketplace-afbeelding op te geven wanneer u een VM programmatisch maakt met de CLI-, Resource Manager-sjablonen of andere hulpprogramma's.

Blader ook door beschikbare afbeeldingen en aanbiedingen met de [Azure Marketplace](https://azuremarketplace.microsoft.com/) storefront, de [Azure-portal](https://portal.azure.com)of [Azure PowerShell.](../windows/cli-ps-findimage.md) 

Zorg ervoor dat u de nieuwste [Azure CLI hebt](/cli/azure/install-azure-cli) `az login`geïnstalleerd en bent aangemeld bij een Azure-account ( ).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="list-popular-images"></a>Populaire afbeeldingen weergeven

Voer de opdracht [az vm-afbeeldingslijst](/cli/azure/vm/image) uit, zonder de `--all` optie, om een lijst met populaire VM-afbeeldingen in de Azure Marketplace weer te geven. Voer bijvoorbeeld de volgende opdracht uit om een lijst met populaire afbeeldingen in tabelindeling weer te geven:

```azurecli
az vm image list --output table
```

De uitvoer omvat de afbeelding URN (de waarde in de kolom *Urn).* Bij het maken van een VM met een van deze populaire Marketplace-afbeeldingen u ook de *UrnAlias*opgeven, een verkorte vorm zoals *UbuntuLTS.*

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-RAW               RedHat:RHEL:7-RAW:latest                                        RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Specifieke installatiekopieën zoeken

Als u een specifieke VM-afbeelding `az vm image list` in de `--all` Marketplace wilt vinden, gebruikt u de opdracht met de optie. Deze versie van de opdracht neemt enige tijd in beslag en kan `--publisher` langdurige uitvoer retourneren, zodat u de lijst meestal filtert op of een andere parameter. 

Met de volgende opdracht worden bijvoorbeeld alle Debian-aanbiedingen weergegeven (vergeet niet dat er zonder de `--all` switch alleen de lokale cache van veelvoorkomende afbeeldingen wordt doorzocht):

```azurecli
az vm image list --offer Debian --all --output table 

```

Gedeeltelijke uitvoer: 

```
Offer              Publisher    Sku                  Urn                                                    Version
-----------------  -----------  -------------------  -----------------------------------------------------  --------------
Debian             credativ     7                    credativ:Debian:7:7.0.201602010                        7.0.201602010
Debian             credativ     7                    credativ:Debian:7:7.0.201603020                        7.0.201603020
Debian             credativ     7                    credativ:Debian:7:7.0.201604050                        7.0.201604050
Debian             credativ     7                    credativ:Debian:7:7.0.201604200                        7.0.201604200
Debian             credativ     7                    credativ:Debian:7:7.0.201606280                        7.0.201606280
Debian             credativ     7                    credativ:Debian:7:7.0.201609120                        7.0.201609120
Debian             credativ     7                    credativ:Debian:7:7.0.201611020                        7.0.201611020
Debian             credativ     7                    credativ:Debian:7:7.0.201701180                        7.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201602010                        8.0.201602010
Debian             credativ     8                    credativ:Debian:8:8.0.201603020                        8.0.201603020
Debian             credativ     8                    credativ:Debian:8:8.0.201604050                        8.0.201604050
Debian             credativ     8                    credativ:Debian:8:8.0.201604200                        8.0.201604200
Debian             credativ     8                    credativ:Debian:8:8.0.201606280                        8.0.201606280
Debian             credativ     8                    credativ:Debian:8:8.0.201609120                        8.0.201609120
Debian             credativ     8                    credativ:Debian:8:8.0.201611020                        8.0.201611020
Debian             credativ     8                    credativ:Debian:8:8.0.201701180                        8.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201703150                        8.0.201703150
Debian             credativ     8                    credativ:Debian:8:8.0.201704110                        8.0.201704110
Debian             credativ     8                    credativ:Debian:8:8.0.201704180                        8.0.201704180
Debian             credativ     8                    credativ:Debian:8:8.0.201706190                        8.0.201706190
Debian             credativ     8                    credativ:Debian:8:8.0.201706210                        8.0.201706210
Debian             credativ     8                    credativ:Debian:8:8.0.201708040                        8.0.201708040
Debian             credativ     8                    credativ:Debian:8:8.0.201710090                        8.0.201710090
Debian             credativ     8                    credativ:Debian:8:8.0.201712040                        8.0.201712040
Debian             credativ     8                    credativ:Debian:8:8.0.201801170                        8.0.201801170
Debian             credativ     8                    credativ:Debian:8:8.0.201803130                        8.0.201803130
Debian             credativ     8                    credativ:Debian:8:8.0.201803260                        8.0.201803260
Debian             credativ     8                    credativ:Debian:8:8.0.201804020                        8.0.201804020
Debian             credativ     8                    credativ:Debian:8:8.0.201804150                        8.0.201804150
Debian             credativ     8                    credativ:Debian:8:8.0.201805160                        8.0.201805160
Debian             credativ     8                    credativ:Debian:8:8.0.201807160                        8.0.201807160
Debian             credativ     8                    credativ:Debian:8:8.0.201901221                        8.0.201901221
...
```

Vergelijkbare filters toepassen `--location` `--publisher`met `--sku` de opties , en opties. U gedeeltelijke overeenkomsten uitvoeren op een `--offer Deb` filter, zoals zoeken naar alle Debian-afbeeldingen.

Als u met de `--location` optie geen bepaalde locatie opgeeft, worden de waarden voor de standaardlocatie geretourneerd. (Stel een andere standaardlocatie in door .) `az configure --defaults location=<location>`

In de volgende opdracht worden bijvoorbeeld alle Debian 8 SKU's op de locatie West-Europa weergegeven:

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

Gedeeltelijke uitvoer:

```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
Debian   credativ     8                  credativ:Debian:8:8.0.201710090                  8.0.201710090
Debian   credativ     8                  credativ:Debian:8:8.0.201712040                  8.0.201712040
Debian   credativ     8                  credativ:Debian:8:8.0.201801170                  8.0.201801170
Debian   credativ     8                  credativ:Debian:8:8.0.201803130                  8.0.201803130
Debian   credativ     8                  credativ:Debian:8:8.0.201803260                  8.0.201803260
Debian   credativ     8                  credativ:Debian:8:8.0.201804020                  8.0.201804020
Debian   credativ     8                  credativ:Debian:8:8.0.201804150                  8.0.201804150
Debian   credativ     8                  credativ:Debian:8:8.0.201805160                  8.0.201805160
Debian   credativ     8                  credativ:Debian:8:8.0.201807160                  8.0.201807160
Debian   credativ     8                  credativ:Debian:8:8.0.201901221                  8.0.201901221
...
```

## <a name="navigate-the-images"></a>Navigeren door de afbeeldingen
 
Een andere manier om een afbeelding op een locatie te vinden, is door de az [vm-lijst-uitgevers](/cli/azure/vm/image), [az vm-lijstaanbiedingen](/cli/azure/vm/image)en de az [vm-lijst-skus-opdrachten](/cli/azure/vm/image) achter elkaar uit te voeren. Met deze opdrachten bepaalt u de volgende waarden:

1. Geef de uitgevers van installatiekopieën weer.
2. Geef de aanbiedingen voor een bepaalde uitgever weer.
3. Geef de SKU's voor een bepaalde aanbieding weer.

Vervolgens u voor een geselecteerde SKU een te implementeren versie kiezen.

In de volgende opdracht worden bijvoorbeeld de afbeeldingsuitgevers op de locatie West-VS weergegeven:

```azurecli
az vm image list-publishers --location westus --output table
```

Gedeeltelijke uitvoer:

```
Location    Name
----------  ----------------------------------------------------
westus      128technology
westus      1e
westus      4psa
westus      5nine-software-inc
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      accessdata-group
westus      accops
westus      Acronis
westus      Acronis.Backup
westus      actian-corp
westus      actian_matrix
westus      actifio
westus      activeeon
westus      advantech-webaccess
westus      aerospike
westus      affinio
westus      aiscaler-cache-control-ddos-and-url-rewriting-
westus      akamai-technologies
westus      akumina
...
```

Gebruik deze informatie om aanbiedingen van een specifieke uitgever te vinden. Voor de *Canonical-uitgever* in de West-Amerikaanse locatie `azure vm image list-offers`vindt u bijvoorbeeld aanbiedingen door het uitvoeren van . Geef de locatie en de uitgever door zoals in het volgende voorbeeld:

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

Uitvoer:

```
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
```
U ziet dat Canonical in de regio West-VS de *UbuntuServer-aanbieding* op Azure publiceert. Maar welke SKU's? Als u deze `azure vm image list-skus` waarden wilt ontvangen, voert u de locatie, uitgever en aanbieding in die u hebt ontdekt:

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Uitvoer:

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-LTS
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      18.04-DAILY-LTS
westus      18.04-LTS
westus      18.10
westus      18.10-DAILY
westus      19.04-DAILY
```

Gebruik ten `az vm image list` slotte de opdracht om een specifieke versie van de SKU te vinden die u wilt, bijvoorbeeld *18.04-LTS:*

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 18.04-LTS --all --output table
```

Gedeeltelijke uitvoer:

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201804262  18.04.201804262
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805170  18.04.201805170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805220  18.04.201805220
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806130  18.04.201806130
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806170  18.04.201806170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201807240  18.04.201807240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808060  18.04.201808060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808080  18.04.201808080
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808140  18.04.201808140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808310  18.04.201808310
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201809110  18.04.201809110
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810030  18.04.201810030
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810240  18.04.201810240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810290  18.04.201810290
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201811010  18.04.201811010
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812031  18.04.201812031
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812040  18.04.201812040
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812060  18.04.201812060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901140  18.04.201901140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901220  18.04.201901220
...
```

Nu u precies de afbeelding kiezen die u wilt gebruiken door nota te nemen van de URN-waarde. Geef deze waarde `--image` door aan de parameter wanneer u een VM maakt met de opdracht [AZ VM Create.](/cli/azure/vm) Vergeet niet dat u het versienummer in de URN optioneel vervangen door laatste. Deze versie is altijd de nieuwste versie van de afbeelding. 

Als u een VM implementeert met een resourcemanagersjabloon, `imageReference` stelt u de afbeeldingsparameters afzonderlijk in in de eigenschappen. Zie de [sjabloonverwijzing](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Eigenschappen van plan weergeven

Voer de opdracht az vm image [show](/cli/azure/image) uit om de aankoopplangegevens van een afbeelding weer te geven. Als `plan` de eigenschap in `null`de uitvoer niet is, heeft de afbeelding termen die u moet accepteren voordat de programmatische implementatie wordt uitgevoerd.

De Canonical Ubuntu Server 18.04 LTS-afbeelding heeft bijvoorbeeld geen `plan` aanvullende `null`voorwaarden, omdat de informatie:

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

Uitvoer:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "westus",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

Als u een vergelijkbare opdracht uitvoert voor de afbeelding `plan` RabbitMQ `product`Certified `publisher`by Bitnami, worden de volgende eigenschappen weergegeven: `name`, , en . (Sommige afbeeldingen hebben `promotion code` ook een eigenschap.) Als u deze afbeelding wilt implementeren, raadpleegt u de volgende secties om de voorwaarden te accepteren en programmatische implementatie in te schakelen.

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
Uitvoer:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1901151016",
  "location": "westus",
  "name": "3.7.1901151016",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

### <a name="accept-the-terms"></a>De gebruiksvoorwaarden accepteren

Gebruik de opdracht accepteren van az [vm-afbeeldingen](/cli/azure/vm/image?) om de licentievoorwaarden te bekijken en te accepteren. Wanneer u de voorwaarden accepteert, schakelt u programmatische implementatie in uw abonnement in. U hoeft slechts één keer per abonnement voor de afbeelding voorwaarden te accepteren. Bijvoorbeeld:

```azurecli
az vm image accept-terms --urn bitnami:rabbitmq:rabbitmq:latest
``` 

De uitvoer `licenseTextLink` omvat a aan de licentievoorwaarden, `accepted` `true`en geeft aan dat de waarde van is:

```
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2019-01-25T20:37:49.937096Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

### <a name="deploy-using-purchase-plan-parameters"></a>Implementeren met behulp van parameters van het aankoopplan

Nadat u de voorwaarden voor de afbeelding hebt geaccepteerd, u een virtuele machine in het abonnement implementeren. Als u de afbeelding `az vm create` wilt implementeren met behulp van de opdracht, geeft u naast een URN voor de afbeelding ook parameters voor het aankoopplan op. Bijvoorbeeld om een VM te implementeren met de RabbitMQ Certified by Bitnami-afbeelding:

```azurecli
az group create --name myResourceGroupVM --location westus

az vm create --resource-group myResourceGroupVM --name myVM --image bitnami:rabbitmq:rabbitmq:latest --plan-name rabbitmq --plan-product rabbitmq --plan-publisher bitnami
```

## <a name="next-steps"></a>Volgende stappen
Zie Linux VM's maken en beheren met de Azure CLI als u snel een virtuele machine wilt maken door de [afbeeldingsgegevens te gebruiken.](tutorial-manage-vm.md)
