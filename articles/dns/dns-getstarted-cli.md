---
title: 'Snelstart: een Azure DNS-zone maken en opnemen - Azure CLI'
titleSuffix: Azure DNS
description: 'Snelstart: Informatie over het maken van een DNS-zone en -record in Azure DNS. Dit is een stapsgewijze handleiding voor het maken en beheren van uw eerste DNS-zone en -record met behulp van de Azure CLI.'
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: e6904c013cf2ed897bdc7c8b32f04fe500fc31d9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76937198"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>Snelstart: Een Azure DNS-zone en -record maken met behulp van de Azure CLI

Dit artikel begeleidt u stapsgewijs door de procedure voor het maken van uw eerste DNS-zone en -record met behulp van de Azure-CLI die beschikbaar is voor Windows, Mac en Linux. U deze stappen ook uitvoeren met de [Azure-portal](dns-getstarted-portal.md) of [Azure PowerShell.](dns-getstarted-powershell.md)

Een DNS-zone wordt gebruikt om de DNS-records voor een bepaald domein te hosten. Als u uw domein wilt hosten in Azure DNS, moet u een DNS-zone maken voor die domeinnaam. Alle DNS-records voor uw domein worden vervolgens gemaakt binnen deze DNS-zone. Tot slot moet u de naamservers voor het domein configureren om de DNS-zone te publiceren naar internet. Deze stappen worden hieronder allemaal beschreven.

Azure DNS ondersteunt ook privé-DNS-zones. Voor meer informatie over privé-DNS-zones raadpleegt u [Using Azure DNS for private domains](private-dns-overview.md) (Azure DNS gebruiken voor privédomeinen). Zie voor een voorbeeld van het maken van een privé-DNS-zone [Aan de slag met Azure DNS-privézones met CLI](./private-dns-getstarted-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-the-resource-group"></a>De resourcegroep maken

Voordat u de DNS-zone maakt, maakt u een resourcegroep die de DNS-zone gaat bevatten:

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>Een DNS-zone maken

Een DNS-zone wordt gemaakt met de opdracht `az network dns zone create`. Typ `az network dns zone create -h` om Help weer te geven voor deze opdracht.

In het volgende voorbeeld wordt een DNS-zone met de naam *contoso.xyz* in de brongroep *MyResourceGroup aanmaken.* Gebruik het voorbeeld om een DNS-zone te maken door de waarden te vervangen door uw eigen waarden.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.xyz
```

## <a name="create-a-dns-record"></a>Een DNS-record maken

Gebruik de opdracht `az network dns record-set [record type] add-record` om een DNS-record te maken. Zie `azure network dns record-set A add-record -h` voor meer informatie over de A-records.

In het volgende voorbeeld wordt een record gemaakt met de relatieve naam "www" in de DNS-zone "contoso.xyz" in de resourcegroep "MyResourceGroup". De volledig gekwalificeerde naam van het record is "www.contoso.xyz". Het recordtype is "A", met IP-adres "10.10.10.10" en een standaard TTL van 3600 seconden (1 uur).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.xyz -n www -a 10.10.10.10
```

## <a name="view-records"></a>Records weergeven

Als u de DNS-records in uw zone wilt weergeven,voert u de volgende opdracht uit:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.xyz
```

## <a name="test-the-name-resolution"></a>De naamomzetting testen

Nu u een testzone hebt met daarin een DNS-record, kunt u de naamomzetting testen met het hulpprogramma *nslookup*. 

**DNS-naamomzetting testen:**

1. Voer de volgende cmdlet uit om de lijst met naamservers voor uw zone op te halen:

   ```azurecli
   az network dns record-set ns show --resource-group MyResourceGroup --zone-name contoso.xyz --name @
   ```

1. Kopieer een van de namen van de naamserver uit de uitvoer van de vorige stap.

1. Open een opdrachtprompt en voer de volgende opdracht uit:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Bijvoorbeeld:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Er verschijnt een scherm dat er ongeveer als volgt uitziet:

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

De hostnaam **www\.contoso.xyz** wordt opgelost tot **10.10.10.10,** net zoals u het hebt geconfigureerd. Met dit resultaat wordt gecontroleerd of de naamomzetting juist werkt.

## <a name="delete-all-resources"></a>Alle resources verwijderen

Als u ze niet langer nodig hebt, kunt u alle resources die u in deze snelstartgids hebt gemaakt verwijderen door de resourcegroep te verwijderen:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Nu u uw eerste DNS-zone en -record hebt gemaakt met behulp van de Azure CLI, kunt u records voor een web-app maken in een aangepast domein.

> [!div class="nextstepaction"]
> [DNS-records voor een web-app in een aangepast domein maken](./dns-web-sites-custom-domain.md)
