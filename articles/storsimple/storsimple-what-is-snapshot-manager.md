---
title: Wat is StorSimple Snapshot Manager? | Microsoft Docs
description: Beschrijft de StorSimple Snapshot Manager, de architectuur en de functies ervan.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: twooley
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e58e2d929dd1e4db16ce495ad54045e9dc3a6fb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267415"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>Een inleiding tot StorSimple Snapshot Manager

## <a name="overview"></a>Overzicht
StorSimple Snapshot Manager is een MMC-module (Microsoft Management Console) die gegevensbescherming en back-upbeheer in een Microsoft Azure StorSimple-omgeving vereenvoudigt. Met StorSimple Snapshot Manager u Microsoft Azure StorSimple-gegevens beheren in het datacenter en in de cloud als één geïntegreerde opslagoplossing, waardoor back-upprocessen worden vereenvoudigd en de kosten worden verlaagd.

Dit overzicht introduceert de StorSimple Snapshot Manager, beschrijft de functies ervan en legt zijn rol in Microsoft Azure StorSimple uit. 

Zie [StorSimple 8000-serie, inclusief](storsimple-overview.md)het StorSimple-apparaat, de StorSimple Manager-service, StorSimple Snapshot Manager en StorSimple-adapter voor SharePoint, voor een overzicht van het volledige Microsoft Azure StorSimple-systeem, inclusief de StorSimple-apparaat, de StorSimple Manager-service en de StorSimple-adapter voor SharePoint. 

> [!NOTE]
> * U StorSimple Snapshot Manager niet gebruiken om Microsoft Azure StorSimple Virtual Arrays (ook bekend als StorSimple on-premises virtuele apparaten) te beheren.
> * Als u van plan bent StorSimple Update 2 op uw StorSimple-apparaat te installeren, moet u de nieuwste versie van StorSimple Snapshot Manager downloaden en installeren **voordat u StorSimple Update 2 installeert.** De nieuwste versie van StorSimple Snapshot Manager is achterwaarts compatibel en werkt met alle vrijgegeven versies van Microsoft Azure StorSimple. Als u de vorige versie van StorSimple Snapshot Manager gebruikt, moet u deze bijwerken (u hoeft de vorige versie niet te verwijderen voordat u de nieuwe versie installeert).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>StorSimple Snapshot Manager doel en architectuur
StorSimple Snapshot Manager biedt een centrale beheerconsole die u gebruiken om consistente, point-in-time back-upkopieën van lokale en cloudgegevens te maken. U de console bijvoorbeeld gebruiken om:

* Volumes configureren, een back-up maken en verwijderen.
* Configureer volumegroepen om ervoor te zorgen dat back-upgegevens toepassingsconsistent zijn.
* Beheer back-upbeleid, zodat er een back-up van gegevens wordt gemaakt volgens een vooraf bepaalde planning.
* Maak lokale en cloudsnapshots, die in de cloud kunnen worden opgeslagen en kunnen worden gebruikt voor noodherstel.

De StorSimple Snapshot Manager haalt de lijst op met aanvragen die zijn geregistreerd bij de VSS-provider op de host. Als u vervolgens toepassingsconsistente back-ups wilt maken, controleert u de volumes die door een toepassing worden gebruikt en stelt het volumegroepen voor om te configureren. StorSimple Snapshot Manager gebruikt deze volumegroepen om back-ups te genereren die toepassingsconsistent zijn. (Consistentie van de toepassing bestaat wanneer alle gerelateerde bestanden en databases worden gesynchroniseerd en de werkelijke status van de toepassing op een bepaald moment weergeven.) 

StorSimple Snapshot Manager back-ups nemen de vorm aan van incrementele momentopnamen, die alleen de wijzigingen vastleggen sinds de laatste back-up. Hierdoor hebben back-ups minder opslagruimte nodig en kunnen ze snel worden gemaakt en hersteld. StorSimple Snapshot Manager gebruikt de Windows Volume Shadow Copy Service (VSS) om ervoor te zorgen dat momentopnamen toepassingsconsistente gegevens vastleggen. (Ga voor meer informatie naar de sectie Integratie met Windows Volume Shadow Copy Service.) Met StorSimple Snapshot Manager u back-upschema's maken of direct back-ups maken als dat nodig is. Als u gegevens uit een back-up wilt herstellen, u met StorSimple Snapshot Manager kiezen uit een catalogus met lokale of cloudmomentopnamen. Azure StorSimple herstelt alleen de gegevens die nodig zijn als nodig is, waardoor vertragingen in de beschikbaarheid van gegevens tijdens herstelbewerkingen worden voorkomen.)

![StorSimple Snapshot Manager-architectuur](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**StorSimple Snapshot Manager-architectuur** 

## <a name="support-for-multiple-volume-types"></a>Ondersteuning voor meerdere volumetypen
U de StorSimple Snapshot Manager gebruiken om de volgende typen volumes te configureren en een back-up te maken: 

* **Basisvolumes** – Een basisvolume is een enkele partitie op een basisschijf. 
* **Eenvoudige volumes** – Een eenvoudig volume is een dynamisch volume dat schijfruimte van één dynamische schijf bevat. Een eenvoudig volume bestaat uit één gebied op een schijf of meerdere regio's die op dezelfde schijf met elkaar zijn verbonden. (U alleen eenvoudige volumes maken op dynamische schijven.) Eenvoudige volumes zijn niet fout tolerant.
* **Dynamische volumes** – Een dynamisch volume is een volume dat is gemaakt op een dynamische schijf. Dynamische schijven gebruiken een database om informatie bij te houden over volumes die zich op dynamische schijven in een computer bevinden. 
* **Dynamische volumes met mirroring** – Dynamische volumes met mirroring zijn gebaseerd op de RAID 1-architectuur. Met RAID 1 worden identieke gegevens op twee of meer schijven geschreven, waardoor een gespiegelde set wordt geproduceerd. Een leesaanvraag kan vervolgens worden afgehandeld door elke schijf die de gevraagde gegevens bevat.
* **Gedeelde clustervolumes** – Met clustergedeelde volumes (CVS's) kunnen meerdere knooppunten in een failovercluster tegelijkertijd lezen of schrijven naar dezelfde schijf. Failover van het ene knooppunt naar het andere knooppunt kan snel optreden, zonder dat een wijziging in het eigendom van de schijf of het monteren, demonteren en verwijderen van een volume vereist is. 

> [!IMPORTANT]
> Meng geen CSV's en niet-CSV's in dezelfde momentopname. Het mengen van CSV's en niet-CSV's in een momentopname wordt niet ondersteund. 
> 
> 

U StorSimple Snapshot Manager gebruiken om hele volumegroepen te herstellen of afzonderlijke volumes te klonen en afzonderlijke bestanden te herstellen.

* [Volumes en volumegroepen](#volumes-and-volume-groups) 
* [Back-uptypen en back-upbeleid](#backup-types-and-backup-policies) 

Zie de gebruikersinterface van [StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md)voor meer informatie over de functies van StorSimple Snapshot Manager en hoe deze te gebruiken.

## <a name="volumes-and-volume-groups"></a>Volumes en volumegroepen
Met StorSimple Snapshot Manager maakt u volumes en configureert u deze in volumegroepen. 

StorSimple Snapshot Manager gebruikt volumegroepen om back-ups te maken die toepassingsconsistent zijn. Toepassingsconsistentie bestaat wanneer alle gerelateerde bestanden en databases worden gesynchroniseerd en de werkelijke status van een toepassing op een bepaald moment weergeven. Volumegroepen (die ook wel *consistentiegroepen*worden genoemd) vormen de basis van een back-up- of hersteltaak.

Volumegroepen zijn niet hetzelfde als volumecontainers. Een volumecontainer bevat een of meer volumes die een cloudopslagaccount en andere kenmerken delen, zoals versleuteling en bandbreedteverbruik. Een container met één volume kan maximaal 256 dun ingerichte StorSimple-volumes bevatten. Ga voor meer informatie over volumecontainers naar [Uw volumecontainers beheren.](storsimple-manage-volume-containers.md) Volumegroepen zijn verzamelingen van volumes die u configureert om back-upbewerkingen te vergemakkelijken. Als u twee volumes selecteert die tot verschillende volumecontainers behoren, plaatst u deze in één volumegroep en maakt u vervolgens een back-upbeleid voor die volumegroep, wordt er een back-up van elk volume gemaakt in de juiste volumecontainer met behulp van het juiste opslagaccount.

> [!NOTE]
> Alle volumes in een volumegroep moeten afkomstig zijn van één cloudserviceprovider.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integratie met Windows Volume Shadow Copy Service
StorSimple Snapshot Manager gebruikt de WINDOWS Volume Shadow Copy Service (VSS) om toepassingsconsistente gegevens vast te leggen. VSS vergemakkelijkt de consistentie van toepassingen door te communiceren met VSS-bewuste toepassingen om het maken van incrementele momentopnamen te coördineren. VSS zorgt ervoor dat de toepassingen tijdelijk inactief of rustig zijn wanneer momentopnamen worden gemaakt. 

De StorSimple Snapshot Manager-implementatie van VSS werkt met SQL Server en generieke NTFS-volumes. Het proces is als volgt: 

1. Een aanvrager, die meestal een oplossing voor gegevensbeheer en -beveiliging is (zoals StorSimple Snapshot Manager) of een back-uptoepassing, beroept zich op VSS en vraagt om informatie te verzamelen van de schrijversoftware in de doeltoepassing.
2. VSS neemt contact op met de writer component om een beschrijving van de gegevens op te halen. De schrijver geeft de beschrijving van de gegevens terug om een back-up te maken. 
3. VSS signalen van de schrijver om de toepassing voor te bereiden op back-up. De schrijver bereidt de gegevens voor op back-up door het invullen van open transacties, het bijwerken van transactielogboeken, enzovoort, en vervolgens waarschuwt VSS.
4. VSS instrueert de schrijver om de gegevensopslag van de toepassing tijdelijk te stoppen en ervoor te zorgen dat er geen gegevens naar het volume worden geschreven terwijl de schaduwkopie wordt gemaakt. Deze stap zorgt voor consistentie van de gegevens en duurt niet meer dan 60 seconden.
5. VSS instrueert de provider om de schaduwkopie te maken. Providers, die op software of hardware kunnen zijn gebaseerd, beheren de volumes die momenteel worden uitgevoerd en maken schaduwkopieën van hen op aanvraag. De provider maakt de schaduwkopie en waarschuwt VSS wanneer deze is voltooid.
6. VSS neemt contact op met de schrijver om de toepassing te melden dat I/O kan worden hervat en ook om te bevestigen dat I/O met succes is onderbroken tijdens het maken van schaduwkopieën. 
7. Als de kopie is geslaagd, geeft VSS de locatie van de kopie terug aan de aanvrager. 
8. Als gegevens zijn geschreven terwijl de schaduwkopie is gemaakt, is de back-up inconsistent. VSS verwijdert de schaduwkopie en waarschuwt de aanvrager. De aanvrager kan het back-upproces automatisch herhalen of de beheerder op de hoogte stellen om het op een later tijdstip opnieuw te proberen.

Zie de volgende illustratie.

![VSS-proces](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Windows Volume Shadow Copy-serviceproces** 

## <a name="backup-types-and-backup-policies"></a>Back-uptypen en back-upbeleid
Met StorSimple Snapshot Manager u een back-up maken van gegevens en deze lokaal en in de cloud opslaan. U StorSimple Snapshot Manager gebruiken om direct een back-up van gegevens te maken, of u een back-upbeleid gebruiken om een planning te maken voor het automatisch maken van back-ups. Met back-upbeleid u ook opgeven hoeveel momentopnamen worden bewaard. 

### <a name="backup-types"></a>Back-uptypen
U StorSimple Snapshot Manager gebruiken om de volgende typen back-ups te maken:

* **Lokale momentopnamen** : lokale momentopnamen zijn point-in-time kopieën van volumegegevens die zijn opgeslagen op het StorSimple-apparaat. Dit type back-up kan doorgaans snel worden gemaakt en hersteld. U een lokale momentopname gebruiken zoals u een lokale back-upkopie zou maken.
* **Cloudmomentopnamen** – Cloudsnapshots zijn point-in-time kopieën van volumegegevens die in de cloud zijn opgeslagen. Een cloudmomentopname is gelijk aan een momentopname die wordt gerepliceerd op een ander, off-site opslagsysteem. Cloudsnapshots zijn vooral handig in scenario's voor noodherstel.

### <a name="on-demand-and-scheduled-backups"></a>On-demand en geplande back-ups
Met StorSimple Snapshot Manager u een eenmalige back-up starten die onmiddellijk moet worden gemaakt, of u een back-upbeleid gebruiken om terugkerende back-upbewerkingen te plannen.

Een back-upbeleid is een set geautomatiseerde regels die u gebruiken om regelmatige back-ups te plannen. Met een back-upbeleid u de frequentie en parameters definiëren voor het maken van momentopnamen van een specifieke volumegroep. U beleidsregels gebruiken om begin- en vervaldatums, tijden, frequenties en bewaarvereisten op te geven voor zowel lokale als cloudmomentopnamen. Een beleid wordt direct na het definiëren toegepast. 

U StorSimple Snapshot Manager gebruiken om back-upbeleid te configureren of opnieuw te configureren wanneer dat nodig is. 

U configureert de volgende gegevens voor elk back-upbeleid dat u maakt:

* **Naam** : de unieke naam van het geselecteerde back-upbeleid.
* **Type** : het type back-upbeleid; lokale momentopname of cloudmomentopname.
* **Volumegroep** : de volumegroep waaraan het geselecteerde back-upbeleid is toegewezen.
* **Retentie** – Het aantal back-ups dat moet worden bewaard. Als u het selectievakje **Alles** inschakelt, worden alle back-upkopieën bewaard totdat het maximum aantal back-upkopieën per volume is bereikt, waarna het beleid mislukt en een foutbericht genereert. U ook een aantal back-ups opgeven die u wilt behouden (tussen 1 en 64).
* **Datum** : de datum waarop het back-upbeleid is gemaakt.

Ga naar [StorSimple Snapshot Manager gebruiken om back-upbeleid te maken en te beheren voor](storsimple-snapshot-manager-manage-backup-policies.md)informatie over het configureren van back-upbeleid.

### <a name="backup-job-monitoring-and-management"></a>Back-up taakbewaking en -beheer
U de StorSimple Snapshot Manager gebruiken om aankomende, geplande en voltooide back-uptaken te controleren en te beheren. Daarnaast biedt StorSimple Snapshot Manager een catalogus met maximaal 64 voltooide back-ups. U de catalogus gebruiken om volumes of afzonderlijke bestanden te zoeken en te herstellen. 

Ga naar [StorSimple Snapshot Manager gebruiken om back-uptaken weer te geven en te beheren voor](storsimple-snapshot-manager-manage-backup-jobs.md)informatie over het controleren van back-uptaken.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het gebruik van StorSimple Snapshot Manager om uw StorSimple-oplossing te beheren.](storsimple-snapshot-manager-admin.md)
* [StorSimple Snapshot Manager downloaden](https://www.microsoft.com/download/details.aspx?id=44220).

