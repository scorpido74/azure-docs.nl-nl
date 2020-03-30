---
title: Een mapstructuur toewijzen aan een Azure File Sync-topologie
description: Een bestaande bestands- en mapstructuur toewijzen aan Azure-bestandsshares voor gebruik met Azure File Sync. Een gemeenschappelijk tekstblok, gedeeld tussen migratiedocumenten.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 25c333aae49bff8d0596d4f5403c18576bf198b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124738"
---
In deze stap evalueert u hoeveel Azure-bestandsshares u nodig hebt. Eén Windows Server (of cluster) kan maximaal 30 Azure-bestandsshares synchroniseren.

Mogelijk hebt u meer mappen op uw volumes die u momenteel lokaal deelt als SMB-aandelen deelt met uw gebruikers en apps. De eenvoudigste zou zijn om voor te stellen voor een on-premises delen om 1:1 in kaart te brengen naar een Azure-bestandsshare. Als u een klein genoeg getal hebt, onder de 30 voor één Windows-server, wordt een 1:1-toewijzing aanbevolen.

Als u meer aandelen dan 30 hebt, is het vaak niet nodig om een on-premises share 1:1 toe te voegen aan een Azure-bestandsshare.
Overweeg de volgende opties:

#### <a name="share-grouping"></a>Groepering delen

Als uw HR-afdeling bijvoorbeeld in totaal 15 aandelen heeft, u overwegen om alle HR-gegevens op te slaan in één Azure-bestandsshare. Het opslaan van meerdere on-premises shares in één Azure-bestandsshare belet u niet om de gebruikelijke 15 SMB-shares te maken op uw lokale Windows Server. Het betekent alleen dat u de hoofdmappen van deze 15 shares organiseert als submappen onder een gemeenschappelijke map. Vervolgens synchroniseert u deze algemene map met een Azure-bestandsshare. Op die manier is slechts één Azure-bestandsaandeel in de cloud nodig voor deze groep on-premises aandelen.

#### <a name="volume-sync"></a>Volumesynchronisatie

Azure File Sync ondersteunt het synchroniseren van de hoofdmap van een volume naar een Azure-bestandsshare.
Als u de hoofdmap synchroniseert, komen alle submappen en bestanden in dezelfde Azure-bestandsshare terecht.

Het synchroniseren van de wortel van het volume is niet altijd het beste antwoord. Er zijn voordelen in het synchroniseren van meerdere locaties, waardoor het aantal items lager per synchronisatiebereik blijft. Het instellen van Azure File Sync met een lager aantal items is niet alleen gunstig voor bestandssynchronisatie. Een lager aantal items komt ook andere scenario's ten goede, zoals:

* cloud-side restore from an Azure file share snapshot taken as a backup cloud-side restore from an Azure file share snapshot taken as a backup cloud-side restore from an Azure file share snapshot taken as a backup cloud-
* noodherstel van een on-premises server kan aanzienlijk versnellen
* wijzigingen die rechtstreeks in een Azure-bestandsshare (buiten synchronisatie) zijn aangebracht, kunnen sneller worden gedetecteerd en gesynchroniseerd

#### <a name="a-structured-approach-to-a-deployment-map"></a>Een gestructureerde benadering van een implementatiekaart

Voordat u cloudopslag in een volgende stap implementeert, is het belangrijk om een kaart te maken tussen on-premises mappen en Azure-bestandsshares. Deze toewijzing geeft vervolgens aan hoeveel en welke Azure File Sync-groepresources u indient. Een synchronisatiegroep koppelt de Azure-bestandsshare en de map op uw server aan elkaar en maakt een synchronisatieverbinding.

Als u wilt beslissen hoeveel Azure-bestandsshares u nodig hebt, controleert u de volgende limieten en aanbevolen procedures. Als u dit doet, u uw kaart optimaliseren:

* Een server waarop de Azure File Sync-agent is geïnstalleerd, kan worden gesynchroniseerd met maximaal 30 Azure-bestandsshares.
* Een Azure-bestandsshare wordt geïmplementeerd in een opslagaccount. Dat maakt het opslagaccount een schaaldoel voor prestatienummers zoals IOPS en doorvoer. Twee standaard (niet premium) Azure-bestandsshares kunnen in theorie de maximale prestaties verzadigen die een opslagaccount kan leveren. Als u alleen Azure File Sync wilt koppelen aan deze bestandsshares, ontstaat er geen probleem om meerdere Azure-bestandsshares in hetzelfde opslagaccount te groeperen. Bekijk de prestatiedoelen voor Azure-bestandsshare voor meer inzicht in de relevante statistieken die u moet overwegen. Als u van plan bent een app naar Azure op te tillen waarmee het Azure-bestandsshare native wordt gebruikt, hebt u mogelijk meer prestaties nodig van uw Azure-bestandsshare. Als dit een mogelijkheid is, zelfs in de toekomst, is het toewijzen van een Azure-bestandsshare aan een eigen opslagaccount het beste.
* Er is een limiet van 250 opslagaccounts per abonnement in één Azure-regio.

> [!TIP]
> Met deze informatie in het achterhoofd wordt het vaak noodzakelijk om meerdere mappen op het hoogste niveau op uw volumes te groeperen in een gemeenschappelijke, nieuwe hoofdmap. Vervolgens synchroniseert u deze nieuwe hoofdmap en alle mappen die u erin hebt gegroepeerd, met één Azure-bestandsshare.                                                    

Met deze techniek u binnen de 30 Azure-synchronisatielimiet voor bestandsdelen per server blijven.
Deze groepering onder een gemeenschappelijke hoofdwortel heeft geen invloed op de toegang tot uw gegevens. Uw ACL's blijven zoals het is, u hoeft alleen maar delen paden (zoals SMB of NFS aandelen) die u zou kunnen hebben op de server mappen die u nu veranderd in een gemeenschappelijke root aan te passen. Verder verandert er niets.

Een ander belangrijk aspect van Azure File Sync en een evenwichtige prestaties en ervaring is inzicht in de schaalfactoren voor azure file sync prestaties. Uiteraard, wanneer bestanden worden gesynchroniseerd via het internet, grotere bestanden meer tijd en bandbreedte te synchroniseren.

> [!IMPORTANT]
> De belangrijkste schaalvector voor Azure File Sync is het aantal items (bestanden en mappen) dat moet worden gesynchroniseerd.

Azure File Sync ondersteunt het synchroniseren van maximaal 100.000 items naar één Azure-bestandsshare. Deze limiet kan worden overschreden en geeft alleen weer wat het Azure File Sync-team regelmatig test.

Het is een aanbevolen gewoonte om het aantal items per synchronisatiebereik laag te houden. Dat aspect is een belangrijke factor die moet worden overwogen bij het toewijzen van mappen naar Azure-bestandsshares.

Zelfs als in uw situatie een set mappen logisch kan synchroniseren met dezelfde Azure-bestandsshare (met behulp van de nieuwe, gemeenschappelijke hoofdmapbenadering van bovenaf), is het misschien nog steeds beter om mappen zodanig te hergroeperen dat ze worden gesynchroniseerd met twee in plaats van één Azure-bestandsshare. Deze aanpak kan worden gebruikt om het aantal bestanden en mappen per bestandsaandeel in evenwicht te houden op de server.

#### <a name="create-a-mapping-table"></a>Een toewijzingstabel maken

:::row:::
    :::column:::
        [![](media/storage-files-migration-namespace-mapping/namespace-mapping.png "An example of a mapping table. Download the file below to experience and use the content of this image.")](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Gebruik een combinatie van de vorige concepten om te bepalen hoeveel Azure-bestandsshares u nodig hebt en in welke delen van uw bestaande gegevens worden beland in welke Azure-bestandsshare.
        
        Maak een tabel die uw gedachten registreert, zodat u er in de volgende stap naar verwijzen. Georganiseerd blijven is belangrijk omdat het gemakkelijk kan zijn om details van uw toewijzingsplan te verliezen bij het inrichten van veel Azure-resources tegelijk. Als u wilt helpen bij het maken van een volledige toewijzing, u een Microsoft Excel-bestand als sjabloon downloaden.

[//]: # (HTML wordt weergegeven als de enige manier om een geneste tabel met twee kolommen toe te voegen met werkende afbeeldingsparsing en tekst/hyperlink op dezelfde regel.)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Microsoft Excel file icon that helps to set the context for the type of file download for the link next to it.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">Download een sjabloon voor naamruimtetoewijzing.</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::
