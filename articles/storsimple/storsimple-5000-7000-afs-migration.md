---
title: Gegevens van de StorSimple 5000-7000-serie migreren naar Azure File Sync| Microsoft Documenten
description: Beschrijft hoe u gegevens migreert van de StorSimple 5000/7000-serie naar Azure File Sync (AFS).
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: b46e9ee8fc3e14981a01cc2425a8ce55d06c5a9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65150738"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Gegevens uit de StorSimple 5000-7000-serie migreren naar Azure File Sync

> [!IMPORTANT]
> Op 9 juli 2019 bereikt de StorSimple 5000/7000-serie de status end of support (EOS). We raden klanten uit de StorSimple 5000/7000-serie aan te migreren naar een van de alternatieven die in het document worden beschreven.

Gegevensmigratie is het proces waarbij gegevens van de ene opslaglocatie naar de andere worden verplaatst. Dit houdt in dat u een exacte kopie maakt van de huidige gegevens van een organisatie van het ene apparaat naar een ander apparaat, bij voorkeur zonder actieve toepassingen te verstoren of uit te schakelen, en vervolgens alle activiteit voor invoer/uitvoer (I/O) omte leiden naar het nieuwe apparaat. 

StorSimple 5000- en 7000-serie opslagapparaten bereiken het einde van de service in juli 2019. Dit houdt in dat Microsoft de hardware en software voor StorSimple 5000/7000-series na juli 2019 niet meer kan ondersteunen. Klanten die deze apparaten gebruiken, moeten hun StorSimple-gegevens migreren naar andere hybride opslagoplossingen op Azure. Dit artikel behandelt de migratie van gegevens van een apparaat uit de StorSimple 5000/7000-serie naar Azure File Sync (AFS).

## <a name="intended-audience"></a>Doelgroep

Dit artikel is bedoeld voor it-professionals en kenniswerkers die verantwoordelijk zijn voor het implementeren en beheren van Apparaten uit de StorSimple 5000/7000-serie in het datacenter. De klanten die hun StorSimple-apparaten gebruiken voor bestandsserverworkloads (met Windows Server) kunnen dit migratiepad bijzonder aantrekkelijk vinden. Als u van mening bent dat de functies van Azure File Sync goed werken voor uw organisatie, helpt dit artikel u te begrijpen hoe u vanuit StorSimple naar die oplossingen gaan.

## <a name="migration-considerations"></a>Overwegingen bij migraties

Dit proces werkt voor klanten die een Windows-bestandsshare hebben geconfigureerd met een StorSimple-volume voor opslag. Als u gegevens migreert van StorSimple 5000/7000 naar Azure File Sync, wordt die locatie voor bestandsshare geconverteerd naar een [servereindpunt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) en vervolgens lokaal gekoppelde stations gebruikt als een ander eindpunt dat vervolgens uw nieuwe locatie wordt. 

Bij de overstap naar AFS moeten de volgende punten in aanmerking worden genomen:

1. Azure Files heeft momenteel een beperking van 5 TB/share. Deze beperking kan worden overwonnen door Azure File Sync te gebruiken met gegevens die zijn verspreid over meerdere Azure-bestandsshares. Bekijk het [groeipatroon gegevens voor azure-bestanden implementatie voor](https://docs.microsoft.com/azure/storage/files/storage-files-planning)meer informatie .
2. Met deze migratie wordt de volledige primaire gegevensset naar een on-premises apparaat gedownload, omdat de gegevenskopie wordt uitgevoerd vanaf het on-premises apparaat. Zorg ervoor dat u voldoende bandbreedte hebt om deze overdracht mogelijk te maken.
3. Dit proces behoudt de momentopnamen die al zijn gemaakt niet. Het migreert alleen de primaire gegevens. Het proces behoudt ook niet de bijbehorende bandbreedtesjablonen of back-upbeleid. [Gebruik Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-files) om het back-upbeleid in te stellen nadat de gegevens zijn gemigreerd in het Azure-bestandsaandeel.
4. StorSimple levert first party hardware. Met Azure Files/Azure File Sync gebruikt u echter uw eigen lokale Windows Server-hardware als lokale cache. U moet ervoor zorgen dat u over voldoende opslagcapaciteit beschikt om de gegevensset van uw keuze lokaal te houden. Voor meer informatie over het tieren en instellen van het vereiste doel voor vrije ruimte, controleert u hoe [u een servereindpunt maakt bij het implementeren van Azure File Sync.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal) 
5. Bekijk de [prijzen voor Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/) omdat deze verschilt van StorSimple. AFS heeft geen ontdubbeling en compressie zoals StorSimple.

## <a name="migration-prerequisites"></a>Migratievereisten

Hier vindt u de migratievereisten voor uw oudere apparaat uit de 5000- of 7000-serie naar Azure File Sync. Voordat u begint, moet u ervoor zorgen dat u:

- Toegang tot een StorSimple 5000/7000-serie apparaat met softwareversie v2.1.1.518 of hoger. Eerdere versies worden niet ondersteund. De rechterbovenhoek van de web-gebruikersinterface van uw StorSimple-apparaat moet de softwareversie weergeven die wordt uitgevoerd.  
    Als uw apparaat geen V2.1.1.518 draait, u uw systeem upgraden naar de vereiste minimale versie. Raadpleeg uw systeem [upgraden naar v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518)voor gedetailleerde instructies.
- Controleer op actieve back-uptaken die worden uitgevoerd op het bronapparaat. Dit geldt ook voor de taken op de StorSimple Data Protection Console-host. Wacht tot de huidige taken zijn voltooid. 
- Toegang tot een Windows Server-host die is verbonden met uw StorSimple 5000-7000-serie apparaat. De host moet een ondersteunde Windows Server-versie uitvoeren zoals beschreven in [azure file sync-interoperabiliteit.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)
- StorSimple volumes zijn gemonteerd op de host en bevatten bestandsshares.
- De host heeft voldoende lokale opslag om uw lokaal opgeslagen gegevens vast te houden.
- Toegang tot het Azure-abonnement dat u gebruikt om Azure File Sync te implementeren. U problemen ondervinden bij het maken van een eindpunt voor de cloud voor uw synchronisatiegroep als u geen machtigingen op eigenaar- of beheerdersniveau hebt.
- Toegang tot een [v2-opslagaccount voor algemene doeleinden](https://docs.microsoft.com/azure/storage/common/storage-account-overview) met een Azure File Share waarmee u wilt synchroniseren. Zie [Een opslagaccount maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) voor meer informatie.
  - Een [Azure-bestandsshare maken](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

## <a name="migration-process"></a>Migratieproces

Het migreren van gegevens van StorSimple 5000-7000 naar AFS is een proces in twee stappen:
1.  Repliceer de gegevens van de on-premises bestandsserver waar de StorSimple-volumes zijn gemonteerd op een Azure-bestandenaandeel.  Replicatie gebeurt via een AFS-agent die u installeert.
2.  Koppel het StorSimple-apparaat los. De lokale schijven fungeren dan als de lokale cache.

### <a name="migration-steps"></a>Migratiestappen

Voer de volgende stappen uit om de Windows-bestandsshare die is geconfigureerd op StorSimple-volumes te migreren naar een Azure File Sync-share. 
1.  Voer deze stappen uit op dezelfde Windows Server-host waarbij de StorSimple-volumes zijn gemonteerd of gebruik een ander systeem. 
    - [Bereid Windows Server voor op gebruik met Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync).
    - [Installeer de Azure File Sync-agent](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent).
    - [De service Storage Sync implementeren](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service). 
    - [Windows Server registreren met de opslagsynchronisatieservice](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service). 
    - [Maak een synchronisatiegroep en een eindpunt in de cloud](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint). Er moeten synchronisatiegroepen worden gemaakt voor elk Windows-bestandsaandeel dat moet worden gemigreerd van de host.
    - [Een servereindpunt maken.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint) Geef het pad op als het pad van het StorSimple-volume dat uw gegevens over bestandsshare bevat. Als het StorSimple-volume bijvoorbeeld `J`station is en `J:/<myafsshare>`uw gegevens zich bevinden in , voegt u dit pad toe als een servereindpunt. Laat de **tiering** als **uitgeschakeld**.
2.  Wacht tot de synchronisatie van de bestandsserver is voltooid. Controleer voor elke server in een bepaalde synchronisatiegroep:
    - De tijdstempels voor de laatste poging tot synchronisatie voor zowel uploaden als downloaden zijn recent.
    - De status is groen voor zowel uploaden als downloaden.
    - De **synchronisatieactiviteit** toont zeer weinig of geen bestanden die nog worden gesynchroniseerd.
    - De **bestanden niet synchroniseren** is 0 voor zowel uploaden als downloaden.
    Ga voor meer informatie over wanneer de serversynchronisatie is voltooid, naar [Azure File Sync oplossen.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other) De synchronisatie kan enkele uren tot dagen duren, afhankelijk van de grootte en bandbreedte van uw gegevens. Zodra de synchronisatie is voltooid, zijn al uw gegevens veilig in het Azure File Share. 
3.  Ga naar de aandelen op de StorSimple volumes. Selecteer een aandeel, klik met de rechtermuisknop en selecteer **Eigenschappen**. Let op de machtigingen voor delen onder **Beveiliging**. Deze machtigingen moeten handmatig worden toegepast op het nieuwe aandeel in de latere stap.
4.  Afhankelijk van of u dezelfde Windows Server-host of een andere host gebruikt, zijn de volgende stappen anders.

    Sla deze stap over en ga naar de volgende stap als u een andere Windows Server-host gebruikt. Als u dezelfde Windows File Server voor AFS gebruikt, zult u nu een paar minuten downtime ervaren. 
    - **Downtime starts** - Verwijder het servereindpunt dat u hebt gemaakt in *stap 1F.* 
    - Maak een nieuw servereindpunt met het pad waar u wilt dat de gegevens zich in de toekomst bevinden.
    - Zodra het servereindpunt als gezond wordt weergegeven (dit kan enkele minuten duren), ziet u de gegevens op deze nieuwe locatie. U nu uw Windows Server-host configureren om bestanden vanaf deze nieuwe locatie weer te geven. - **Downtime eindigt**.
5.  Als u een andere Windows File Server voor Azure File Sync gebruikt, krijgt u geen downtime. 
    - Voeg een ander servereindpunt toe aan het pad van de lokale opslag dat u bereid bent te gebruiken als cache in plaats van het StorSimple-apparaat. 
    - U de bestanden in de nieuwe server in enkele minuten bekijken. U bent vrij om op elk gewenst moment de overstap te maken van uw StorSimple-apparaat naar deze nieuwe locatie op de host.

    > [!TIP] 
    > Overweeg het configureren van deze nieuwe bestandsshare met dezelfde naam en hetzelfde pad als het pad dat het vervangt, om verstoring te minimaliseren. Als u DFS-N gebruikt, moet u mogelijk wijzigingen aanbrengen in de configuratie.
6.  Configureer de machtigingen voor delen opnieuw zoals aangegeven in *stap 3*.

Als u problemen ondervindt tijdens de gegevensmigratie, neemt u [contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md). 



## <a name="next-steps"></a>Volgende stappen

Als AFS niet de juiste oplossing voor u is, leest u hoe u [gegevens migreert van een StorSimple 5000-7000-serie naar een apparaat uit de 8000-serie.](storsimple-8000-migrate-from-5000-7000.md)

