---
title: DNS-recordsets en -records beheren met Azure DNS
description: Azure DNS biedt de mogelijkheid om DNS-recordsets en -records te beheren wanneer u uw domein host.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/6/2018
ms.author: rohink
ms.openlocfilehash: 4012b32eb2684126b8dc64b9e86bf35a016e9fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936847"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>DNS-records en recordsets beheren met behulp van de Azure-portal

In dit artikel ziet u hoe u recordsets en records voor uw DNS-zone beheert met behulp van de Azure-portal.

Het is belangrijk om het verschil tussen DNS-recordsets en individuele DNS-records te begrijpen. Een recordset is een verzameling records in een zone met dezelfde naam en hetzelfde type. Zie [DNS-recordsets en -records maken met behulp van de Azure-portal](dns-getstarted-create-recordset-portal.md)voor meer informatie.

## <a name="create-a-new-record-set-and-record"></a>Een nieuwe recordset en record maken

Zie [DNS-records maken met de Azure-portal](dns-getstarted-create-recordset-portal.md)als u een recordset wilt maken in de Azure-portal.

## <a name="view-a-record-set"></a>Een recordset weergeven

1. Ga in de Azure-portal naar het **DNS-zoneblad.**
2. Zoek naar de recordset en selecteer deze. Hiermee worden de recordseteigenschappen geopend.

    ![Zoeken naar een recordset](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Een nieuwe record toevoegen aan een recordset

U maximaal 20 records toevoegen aan elke recordset. Een recordset mag niet twee identieke records bevatten. Lege recordsets (met nul records) kunnen worden gemaakt, maar worden niet weergegeven op de Azure DNS-naamservers. Recordsets van type CNAME kunnen hoogstens één record bevatten.

1. Klik in het eigenschappenblad **recordset** voor uw DNS-zone op de recordset waaraan u een record wilt toevoegen.

    ![Een recordset selecteren](./media/dns-operations-recordsets-portal/selectset500.png)

2. Geef de recordseteigenschappen op door de velden in te vullen.

    ![Een record toevoegen](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Klik boven aan het blad op **Opslaan** om uw instellingen op te slaan. Sluit dan het mes.
4. In de hoek ziet u dat de record wordt opgeslagen.

    ![Recordset opslaan](./media/dns-operations-recordsets-portal/saving150.png)

Nadat de record is opgeslagen, worden de waarden op het **DNS-zoneblad** weergegeven in de nieuwe record.

## <a name="update-a-record"></a>Een record bijwerken

Wanneer u een record bijwerkt in een bestaande recordset, zijn de velden die u bijwerken afhankelijk van het type record waarmee u werkt.

1. Zoek op het **recordsetblad eigenschappenblad** voor uw recordset naar de record.
2. Wijzig de record. Wanneer u een record wijzigt, u de beschikbare instellingen voor de record wijzigen. In het volgende voorbeeld wordt het **veld IP-adres** geselecteerd en wordt het IP-adres momenteel gewijzigd.

    ![Een record wijzigen](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Klik boven aan het blad op **Opslaan** om uw instellingen op te slaan. In de rechterbovenhoek ziet u de melding dat de record is opgeslagen.

    ![Opgeslagen recordset](./media/dns-operations-recordsets-portal/saved150.png)

Nadat de record is opgeslagen, worden de waarden voor de record die op het **DNS-zoneblad** is ingesteld, de bijgewerkte record weergegeven.

## <a name="remove-a-record-from-a-record-set"></a>Een record verwijderen uit een recordset

U de Azure-portal gebruiken om records uit een recordset te verwijderen. Houd er rekening mee dat het verwijderen van de laatste record uit een recordset de recordset niet verwijdert.

1. Zoek op het **recordsetblad eigenschappenblad** voor uw recordset naar de record.
2. Klik op de record die u wilt verwijderen. Selecteer vervolgens **Verwijderen**.

    ![Een record verwijderen](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Klik boven aan het blad op **Opslaan** om uw instellingen op te slaan.
4. Nadat de record is verwijderd, weerspiegelen de waarden voor de record op het **DNS-zoneblad** de verwijdering.

## <a name="delete-a-record-set"></a><a name="delete"></a>Een recordset verwijderen

1. Klik op het blad **Eigenschappen van recordset** voor uw recordset op **Verwijderen**.

    ![Een recordset verwijderen](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. Er verschijnt een bericht met de vraag of u de recordset wilt verwijderen.
3. Controleer of de naam overeenkomt met de recordset die u wilt verwijderen en klik op **Ja**.
4. Controleer op het **DNS-zoneblad** of de recordset niet meer zichtbaar is.

## <a name="work-with-ns-and-soa-records"></a>Werken met NS- en SOA-records

NS- en SOA-records die automatisch worden gemaakt, worden anders beheerd dan andere recordtypen.

### <a name="modify-soa-records"></a>SOA-records wijzigen

U geen records toevoegen of verwijderen uit de automatisch gemaakte SOA-record die is ingesteld op de zonetop (naam =\@). U echter een van de parameters in de SOA-record (behalve 'Host') en de recordset TTL wijzigen.

### <a name="modify-ns-records-at-the-zone-apex"></a>NS-records wijzigen op de zonetop

Het NS-record dat is ingesteld op de zone-top wordt automatisch gemaakt met elke DNS-zone. Het bevat de namen van de Azure DNS-naamservers die aan de zone zijn toegewezen.

U extra naamservers toevoegen aan deze NS-recordset om co-hostingdomeinen met meer dan één DNS-provider te ondersteunen. U ook de TTL en metagegevens voor deze recordset wijzigen. U de vooraf ingevulde Azure DNS-naamservers echter niet verwijderen of wijzigen.

Dit geldt alleen voor het NS-record dat op de zonetop is gevestigd. Andere NS-recordsets in uw zone (zoals gebruikt om onderliggende zones te delegeren) kunnen zonder beperking worden gewijzigd.

### <a name="delete-soa-or-ns-record-sets"></a>SOA- of NS-recordsets verwijderen

U de SOA- en NS-recordsets niet\@verwijderen op de zonetop (naam = ) die automatisch worden gemaakt wanneer de zone wordt gemaakt. Ze worden automatisch verwijderd wanneer u de zone verwijdert.

## <a name="next-steps"></a>Volgende stappen

* Zie het [Azure DNS-overzicht](dns-overview.md)voor meer informatie over Azure DNS.
* Zie [DNS-zones en recordsets maken met de .NET SDK](dns-sdk.md)voor meer informatie over het automatiseren van DNS.
* Zie [Overzicht van reverse DNS en ondersteuning in Azure](dns-reverse-dns-overview.md)voor meer informatie over omgekeerde DNS-records.
* Zie overzicht van [Azure DNS-aliasrecords](dns-alias.md)voor meer informatie over Azure DNS-aliasrecords.
