---
title: DNS-records beheren in Azure DNS met Azure PowerShell | Microsoft Documenten
description: DNS-recordsets en -records beheren op Azure DNS wanneer u uw domein host op Azure DNS. Alle PowerShell-opdrachten voor bewerkingen op recordsets en -records.
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: rohink
ms.openlocfilehash: b9244d9b2bdc9cb20195bbc103c0b1eb48a9de63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932532"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>DNS-records en recordsets beheren in Azure DNS met Azure PowerShell

> [!div class="op_single_selector"]
> * [Azure-portal](dns-operations-recordsets-portal.md)
> * [Klassieke versie van Azure CLI](dns-operations-recordsets-cli-nodejs.md)
> * [Azure-CLI](dns-operations-recordsets-cli.md)
> * [Powershell](dns-operations-recordsets.md)

In dit artikel ziet u hoe u DNS-records voor uw DNS-zone beheert met Azure PowerShell. DNS-records kunnen ook worden beheerd met behulp van de cross-platform [Azure CLI](dns-operations-recordsets-cli.md) of de [Azure-portal.](dns-operations-recordsets-portal.md)

De voorbeelden in dit artikel gaan ervan uit dat u Azure PowerShell al hebt [geïnstalleerd, bent aangemeld en een DNS-zone](dns-operations-dnszones.md)hebt gemaakt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>Inleiding

Voordat u DNS-records in DNS Azure maakt, leest u eerst hoe Azure DNS DNS-records organiseert in DNS-recordsets.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Zie [DNS-zones en -records](dns-zones-records.md) voor meer informatie over DNS-records in Azure DNS.


## <a name="create-a-new-dns-record"></a>Een nieuwe DNS-record maken

Als uw nieuwe record dezelfde naam en type heeft als een bestaande record, moet u [deze toevoegen aan de bestaande recordset](#add-a-record-to-an-existing-record-set). Als uw nieuwe record een andere naam en type heeft dan alle bestaande records, moet u een nieuwe recordset maken. 

### <a name="create-a-records-in-a-new-record-set"></a>'A'-records maken in een nieuwe recordset

U kunt recordsets maken met behulp van de cmdlet `New-AzDnsRecordSet`. Wanneer u een recordset maakt, moet u de recordsetnaam, de zone, de time to live (TTL), het recordtype en de records opgeven die moeten worden gemaakt.

De parameters voor het toevoegen van records aan een recordset variëren afhankelijk van het type recordset. Wanneer u bijvoorbeeld een recordset van type 'A' gebruikt, moet `-IPv4Address`u het IP-adres opgeven met behulp van de parameter . Andere parameters worden gebruikt voor andere recordtypen. Zie Aanvullende voorbeelden van recordtypen voor meer informatie.

In het volgende voorbeeld wordt een recordset met de relatieve naam 'www' in de DNS-zone 'contoso.com' aan. De volledig gekwalificeerde naam van het record is 'www.contoso.com'. Het recordtype is 'A', en de TTL is 3600 seconden. De recordset bevat één record, met IP-adres '1.2.3.4'.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Als u een record wilt maken dat is ingesteld op de 'top' van\@een zone (in dit geval 'contoso.com'), gebruikt u de recordvaste naam ' (met uitzondering van aanhalingstekens):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Als u een recordset met meer dan één record wilt maken, maakt u eerst `New-AzDnsRecordSet` een lokale array en voegt u de records toe en geeft u de array als volgt door:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[Recordsetmetagegevens](dns-zones-records.md#tags-and-metadata) kunnen worden gebruikt om toepassingsspecifieke gegevens aan elke recordset te koppelen als sleutelwaardeparen. In het volgende voorbeeld ziet u hoe u een recordset maakt met twee metagegevens, 'dept=finance' en 'environment=production'.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Azure DNS ondersteunt ook 'lege' recordsets, die kunnen fungeren als tijdelijke aanduiding om een DNS-naam te reserveren voordat u DNS-records maakt. Lege recordsets zijn zichtbaar in het Azure DNS-beheervlak, maar worden wel weergegeven op de Azure DNS-naamservers. In het volgende voorbeeld wordt een lege recordset gemaakt:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Records van andere typen maken

Na in detail te hebben gezien hoe u 'A'-records maakt, wordt in de volgende voorbeelden weergegeven hoe u records maken van andere recordtypen die worden ondersteund door Azure DNS.

In elk geval laten we zien hoe je een recordset maakt met één record. De eerdere voorbeelden voor 'A'-records kunnen worden aangepast om recordsets te maken van andere typen die meerdere records bevatten, met metagegevens of om lege recordsets te maken.

We geven geen voorbeeld om een SOA-recordset te maken, omdat SOA's bij elke DNS-zone worden gemaakt en verwijderd en niet afzonderlijk kunnen worden gemaakt of verwijderd. De [SOA kan echter worden gewijzigd, zoals in een later voorbeeld wordt weergegeven.](#to-modify-an-soa-record)

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Een AAAA-recordset met één record maken

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Een CAA-record set maken met één record

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Een CNAME-recordset met één record maken

> [!NOTE]
> De DNS-standaarden staan cname-records niet`-Name '@'`toe aan de top van een zone ( ), noch staan ze recordsets toe die meer dan één record bevatten.
> 
> Zie [CNAME-records](dns-zones-records.md#cname-records)voor meer informatie .


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Een MX-recordset met één record maken

In dit voorbeeld gebruiken we de\@recordsetnaam ' ' om een MX-record te maken op de zone-top (in dit geval 'contoso.com').


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Een NS-recordset met één record maken

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Een PTR-recordset met één record maken

In dit geval vertegenwoordigt 'my-arpa-zone.com' de ARPA-omgekeerde opzoekzone die uw IP-bereik vertegenwoordigt. Elke PTR-recordset die is ingesteld in deze zone komt overeen met een IP-adres in dit IP-bereik. De recordnaam '10' is het laatste octet van het IP-adres binnen dit IP-bereik dat door deze record wordt weergegeven.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Een SRV-recordset met één record maken

Wanneer u een [SRV-recordset maakt,](dns-zones-records.md#srv-records)geeft u de * \_service* en * \_het protocol* op in de recordsetnaam. Het is niet nodig\@om ' ' in de recordsetnaam op te nemen bij het maken van een SRV-record op de zonetop.

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Een TXT-recordset maken met één record

In het volgende voorbeeld ziet u hoe u een TXT-record maakt. Zie [TXT-records](dns-zones-records.md#txt-records)voor meer informatie over de maximale tekenreekslengte die wordt ondersteund in TXT-records.

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Een recordset

Als u een bestaande `Get-AzDnsRecordSet`recordset wilt ophalen, gebruikt u . Met deze cmdlet wordt een lokaal object geretourneerd dat de recordset in Azure DNS vertegenwoordigt.

Net `New-AzDnsRecordSet`als bij , moet de gegeven recordsetnaam een *relatieve* naam zijn, wat betekent dat de zonenaam moet worden uitgesloten. U moet ook het recordtype en de zone opgeven die de recordset bevat.

In het volgende voorbeeld ziet u hoe u een recordset ophaalt. In dit voorbeeld wordt de `-ZoneName` zone `-ResourceGroupName` opgegeven met behulp van de parameters en parameters.

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

U ook de zone opgeven met behulp van `-Zone` een zoneobject, dat is doorgegeven met behulp van de parameter.

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Lijstrecordsets

U `Get-AzDnsZone` ook recordsets in een zone weergeven `-Name` door `-RecordType` de en/of parameters weg te laten.

In het volgende voorbeeld worden alle recordsets in de zone geretourneerd:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

In het volgende voorbeeld ziet u hoe alle recordsets van een bepaald type kunnen worden opgehaald door het recordtype op te geven terwijl de recordnaam wordt weggegeven:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Als u alle recordsets met een bepaalde naam wilt ophalen, moet u alle recordsets ophalen en vervolgens de resultaten filteren:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

In alle bovenstaande voorbeelden kan de zone worden `-ZoneName` opgegeven `-ResourceGroupName`met behulp van de parameters en parameters (zoals weergegeven), of door een zoneobject op te geven:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Een record toevoegen aan een bestaande recordset

Voer de volgende drie stappen uit om een record toe te voegen aan een bestaande recordset:

1. De bestaande recordset oppakken

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Voeg de nieuwe record toe aan de lokale recordset. Dit is een off-line operatie.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. De wijziging opnieuw vastleggen in de Azure DNS-service. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

Met `Set-AzDnsRecordSet` behulp van *vervangt* u de bestaande recordset in Azure DNS (en alle records die deze bevat) door de opgegeven recordset. [Etag-controles](dns-zones-records.md#etags) worden gebruikt om ervoor te zorgen dat gelijktijdige wijzigingen niet worden overschreven. U de `-Overwrite` optionele schakelaar gebruiken om deze controles te onderdrukken.

Deze reeks bewerkingen kan ook worden *gepipeteerd,* wat betekent dat u het recordsetobject passeert met behulp van de pijp in plaats van het als parameter door te geven:

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

De bovenstaande voorbeelden laten zien hoe je een 'A'-record toevoegt aan een bestaande recordset van het type 'A'. Een vergelijkbare reeks bewerkingen wordt gebruikt om records toe te `-Ipv4Address` voegen `Add-AzDnsRecordConfig` aan recordsets van andere typen, waarbij de parameter wordt vervangen door andere parameters die specifiek zijn voor elk recordtype. De parameters voor elk recordtype zijn `New-AzDnsRecordConfig` dezelfde als voor de cmdlet, zoals weergegeven in bovenstaande voorbeelden van extra recordtypen.

Recordsets van het type 'CNAME' of 'SOA' mogen niet meer dan één record bevatten. Deze beperking vloeit voort uit de DNS-standaarden. Het is geen beperking van Azure DNS.

## <a name="remove-a-record-from-an-existing-record-set"></a>Een record verwijderen uit een bestaande recordset

Het proces om een record uit een recordset te verwijderen, is vergelijkbaar met het proces om een record toe te voegen aan een bestaande recordset:

1. De bestaande recordset oppakken

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Verwijder de record uit het lokale recordsetobject. Dit is een off-line operatie. De record die wordt verwijderd, moet exact overeenkomen met een bestaande record voor alle parameters.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. De wijziging opnieuw vastleggen in de Azure DNS-service. Gebruik de `-Overwrite` optionele schakelaar om [Etag-controles](dns-zones-records.md#etags) op gelijktijdige wijzigingen te onderdrukken.

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

Als u de bovenstaande reeks gebruikt om de laatste record uit een recordset te verwijderen, wordt de recordset niet verwijderd, maar blijft er een lege recordset achter. Zie [Een recordset verwijderen](#delete-a-record-set)als u een recordset volledig wilt verwijderen.

Net als bij het toevoegen van records aan een recordset, kan de volgorde van bewerkingen om een recordset te verwijderen ook worden gepipeteerd:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Verschillende recordtypen worden ondersteund door de juiste `Remove-AzDnsRecordSet`typespecifieke parameters door te geven aan . De parameters voor elk recordtype zijn `New-AzDnsRecordConfig` dezelfde als voor de cmdlet, zoals weergegeven in bovenstaande voorbeelden van extra recordtypen.


## <a name="modify-an-existing-record-set"></a>Een bestaande recordset wijzigen

De stappen voor het wijzigen van een bestaande recordset zijn vergelijkbaar met de stappen die u neemt bij het toevoegen of verwijderen van records uit een recordset:

1. Haal de bestaande record `Get-AzDnsRecordSet`op die is ingesteld met behulp van .
2. Wijzig het lokale recordsetobject door:
    * Records toevoegen of verwijderen
    * De parameters van bestaande records wijzigen
    * De recordset metagegevens en time to live (TTL) wijzigen
3. Bega uw wijzigingen `Set-AzDnsRecordSet` met behulp van de cmdlet. Hiermee wordt de bestaande recordset in Azure DNS *vervangen* door de opgegeven recordset.

Bij `Set-AzDnsRecordSet`gebruik worden [Etag-controles](dns-zones-records.md#etags) gebruikt om ervoor te zorgen dat gelijktijdige wijzigingen niet worden overschreven. U de `-Overwrite` optionele schakelaar gebruiken om deze controles te onderdrukken.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Een record bijwerken in een bestaande recordset

In dit voorbeeld wijzigen we het IP-adres van een bestaande 'A'-record:

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Een SOA-record wijzigen

U geen records toevoegen of verwijderen uit de automatisch`-Name "@"`gemaakte SOA-record die is ingesteld op de zonetop (, inclusief aanhalingstekens). U echter een van de parameters in de SOA-record (behalve 'Host') en de recordset TTL wijzigen.

In het volgende voorbeeld ziet u hoe u de eigenschap *E-mail* van de SOA-record wijzigt:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>NS-records wijzigen op de zoneapex

Het NS-record dat is ingesteld op de zone-top wordt automatisch gemaakt met elke DNS-zone. Het bevat de namen van de Azure DNS-naamservers die aan de zone zijn toegewezen.

U extra naamservers toevoegen aan deze NS-recordset om co-hostingdomeinen met meer dan één DNS-provider te ondersteunen. U ook de TTL en metagegevens voor deze recordset wijzigen. U de vooraf ingevulde Azure DNS-naamservers echter niet verwijderen of wijzigen.

Dit geldt alleen voor het NS-record dat op de zonetop is gevestigd. Andere NS-recordsets in uw zone (zoals gebruikt om onderliggende zones te delegeren) kunnen zonder beperking worden gewijzigd.

In het volgende voorbeeld ziet u hoe u een extra naamserver toevoegt aan het NS-record dat is ingesteld op de top van de zone:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Metagegevens van recordset wijzigen

[Recordsetmetagegevens](dns-zones-records.md#tags-and-metadata) kunnen worden gebruikt om toepassingsspecifieke gegevens aan elke recordset te koppelen als sleutelwaardeparen.

In het volgende voorbeeld ziet u hoe u de metagegevens van een bestaande recordset wijzigen:

```powershell
# Get the record set
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Een recordset verwijderen

Recordsets kunnen worden verwijderd `Remove-AzDnsRecordSet` met behulp van de cmdlet. Als u een recordset verwijdert, worden ook alle records binnen de recordset verwijderd.

> [!NOTE]
> U de SOA- en NS-recordsets niet verwijderen op de zonetop (`-Name '@'`).  Azure DNS heeft deze automatisch gemaakt toen de zone is gemaakt en verwijdert ze automatisch wanneer de zone wordt verwijderd.

In het volgende voorbeeld ziet u hoe u een recordset verwijdert. In dit voorbeeld worden de naam van de recordset, het recordsettype, de zonenaam en de resourcegroep expliciet opgegeven.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Als alternatief kan de recordset worden opgegeven op naam en type en de zone die is opgegeven met een object:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Als derde optie kan de recordset zelf worden opgegeven met een recordsetobject:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

Wanneer u de recordset opgeeft die moet worden verwijderd met behulp van een recordsetobject, worden [Etag-controles](dns-zones-records.md#etags) gebruikt om ervoor te zorgen dat gelijktijdige wijzigingen niet worden verwijderd. U de `-Overwrite` optionele schakelaar gebruiken om deze controles te onderdrukken.

Het recordsetobject kan ook worden doorgegeven in plaats van als parameter:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Bevestigingsprompts

De cmdlets `New-AzDnsRecordSet`, `Set-AzDnsRecordSet` en `Remove-AzDnsRecordSet` ondersteunen bevestigingspromts.

Elke cmdlet vraagt om `$ConfirmPreference` bevestiging als de voorkeurvariabele `Medium` PowerShell een waarde van of lager heeft. Aangezien de standaardwaarde voor `$ConfirmPreference` is, `High`worden deze prompts niet gegeven bij het gebruik van de standaard PowerShell-instellingen.

U kunt de huidige instelling van `$ConfirmPreference` overschrijven met behulp van de parameter `-Confirm`. Als u `-Confirm` of `-Confirm:$True` opgeeft, vraagt de cmdlet u om bevestiging voordat deze wordt uitgevoerd. Als u `-Confirm:$False` opgeeft, wordt u niet om bevestiging gevraagd. 

Zie [over voorkeursvariabelen](/powershell/module/microsoft.powershell.core/about/about_preference_variables) voor meer informatie over `-Confirm` en `$ConfirmPreference`.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [zones en records in Azure DNS](dns-zones-records.md).
<br>
Meer informatie over het [beveiligen van uw zones en records](dns-protect-zones-recordsets.md) bij het gebruik van Azure DNS.
<br>
Bekijk de [azure DNS PowerShell-referentiedocumentatie](/powershell/module/az.dns).
