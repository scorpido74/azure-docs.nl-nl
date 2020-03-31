---
title: Een subdomein delegeren - Azure PowerShell - Azure DNS
description: Ga met dit leerpad aan de slag met het delegeren van een Azure DNS-subdomein met Azure PowerShell.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: 7e019afaae98422b8d5a3c8fa7a5f79e26c6a149
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937713"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Een Azure DNS-subdomein delegeren met Azure PowerShell

U Azure PowerShell gebruiken om een DNS-subdomein te delegeren. Als u bijvoorbeeld eigenaar bent van het contoso.com-domein, u een subdomein met de naam *engineering* delegeren aan een andere, afzonderlijke zone die u afzonderlijk van de contoso.com-zone beheren.

U desgevraagd een subdomein delegeren via de [Azure Portal.](delegate-subdomain.md)

> [!NOTE]
> Contoso.com wordt gebruikt als een voorbeeld in dit artikel. Vervang uw eigen domeinnaam door contoso.com.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Als u een Azure DNS-subdomein wilt delegeren, moet u eerst uw openbare domein delegeren aan Azure DNS. Zie [Een domein delegeren aan Azure DNS](./dns-delegate-domain-azure-dns.md) voor instructies over het configureren van uw naamservers voor overdracht. Zodra uw domein is gedelegeerd aan uw Azure DNS-zone, u uw subdomein delegeren.

## <a name="create-a-zone-for-your-subdomain"></a>Een zone voor uw subdomein maken

Maak eerst de zone voor het **subdomein engineering.**

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>Let op de naamservers

Noteer vervolgens de vier naamservers voor het subdomein engineering.

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>Een testrecord maken

Maak een **A-record** in de engineeringzone om te gebruiken voor het testen.

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>Een NS-record maken

Maak vervolgens een NS-record (name server) voor de **engineeringzone** in de contoso.com zone.

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>Test de delegatie

Gebruik nslookup om de delegatie te testen.

1. Open een PowerShell-venster.
2. Typ bij opdrachtprompt`nslookup www.engineering.contoso.com.`
3. U ontvangt een niet-gezaghebbend antwoord met het adres **10.10.10.10**.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [configureren van reverse DNS voor services die worden gehost in Azure.](dns-reverse-dns-for-azure-services.md)