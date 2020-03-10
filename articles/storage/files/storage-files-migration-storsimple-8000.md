---
title: StorSimple 8000-serie migreren naar Azure File Sync
description: Meer informatie over het migreren van een StorSimple 8100-of 8600-apparaat naar Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d937852ace8d9bf39495f1fdd92e6edfc4452a0a
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943593"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 en 8600 migratie naar Azure File Sync

De StorSimple 8000-serie wordt vertegenwoordigd door de 8100 of de 8600 fysieke, on-premises apparaten en hun Cloud service onderdelen. Het is mogelijk om de gegevens van een van deze apparaten te migreren naar een Azure File Sync omgeving. Azure File Sync is de standaard-en strategische Azure-service voor de lange termijn waarmee StorSimple-apparaten kunnen worden gemigreerd.

De StorSimple 8000-serie bereikt het [einde van de levens duur](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) in december 2022. Het is belang rijk dat u zo snel mogelijk begint met het plannen van de migratie. Dit artikel bevat de benodigde achtergrond kennis en migraties stappen voor een geslaagde migratie naar Azure File Sync. 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> Micro soft streeft ernaar klanten te helpen bij hun migratie. E-mail AzureFilesMigration@microsoft. com voor een aangepast migratie plan en voor hulp tijdens de migratie.

Azure File Sync is een micro soft-Cloud service, gebaseerd op twee hoofd onderdelen:

* Bestands synchronisatie en Cloud lagen.
* Bestands shares als systeem eigen opslag in azure, waartoe toegang kan worden verkregen via meerdere protocollen zoals SMB en de REST van het bestand. Een Azure-bestands share is vergelijkbaar met een bestands share op een Windows-Server, die u als een netwerk station zelf kunt koppelen. Het ondersteunt belang rijke aspecten van bestands kwaliteit zoals kenmerken, machtigingen en tijds tempels. Met Azure-bestands shares is het niet meer nodig om een toepassing of service te interpreteren van de bestanden en mappen die zijn opgeslagen in de Cloud. U kunt deze systeem eigen toegang tot de vertrouwde protocollen en clients, zoals Windows Verkenner. Hierdoor kunnen Azure-bestands shares de ideale en meest flexibele benadering zijn voor het opslaan van bestands Server gegevens voor algemene doel einden, evenals voor bepaalde toepassings gegevens in de Cloud.

Dit artikel is gericht op de migratie stappen. Als u vóór de migratie meer wilt weten over Azure File Sync, raden we u aan om de volgende artikelen te lezen:

* [Azure File Sync-overzicht](https://aka.ms/AFS "Overzicht")
* [Azure File Sync-implementatie handleiding](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Migratiedoelen

Het doel is om de integriteit van de productie gegevens te waarborgen en de beschik baarheid te garanderen. Ten laatste moet de downtime tot een minimum worden beperkt, zodat deze kan worden aangepast aan of slechts een beetje meer regel matig onderhouds Vensters kan hebben.

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>StorSimple 8000-serie-migratie pad naar Azure File Sync

Er is een lokale Windows-Server vereist om een Azure File Sync-agent uit te voeren. De Windows-Server kan mini maal een 2012R2-server zijn, maar is in het ideale geval een Windows Server 2019.

Er zijn talloze alternatieve migratie paden en er zou te lang van een artikel kunnen worden gemaakt om ze allemaal te documenteren en te illustreren waarom ze Risico's of nadelen hebben ten opzichte van de route die we aanbevelen als best practice in dit artikel.

![Overzicht migratie fasen StorSimple 8000-serie](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "StorSimple 8000-serie migratie route overzicht van de fasen verderop in dit artikel.")

In de vorige afbeelding ziet u de fasen die overeenkomen met de secties in dit artikel.
We gebruiken een migratie aan de Cloud zijde om te voor komen dat bestanden op uw lokale StorSimple-apparaat onnodig worden teruggehaald. Deze aanpak voor komt het beïnvloeden van het lokale cache gedrag of het gebruik van netwerk bandbreedte, een van de mogelijkheden die van invloed kunnen zijn op uw productie workloads.
Een migratie aan de Cloud zijde wordt uitgevoerd op een moment opname (een volume kloon) van uw gegevens. Uw productie gegevens worden dus geïsoleerd van dit proces-totdat de cut aan het einde van de migratie wordt uitgevoerd. Door te werken met wat in feite een back-up is, zorgt u ervoor dat de migratie veilig en gemakkelijk kan worden herhaald, mocht u problemen ondervinden.

## <a name="considerations-around-existing-storsimple-backups"></a>Overwegingen rond bestaande StorSimple-back-ups

Met StorSimple kunt u back-ups maken in de vorm van volume klonen. In dit artikel wordt gebruikgemaakt van een nieuwe volume kloon om uw Live bestanden te migreren.
Als u naast uw Live gegevens back-ups wilt migreren, zijn alle instructies in dit artikel nog steeds van toepassing. Het enige verschil is dat u in plaats van met een nieuwe volume kloon begint met de oudste back-upvolume-kloon die u moet migreren.

De volg orde is als volgt:

* Bepaal de minimale set volume klonen die u moet migreren. We raden u aan deze lijst zo veel mogelijk te bewaren, omdat de meer back-ups die u het algehele migratie proces, worden gemigreerd.
* Wanneer u het migratie proces doorloopt, begint u met de oudste volume kloon die u wilt migreren en bij elke volgende migratie, gebruikt u de volgende oudste.
* Wanneer de migratie van elk volume is voltooid, moet u een moment opname van de Azure-bestands share maken. Met [moment opnamen van Azure-bestands shares](storage-snapshots-files.md) kunt u Point-in-time back-ups van de bestanden en mappen structuur voor uw Azure-bestands shares opslaan. U hebt deze moment opnamen nodig nadat de migratie is voltooid, om ervoor te zorgen dat u versies van elk volume klonen hebt behouden terwijl u de migratie uitvoert.
* Zorg ervoor dat u moment opnamen van Azure-bestands shares maakt voor alle Azure-bestands shares die worden uitgevoerd door hetzelfde StorSimple-volume. Volume-klonen bevinden zich op het volume niveau, moment opnamen van Azure-bestands shares bevinden zich op het share niveau. U moet een moment opname van een share (op elke Azure-bestands share) maken nadat de migratie van een volume kloon is voltooid.
* Herhaal het migratie proces voor een volume kloon en maak moment opnamen van shares na elk volume kloon totdat u een moment opname van de Live-gegevens hebt gemaakt. Het proces voor het migreren van een volume kloon wordt beschreven in de onderstaande fasen. 

Als u geen back-ups meer hoeft te verplaatsen en u een nieuwe keten van back-ups op de Azure-bestands share wilt starten nadat de migratie van de gegevens is voltooid, is het nuttig om de complexiteit van de migratie en de tijd van de migratie te verminderen. U kunt de beslissing nemen of u back-ups wilt verplaatsen en hoeveel u voor elk volume (niet elke share) hebt in StorSimple.

## <a name="phase-1-get-ready"></a>Fase 1: bereid u voor op

:::row:::
    :::column:::
        ![Een afbeelding die een deel van de eerder overzichts afbeelding illustreert en die de aandacht van deze subsectie van het artikel kan richten.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        De basis voor de migratie is een volume kloon en een virtueel Cloud apparaat met de naam StorSimple 8020.
Deze fase is gericht op de implementatie van deze resources in Azure.
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>Een virtueel StorSimple 8020-apparaat implementeren

Het implementeren van een Cloud apparaat is een proces waarvoor beveiliging, netwerken en enkele andere overwegingen vereist zijn.

> [!IMPORTANT]
> De volgende hand leiding bevat enkele overbodige secties. Lees en volg het artikel vanaf het begin tot en met stap 3. Ga vervolgens terug naar dit artikel. U hoeft ' stap 3 ' of iets anders dan in die hand leiding op dit moment niet uit te voeren.

[Implementatie van een virtuele machine met StorSimple 8020](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>Bepalen welk volume-kloon moet worden gebruikt

Wanneer u klaar bent om de migratie te starten, is de eerste stap het maken van een nieuwe volume kloon, net zoals u zou doen voor back-up, waarmee de huidige status van uw StorSimple-Cloud opslag wordt vastgelegd. Maak een kloon voor elk StorSimple-volume dat u hebt.
Als u back-ups nodig hebt, is de eerste kloon van het volume dat u gebruikt, geen zojuist gemaakte kloon, maar de oudste volume kloon (oudste back-up) die u moet migreren.
Raadpleeg de sectie [' overwegingen bij bestaande StorSimple-back-ups '](#considerations-around-existing-storsimple-backups) voor gedetailleerde richt lijnen.

> [!IMPORTANT]
> De volgende hand leiding bevat enkele overbodige secties. Lees en volg alleen de stappen die worden beschreven in de sectie gekoppeld. Ga vervolgens terug naar dit artikel. U hoeft niet de sectie ' volgende stappen ' uit te voeren.

[Een kloon van een volume maken](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>De volume kloon gebruiken

De laatste fase van fase 1 is het maken van de volume kloon die u hebt gekozen, beschikbaar op het virtuele 8020-apparaat in Azure.

> [!IMPORTANT]
> De volgende hand leiding bevat de benodigde stappen, maar ook aan het einde van een instructie voor het format teren van het volume. **volume niet FORMAT teren** Lees en volg de link-to ' sectie 7 ' van de instructie begin tot en met ' 10. Een eenvoudig volume Format teren,...  Stop voordat u deze stap volgt en ga terug naar dit artikel.

[Een volume kloon koppelen op het virtuele 8020-apparaat in azure](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>Samen vatting fase 1

Nu u fase 1 hebt voltooid, hebt u het volgende gedaan:

* Een virtueel StorSimple 8020-apparaat in azure geïmplementeerd.
* Bepaald op welke volume-kloon u de migratie wilt starten.
* Uw volume-kloon (s) (één voor elk Live volume) koppelen aan het virtuele StorSimple-apparaat in azure, met de gegevens die beschikbaar zijn voor verder gebruik.

## <a name="phase-2-cloud-vm"></a>Fase 2: Cloud-VM

:::row:::
    :::column:::
        ![Een afbeelding die een deel van de eerder overzichts afbeelding illustreert en die de aandacht van deze subsectie van het artikel kan richten.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        Nadat de eerste kloon beschikbaar is op het virtuele apparaat StorSimple 8020 in azure, is het nu tijd om een virtuele machine in te richten en de kopie van het volume (of meerdere) beschikbaar te maken voor de VM via iSCSI.
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>Een Azure-VM implementeren

De virtuele Windows Server-machine in Azure is net als de StorSimple 8020, een tijdelijk onderdeel van een infra structuur die alleen nodig is tijdens de migratie.
De configuratie van de virtuele machine die u implementeert, is vooral afhankelijk van het aantal items (bestanden en mappen) dat u wilt synchroniseren. Als u problemen hebt, kunt u het beste een configuratie met hogere prestaties uitvoeren.

Met één Windows-Server kunnen Maxi maal 30 Azure-bestands shares worden gesynchroniseerd.
De specificaties die u kiest, moeten elk aandeel/pad of de hoofdmap van het StorSimple-volume omvatten en de items (bestanden en mappen) tellen.

De totale grootte van de gegevens is minder dan een bottleneck. het is het aantal items dat u nodig hebt om de machine specificaties aan te passen.

* [Meer informatie over het aanpassen van de grootte van een Windows-Server op basis van het aantal items (bestanden en mappen) dat u wilt synchroniseren.](storage-sync-files-planning.md#recommended-system-resources)
* [Meer informatie over het implementeren van een Windows Server-VM.](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> Zorg ervoor dat de virtuele machine is geïmplementeerd in dezelfde Azure-regio als het StorSimple 8020-virtueel apparaat. Als onderdeel van deze migratie moet u ook de regio van uw Cloud gegevens wijzigen in de regio waarin deze is opgeslagen. u kunt dit in een later stadium doen wanneer u Azure-bestands shares inricht.

### <a name="expose-the-storsimple-8020-volumes-to-the-vm"></a>De StorSimple 8020-volumes beschikbaar maken voor de VM

In deze fase verbindt u een of meer StorSimple-volumes van het virtuele 8020-apparaat via iSCSI naar de Windows Server-VM die u hebt ingericht.

> [!IMPORTANT]
> Voor de volgende artikelen vult u alleen het **persoonlijk IP-adres ophalen voor het Cloud apparaat** in en **maakt u verbinding via iSCSI** -secties en gaat u terug naar dit artikel.

1. [Privé IP-adres voor het Cloud apparaat ophalen](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [Verbinding maken via iSCSI](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>Samen vatting fase 2

Nu u fase 2 hebt voltooid, hebt u het volgende gedaan: 

* Een Windows Server-VM ingericht in dezelfde regio als het virtuele StorSimple-apparaat van 8020
* Alle toepasselijke volumes van de 8020 beschikbaar gemaakt voor de virtuele machine van Windows Server via iSCSI.
* U ziet nu inhoud van bestanden en mappen, wanneer u bestanden Verkenner op de Server-VM op de gekoppelde volumes gebruikt.

Ga alleen verder met fase 3 wanneer u deze stappen hebt uitgevoerd voor alle volumes die moeten worden gemigreerd.

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>Fase 3: Azure-bestands shares instellen en voorbereiden op Azure File Sync

:::row:::
    :::column:::
        ![Een afbeelding die een deel van de eerder overzichts afbeelding illustreert en die de aandacht van deze subsectie van het artikel kan richten.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        In deze fase gaat u een aantal Azure-bestands shares vaststellen en inrichten, een on-premises Windows-Server maken als een StorSimple-apparaat en de server configureren voor Azure File Sync. 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>Uw bestaande naam ruimten toewijzen aan Azure-bestands shares

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>Azure-bestands shares implementeren

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> Als u de Azure-regio wilt wijzigen in de huidige regio waarin uw StorSimple-gegevens zich bevinden, dient u de Azure-bestands shares in te richten in de nieuwe regio die u wilt gebruiken. U bepaalt de regio door deze te selecteren wanneer u de opslag accounts inricht die uw Azure-bestands shares bevatten. Zorg ervoor dat ook de Azure File Sync resource die u hieronder inricht, zich in dezelfde nieuwe regio bevindt.

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>De Azure File Sync Cloud resource implementeren

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Als u de Azure-regio wilt wijzigen vanuit de huidige regio waarin uw StorSimple-gegevens zich bevinden, hebt u de opslag accounts voor uw Azure-bestands shares in de nieuwe regio ingericht. Zorg ervoor dat u dezelfde regio hebt geselecteerd wanneer u deze opslag synchronisatie service implementeert.

### <a name="deploy-an-on-premises-windows-server"></a>Een on-premises Windows-Server implementeren

* Maak een Windows Server 2019-met een minimale 2012R2-als een virtuele machine of fysieke server. Een failover-cluster van Windows Server wordt ook ondersteund. Gebruik de server die u hebt voor de StorSimple 8100 of 8600 mogelijk niet opnieuw.
* Het inrichten of toevoegen van direct gekoppelde opslag (DAS in vergelijking met NAS), wat niet wordt ondersteund.

Het is best practice om uw nieuwe Windows-Server evenveel of grotere opslag ruimte te geven dan uw StorSimple 8100-of 8600-apparaat lokaal beschikbaar is voor caching. U gebruikt de Windows-Server op dezelfde manier als waarop u het StorSimple-apparaat hebt gebruikt, als dit dezelfde hoeveelheid opslag ruimte heeft als het apparaat, dan moet de cache-ervaring gelijk zijn, als dat niet zo is.
U kunt op uw Windows-Server ook opslag ruimte toevoegen of verwijderen. Zo kunt u de grootte van uw lokale volume en de hoeveelheid lokale opslag die beschikbaar is voor caching, schalen.

### <a name="prepare-the-windows-server-for-file-sync"></a>De Windows-Server voorbereiden voor bestands synchronisatie

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>Azure File Sync configureren op de Windows-Server

Uw geregistreerde on-premises Windows-Server moet gereed zijn en verbonden zijn met internet voor dit proces.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Zorg ervoor dat u Cloud lagen inschakelt.** Cloud lagen is de AFS-functie waarmee de lokale server minder opslag capaciteit kan hebben dan is opgeslagen in de Cloud, maar die de volledige naam ruimte beschikbaar heeft. Lokaal interessante gegevens worden ook lokaal in de cache opgeslagen voor snelle, lokale toegangs prestaties. Een andere reden om Cloud-Tiering in deze stap in te scha kelen, is dat er op dit moment geen bestands inhoud in deze fase kan worden gesynchroniseerd.

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>Fase 4: de Azure-VM configureren voor synchronisatie

:::row:::
    :::column:::
        ![Een afbeelding die een deel van de eerder overzichts afbeelding illustreert en die de aandacht van deze subsectie van het artikel kan richten.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        Deze fase heeft betrekking op uw Azure-VM met de iSCSI-gekoppelde, eerste volume-kloon (en). Tijdens deze fase krijgt u de virtuele machine die is verbonden via Azure File Sync en begint u een eerste ronde van het verplaatsen van bestanden van uw StorSimple volume-kloon (s).
        
    :::column-end:::
:::row-end:::

U hebt al uw on-premises server geconfigureerd die uw StorSimple 8100-of 8600-apparaat vervangt, voor Azure File Sync. 

Het configureren van de virtuele machine van Azure is een bijna identiek proces met een extra stap. De volgende stappen leiden u door het proces.

> [!IMPORTANT]
> Het is belang rijk dat de virtuele machine **van Azure niet is geconfigureerd voor het inschakelen van Cloud lagen.** U gaat het volume van deze server uitwisselen met nieuwere volume klonen tijdens de migratie. Cloud lagen heeft geen voor deel en overhead voor het CPU-gebruik dat u moet vermijden.

1. [Implementeer de AFS-agent. (Zie de vorige sectie)](#prepare-the-windows-server-for-file-sync)
2. [De VM voorbereiden voor Azure File Sync.](#get-the-vm-ready-for-azure-file-sync)
3. [Synchronisatie configureren](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>De VM gereed voor Azure File Sync ophalen

Azure File Sync wordt gebruikt om de bestanden van de gekoppelde iSCSI-StorSimple-volumes te verplaatsen naar de Azure-doel bestands shares.
Tijdens dit migratie proces moet u verschillende volume klonen koppelen aan uw VM, onder dezelfde stationsletter. Azure File Sync moet worden geconfigureerd om de volgende volume kloon te zien die u hebt gekoppeld als nieuwere versie van de bestanden en mappen en de Azure-bestands shares die zijn verbonden via Azure File Sync, bijwerken. 

> [!IMPORTANT]
> Om dit te laten werken, moet een register sleutel op de server worden ingesteld voordat Azure File Sync is geconfigureerd.

1. Maak een nieuwe map op het systeem station van de virtuele machine. Azure File Sync gegevens moeten worden bewaard in plaats van op de gekoppelde volume klonen. Bijvoorbeeld: `"C:\syncmetadata"`
2. Open regedit en ga naar de volgende register component: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. Maak een nieuwe sleutel van het type teken reeks met de naam: ***MetadataRootPath***
4. Stel het volledige pad naar de map die u hebt gemaakt op het systeem volume in, bijvoorbeeld: `C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>Azure File Sync op de Azure-VM configureren

Deze stap is vergelijkbaar met de vorige sectie, waarin wordt beschreven hoe u AFS op de on-premises server configureert.

Het verschil is dat u Cloud lagen niet op deze server moet inschakelen en dat u ervoor moet zorgen dat de juiste mappen zijn verbonden met de juiste Azure-bestands shares. Als dat niet het geval is, komt de naam van Azure-bestands shares en gegevens inhoud niet overeen en is er geen manier om de naam van de cloud resources of lokale mappen te wijzigen zonder de synchronisatie opnieuw te configureren.

Raadpleeg de [vorige sectie voor informatie over het configureren van Azure file sync op een Windows-Server](#configure-azure-file-sync-on-the-windows-server).

### <a name="step-4-summary"></a>Stap 4-samen vatting

Op dit moment hebt u Azure File Sync op de Azure VM geconfigureerd, hebt u uw StorSimple-volume-kloon (s) via iSCSI gekoppeld.
Gegevens worden nu vanuit de Azure-VM naar de verschillende Azure-bestands shares gestroomt en er wordt een volledig moment volle naam ruimte weer gegeven op uw on-premises Windows-Server.

> [!IMPORTANT]
> Zorg ervoor dat er geen wijzigingen zijn aangebracht of dat gebruikers toegang tot de Windows-Server op dit moment wordt verleend.

De eerste volume kloon gegevens die door de virtuele machine van Azure worden verplaatst naar de Azure-bestands shares kunnen lang, mogelijk weken duren. Het schatten van de tijd die nodig is voor het maken van een schatting is een lastigheid en is afhankelijk van een groot aantal factoren. Met name de snelheid waarmee de virtuele Azure-machine toegang heeft tot bestanden op de StorSimple-volumes en hoe snel Azure File Sync de bestanden en mappen kan verwerken die moeten worden gesynchroniseerd. 

Vanuit de praktijk kunnen we aannemen dat de band breedte, dus de werkelijke gegevens grootte, een onderliggende rol speelt. De tijd dat deze of volgende migratie ronding duurt, is voornamelijk afhankelijk van het aantal items dat per seconde kan worden verwerkt. Bijvoorbeeld 1 TiB met een 100.000 bestanden en mappen zullen waarschijnlijk langzamer verlopen dan 1 TiB met slechts 50.000 bestanden en mappen.

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>Fase 5: meerdere volume klonen sequentieel door lopen

:::row:::
    :::column:::
        ![Een afbeelding die een deel van de eerder overzichts afbeelding illustreert en die de aandacht van deze subsectie van het artikel kan richten.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        Zoals beschreven in de vorige fase kan de initiële synchronisatie lang duren. Uw gebruikers en toepassingen hebben nog steeds toegang tot het on-premises StorSimple 8100-of 8600-apparaat. Dit betekent dat wijzigingen worden samengevoegd en dat elke dag een grotere Delta tussen de Live gegevens en de eerste volume kloon is, u momenteel een migratie, formulier. In deze sectie leert u hoe u de downtime kunt minimaliseren door meerdere volume klonen te gebruiken en te vertellen wanneer de synchronisatie is voltooid.
    :::column-end:::
:::row-end:::

Helaas is het migratie proces niet onmiddellijk. Dit betekent dat de bovengenoemde Delta aan de Live gegevens een onvermijdbaar gevolg is. Het goede nieuws is dat u het proces voor het koppelen van nieuwe volume klonen kunt herhalen. De Delta van elke volume kloon wordt geleidelijk kleiner. Uiteindelijk wordt een synchronisatie voltooid binnen een periode die u acceptabel vindt voor het offline halen van gebruikers en apps om uw on-premises Windows-Server te knippen.

Herhaal de volgende stappen totdat de synchronisatie is voltooid in een snel voldoende duur waarmee u gebruikers en apps offline kunt halen:

1. [Bepalen of de synchronisatie is voltooid voor een bepaalde volume kloon.](#determine-when-sync-is-done)
2. [Maak een nieuwe volume kloon (s) en koppel deze aan het virtuele 8020-apparaat.](#the-next-volume-clones)
3. [Bepaal wanneer de synchronisatie is voltooid.](#determine-when-sync-is-done)
4. [Strategie voor knippen](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>De volgende volume-kloon (s)

We hebben eerder in dit artikel een volume kloon (s) behandeld.
Deze fase heeft twee acties:

1. [Een volume kloon nemen](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [Koppel dat volume kloon (zie hierboven)](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>Bepalen wanneer de synchronisatie is voltooid

Wanneer de synchronisatie is voltooid, kunt u uw tijd afmeten en bepalen of u het proces van het maken van een volume kloon moet herhalen en koppelen, of dat de tijd die nodig is voor de laatste volume kloon voldoende klein is.

Om te bepalen of de synchronisatie is voltooid:

1. Open de Logboeken en navigeer naar **toepassingen en services**
2. Navigeren en **Microsoft\FileSync\Agent\Telemetry** openen
3. Zoek naar de meest recente **gebeurtenis 9102**, die overeenkomt met een voltooide synchronisatie sessie
4. Selecteer **Details** en controleer of de waarde voor **SyncDirection** is **geüpload**
5. Controleer het **HResult** en controleer of het **0**wordt weer gegeven. Dit betekent dat de synchronisatie sessie is geslaagd. Als HResult een andere waarde dan nul is, is er een fout opgetreden tijdens de synchronisatie. Als de **PerItemErrorCount** groter is dan 0, worden sommige bestanden of mappen niet goed gesynchroniseerd. Het is mogelijk een HResult van 0 te hebben, maar een PerItemErrorCount die groter is dan 0. Op dit moment hoeft u zich geen zorgen te maken over de PerItemErrorCount. Deze bestanden worden later onderschept. Als dit aantal fouten significant is, neemt u contact op met de klant ondersteuning en vraagt u om een verbinding te maken met de Azure File Sync product groep voor direct advies over de beste, volgende fasen.
6. Controleer of er meerdere 9102 gebeurtenissen met HResult 0 in een rij worden weer geven. Dit geeft aan dat de synchronisatie is voltooid voor deze volume kloon.

### <a name="cut-over-strategy"></a>Strategie voor knippen

1. Bepaal of de synchronisatie van een volume kloon nu snel genoeg is. (Delta kleine genoeg.)
2. Haal het StorSimple-apparaat offline.
3. Een laatste RoboCopy.

Meet de tijd en bepaal of de synchronisatie vanaf een recente volume-kloon binnen een tijd venster klein genoeg kan worden, zodat u zich kunt veroorloven als uitval tijd in uw systeem.

Het is nu tijd om gebruikers toegang tot het StorSimple-apparaat uit te scha kelen. Geen wijzigingen meer. De downtime is begonnen.
U moet het apparaat online houden en er verbinding mee hebben, maar dit moet nu worden voor komen.

In fase 6 worden er verschillen opgevangen in de Live gegevens sinds de laatste volume kloon.

## <a name="phase-6-a-final-robocopy"></a>Fase 6: een laatste RoboCopy

Op dit moment zijn er twee verschillen tussen uw on-premises Windows-Server en het StorSimple 8100-of 8600-apparaat:

1. Mogelijk zijn er bestanden die niet zijn gesynchroniseerd (Zie **PerItemErrors** in het bovenstaande gebeurtenis logboek)
2. Het StorSimple-apparaat heeft een gevulde cache vs. de Windows-Server alleen een naam ruimte zonder bestands inhoud lokaal opgeslagen.

![Een afbeelding die een deel van de eerder overzichts afbeelding illustreert en die de aandacht van deze subsectie van het artikel kan richten.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

We kunnen de cache van de Windows-Server tot de status van het apparaat halen en ervoor zorgen dat er geen bestanden achterblijven met een laatste RoboCopy.

> [!CAUTION]
> Het is belang rijk dat de RoboCopy-opdracht die u volgt, precies zoals hieronder wordt beschreven. Het is alleen mogelijk om bestanden te kopiëren die lokaal zijn en bestanden die niet zijn verplaatst via de methode voor volume-kloon + synchronisatie. U kunt de problemen oplossen die niet later zijn gesynchroniseerd, nadat de migratie is voltooid. (Zie [Azure file sync Troubleshooting](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)(Engelstalig). De meest waarschijnlijke niet-afdruk bare tekens in bestands namen die u niet mist wanneer ze worden verwijderd.)

RoboCopy-opdracht:

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Achtergrondbitmap

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Hiermee kan RoboCopy meerdere threads uitvoeren. De standaard waarde is 8, maximum is 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Hiermee wordt de status van de uitvoer naar een logboek bestand opgeslagen als UNICODE (het bestaande logboek wordt overschreven).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Uitvoer naar console venster. Wordt gebruikt in combi natie met uitvoer naar een logboek bestand.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Hiermee kan voor RoboCopy alleen verschillen worden overwogen tussen de bron (StorSimple-apparaat) en het doel (Windows Server-map).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      betrouw baarheid van het bestand kopiëren (standaard is/COPY: DAT), kopieer vlaggen: D = gegevens, A = kenmerken, T = tijds tempels, S = Security = NTFS Acl's, O = eigenaargegevens, U = controle-informatie
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      ALLE bestands gegevens kopiëren (gelijk aan/COPY: DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      betrouw baarheid voor het kopiëren van mappen (standaard is/DCOPY: DA), kopieer vlaggen: D = gegevens, A = kenmerken, T = tijds tempels
   :::column-end:::
:::row-end:::

U moet deze RoboCopy-opdracht uitvoeren voor elke directory's op de Windows-Server als doel, die u hebt geconfigureerd met bestands synchronisatie naar een Azure-bestand.

U kunt meerdere van deze opdrachten parallel uitvoeren.
Zodra deze RoboCopy-stap is voltooid, kunt u uw gebruikers en apps toegang geven tot de Windows-Server, net zoals ze eerder het StorSimple-apparaat hebben gedaan.

Raadpleeg de Robocopy-logboek bestand (en) om te zien of er bestanden achterblijven. Als er problemen zouden bestaan, kunt u deze in de meeste gevallen oplossen nadat de migratie is voltooid en uw gebruikers en apps op de Windows-Server zijn geïnstalleerd. Als u problemen wilt oplossen, moet u dit doen vóór fase 7.

Het is waarschijnlijk nodig om de SMB-shares te maken op de Windows-Server die u eerder had voor de StorSimple-gegevens. U kunt deze stap vooraan laden en eerder geen tijd kwijt raken, maar u moet er wel voor zorgen dat de bestanden niet worden gewijzigd op de Windows-Server.

Als u een DFS-N-implementatie hebt, kunt u de DFN-naam ruimten naar de nieuwe locatie van de servermap wijzen. Als u geen DFS-N-implementatie hebt en u uw 8100 8600-apparaat lokaal hebt voor een Windows-Server, kunt u die server uit het domein halen en moet het domein worden toegevoegd aan uw nieuwe Windows Server met AFS naar het domein. Geef deze dezelfde server naam als de oude server en dezelfde share namen, blijft de afgesneden naar de nieuwe server transparant voor uw gebruikers, groeps beleid of scripts.

## <a name="phase-7-deprovision"></a>Fase 7: inrichting opheffen

Tijdens de laatste fase kunt u meerdere volume klonen herhalen en uiteindelijk de gebruikers toegang tot de nieuwe Windows-Server ontknippen nadat u het StorSimple-apparaat offline hebt gezet.

U kunt nu beginnen met het ongedaan maken van de inrichting van overbodige resources.
Voordat u begint, is het een best practice om uw nieuwe Azure File Sync implementatie in productie te observeren, voor een beetje. Hiermee kunt u eventuele problemen oplossen die kunnen optreden.

Zodra u tevreden bent en uw AFS-implementatie gedurende ten minste een paar dagen hebt waargenomen, kunt u de inrichting van resources in deze volg orde ongedaan maken:

1. Schakel de virtuele Azure-machine uit die we hebben gebruikt om gegevens te verplaatsen van de volume klonen naar de Azure-bestands shares via bestands synchronisatie.
2. Ga naar uw opslag synchronisatie service resource in Azure en hef de registratie van de Azure VM op. Hiermee verwijdert u het uit alle synchronisatie groepen.

    > [!WARNING]
    > **Zorg ervoor dat u de juiste computer selecteert.** U hebt de Cloud-VM uitgeschakeld. Dit betekent dat deze moet worden weer gegeven als de enige offline-server in de lijst met geregistreerde servers. U mag de registratie van de on-premises Windows-Server bij deze stap niet opheffen.

3. Azure-VM en de bijbehorende resources verwijderen.
4. Schakel het 8020 virtuele-StorSimple-apparaat uit.
5. De inrichting van alle StorSimple-resources in azure ongedaan maken.
6. Ontkoppel het fysieke StorSimple-apparaat uit uw Data Center.

De migratie is voltooid.

## <a name="next-steps"></a>Volgende stappen

Krijg meer vertrouwd met Azure File Sync. Met name met de flexibiliteit van beleids regels voor Cloud lagen.

Als u in de Azure Portal, of in de eerdere gebeurtenissen ziet, dat sommige bestanden permanent niet worden gesynchroniseerd, raadpleegt u de hand leiding voor het oplossen van problemen om deze problemen op te lossen.

* [Overzicht van Azure File Sync: aka.ms/AFS](https://aka.ms/AFS)
* [Cloud lagen](storage-sync-cloud-tiering.md) 
* [Gids voor het oplossen van problemen Azure File Sync](storage-sync-files-troubleshoot.md)
