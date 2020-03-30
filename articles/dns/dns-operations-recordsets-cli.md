---
title: DNS-records in Azure DNS beheren met de Azure CLI
description: DNS-recordsets en -records beheren op Azure DNS wanneer u uw domein host op Azure DNS.
author: rohinkoul
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 05/15/2018
ms.author: rohink
ms.openlocfilehash: 4e017dc940e1d32888ff279904e44d34db1fd5c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936892"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>DNS-records en recordsets in Azure DNS beheren met de Azure CLI

> [!div class="op_single_selector"]
> * [Azure-portal](dns-operations-recordsets-portal.md)
> * [Azure-CLI](dns-operations-recordsets-cli.md)
> * [Powershell](dns-operations-recordsets.md)

In dit artikel ziet u hoe u DNS-records voor uw DNS-zone beheert met behulp van het cross-platform Azure CLI, dat beschikbaar is voor Windows, Mac en Linux. U uw DNS-records ook beheren met [Azure PowerShell](dns-operations-recordsets.md) of de [Azure-portal.](dns-operations-recordsets-portal.md)

In de voorbeelden in dit artikel wordt ervan uitgegaan dat u de Azure CLI al hebt [geïnstalleerd, bent aangemeld en een DNS-zone](dns-operations-dnszones-cli.md)hebt gemaakt.

## <a name="introduction"></a>Inleiding

Voordat u DNS-records in DNS Azure maakt, leest u eerst hoe Azure DNS DNS-records organiseert in DNS-recordsets.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Zie [DNS-zones en -records](dns-zones-records.md) voor meer informatie over DNS-records in Azure DNS.

## <a name="create-a-dns-record"></a>Een DNS-record maken

Als u een DNS-record wilt maken, gebruikt u de `az network dns record-set <record-type> add-record` opdracht (waar `<record-type>` is het type record, d.w.z. a, srv, txt, enz.) Voor hulp, `az network dns record-set --help`zie .

Bij het maken van een record, dient u de naam van de resourcegroep, de zonenaam, de naam van de recordset, het recordtype en de details van de record die wordt gemaakt op te geven. De gegeven recordnaam moet een *relatieve* naam zijn, wat betekent dat de zonenaam moet worden uitgesloten.

Als de recordset nog niet bestaat, maakt u er met deze opdracht een. Als de recordset al bestaat, voegt u met deze opdracht de opgegeven record toe aan de bestaande recordset.

Als er een nieuwe recordset wordt gemaakt, wordt een standaard time-to-live (TTL) van 3600 gebruikt. Zie [Een DNS-recordset maken](#create-a-dns-record-set)voor instructies over het gebruik van verschillende TTLs.

In het volgende voorbeeld maakt u een A-record met de naam *www* in de zone *contoso.com* in de resourcegroep *MyResourceGroup*. Het IP-adres van de A-record is *1.2.3.4*.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Als u een record wilt maken dat in de top van de\@zone is gevestigd (in dit geval 'contoso.com'), gebruikt u de recordnaam " ", inclusief de aanhalingstekens:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Een DNS-recordset maken

In de bovenstaande voorbeelden is de DNS-record toegevoegd aan een bestaande recordset of is de recordset *impliciet*gemaakt. U de recordset ook *expliciet* maken voordat u er records aan toevoegt. Azure DNS ondersteunt 'lege' recordsets, die kunnen fungeren als tijdelijke aanduiding om een DNS-naam te reserveren voordat u DNS-records maakt. Lege recordsets zijn zichtbaar in het Azure DNS-beheervlak, maar worden niet weergegeven op de Azure DNS-naamservers.

Recordsets worden gemaakt `az network dns record-set <record-type> create` met behulp van de opdracht. Zie `az network dns record-set <record-type> create --help` voor help.

Als u de recordset expliciet maakt, u recordseteigenschappen opgeven, zoals de [Time-To-Live (TTL)](dns-zones-records.md#time-to-live) en metagegevens. [Recordsetmetagegevens](dns-zones-records.md#tags-and-metadata) kunnen worden gebruikt om toepassingsspecifieke gegevens aan elke recordset te koppelen als sleutelwaardeparen.

In het volgende voorbeeld wordt een lege recordset van type 'A' `--ttl` gemaakt met `-l`een TTL van 60 seconden, met behulp van de parameter (korte vorm):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

In het volgende voorbeeld wordt een recordset gemaakt met twee metagegevens, 'dept=finance' en 'environment=production', met behulp van de `--metadata` parameter:

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Nadat een lege recordset is gemaakt, kunnen records worden toegevoegd met gebruik maken zoals `azure network dns record-set <record-type> add-record` beschreven in Een [DNS-record maken.](#create-a-dns-record)

## <a name="create-records-of-other-types"></a>Records van andere typen maken

Na in detail te hebben gezien hoe je 'A'-records maakt, laten de volgende voorbeelden zien hoe je records maken van andere recordtypen die worden ondersteund door Azure DNS.

De parameters die u gebruikt om de gegevens van de record op te geven, variëren afhankelijk van het type record. Voor een record van het type 'A' geeft u bijvoorbeeld het IPv4-adres met de parameter `--ipv4-address <IPv4 address>` op. De parameters voor elk recordtype `az network dns record-set <record-type> add-record --help`kunnen worden weergegeven met behulp van .

In elk geval laten we zien hoe je één plaat maakt. De record wordt toegevoegd aan de bestaande recordset of een recordset die impliciet is gemaakt. Zie [Een DNS-recordset maken](#create-a-dns-record-set)voor meer informatie over het maken van recordsets en het expliciet definiëren van recordset.

We geven geen voorbeeld om een SOA-recordset te maken, omdat SOA's bij elke DNS-zone worden gemaakt en verwijderd en niet afzonderlijk kunnen worden gemaakt of verwijderd. De [SOA kan echter worden gewijzigd, zoals in een later voorbeeld wordt weergegeven.](#to-modify-an-soa-record)

### <a name="create-an-aaaa-record"></a>Een AAAA-record maken

```azurecli
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>Een CAA-record maken

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>Een CNAME-record maken

> [!NOTE]
> De DNS-standaarden staan cname-records niet`--Name "@"`toe aan de top van een zone ( ), noch staan ze recordsets toe die meer dan één record bevatten.
> 
> Zie [CNAME-records](dns-zones-records.md#cname-records)voor meer informatie .

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Een MX-record maken

In dit voorbeeld gebruiken we de\@recordsetnaam " om de MX-record te maken op de zonetop (in dit geval 'contoso.com').

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Een NS-record maken

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Een PTR-record maken

In dit geval vertegenwoordigt 'my-arpa-zone.com' de ARPA-zone die uw IP-bereik vertegenwoordigt. Elke PTR-recordset die is ingesteld in deze zone komt overeen met een IP-adres in dit IP-bereik.  De recordnaam '10' is het laatste octet van het IP-adres binnen dit IP-bereik dat door deze record wordt weergegeven.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Een SRV-record maken

Wanneer u een [SRV-recordset maakt,](dns-zones-records.md#srv-records)geeft u de * \_service* en * \_het protocol* op in de recordsetnaam. Het is niet nodig\@om " " op te nemen in de recordsetnaam bij het maken van een SRV-record op de zonetop.

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>Een TXT-record maken

In het volgende voorbeeld ziet u hoe u een TXT-record maakt. Zie [TXT-records](dns-zones-records.md#txt-records)voor meer informatie over de maximale tekenreekslengte die wordt ondersteund in TXT-records.

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Een recordset

Als u een bestaande `az network dns record-set <record-type> show`recordset wilt ophalen, gebruikt u . Zie `az network dns record-set <record-type> show --help` voor help.

Net als bij het maken van een record- of recordset moet de gegeven recordnaam een *relatieve* naam zijn, wat betekent dat de zonenaam moet worden uitgesloten. U moet ook het recordtype opgeven, de zone met de recordset en de resourcegroep die de zone bevat.

In het volgende voorbeeld wordt de *recordwww* van type A opgehaald uit zone *contoso.com* in resourcegroep *MyResourceGroup:*

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Lijstrecordsets

U alle records in een `az network dns record-set list` DNS-zone weergeven met behulp van de opdracht. Zie `az network dns record-set list --help` voor help.

In dit voorbeeld worden alle recordsets in de zone *contoso.com*, in resourcegroep *MyResourceGroup*geretourneerd, ongeacht de naam of het recordtype:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

In dit voorbeeld worden alle recordsets geretourneerd die overeenkomen met het opgegeven recordtype (in dit geval 'A'-records):

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Een record toevoegen aan een bestaande recordset

U kunt `az network dns record-set <record-type> add-record` beide gebruiken om een record te maken in een nieuwe recordset of om een record toe te voegen aan een bestaande recordset.

Zie [Een DNS-record maken](#create-a-dns-record) en [Records van andere typen](#create-records-of-other-types) maken voor meer informatie.

## <a name="remove-a-record-from-an-existing-record-set"></a>Verwijder een record uit een bestaande recordset.

Als u een DNS-record wilt `az network dns record-set <record-type> remove-record`verwijderen uit een bestaande recordset, gebruikt u . Zie `az network dns record-set <record-type> remove-record -h` voor help.

Met deze opdracht wordt een DNS-record verwijderd uit een recordset. Als de laatste record in een recordset wordt verwijderd, wordt de recordset zelf ook verwijderd. Als u de lege recordset `--keep-empty-record-set` wilt behouden, gebruikt u de optie.

U moet de record opgeven die moet worden verwijderd en de zone waaruit deze moet `az network dns record-set <record-type> add-record`worden verwijderd, met dezelfde parameters als bij het maken van een record met behulp van . Deze parameters worden beschreven in [Een DNS-record maken](#create-a-dns-record) en [Records maken van andere typen](#create-records-of-other-types) hierboven.

In het volgende voorbeeld wordt de A-record met de waarde '1.2.3.4' verwijderd uit de recordset met de naam *www* in de zone *contoso.com*, in de resourcegroep *MyResourceGroup*.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Een bestaande recordset wijzigen

Elke recordset bevat een [time-to-live (TTL)](dns-zones-records.md#time-to-live), [metadata](dns-zones-records.md#tags-and-metadata)en DNS-records. In de volgende secties wordt uitgelegd hoe u elk van deze eigenschappen wijzigen.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>Een A,AAA,CAA, MX, NS, PTR, SRV of TXT-record wijzigen

Als u een bestaande record van het type A, AAAA, CAA, MX, NS, PTR, SRV of TXT wilt wijzigen, moet u eerst een nieuwe record toevoegen en vervolgens de bestaande record verwijderen. Zie de eerdere gedeelten van dit artikel voor gedetailleerde instructies over het verwijderen en toevoegen van records.

In het volgende voorbeeld wordt uitgelegd hoe u een A-record wijzigen, van IP-adres 1.2.3.4 naar IP-adres 5.6.7.8:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

U de records in het automatisch gemaakte NS-record op de`--Name "@"`zonetop ( inclusief aanhalingstekens) niet toevoegen, verwijderen of wijzigen. Voor deze recordset zijn de enige wijzigingen die zijn toegestaan het wijzigen van de recordset TTL en metagegevens.

### <a name="to-modify-a-cname-record"></a>Een CNAME-record wijzigen

In tegenstelling tot de meeste andere recordtypen kan een CNAME-recordset slechts één record bevatten.  Daarom u de huidige waarde niet vervangen door een nieuwe record toe te voegen en de bestaande record te verwijderen, zoals voor andere recordtypen.

Als u in plaats daarvan `az network dns record-set cname set-record`een CNAME-record wilt wijzigen, gebruikt u . Meer informatie vindt u in `az network dns record-set cname set-record --help`

Het voorbeeld wijzigt het CNAME-record dat is ingesteld *op www* in de zone *contoso.com*, in resourcegroep *MyResourceGroup,* om te wijzen op 'www.fabrikam.net' in plaats van de bestaande waarde:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Een SOA-record wijzigen

In tegenstelling tot de meeste andere recordtypen kan een CNAME-recordset slechts één record bevatten.  Daarom u de huidige waarde niet vervangen door een nieuwe record toe te voegen en de bestaande record te verwijderen, zoals voor andere recordtypen.

Gebruik `az network dns record-set soa update`in plaats daarvan . Zie `az network dns record-set soa update --help` voor help.

In het volgende voorbeeld ziet u hoe u de eigenschap 'e-mail' instelt van het SOA-record voor de zone *contoso.com* in de resourcegroep *MyResourceGroup:*

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>NS-records wijzigen op de zoneapex

Het NS-record dat is ingesteld op de zone-top wordt automatisch gemaakt met elke DNS-zone. Het bevat de namen van de Azure DNS-naamservers die aan de zone zijn toegewezen.

U extra naamservers toevoegen aan deze NS-recordset om co-hostingdomeinen met meer dan één DNS-provider te ondersteunen. U ook de TTL en metagegevens voor deze recordset wijzigen. U de vooraf ingevulde Azure DNS-naamservers echter niet verwijderen of wijzigen.

Dit geldt alleen voor het NS-record dat op de zonetop is gevestigd. Andere NS-recordsets in uw zone (zoals gebruikt om onderliggende zones te delegeren) kunnen zonder beperking worden gewijzigd.

In het volgende voorbeeld ziet u hoe u een extra naamserver toevoegt aan het NS-record dat is ingesteld op de top van de zone:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>De TTL van een bestaande recordset wijzigen

Als u de TTL van een `azure network dns record-set <record-type> update`bestaande recordset wilt wijzigen, gebruikt u . Zie `azure network dns record-set <record-type> update --help` voor help.

In het volgende voorbeeld ziet u hoe u een recordset TTL wijzigt, in dit geval tot 60 seconden:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>De metagegevens van een bestaande recordset wijzigen

[Recordsetmetagegevens](dns-zones-records.md#tags-and-metadata) kunnen worden gebruikt om toepassingsspecifieke gegevens aan elke recordset te koppelen als sleutelwaardeparen. Als u de metagegevens van `az network dns record-set <record-type> update`een bestaande recordset wilt wijzigen, gebruikt u . Zie `az network dns record-set <record-type> update --help` voor help.

In het volgende voorbeeld ziet u hoe u een recordset wijzigt met twee metagegevens, 'dept=finance' en 'environment=production'. Houd er rekening mee dat bestaande metagegevens worden *vervangen* door de opgegeven waarden.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Een recordset verwijderen

Recordsets kunnen worden verwijderd `az network dns record-set <record-type> delete` met behulp van de opdracht. Zie `azure network dns record-set <record-type> delete --help` voor help. Als u een recordset verwijdert, worden ook alle records binnen de recordset verwijderd.

> [!NOTE]
> U de SOA- en NS-recordsets niet verwijderen op de zonetop (`--name "@"`).  Deze worden automatisch gemaakt wanneer de zone is gemaakt en worden automatisch verwijderd wanneer de zone wordt verwijderd.

In het volgende voorbeeld wordt de recordset met de naam *www* van type A verwijderd uit de zone *contoso.com* in resourcegroep *MyResourceGroup:*

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

U wordt gevraagd de verwijderingsbewerking te bevestigen. Gebruik de `--yes` schakelaar om deze prompt te onderdrukken.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [zones en records in Azure DNS](dns-zones-records.md).
<br>
Meer informatie over het [beveiligen van uw zones en records](dns-protect-zones-recordsets.md) bij het gebruik van Azure DNS.
