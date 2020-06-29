---
title: Gegevens migreren van de StorSimple 5000-7000-serie naar Azure File Sync | Microsoft Docs
description: Hierin wordt beschreven hoe u gegevens migreert van de StorSimple 5000/7000-serie naar Azure File Sync (AFS).
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 5d656fd8757580b8ce96acf168e92fc847d400ca
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/28/2020
ms.locfileid: "85514062"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Gegevens migreren van de StorSimple 5000-7000-serie naar Azure File Sync

> [!IMPORTANT]
> Op 9 juli 2019 de StorSimple-serie van 5000/7000 om de status van het end of support (EOS) te bereiken. We raden aan dat klanten van de StorSimple 5000/7000-serie migreren naar een van de alternatieven die in het document worden beschreven.

Gegevens migratie is het proces van het verplaatsen van gegevens van de ene opslag locatie naar de andere. Dit houdt in dat u een exacte kopie van de huidige gegevens van een organisatie van het ene naar het andere apparaat kunt maken, bij voor keur zonder de actieve toepassingen te onderbreken of uit te scha kelen, en vervolgens alle I/O-activiteit (invoer/uitvoer) naar het nieuwe apparaat om te leiden. 

Opslag apparaten uit de StorSimple 5000-en 7000-serie zullen het eind van de service in juli 2019 bereiken. Dit impliceert dat micro soft de hardware en software voor de StorSimple 5000/7000-serie na juli 2019 niet meer kan ondersteunen. Klanten die deze apparaten gebruiken, moeten hun StorSimple-gegevens migreren naar andere hybride opslag oplossingen op Azure. In dit artikel wordt de migratie van gegevens van een StorSimple 5000/7000 series-apparaat naar Azure File Sync (AFS) behandeld.

## <a name="intended-audience"></a>Doelgroep

Dit artikel is bedoeld voor IT-professionals en kennis werkers die verantwoordelijk zijn voor het implementeren en beheren van StorSimple 5000/7000 series-apparaten in het Data Center. De klanten die hun StorSimple-apparaten gebruiken voor bestands server werkbelastingen (met Windows Server), kunnen dit migratie traject in het bijzonder aantrekkelijker maken. Als u van mening bent dat de functies van Azure File Sync goed werken voor uw organisatie, kunt u in dit artikel lezen hoe u deze oplossingen van StorSimple kunt verplaatsen.

## <a name="migration-considerations"></a>Overwegingen bij migratie

Dit proces werkt voor klanten die een Windows-bestands share hebben geconfigureerd met behulp van een StorSimple-volume voor opslag. Voor het migreren van gegevens van StorSimple 5000/7000 naar Azure File Sync moet u de locatie van de bestands share converteren naar een [Server eindpunt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) en vervolgens lokaal gekoppelde stations gebruiken als een ander eind punt dat vervolgens uw nieuwe locatie wordt. 

Bij het overstappen op AFS moeten de volgende punten in overweging worden genomen:

1. Azure Files heeft momenteel een beperking van 5 TB/share. Deze beperking kan worden Weggen Omen door Azure File Sync met gegevens verspreiding over meerdere Azure-bestands shares te gebruiken. Raadpleeg het [gegevens groei patroon voor Azure files implementatie](https://docs.microsoft.com/azure/storage/files/storage-files-planning)voor meer informatie.
2. Met deze migratie wordt de volledige primaire gegevensset gedownload naar een on-premises apparaat, omdat de gegevens kopie wordt uitgevoerd vanaf het on-premises apparaat. Zorg ervoor dat u voldoende band breedte hebt om deze overdracht te kunnen ondersteunen.
3. Met dit proces blijven de moment opnamen die al zijn gemaakt, niet bewaard. De primaire gegevens worden alleen gemigreerd. Het proces behoudt ook de gekoppelde bandbreedte sjablonen of het back-upbeleid niet. [Gebruik Azure backup](https://docs.microsoft.com/azure/backup/backup-azure-files) om het back-upbeleid in te stellen nadat de gegevens zijn gemigreerd op de Azure-bestands share.
4. StorSimple biedt de eerste partij hardware. Met Azure Files/Azure File Sync gebruikt u echter uw eigen lokale Windows Server-hardware als lokale cache. U moet ervoor zorgen dat u voldoende opslag capaciteit hebt om de gegevensset van uw lokale keuze te bewaren. Lees hoe u [een server eindpunt maakt bij het implementeren van Azure file sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal)voor meer informatie over het trapsgewijs scha kelen en het instellen van de vereiste beschik bare ruimte. 
5. Bekijk de [prijzen voor Azure file sync](https://azure.microsoft.com/pricing/details/storage/files/) , aangezien deze variëren van StorSimple. AFS heeft geen ontdubbeling en compressie, zoals StorSimple.

## <a name="migration-prerequisites"></a>Migratie vereisten

Hier vindt u de migratie vereisten voor uw verouderde 5000-of 7000-serie apparaat Azure File Sync. Voordat u begint, moet u het volgende hebben:

- Toegang tot een StorSimple 5000/7000 series-apparaat waarop software versie v 2.1.1.518 of hoger wordt uitgevoerd. Eerdere versies worden niet ondersteund. In de rechter bovenhoek van de Web-UI van uw StorSimple-apparaat moet de software versie worden weer gegeven die wordt uitgevoerd.  
    Als op uw apparaat geen v-2.1.1.518 wordt uitgevoerd, moet u uw systeem upgraden naar de vereiste minimale versie. Raadpleeg [uw systeem upgraden naar v 2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518)voor gedetailleerde instructies.
- Controleer op actieve back-uptaken die worden uitgevoerd op het bron apparaat. Dit omvat de taken op de StorSimple Data Protection-host van de console. Wacht totdat de huidige taken zijn voltooid. 
- Toegang tot een Windows Server-host die is verbonden met uw StorSimple 5000-7000 series-apparaat. Op de host moet een ondersteunde versie van Windows Server worden uitgevoerd, zoals beschreven in [Azure file sync interoperabiliteit](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning).
- StorSimple-volumes worden op de host gekoppeld en bevatten bestands shares.
- De host heeft voldoende lokale opslag om uw lokaal opgeslagen gegevens in de cache te bewaren.
- Toegang op eigenaarniveau van het Azure-abonnement dat u gaat gebruiken om Azure File Sync te implementeren. U kunt problemen ondervinden bij het maken van een Cloud eindpunt voor uw synchronisatie groep als u geen machtigingen voor de eigenaar of het administrator niveau hebt.
- Toegang tot een [v2-opslag account voor algemeen gebruik](https://docs.microsoft.com/azure/storage/common/storage-account-overview) met een Azure-bestands share die u wilt synchroniseren. Zie [Een opslagaccount maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) voor meer informatie.
  - [Een Azure-bestands share maken](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

## <a name="migration-process"></a>Migratieproces

Het migreren van gegevens van StorSimple 5000-7000 naar AFS is een proces dat uit twee stappen bestaat:
1.  Repliceer de gegevens van de on-premises Bestands server waar de StorSimple-volumes zijn gekoppeld aan een Azure Files share.  Replicatie wordt uitgevoerd via een AFS-agent die u installeert.
2.  Verbreek de verbinding met het StorSimple-apparaat. De lokale schijven fungeren vervolgens als lokale cache.

### <a name="migration-steps"></a>Migratiestappen

Voer de volgende stappen uit om de Windows-bestands share die is geconfigureerd op StorSimple-volumes te migreren naar een Azure File Sync share. 
1.  Voer de volgende stappen uit op dezelfde Windows Server-host waar de StorSimple-volumes zijn gekoppeld of een ander systeem gebruiken. 
    - [Windows Server voorbereiden op gebruik met Azure file sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync).
    - [Installeer de Azure file sync-agent](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent).
    - [Implementeer de opslag synchronisatie service](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service). 
    - [Windows Server bij de opslag synchronisatie service registreren](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service). 
    - [Maak een synchronisatie groep en een eind punt in de Cloud](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint). Synchronisatie groepen moeten worden gemaakt voor elke Windows-bestands share die van de host moet worden gemigreerd.
    - [Een server eindpunt maken](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint). Geef het pad op naar het StorSimple-volume dat de bestands share gegevens bevat. Als het StorSimple-volume bijvoorbeeld station is `J` en uw gegevens zich in bevinden `J:/<myafsshare>` , voegt u dit pad toe als server eindpunt. Zorg ervoor **Tiering** dat de lagen **uitgeschakeld**blijven.
2.  Wacht tot de synchronisatie van de bestands server is voltooid. Voor elke server in een bepaalde synchronisatie groep moet u het volgende doen:
    - De tijds tempels voor de laatste synchronisatie poging voor zowel uploaden als downloaden zijn recent.
    - De status is groen voor uploaden en downloaden.
    - De **synchronisatie activiteit** toont weinig of geen bestanden om te synchroniseren.
    - De **bestanden die niet worden gesynchroniseerd** , zijn 0 voor uploaden en downloaden.
    Ga voor meer informatie over wanneer de synchronisatie van de server is voltooid naar [problemen oplossen Azure file sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other). De synchronisatie kan enkele uren in beslag nemen, afhankelijk van de grootte en band breedte van uw gegevens. Zodra de synchronisatie is voltooid, zijn al uw gegevens veilig in de Azure-bestands share. 
3.  Ga naar de shares op de StorSimple-volumes. Selecteer een share, klik met de rechter muisknop en selecteer **Eigenschappen**. Noteer de share machtigingen onder **beveiliging**. Deze machtigingen moeten hand matig worden toegepast op de nieuwe share in de volgende stap.
4.  Afhankelijk van of u dezelfde Windows Server-host of een ander gebruikt, zullen de volgende stappen verschillen.

    Sla deze stap over en ga naar de volgende stap als u een andere Windows Server-host gebruikt. Als u dezelfde Windows-Bestands server gebruikt voor AFS, treedt er een paar minuten uitval tijd op. 
    - **Downtime start** : Verwijder het server-eind punt dat u hebt gemaakt in *stap 1F*. 
    - Maak een nieuw server eindpunt met het pad naar de locatie waar u de gegevens wilt opslaan.
    - Zodra het eind punt van de server in orde is (dit kan enkele minuten duren), worden de gegevens op deze nieuwe locatie weer gegeven. U kunt de Windows Server-host nu configureren voor het leveren van bestanden vanaf deze nieuwe locatie. - **Uitval tijd eindigt**.
5.  Als u een andere Windows-Bestands server gebruikt voor Azure File Sync, treedt er geen uitval tijd op. 
    - Voeg nog een server eindpunt toe met het pad van de lokale opslag die u hebt voor bereid voor gebruik als cache in plaats van het StorSimple-apparaat. 
    - U kunt de bestanden in de nieuwe server binnen enkele minuten weer geven. U kunt op elk gewenst moment de switch van uw StorSimple-apparaat naar deze nieuwe locatie op de host maken.

    > [!TIP] 
    > Overweeg om deze nieuwe bestands share te configureren met dezelfde naam en hetzelfde pad als die die wordt vervangen, om onderbrekingen tot een minimum te beperken. Als u DFS-N gebruikt, moet u mogelijk wijzigingen aanbrengen in de configuratie.
6.  Configureer de machtigingen voor delen opnieuw, zoals vermeld in *stap 3*.

Als u problemen ondervindt tijdens de gegevens migratie, neemt u [contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md). 



## <a name="next-steps"></a>Volgende stappen

Als AFS niet de juiste oplossing voor u is, leert u hoe u [gegevens migreert van een StorSimple 5000-7000-serie naar een 8000 Series-apparaat](storsimple-8000-migrate-from-5000-7000.md).

