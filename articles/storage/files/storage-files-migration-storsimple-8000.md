---
title: StorSimple 8000-serie migratie naar Azure File Sync
description: Meer informatie over het migreren van een StorSimple 8100- of 8600-toestel naar Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7e5f70d0323aa5c502491ab99db303fde31ade83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528622"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100- en 8600-migratie naar Azure File Sync

De StorSimple 8000-serie wordt vertegenwoordigd door de 8100 of de 8600 fysieke, on-premises apparaten en hun cloudservicecomponenten. Het is mogelijk om de gegevens van een van deze apparaten te migreren naar een Azure File Sync-omgeving. Azure File Sync is de standaard- en strategische Azure-service voor de lange termijn waarnaar StorSimple-apparaten kunnen worden gemigreerd.

De StorSimple 8000-serie bereikt in december 2022 zijn [einde van de levensduur.](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) Het is belangrijk om zo snel mogelijk te beginnen met het plannen van uw migratie. In dit artikel vindt u de nodige achtergrondkennis en migratiestappen voor een succesvolle migratie naar Azure File Sync. 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> Microsoft zet zich in om klanten te helpen bij hun migratie. E-mail AzureFilesMigration@microsoft .com voor een aangepast migratieplan en hulp tijdens de migratie.

Azure File Sync is een Microsoft-cloudservice, gebaseerd op twee hoofdcomponenten:

* Bestandssynchronisatie en cloudtiering.
* Bestandsshares als native storage in Azure, die toegankelijk is via meerdere protocollen zoals SMB en file REST. Een Azure-bestandsshare is vergelijkbaar met een bestandsshare op een Windows Server, die u native monteren als netwerkstation. Het ondersteunt belangrijke bestandsgetrouwheidsaspecten zoals kenmerken, machtigingen en tijdstempels. Met Azure-bestandsshares is er niet langer een toepassing of service nodig om de bestanden en mappen die in de cloud zijn opgeslagen, te interpreteren. U ze native openen via bekende protocollen en clients zoals Windows File Explorer. Dat maakt Azure-bestandsaandelen de ideale en meest flexibele benadering om gegevens van bestandenserver voor algemene doeleinden op te slaan, evenals bepaalde toepassingsgegevens, in de cloud.

Dit artikel richt zich op de migratiestappen. Als u voor de migratie meer wilt weten over Azure File Sync, raden we de volgende artikelen aan:

* [Azure File Sync - overzicht](https://aka.ms/AFS "Overzicht")
* [Azure File Sync - implementatiehandleiding](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Migratiedoelen

Het doel is om de integriteit van de productiegegevens te garanderen en de beschikbaarheid te garanderen. Dit laatste vereist het tot een minimum beperken van downtime, zodat het in of slechts een beetje hoger kan zijn dan reguliere onderhoudsvensters.

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>Migratiepad van de StorSimple 8000-serie naar Azure File Sync

Een lokale Windows Server is vereist om een Azure File Sync-agent uit te voeren. De Windows Server kan minimaal een 2012R2-server zijn, maar is idealiter een Windows Server 2019.

Er zijn tal van, alternatieve migratiepaden en het zou te lang van een artikel om ze allemaal te documenteren en illustreren waarom ze risico's of nadelen dragen over de route die we aanbevelen als een beste praktijk in dit artikel.

![Overzicht van de migratiefasen van de StorSimple 8000-serie](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "StorSimple 8000 serie migratie route overzicht van de fasen verder hieronder in dit artikel.")

De vorige afbeelding toont fasen die overeenkomen met secties in dit artikel.
We gebruiken een cloudmigratie om te voorkomen dat bestanden onnodig worden teruggeroepen naar uw lokale StorSimple-toestel. Deze aanpak voorkomt dat dit gevolgen heeft voor lokaal cachinggedrag of het gebruik van netwerkbandbreedte, wat van invloed kan zijn op uw productieworkloads.
Er wordt een cloudmigratie op een momentopname (een volumekloon) van uw gegevens verwerkt. Dus uw productiegegevens worden geïsoleerd van dit proces - tot cut-over aan het einde van de migratie. Werken off van wat in wezen een back-up, maakt de migratie veilig en gemakkelijk herhaalbaar, moet je in de problemen.

## <a name="considerations-around-existing-storsimple-backups"></a>Overwegingen rond bestaande StorSimple back-ups

Met StorSimple u back-ups maken in de vorm van volumeklonen. In dit artikel wordt een nieuwe volumekloon gebruikt om uw live bestanden te migreren.
Als u back-ups moet migreren naast uw live gegevens, dan is alle richtlijnen in dit artikel nog steeds van toepassing. Het enige verschil is dat in plaats van te beginnen met een nieuwe volume kloon, zult u beginnen met de oudste back-up volume kloon die u nodig hebt om te migreren.

De volgorde is als volgt:

* Bepaal de minimale set volumeklonen die u moet migreren. We raden u aan deze lijst zo mogelijk tot een minimum te beperken, omdat hoe meer back-ups u migreert naardes te langer het totale migratieproces zal duren.
* Wanneer u het migratieproces doorloopt, begint u met de oudste volumekloon die u wilt migreren en gebruikt u bij elke volgende migratie de volgende oudste.
* Wanneer elke volumekloonmigratie is voltooid, moet u een momentopname voor azure-bestandsshare maken. [Azure-momentopnamen voor bestandsshare](storage-snapshots-files.md) zijn de manier waarop u point-in-time back-ups van de bestanden en mapstructuur voor uw Azure-bestandsshares bewaart. U hebt deze momentopnamen nodig nadat de migratie is voltooid, om ervoor te zorgen dat u versies van elk van uw volumeklonen hebt bewaard terwijl u door de migratie gaat.
* Controleer of u Azure-momentopnamen voor bestandsshares maakt voor alle Azure-bestandsshares die worden weergegeven door hetzelfde StorSimple-volume. Volumeklonen bevinden zich op het volumeniveau, Azure-momentopnamen voor bestandsdelen bevinden zich op het aandeelniveau. U moet een momentopname van een share maken (op elke Azure-bestandsshare) nadat de migratie van een volumekloon is voltooid.
* Herhaal het migratieproces voor een volumekloon en maak momentopnamen van delen na elke volumekloon totdat u wordt ingehaald door een momentopname van de live-gegevens. Het proces van het migreren van een volumekloon wordt beschreven in de onderstaande fasen. 

Als u helemaal geen back-ups hoeft te verplaatsen en een nieuwe keten van back-ups starten aan de Azure-bestandsshare-kant nadat de migratie van alleen de live-gegevens is uitgevoerd, is dat gunstig om de complexiteit van de migratie te verminderen en de hoeveelheid tijd die de migratie in beslag neemt. U de beslissing nemen om back-ups al dan niet te verplaatsen en hoeveel voor elk volume (niet elk aandeel) u in StorSimple hebt.

## <a name="phase-1-get-ready"></a>Fase 1: Maak je klaar

:::row:::
    :::column:::
        ![Een afbeelding die een deel van de eerdere overzichtsafbeelding illustreert waarmee deze onderafdeling van het artikel centraal staat.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        De basis voor de migratie is een volumekloon en een virtueel cloudtoestel, een StorSimple 8020 genaamd.
Deze fase richt zich op de implementatie van deze resources in Azure.
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>Een StorSimple 8020 virtueel toestel implementeren

Het implementeren van een cloudtoestel is een proces dat beveiliging, netwerken en een paar andere overwegingen vereist.

> [!IMPORTANT]
> De volgende handleiding bevat een aantal onnodige secties. Lees en volg het artikel van het begin tot en met stap 3. Keer dan terug naar dit artikel. U hoeft niet te voltooien "Stap 3" of iets daarbuiten in die gids, op dit moment.

[Implementatie van een StorSimple 8020 virtueel toestel](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>Een volumekloon bepalen die moet worden gebruikt

Wanneer u klaar bent om de migratie te beginnen, is de eerste stap om een nieuwe volumekloon te nemen - net zoals u zou doen voor back-up - die de huidige status van uw StorSimple-cloudopslag vastlegt. Neem een kloon voor elk van de StorSimple volumes die je hebt.
Als u behoefte hebt aan het verplaatsen van back-ups, dan is de eerste volumekloon die u gebruikt geen nieuw gemaakte kloon, maar de oudste volumekloon (oudste back-up) die u moet migreren.
Raadpleeg de rubriek ["Overwegingen rond bestaande StorSimple-back-ups"](#considerations-around-existing-storsimple-backups) voor gedetailleerde richtlijnen.

> [!IMPORTANT]
> De volgende handleiding bevat een aantal onnodige secties. Lees en volg alleen de stappen die in de sectie gekoppeld aan worden beschreven. Keer dan terug naar dit artikel. U hoeft de sectie 'Volgende stappen' niet te volgen.

[Een kloon van een volume maken](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>De volumekloon gebruiken

De laatste fase van fase 1 is om de volumekloon die u hebt gekozen, beschikbaar te maken op het virtuele toestel 8020 in Azure.

> [!IMPORTANT]
> De volgende gids bevat de nodige stappen, maar ook - aan het eind - een instructie om het volume op te maken. **HET VOLUME NIET OPMAKEN** Lees en volg de gelinkte "sectie 7" vanaf het begin tot instructie: "10. Om een eenvoudig volume op te maken, ..."  Stop voordat u deze stap volgt en keer terug naar dit artikel.

[Een volumekloon monteren op het virtuele toestel 8020 in Azure](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>Samenvatting fase 1

Nu je fase 1 hebt voltooid, heb je het volgende gedaan:

* Een StorSimple 8020 virtueel toestel geïmplementeerd in Azure.
* Bepaald met welk volume kloon u de migratie zult beginnen.
* Monteerde uw volumekloon(s) (één voor elk live volume) naar het virtuele storSimple-toestel in Azure, met zijn gegevens beschikbaar voor verder gebruik.

## <a name="phase-2-cloud-vm"></a>Fase 2: Cloud VM

:::row:::
    :::column:::
        ![Een afbeelding die een deel van de eerdere overzichtsafbeelding illustreert waarmee deze onderafdeling van het artikel centraal staat.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        Nadat uw eerste kloon beschikbaar is op het virtuele apparaat StorSimple 8020 in Azure, is het nu tijd om een VM in te richten en de volumekloon (of meerdere) bloot te stellen aan die VM via iSCSI.
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>Een Azure VM implementeren

De virtuele Windows Server-machine in Azure is net als de StorSimple 8020, een tijdelijk stukje infrastructuur dat alleen nodig is tijdens de migratie.
De configuratie van de VM die u implementeert, is grotendeels afhankelijk van het aantal items (bestanden en mappen) dat u synchroniseert. We raden u aan om met een hogere prestatieconfiguratie te gaan als u zich zorgen maakt.

Eén Windows Server kan maximaal 30 Azure-bestandsshares synchroniseren.
De specs die u beslist over de noodzaak om elk aandeel / pad of de wortel van de StorSimple volume omvatten en tellen de items (bestanden en mappen).

De totale grootte van de gegevens is minder een knelpunt - het is het aantal items die u nodig hebt om de machine specificaties aan te passen.

* [Meer informatie over het vergroten van een Windows Server op basis van het aantal items (bestanden en mappen) dat u moet synchroniseren.](storage-sync-files-planning.md#recommended-system-resources)

    **Let op:** Het eerder gekoppelde artikel presenteert een tabel met een bereik voor servergeheugen (RAM). Oriëntnaar het grote getal voor de Azure VM. U zich oriënteren op het kleinere nummer voor uw on-premises machine.

* [Meer informatie over het implementeren van een Windows Sever VM.](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> Zorg ervoor dat de VM wordt geïmplementeerd in dezelfde Azure-regio als het virtuele apparaat StorSimple 8020. Als u in het kader van deze migratie ook de regio van uw cloudgegevens moet wijzigen uit de regio waarin deze momenteel is opgeslagen, u dat in een later stadium doen wanneer u Azure-bestandsshares indient.

> [!IMPORTANT]
> Om te optimaliseren voor prestaties, implementeert u een **zeer snelle OS-schijf** voor uw cloud-VM. U slaat de synchronisatiedatabase op de OS-schijf op voor al uw gegevensvolumes. Zorg er bovendien voor dat u een **grote OS-schijf maakt.** Afhankelijk van het aantal items (bestanden en mappen) op uw StorSimple-volumes heeft de OS-schijf mogelijk een **paar honderd GiB-ruimte** nodig om de synchronisatiedatabase te kunnen aanpassen.

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>De StorSimple 8020-volumes blootstellen aan de Azure VM

In deze fase verbindt u een of meer StorSimple-volumes van het virtuele toestel 8020 via iSCSI met de windows server-vm die u hebt ingericht.

> [!IMPORTANT]
> Voer voor de volgende artikelen alleen het **PRIVÉ-IP voor het cloudtoestel en** **Verbinding maken via iSCSI-secties** in en keer terug naar dit artikel.

1. [Privé IP-adres voor het cloudapparaat ophalen](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [Verbinding maken via iSCSI](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>Samenvatting fase 2

Nu je fase 2 hebt voltooid, heb je: 

* Een Windows Server VM in dezelfde regio ingericht als het 8020 virtuele StorSimple-toestel
* Alle toepasselijke volumes van de 8020 blootstaan aan de Windows Server VM via iSCSI.
* U ziet nu bestands- en mapinhoud wanneer u Verkenner op de server-vm op de gemonteerde volumes gebruikt.

Ga alleen door naar fase 3 wanneer u deze stappen hebt voltooid voor alle volumes die migratie nodig hebben.

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>Fase 3: Azure-bestandsshares instellen en je voorbereiden op Azure File Sync

:::row:::
    :::column:::
        ![Een afbeelding die een deel van de eerdere overzichtsafbeelding illustreert waarmee deze onderafdeling van het artikel centraal staat.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        In deze fase bepaalt en inziet u een aantal Azure-bestandsshares, maakt u een on-premises Windows Server als een StorSimple-toestelvervangend en configureert u die server voor Azure File Sync. 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>Uw bestaande naamruimten toewijzen aan Azure-bestandsshares

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>Azure-bestandsshares implementeren

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> Als u het Azure-gebied moet wijzigen vanuit de huidige regio waarin uw StorSimple-gegevens zich bevinden, u de Azure-bestandsshares inrichten in de nieuwe regio die u wilt gebruiken. U bepaalt het gebied door het te selecteren wanneer u de opslagaccounts indient die uw Azure-bestandsshares bevatten. Zorg ervoor dat ook de Azure File Sync-bron die u hieronder indient, zich in dezelfde nieuwe regio bevindt.

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>De Azure File Sync-cloudbron implementeren

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Als u het Azure-gebied moet wijzigen uit de huidige regio waarin uw StorSimple-gegevens zich bevinden, hebt u de opslagaccounts voor uw Azure-bestandsshares in de nieuwe regio ingericht. Zorg ervoor dat u datzelfde gebied hebt geselecteerd wanneer u deze opslagsynchronisatieservice implementeert.

### <a name="deploy-an-on-premises-windows-server"></a>Een on-premises Windows Server implementeren

* Maak een Windows Server 2019 - minimaal 2012R2 - als virtuele machine of fysieke server. Een fail-overcluster van Windows Server wordt ook ondersteund. Gebruik de server die u mogelijk hebt voor de StorSimple 8100 of 8600 niet opnieuw.
* Direct Attached Storage inrichten of toevoegen (DAS in vergelijking met NAS, die niet wordt ondersteund).

Het is de beste gewoonte om uw nieuwe Windows Server een gelijke of grotere hoeveelheid opslagruimte te geven dan uw StorSimple 8100- of 8600-toestel lokaal beschikbaar is voor caching. U gebruikt de Windows Server op dezelfde manier als het StorSimple-toestel, als het dezelfde hoeveelheid opslagruimte heeft als het apparaat, dan moet de cache-ervaring vergelijkbaar zijn, zo niet hetzelfde.
U naar eigen zeggen opslag van uw Windows Server toevoegen of verwijderen. Hiermee u uw lokale volumegrootte en de hoeveelheid lokale opslag schalen die beschikbaar is voor caching.

### <a name="prepare-the-windows-server-for-file-sync"></a>De Windows Server voorbereiden op bestandssynchronisatie

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>Azure-bestandssynchronisatie configureren op de Windows Server

Uw geregistreerde on-premises Windows Server moet voor dit proces klaar zijn en verbonden zijn met internet.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Zorg ervoor dat u cloud tiering inschakelt!** Cloudtiering is de AFS-functie waarmee de lokale server minder opslagcapaciteit heeft dan in de cloud is opgeslagen, maar toch de volledige naamruimte beschikbaar heeft. Lokaal interessante gegevens worden ook lokaal opgeslagen voor snelle, lokale toegangsprestaties. Een andere reden om cloudtiering in deze stap in te schakelen, is dat we in dit stadium de bestandsinhoud niet willen synchroniseren, alleen de naamruimte moet op dit moment worden verplaatst.

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>Fase 4: De Azure VM configureren voor synchronisatie

:::row:::
    :::column:::
        ![Een afbeelding die een deel van de eerdere overzichtsafbeelding illustreert waarmee deze onderafdeling van het artikel centraal staat.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        Deze fase heeft betrekking op uw Azure VM met de iSCSI-gemonteerde kloon(s) met het eerste volume. Tijdens deze fase krijgt u de VM verbonden via Azure File Sync en start u een eerste ronde van het verplaatsen van bestanden van uw StorSimple-volumekloon(s).
        
    :::column-end:::
:::row-end:::

U hebt uw on-premises server al geconfigureerd die uw StorSimple 8100- of 8600-toestel vervangt voor Azure File Sync. 

Het configureren van de Azure VM is een bijna identiek proces, met een extra stap. De volgende stappen zullen u door het proces begeleiden.

> [!IMPORTANT]
> Het is belangrijk dat de Azure VM niet is **geconfigureerd met cloud tiering ingeschakeld!** U wisselt het volume van deze server om met nieuwere volumeklonen gedurende de hele migratie. Cloud tiering heeft geen voordeel en overhead op CPU-gebruik moet u vermijden.

1. [Zet de AFS-agent in. (zie vorige sectie)](#prepare-the-windows-server-for-file-sync)
2. [De VM klaarmaken voor Azure File Sync.](#get-the-vm-ready-for-azure-file-sync)
3. [Synchronisatie configureren](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>De VM klaarmaken voor Azure File Sync

Azure File Sync wordt gebruikt om de bestanden te verplaatsen van de gemonteerde iSCSI StorSimple-volumes naar de beoogde Azure-bestandsshares.
Tijdens dit migratieproces monteert u verschillende volumeklonen aan uw VM, onder dezelfde stationsletter. Azure File Sync moet zijn geconfigureerd om de volgende volumekloon te zien die u hebt gemonteerd als een nieuwere versie van de bestanden en mappen en de Azure-bestandsshares bijwerken die via Azure File Sync zijn verbonden. 

> [!IMPORTANT]
> Om dit te laten werken, moet een registersleutel op de server worden ingesteld voordat Azure File Sync is geconfigureerd.

1. Maak een nieuwe map op het systeemstation van de VM. Azure File Sync-informatie moet daar worden gehandhaafd in plaats van op de gemonteerde volumeklonen. Bijvoorbeeld: `"C:\syncmetadata"`
2. Open regedit en zoek de volgende registerkorf:`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. Een nieuwe tekenreeks met sleutel met de naam: ***MetadataRootPath maken***
4. Stel het volledige pad in op de map die u op het systeemvolume hebt gemaakt, bijvoorbeeld:`C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>Azure-bestandssynchronisatie configureren op de Azure-vm

Deze stap is vergelijkbaar met de vorige sectie, waarin wordt besproken hoe u AFS configureert op de on-premises server.

Het verschil is dat u cloudtiering op deze server niet moet inschakelen en dat u ervoor moet zorgen dat de juiste mappen zijn verbonden met de juiste Azure-bestandsshares. Anders komt uw naamgeving van Azure-bestandsshares en gegevensinhoud niet overeen en is er geen manier om de naam van de cloudbronnen of lokale mappen te wijzigen zonder synchronisatie opnieuw te configureren.

Raadpleeg de [vorige sectie over het configureren van Azure File Sync op een Windows Server.](#configure-azure-file-sync-on-the-windows-server)

### <a name="step-4-summary"></a>Samenvatting van stap 4

Op dit moment hebt u Azure File Sync geconfigureerd op de Azure VM die u uw StorSimple-volumekloon(s) via iSCSI hebt gemonteerd.
Er stromen nu gegevens van de Azure VM naar de verschillende Azure-bestandsshares en van daaruit wordt een volledig vermoeide naamruimte weergegeven op uw on-premises Windows Server.

> [!IMPORTANT]
> Controleer of er op dit moment geen wijzigingen zijn aangebracht of dat gebruikerstoegang wordt verleend tot de Windows Server.

De initiële gegevens van de volumekloon die via de Azure VM naar de Azure-bestandsshares worden verplaatst, kunnen veel tijd, mogelijk weken, in beslag nemen. Het schatten van de tijd die dit zal duren is lastig en hangt af van vele factoren. Met name de snelheid waarmee de Azure VM toegang heeft tot bestanden op de StorSimple-volumes en hoe snel Azure File Sync de bestanden en mappen kan verwerken die moeten worden gesynchroniseerd. 

Uit ervaring kunnen we aannemen dat de bandbreedte - dus de werkelijke gegevensgrootte - een ondergeschikte rol speelt. De tijd die deze of een volgende migratieronde in beslag neemt, is meestal afhankelijk van het aantal artikelen dat per seconde kan worden verwerkt. Dus bijvoorbeeld 1 TiB met een 100.000 bestanden en mappen zal waarschijnlijk langzamer eindigen dan 1 TiB met slechts 50.000 bestanden en mappen.

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>Fase 5: Herhalen door meerdere volume klonen

:::row:::
    :::column:::
        ![Een afbeelding die een deel van de eerdere overzichtsafbeelding illustreert waarmee deze onderafdeling van het artikel centraal staat.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        Zoals besproken in de vorige fase, kan de eerste synchronisatie lang duren. Uw gebruikers en toepassingen hebben nog steeds toegang tot het on-premises StorSimple 8100- of 8600-toestel. Dat betekent dat veranderingen zich opstapelen, en met elke dag een grotere delta tussen de live gegevens en de eerste volumekloon, bent u momenteel migratie, vormen. In deze sectie leert u hoe u downtime minimaliseren door meerdere volumeklonen te gebruiken en te zien wanneer synchronisatie is uitgevoerd.
    :::column-end:::
:::row-end:::

Helaas is het migratieproces niet onmiddellijk. Dat betekent dat de bovengenoemde delta aan de levende gegevens een onvermijdelijk gevolg is. Het goede nieuws is dat je het proces van het monteren van nieuwe volume klonen herhalen. De delta van elke volumekloon zal geleidelijk kleiner zijn. Dus uiteindelijk, een synchronisatie zal eindigen in een periode die u aanvaardbaar acht voor het nemen van gebruikers en apps offline te snijden naar uw on-premises Windows-server.

Herhaal de volgende stappen totdat de synchronisatie snel genoeg is voltooid, zodat u zich op uw gemak voelt om gebruikers en apps offline te halen:

1. [Bepalen of de synchronisatie is voltooid voor een bepaalde volumekloon.](#determine-when-sync-is-done)
2. [Neem een nieuwe volume kloon (s) en monteer het aan de 8020 virtuele toestel.](#the-next-volume-clones)
3. [Bepaal wanneer synchronisatie is uitgevoerd.](#determine-when-sync-is-done)
4. [Cut-over strategie](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>De volgende volumekloon(s)

We hebben besproken het nemen van een volume kloon (s) eerder in dit artikel.
Deze fase heeft twee acties:

1. [Neem een volumekloon](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [Monteer die volumekloon (zie hierboven)](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>Bepalen wanneer synchronisatie is uitgevoerd

Wanneer synchronisatie is gedaan, u uw tijdmeting stoppen en bepalen of u het proces van het nemen van een volumekloon moet herhalen en deze moet monteren of dat de tijd die synchronisatie met de laatste volumekloon heeft genomen, voldoende klein was.

Om te bepalen dat de synchronisatie voltooid is:

1. De logboeken openen en naar **Toepassingen en services** navigeren
2. Navigeren en **openen van Microsoft\FileSync\Agent\Telemetrie**
3. Zoek naar de meest recente **gebeurtenis 9102**, die overeenkomt met een voltooide synchronisatiesessie
4. Selecteer **Details** en controleer of de waarde **SyncDirection** **is Uploaden**
5. Controleer de **HResult** en bevestig dat het **0**toont. Dit betekent dat de synchronisatiesessie is geslaagd. Als HResult een niet-nulwaarde is, is er een fout opgetreden tijdens het synchroniseren. Als het **Aantal PerItemError's** groter is dan 0, zijn sommige bestanden of mappen niet goed gesynchroniseerd. Het is mogelijk om een HResult van 0 maar een PerItemErrorCount te hebben dat groter is dan 0. Op dit moment hoeft u zich geen zorgen te maken over de PerItemErrorCount. We zullen deze bestanden later vangen. Als dit aantal fouten aanzienlijk is, nemen duizenden items contact op met de klantenservice en vraagt u om te worden verbonden met de azure file sync-productgroep voor directe richtlijnen voor de beste, volgende fasen.
6. Controleer om meerdere 9102-gebeurtenissen met HResult 0 achter elkaar te bekijken. Dit geeft aan dat de synchronisatie is voltooid voor deze volumekloon.

### <a name="cut-over-strategy"></a>Cut-over strategie

1. Bepaal of de synchronisatie van een volumekloon nu snel genoeg is. (Delta klein genoeg.)
2. Haal het StorSimple-toestel offline.
3. Een laatste RoboCopy.

Meet de tijd en bepaal of synchronisatie van een recente volumekloon kan eindigen binnen een tijdvenster dat klein genoeg is, dat u zich veroorloven als downtime in uw systeem.

Het is nu tijd uit te schakelen gebruiker toegang tot de StorSimple toestel. Geen veranderingen meer. Downtime is begonnen.
U moet het apparaat online en aangesloten laten, maar moet nu wijzigingen op het apparaat voorkomen.

In fase 6 zult u inhalen met een delta in de live-gegevens sinds de laatste volume kloon.

## <a name="phase-6-a-final-robocopy"></a>Fase 6: Een definitieve RoboCopy

Op dit moment zijn er twee verschillen tussen uw on-premises Windows Server en het StorSimple 8100- of 8600-toestel:

1. Er kunnen bestanden zijn die niet zijn gesynchroniseerd (zie **PerItemErrors** uit het gebeurtenislogboek hierboven)
2. Het StorSimple-toestel heeft een bevolkte cache versus de Windows Server, slechts een naamruimte zonder dat er op dit moment bestandsinhoud lokaal is opgeslagen.

![Een afbeelding die een deel van de eerdere overzichtsafbeelding illustreert waarmee deze onderafdeling van het artikel centraal staat.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

We kunnen de cache van de Windows Server naar de staat van het toestel brengen en ervoor zorgen dat er geen bestand achterblijft met een definitieve RoboCopy.

> [!CAUTION]
> Het is absoluut noodzakelijk dat de RoboCopy commando die u volgt, is precies zoals hieronder beschreven. We willen alleen bestanden kopiëren die lokaal zijn en bestanden die nog niet eerder door de aanpak volumeclone+sync zijn gegaan. We kunnen de problemen oplossen waarom ze later niet zijn gesynchroniseerd, nadat de migratie is voltooid. (Zie [problemen met Azure File Sync](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing). Het zijn waarschijnlijk onafdrukbare tekens in bestandsnamen die u niet zult missen wanneer ze worden verwijderd.)

RoboCopy, opdracht:

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Achtergrond:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Hiermee kan RoboCopy multithread-reeksen uitvoeren. Standaard is 8, max is 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Uitvoerstatus naar LOG-bestand als UNICODE (overschrijft bestaande logboek).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Uitgangen naar het consolevenster. Wordt gebruikt in combinatie met uitvoer naar een logboekbestand.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Draait RoboCopy in dezelfde modus die een back-uptoepassing zou gebruiken. Hiermee kan RoboCopy bestanden verplaatsen waar de huidige gebruiker geen machtigingen voor heeft.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Hiermee kan RoboCopy alleen delta's tussen bron (StorSimple-toestel) en doel (Windows Server-map) overwegen.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      getrouwheid van de bestandskopie (standaard is /COPY:DAT), copy flags: D=Data, A=Attributes, T=Timestamps, S=Security=NTFS ATFS' s, O=Owner info, U=aUditing info
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / COPYALL
   :::column-end:::
   :::column span="1":::
      ALLE bestandsgegevens kopiëren (gelijk aan /COPY:DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      getrouwheid voor de kopie van mappen (standaard is /DCOPY:DA), copy flags: D=Data, A=Attributes, T=Timestamps
   :::column-end:::
:::row-end:::

U moet deze RoboCopy-opdracht uitvoeren voor elk van de mappen op de Windows Server als doel, die u hebt geconfigureerd met bestandssynchronisatie naar een Azure-bestand.

U meerdere van deze opdrachten parallel uitvoeren.
Zodra deze RoboCopy-stap is voltooid, u uw gebruikers en apps toegang geven tot de Windows Server zoals ze eerder het StorSimple-toestel hebben gedaan.

Raadpleeg het robocopy log bestand(en) om te zien of bestanden zijn achtergelaten. Als er problemen zijn, u deze in de meeste gevallen oplossen nadat de migratie is voltooid en uw gebruikers en apps opnieuw naar uw Windows Server zijn gebracht. Als u problemen moet oplossen, doe dit dan vóór fase 7.

Het is waarschijnlijk nodig om de SMB-aandelen te maken op de Windows Server die u eerder op de StorSimple-gegevens had. U deze stap vooraf laden en dit eerder doen om hier geen tijd te verliezen, maar u moet ervoor zorgen dat er voor dit punt geen wijzigingen in bestanden plaatsvinden op de Windows-server.

Als u een DFS-N-implementatie hebt, u de DFN-naamruimten naar de nieuwe locatie van de servermap wijzen. Als u geen DFS-N-implementatie hebt en u uw 8100 8600-toestel lokaal hebt geopend met een Windows Server, u die server van het domein halen en u uw nieuwe Windows Server met AFS aansluiten bij het domein, en deze server dezelfde servernaam geven als de oude server , en dezelfde namen delen, dan blijft de cut-over naar de nieuwe server transparant voor uw gebruikers, groepsbeleid of scripts.

## <a name="phase-7-deprovision"></a>Fase 7: Deprovision

Tijdens de laatste fase heb je herhaald door meerdere volume klonen, en uiteindelijk waren in staat om te snijden over de toegang van de gebruiker tot de nieuwe Windows Server na het nemen van je StorSimple toestel offline.

U nu beginnen met het deprovisioneneren van onnodige resources.
Voordat u begint, is het een aanbevolen manier om uw nieuwe Azure File Sync-implementatie in productie te observeren. Dat geeft je opties om eventuele problemen die je zou kunnen tegenkomen op te lossen.

Zodra u tevreden bent en uw AFS-implementatie gedurende ten minste een paar dagen hebt waargenomen, u beginnen met het deprovisionen van resources in deze volgorde:

1. Schakel de Azure VM uit die we hebben gebruikt om gegevens van de volumeklonen naar de Azure-bestandsshares via bestandssynchronisatie te verplaatsen.
2. Ga naar uw Storage Sync Service-bron in Azure en de registratie van de Azure-vm uit. Dat verwijdert het uit alle synchronisatiegroepen.

    > [!WARNING]
    > **ZORG ERVOOR dat u de juiste machine kiest.** U hebt de cloud-VM uitgeschakeld, dat betekent dat deze moet worden weergegeven als de enige offlineserver in de lijst met geregistreerde servers. U mag de on-premises Windows Server niet bij deze stap kiezen, zodat u deze uitschrijft.

3. Azure VM en de bijbehorende bronnen verwijderen.
4. Schakel het 8020 virtuele StorSimple-toestel uit.
5. Deprovisioneer alle StorSimple-resources in Azure.
6. Haal de stekker uit het fysieke apparaat storSimple uit uw datacenter.

Uw migratie is voltooid.

## <a name="next-steps"></a>Volgende stappen

Meer vertrouwd raken met Azure File Sync. Vooral met de flexibiliteit van cloud tiering beleid.

Als u in de Azure-portal of van eerdere gebeurtenissen ziet dat sommige bestanden permanent niet worden gesynchroniseerd, raadpleegt u de handleiding voor probleemoplossing voor stappen om deze problemen op te lossen.

* [Overzicht van Azure File Sync: aka.ms/AFS](https://aka.ms/AFS)
* [Cloudlaaging](storage-sync-cloud-tiering.md) 
* [Handleiding voor het oplossen van problemen met Azure File Sync](storage-sync-files-troubleshoot.md)
