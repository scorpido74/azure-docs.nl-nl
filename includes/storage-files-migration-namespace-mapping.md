---
title: Een mapstructuur toewijzen aan een Azure File Sync-topologie
description: Wijs een bestaande bestands-en mapstructuur toe aan Azure-bestands shares voor gebruik met Azure File Sync. Een gemeen schappelijk tekst blok, gedeeld via migratie documenten.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 948090d0ee956ca1798d7b0f46bb33276c4d6354
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143594"
---
In deze stap evalueert u hoeveel Azure-bestands shares u nodig hebt. Eén Windows Server-exemplaar (of-cluster) kan Maxi maal 30 Azure-bestands shares synchroniseren.

Mogelijk hebt u meer mappen op uw volumes die momenteel lokaal worden gedeeld als SMB-shares voor uw gebruikers en apps. De eenvoudigste manier is het maken van een on-premises share die 1:1 toewijst aan een Azure-bestands share. Als u een klein nummer hebt, minder dan 30 voor één Windows Server-exemplaar, raden we u aan een 1:1-toewijzing aan te bieden.

Als u meer shares dan 30 hebt, is het vaak niet nodig om een on-premises share 1:1 toe te wijzen aan een Azure-bestands share. Houd rekening met de volgende opties.

#### <a name="share-grouping"></a>Groepering delen

Als uw HR-afdeling (bijvoorbeeld) een totaal van 15 shares heeft, kunt u overwegen alle HR-gegevens op te slaan in één Azure-bestands share. Als u meerdere on-premises shares in een Azure-bestands share opslaat, is het niet mogelijk om de gebruikelijke 15 SMB-shares op uw lokale Windows Server-exemplaar te maken. Dit betekent alleen dat u de hoofd mappen van deze 15 shares als submappen in een gemeen schappelijke map ordent. Vervolgens synchroniseert u deze algemene map naar een Azure-bestands share. Op die manier is slechts één Azure-bestands share in de Cloud nodig voor deze groep lokale shares.

#### <a name="volume-sync"></a>Volume synchronisatie

Azure File Sync ondersteunt het synchroniseren van de hoofdmap van een volume naar een Azure-bestands share. Als u de hoofdmap synchroniseert, worden alle submappen en bestanden naar dezelfde Azure-bestands share.

Het synchroniseren van de hoofdmap van het volume is niet altijd het beste antwoord. Er zijn voor delen in het synchroniseren van meerdere locaties. Zo kunt u bijvoorbeeld het aantal items verlagen per synchronisatie bereik. Het instellen van Azure File Sync met een lager aantal items is niet alleen nuttig voor bestands synchronisatie. Een lager aantal items is ook voor delen van scenario's als deze:

* Herstel aan de Cloud zijde vanuit een Azure file share-moment opname kan worden gemaakt als back-up.
* Herstel na nood gevallen van een on-premises server kan aanzienlijk versnellen.
* Wijzigingen die rechtstreeks in een Azure-bestands share (externe synchronisatie) zijn aangebracht, kunnen sneller worden gedetecteerd en gesynchroniseerd.

#### <a name="a-structured-approach-to-a-deployment-map"></a>Een gestructureerde benadering van een implementatie toewijzing

Voordat u in een latere stap Cloud opslag implementeert, is het belang rijk om een kaart te maken tussen on-premises mappen en Azure-bestands shares. Met deze toewijzing wordt vervolgens op de hoogte gesteld van het aantal en welke Azure File Sync de *synchronisatie van groeps* bronnen die u wilt inrichten. Een synchronisatie groep is gekoppeld aan de Azure-bestands share en de map op uw server en brengt een synchronisatie verbinding tot stand.

Bekijk de volgende limieten en aanbevolen procedures om de beslissing te nemen over het aantal Azure-bestands shares dat u nodig hebt. Dit helpt u bij het optimaliseren van uw kaart.

* Een server waarop de Azure File Sync-agent is geïnstalleerd, kan worden gesynchroniseerd met Maxi maal 30 Azure-bestands shares.
* Een Azure-bestands share wordt geïmplementeerd in een opslag account. Hiermee wordt het opslag account een schaal doel voor prestatie cijfers zoals IOPS en door voer. 

  Twee standaard (geen Premium) Azure-bestands shares kunnen de maximale prestaties die een opslag account kan leveren, in theorie verzadigen. Als u van plan bent om Azure File Sync toe te voegen aan deze bestands shares, kunt u met verschillende Azure-bestands shares in hetzelfde opslag account geen probleem maken. Bekijk de prestatie doelen van de Azure-bestands share voor dieper inzicht in de relevante metrische gegevens om rekening mee te houden. 

  Als u van plan bent om een app te heffen op Azure die de Azure-bestands share systeem eigen gebruikt, hebt u mogelijk meer prestaties nodig van uw Azure-bestands share. Als dit mogelijk is, kunt u zelfs in de toekomst een Azure-bestands share toewijzen aan een eigen opslag account.
* Er is een limiet van 250 opslag accounts per abonnement in één Azure-regio.

> [!TIP]
> Met deze informatie in het algemeen is het vaak nodig om meerdere mappen op het hoogste niveau op uw volumes te groeperen in een gemeen schappelijke, nieuwe hoofdmap. Vervolgens synchroniseert u deze nieuwe hoofdmap en alle mappen die u erin hebt gegroepeerd, naar één Azure-bestands share. Met deze techniek kunt u binnen de limiet van 30 Azure-bestands shares synchroniseren per server.
>
> Deze groepering onder een gemeen schappelijke hoofdmap heeft geen invloed op de toegang tot uw gegevens. Uw Acl's blijven ongewijzigd. U hoeft alleen share paden (zoals SMB-of NFS-shares) aan te passen die u mogelijk hebt op de Server mappen die u nu hebt gewijzigd in een algemene hoofdmap. Niets else wijzigt.

Een ander belang rijk aspect van Azure File Sync en een evenwichtige prestaties en ervaring is de schaal factoren voor Azure File Sync prestaties te weten. Wanneer bestanden worden gesynchroniseerd via internet, nemen grotere bestanden uiteraard meer tijd en band breedte in beslag om te synchroniseren.

> [!IMPORTANT]
> De belangrijkste schaal vector voor Azure File Sync is het aantal items (bestanden en mappen) dat moet worden gesynchroniseerd.

Azure File Sync ondersteunt het synchroniseren van Maxi maal 100.000 items naar één Azure-bestands share. Deze limiet kan worden overschreden en geeft alleen aan wat het Azure File Sync team regel matig test.

Het is een best practice om te voor komen dat het aantal items per synchronisatie bereik laag is. Dit is een belang rijke factor voor het nemen van uw toewijzing van mappen aan Azure-bestands shares.

In uw situatie is het mogelijk dat een set mappen logisch kan worden gesynchroniseerd met dezelfde Azure-bestands share (met behulp van de nieuwe benadering van de algemene hoofdmap eerder beschreven). Het kan echter nog steeds beter zijn om mappen zodanig te groeperen dat ze worden gesynchroniseerd met twee in plaats van een Azure-bestands share. U kunt deze methode gebruiken om het aantal bestanden en mappen per bestands share op de server te houden.

#### <a name="create-a-mapping-table"></a>Een toewijzings tabel maken

:::row:::
    :::column:::
        [![](media/storage-files-migration-namespace-mapping/namespace-mapping.png "An example of a mapping table. Download the file below to experience and use the content of this image.")](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Gebruik een combi natie van de voor gaande concepten om te bepalen hoeveel Azure-bestands shares u nodig hebt, en welke onderdelen van uw bestaande gegevens in de Azure-bestands share moeten eindigen.
        
        Maak een tabel waarin uw ideeën worden vastgelegd, zodat u deze in de volgende stap kunt raadplegen. Georganiseerd is belang rijk, omdat het eenvoudig kan zijn om gegevens van uw toewijzings plan te verliezen wanneer u een groot aantal Azure-resources tegelijk inricht. Om u te helpen bij het maken van een volledige toewijzing, kunt u een micro soft Excel-bestand downloaden als een sjabloon.

[//]: # (HTML wordt weer gegeven als de enige manier om een geneste tabel met twee kolommen toe te voegen met het parseren van de werk installatie kopie en tekst/Hyper link op dezelfde regel.)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Microsoft Excel file icon that helps to set the context for the type of file download for the link next to it.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">Down load een sjabloon voor het toewijzen van naam ruimten.</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::
