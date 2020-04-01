---
title: Omgekeerde DNS-opzoekzones hosten in Azure DNS
description: Meer informatie over het gebruik van Azure DNS om de omgekeerde DNS-opzoekzones voor uw IP-bereiken te hosten
author: rohinkoul
ms.service: dns
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 78fc3428274be5e1998abe9189bea996f15e278c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454258"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Omgekeerde DNS-opzoekzones hosten in Azure DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In dit artikel wordt uitgelegd hoe u de omgekeerde DNS-opzoekzones voor uw toegewezen IP-bereiken in Azure DNS host. De IP-bereiken die worden weergegeven door de omgekeerde opzoekzones moeten aan uw organisatie worden toegewezen, meestal door uw internetprovider.

Zie Reverse DNS configureren [voor services die worden gehost in Azure](dns-reverse-dns-for-azure-services.md)als u reverse DNS wilt configureren voor een IP-adres dat eigendom is van Azure dat is toegewezen aan uw Azure-service.

Voordat u dit artikel leest, moet u bekend zijn met het [overzicht van reverse DNS en ondersteuning in Azure.](dns-reverse-dns-overview.md)

In dit artikel u de stappen doorlopen om uw eerste reverse lookup DNS-zone en -record te maken met behulp van de Azure-portal, Azure PowerShell, Azure classic CLI of Azure CLI.

## <a name="create-a-reverse-lookup-dns-zone"></a>Een reverse lookup DNS-zone maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **in** het menu Hub de optie **Nieuwe** > **netwerken**en selecteer **vervolgens DNS-zone**.

   ![Selectie "DNS-zone"](./media/dns-reverse-dns-hosting/figure1.png)

1. Geef in het deelvenster **DNS-zone maken** een naam aan uw DNS-zone. De naam van de zone is anders gemaakt voor IPv4- en IPv6-voorvoegsels. Gebruik de instructies voor [IPv4](#ipv4) of [IPv6](#ipv6) om uw zone een naam te geven. Wanneer u klaar bent, selecteert u **Maken** om de zone te maken.

### <a name="ipv4"></a>IPv4

De naam van een IPv4 reverse lookup zone is gebaseerd op het IP-bereik dat het vertegenwoordigt. Het moet in het `<IPv4 network prefix in reverse order>.in-addr.arpa`volgende formaat: . Zie [Overzicht van reverse DNS en ondersteuning in Azure](dns-reverse-dns-overview.md#ipv4)voor voorbeelden.

> [!NOTE]
> Wanneer u klasseloze reverse DNS-opzoekzones maakt in Azure DNS,`-`moet u een`/`koppelteken () gebruiken in plaats van een voorwaartse slash ( ) in de zonenaam.
>
> Voor het IP-bereik 192.0.2.128/26 moet `128-26.2.0.192.in-addr.arpa` u bijvoorbeeld gebruiken `128/26.2.0.192.in-addr.arpa`als zonenaam in plaats van .
>
> Hoewel de DNS-standaarden beide methoden ondersteunen, ondersteunt Azure DNS geen`/`DNS-zonenamen die voor het teken van slash doorsturen ( ) bevatten.

In het volgende voorbeeld ziet u hoe `2.0.192.in-addr.arpa` u een reverse DNS-zone van klasse C maakt die in Azure DNS is genoemd via de Azure-portal:

 ![Deelvenster 'DNS-zone maken', met vakken ingevuld](./media/dns-reverse-dns-hosting/figure2.png)

**Locatie resourcegroep** definieert de locatie voor de resourcegroep. Het heeft geen invloed op de DNS-zone. De locatie van de DNS-zone is altijd 'global' en wordt niet weergegeven.

In de volgende voorbeelden ziet u hoe u deze taak voltooien met Azure PowerShell en Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

De naam van een omgekeerde opzoekzone van IPv6 moet in de volgende vorm staan: `<IPv6 network prefix in reverse order>.ip6.arpa`.  Zie [Overzicht van reverse DNS en ondersteuning in Azure](dns-reverse-dns-overview.md#ipv6)voor voorbeelden.


In het volgende voorbeeld ziet u hoe u een `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` iPv6 reverse DNS-opzoekzone maakt die in Azure DNS is genoemd via de Azure-portal:

 ![Deelvenster 'DNS-zone maken', met vakken ingevuld](./media/dns-reverse-dns-hosting/figure3.png)

**Locatie resourcegroep** definieert de locatie voor de resourcegroep. Het heeft geen invloed op de DNS-zone. De locatie van de DNS-zone is altijd 'global' en wordt niet weergegeven.

In de volgende voorbeelden ziet u hoe u deze taak voltooien met Azure PowerShell en Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Een omgekeerde DNS-opzoekzone delegeren

Nu u uw omgekeerde DNS-opzoekzone hebt gemaakt, moet u ervoor zorgen dat de zone wordt gedelegeerd vanuit de bovenliggende zone. Dns-delegatie stelt het DNS-naamomzettingsproces in staat om de naamservers te vinden die uw omgekeerde DNS-opzoekzone hosten. Deze naamservers kunnen vervolgens dns-omgekeerde query's beantwoorden voor de IP-adressen in uw adresbereik.

Voor zoekzones voor vooruit wordt het proces voor het delegeren van een DNS-zone beschreven in [Uw domein delegeren aan Azure DNS.](dns-delegate-domain-azure-dns.md) Delegatie voor omgekeerde opzoekzones werkt op dezelfde manier. Het enige verschil is dat u de naamservers moet configureren met de ISP die uw IP-bereik heeft opgegeven, in plaats van uw domeinnaamregistrar.

## <a name="create-a-dns-ptr-record"></a>Een DNS PTR-record maken

### <a name="ipv4"></a>IPv4

In het volgende voorbeeld wordt u door het proces van het maken van een PTR-record in een omgekeerde DNS-zone in Azure DNS gebracht. Voor andere typen en voor het wijzigen van bestaande records, raadpleegt u [Manage DNS records and record sets by using the Azure portal](dns-operations-recordsets-portal.md) (DNS-records en -recordsets beheren met behulp van Azure Portal).

1. Selecteer boven aan het **deelvenster DNS-zone** de optie **+ Record instellen** om het deelvenster Record toevoegen **te** openen.

   ![Knop voor het maken van een recordset](./media/dns-reverse-dns-hosting/figure4.png)

1. De naam van het record dat is ingesteld voor een PTR-record moet de rest van het IPv4-adres in omgekeerde volgorde zijn. 

   In dit voorbeeld worden de eerste drie octetten al ingevuld als onderdeel van de zonenaam (.2.0.192). Daarom wordt alleen het laatste octet geleverd in de **doos Naam.** U bijvoorbeeld uw recordset **15** noemen voor een resource waarvan het IP-adres 192.0.2.15 is.  
1. Selecteer **PTR**voor **Type**.  
1. Voer voor **domeinnaam**de volledig gekwalificeerde domeinnaam (FQDN) in van de bron die het IP gebruikt.
1. Selecteer **OK** onder aan het deelvenster om de DNS-record te maken.

   ![Deelvenster Recordset toevoegen, met vakken ingevuld](./media/dns-reverse-dns-hosting/figure5.png)

In de volgende voorbeelden ziet u hoe u deze taak voltooien met PowerShell of Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

In het volgende voorbeeld wordt u door het proces van het maken van een nieuwe PTR-record gezwalk. Voor andere typen en voor het wijzigen van bestaande records, raadpleegt u [Manage DNS records and record sets by using the Azure portal](dns-operations-recordsets-portal.md) (DNS-records en -recordsets beheren met behulp van Azure Portal).

1. Selecteer boven aan het **deelvenster DNS-zone** de optie **+ Record instellen** om het deelvenster Record toevoegen **te** openen.

   ![Knop voor het maken van een recordset](./media/dns-reverse-dns-hosting/figure6.png)

2. De naam van het record dat is ingesteld voor een PTR-record moet de rest van het IPv6-adres in omgekeerde volgorde zijn. Het mag geen nulcompressie bevatten. 

   In dit voorbeeld worden de eerste 64 bits van de IPv6 al ingevuld als onderdeel van de zonenaam (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa). Daarom worden alleen de laatste 64 bits geleverd in de **doos Naam.** De laatste 64 bits van het IP-adres worden in omgekeerde volgorde ingevoerd, met een periode als de scheidingtussen elk hexadecimaal getal. U bijvoorbeeld uw recordset **e.5.0.4.9.f.a.c.b.0.1.4.2.5.f** voor een resource waarvan het IP-adres 2001:0db8:abdc:0000:f524:10bc:10bc:10bc9:405e is, een naam geven.  
3. Selecteer **PTR**voor **Type**.  
4. Voer **voor domeinnaam**de FQDN in van de bron die het IP-adres gebruikt.
5. Selecteer **OK** onder aan het deelvenster om de DNS-record te maken.

![Deelvenster Recordset toevoegen, met vakken ingevuld](./media/dns-reverse-dns-hosting/figure7.png)

In de volgende voorbeelden ziet u hoe u deze taak voltooien met PowerShell of Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

```azurecli
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Records weergeven

Als u de records wilt weergeven die u hebt gemaakt, bladert u naar uw DNS-zone in de Azure-portal. In het onderste gedeelte van het **deelvenster DNS-zone** ziet u de records voor de DNS-zone. U ziet de standaardNS- en SOA-records, plus eventuele nieuwe records die u hebt gemaakt. De NS- en SOA-records worden in elke zone gemaakt. 

### <a name="ipv4"></a>IPv4

In het **deelvenster DNS-zone** worden de IPv4 PTR-records weergegeven:

![Deelvenster 'DNS-zone' met IPv4-records](./media/dns-reverse-dns-hosting/figure8.png)

In de volgende voorbeelden ziet u hoe u de PTR-records weergeven met PowerShell of Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

```azurecli
azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

In het **deelvenster DNS-zone** worden de IPv6 PTR-records weergegeven:

![Deelvenster 'DNS-zone' met IPv6-records](./media/dns-reverse-dns-hosting/figure9.png)

In de volgende voorbeelden ziet u hoe u de records weergeven met PowerShell of Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

```azurecli
azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>Veelgestelde vragen

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Kan ik omgekeerde DNS-zoekzones hosten voor mijn IP-blokkeringsblokken die isp is toegewezen op Azure DNS?

Ja. Het hosten van de ARPA-zones (reverse lookup) voor uw eigen IP-bereiken in Azure DNS wordt volledig ondersteund.

Maak de omgekeerde opzoekzone in Azure DNS zoals uitgelegd in dit artikel en werk vervolgens samen met uw internetprovider om de zone te [delegeren.](dns-domain-delegation.md) U vervolgens de PTR-records voor elke omgekeerde lookup op dezelfde manier beheren als andere recordtypen.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Hoeveel kost het hosten van mijn reverse DNS lookup zone?

Als host van de omgekeerde DNS-zoekzone voor uw IP-blok dat is toegewezen in Azure DNS, wordt deze in rekening gebracht tegen [standaard Azure DNS-tarieven.](https://azure.microsoft.com/pricing/details/dns/)

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Kan ik omgekeerde DNS-opzoekzones hosten voor zowel IPv4- als IPv6-adressen in Azure DNS?

Ja. In dit artikel wordt uitgelegd hoe u zowel IPv4- als IPv6 reverse DNS-opzoekzones in Azure DNS maakt.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Kan ik een bestaande omgekeerde DNS-opzoekzone importeren?

Ja. U Azure CLI gebruiken om bestaande DNS-zones in Azure DNS te importeren. Deze methode werkt voor zowel vooruitzoekzones als omgekeerde opzoekzones.

Zie [Een DNS-zonebestand importeren en exporteren met Azure CLI](dns-import-export.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie [reverse DNS lookup op Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup)voor meer informatie over reverse DNS.
<br>
Meer informatie over het [beheren van reverse DNS-records voor uw Azure-services](dns-reverse-dns-for-azure-services.md).
