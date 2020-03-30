---
title: Reverse DNS voor Azure-services - Azure DNS
description: Ga met dit leerpad aan de slag met het configureren van reverse DNS-lookups voor services die worden gehost in Azure.
services: dns
documentationcenter: na
author: rohinkoul
manager: KumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 073e84ece11f6817bfe2c5a94735ec6e16dac4fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932373"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Reverse DNS configureren voor services die worden gehost in Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In dit artikel wordt uitgelegd hoe u reverse DNS-lookups configureert voor services die in Azure worden gehost.

Services in Azure gebruiken IP-adressen die zijn toegewezen door Azure en eigendom zijn van Microsoft. Deze reverse DNS records (PTR records) moeten worden gemaakt in de overeenkomstige reverse DNS-opzoekzones die eigendom zijn van Microsoft. In dit artikel wordt uitgelegd hoe u dit doen.

Dit scenario mag niet worden verward met de mogelijkheid om [de omgekeerde DNS-opzoekzones voor uw toegewezen IP-bereiken in Azure DNS](dns-reverse-dns-hosting.md)te hosten. In dit geval moeten de IP-bereiken die worden weergegeven door de omgekeerde opzoekzone aan uw organisatie worden toegewezen, meestal door uw internetprovider.

Voordat u dit artikel leest, moet u bekend zijn met dit [overzicht van reverse DNS en ondersteuning in Azure.](dns-reverse-dns-overview.md)

In Azure DNS worden rekenresources (zoals virtuele machines, virtuele machineschaalsets of Service Fabric-clusters) blootgesteld via een PublicIpAddress-bron. Reverse DNS lookups worden geconfigureerd met de eigenschap 'ReverseFqdn' van de PublicIpAddress.


Reverse DNS wordt momenteel niet ondersteund voor de Azure App Service.

## <a name="validation-of-reverse-dns-records"></a>Validatie van omgekeerde DNS-records

Een derde partij mag geen reverse DNS-records kunnen maken voor hun Azure-servicetoewijzing voor uw DNS-domeinen. Om dit te voorkomen, staat Azure alleen het maken van een omgekeerde DNS-record toe waarbij de domeinnaam die is opgegeven in de omgekeerde DNS-record hetzelfde is als of wordt opgelost met de DNS-naam of het IP-adres van een PublicIpAddress of Cloud Service in hetzelfde Azure-abonnement.

Deze validatie wordt alleen uitgevoerd wanneer de omgekeerde DNS-record is ingesteld of gewijzigd. Periodieke hervalidatie wordt niet uitgevoerd.

Stel dat de PublicIpAddress-bron de DNS-naam contosoapp1.northus.cloudapp.azure.com en IP-adres 23.96.52.53 heeft. De ReverseFqdn voor het PublicIpAddress kan worden opgegeven als:
* De DNS-naam voor het PublicIpAddress, contosoapp1.northus.cloudapp.azure.com
* De DNS-naam voor een ander PublicIpAddress in hetzelfde abonnement, zoals contosoapp2.westus.cloudapp.azure.com
* Een DNS-naam van ijdelheid, zoals app1.contoso.com, zolang deze naam *eerst* is geconfigureerd als CNAME om te contosoapp1.northus.cloudapp.azure.com, of naar een ander PublicIpAddress in hetzelfde abonnement.
* Een DNS-naam van ijdelheid, zoals app1.contoso.com, zolang deze naam *eerst* is geconfigureerd als Een A-record naar het IP-adres 23.96.52.53, of naar het IP-adres van een ander PublicIpAddress in hetzelfde abonnement.

Dezelfde beperkingen gelden voor reverse DNS voor Cloud Services.


## <a name="reverse-dns-for-publicipaddress-resources"></a>Reverse DNS voor PublicIpAddress-bronnen

In deze sectie vindt u gedetailleerde instructies voor het configureren van reverse DNS voor PublicIpAddress-bronnen in het implementatiemodel Resource Beheer, met azure PowerShell, Azure classic CLI of Azure CLI. Het configureren van reverse DNS voor PublicIpAddress-bronnen wordt momenteel niet ondersteund via de Azure-portal.

Azure ondersteunt momenteel reverse DNS alleen voor IPv4 PublicIpAddress-bronnen. Het wordt niet ondersteund voor IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Omgekeerde DNS toevoegen aan bestaande PublicIpAddresses

#### <a name="powershell"></a>PowerShell

Reverse DNS bijwerken naar een bestaand PublicIpAddress:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

Als u reverse DNS wilt toevoegen aan een bestaand PublicIpAddress dat nog geen DNS-naam heeft, moet u ook een DNS-naam opgeven:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

Omgekeerde DNS toevoegen aan een bestaand PublicIpAddress:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Als u reverse DNS wilt toevoegen aan een bestaand PublicIpAddress dat nog geen DNS-naam heeft, moet u ook een DNS-naam opgeven:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure-CLI

Omgekeerde DNS toevoegen aan een bestaand PublicIpAddress:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Als u reverse DNS wilt toevoegen aan een bestaand PublicIpAddress dat nog geen DNS-naam heeft, moet u ook een DNS-naam opgeven:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Een openbaar IP-adres maken met omgekeerde DNS

Ga als lid van het volgende over een nieuw PublicIpAddress met de eigenschap reverse DNS:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure-CLI

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Reverse DNS weergeven voor een bestaand PublicIpAddress

Ga als een overzicht van de geconfigureerde waarde voor een bestaand PublicIpAddress:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli"></a>Azure-CLI

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Reverse DNS verwijderen uit bestaande openbare IP-adressen

Ga als lid van het op- en terugschakelen van een andere DNS-eigenschap uit een bestaand PublicIpAddress:

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli"></a>Azure-CLI

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Reverse DNS configureren voor Cloud Services

In deze sectie vindt u gedetailleerde instructies voor het configureren van reverse DNS voor Cloud Services in het klassieke implementatiemodel met Azure PowerShell. Het configureren van reverse DNS voor Cloud Services wordt niet ondersteund via de Azure-portal, Azure classic CLI of Azure CLI.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Reverse DNS toevoegen aan bestaande Cloud Services

Ga als lid van het nieuwe DNS-record over naar een bestaande CloudService:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Een cloudservice maken met reverse DNS

Ga als lid van het volgende over een nieuwe cloudservice met de eigenschap reverse DNS:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Reverse DNS weergeven voor bestaande Cloud Services

Ga als het gaat om de omgekeerde DNS-eigenschap voor een bestaande cloudservice weer te geven:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Reverse DNS verwijderen uit bestaande Cloud Services

Ga als lid van het op- en terugschakelen van een omgekeerde DNS-eigenschap uit een bestaande CloudService:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>Veelgestelde vragen

### <a name="how-much-do-reverse-dns-records-cost"></a>Hoeveel kosten omgekeerde DNS-records?

Ze zijn vrij.  Er zijn geen extra kosten verbonden aan omgekeerde DNS-records of query's.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Worden mijn omgekeerde DNS-records van het internet opgelost?

Ja. Zodra u de omgekeerde DNS-eigenschap voor uw Azure-service hebt ingesteld, beheert Azure alle DNS-delegaties en DNS-zones die nodig zijn om ervoor te zorgen dat omgekeerde DNS-record voor alle internetgebruikers wordt opgelost.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Zijn standaard reverse DNS-records gemaakt voor mijn Azure-services?

Nee. Reverse DNS is een opt-in functie. Er worden geen standaard reverse DNS-records gemaakt als u ervoor kiest deze niet te configureren.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Wat is het formaat voor de volledig gekwalificeerde domeinnaam (FQDN)?

FQDN's worden in voorwaartse volgorde opgegeven en moeten worden beëindigd met een punt (bijvoorbeeld 'app1.contoso.com').

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Wat gebeurt er als de validatiecontrole voor de omgekeerde DNS die ik heb opgegeven mislukt?

Wanneer de reverse DNS-validatiecontrole mislukt, mislukt de bewerking om de omgekeerde DNS-record te configureren. Corrigeer de omgekeerde DNS-waarde naar behoefte en probeer het opnieuw.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Kan ik reverse DNS configureren voor Azure App Service?

Nee. Reverse DNS wordt niet ondersteund voor de Azure App Service.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Kan ik meerdere reverse DNS-records configureren voor mijn Azure-service?

Nee. Azure ondersteunt één omgekeerde DNS-record voor elke Azure Cloud Service of PublicIpAddress.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Kan ik reverse DNS configureren voor IPv6 PublicIpAddress-bronnen?

Nee. Azure ondersteunt momenteel reverse DNS alleen voor IPv4 PublicIpAddress-bronnen en Cloud Services.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Kan ik e-mails verzenden naar externe domeinen vanuit mijn Azure Compute-services?

De technische mogelijkheid om e-mail rechtstreeks vanuit een Azure-implementatie te verzenden, is afhankelijk van het abonnementstype. Ongeacht het abonnementstype raadt Microsoft aan om vertrouwde e-mailrelayservices te gebruiken om uitgaande e-mail te verzenden. Zie [Verbeterde Azure-beveiliging voor het verzenden van e-mails voor het verzenden van e-mails – november 2017 Update](https://blogs.msdn.microsoft.com/mast/2017/11/15/enhanced-azure-security-for-sending-emails-november-2017-update/).

## <a name="next-steps"></a>Volgende stappen

Zie [reverse DNS lookup op Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup)voor meer informatie over reverse DNS.
<br>
Meer informatie over het [hosten van de omgekeerde opzoekzone voor uw IP-bereik dat is toegewezen in Azure DNS.](dns-reverse-dns-for-azure-services.md)

