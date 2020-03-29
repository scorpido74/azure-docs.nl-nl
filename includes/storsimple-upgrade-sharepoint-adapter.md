---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: b2dec95e0258933b50d4437f1cb317639b62883d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176078"
---
### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>SharePoint 2010 upgraden naar SharePoint 2013 en installeer vervolgens de StorSomple-adapter voor SharePoint
> [!IMPORTANT]
> Bestanden die eerder via RBS naar externe opslag zijn verplaatst, zijn pas beschikbaar nadat de upgrade is voltooid en de RBS-functie opnieuw is ingeschakeld. Als u de impact van de gebruiker wilt beperken, voert u een upgrade of herinstallatie uit tijdens een gepland onderhoudsvenster.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>SharePoint 2010 upgraden naar SharePoint 2013 en vervolgens de adapter installeren
1. Noteer in de SharePoint 2010-farm het BLOB-winkelpad voor de externe BLOB's en de inhoudsdatabases waarvoor RBS is ingeschakeld. 
2. Installeer en configureer de nieuwe SharePoint 2013-farm. 
3. Databases, toepassingen en siteverzamelingen verplaatsen van het SharePoint 2010-farm naar het nieuwe SharePoint 2013-farm. Ga voor instructies naar [Overzicht van het upgradeproces naar SharePoint 2013](https://technet.microsoft.com/library/cc262483.aspx).
4. Installeer de StorSimple-adapter voor SharePoint op de nieuwe farm. Ga naar [De StorSimple-adapter voor SharePoint](#install-the-storsimple-adapter-for-sharepoint) installeren voor procedures.
5. Met behulp van de informatie die u in stap 1 hebt opgemerkt, schakelt u RBS in voor dezelfde set inhoudsdatabases en biedt u hetzelfde BLOB-winkelpad dat is gebruikt in de SharePoint 2010-installatie. Ga naar [RBS configureren](#configure-rbs) voor procedures. Nadat u deze stap hebt voltooid, moeten eerder geexternaliseerde bestanden toegankelijk zijn vanaf de nieuwe farm. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>De StorSimple-adapter voor SharePoint upgraden
> [!IMPORTANT]
> U moet deze upgrade plannen tijdens een gepland onderhoudsvenster om de volgende redenen:
> 
> * Eerder geexternaliseerde inhoud is pas beschikbaar als de adapter opnieuw is geïnstalleerd.
> * Alle inhoud die naar de site is geüpload nadat u de vorige versie van de StorSimple-adapter voor SharePoint hebt verwijderd, maar voordat u de nieuwe versie installeert, wordt opgeslagen in de inhoudsdatabase. U moet die inhoud naar het StorSimple-apparaat verplaatsen nadat u de nieuwe adapter hebt geïnstalleerd. U de `RBS Migrate()` Microsoft PowerShell-cmdlet die bij SharePoint is meegeleverd, gebruiken om de inhoud te migreren. Zie [Inhoud migreren naar of uit RBS voor](https://technet.microsoft.com/library/ff628255.aspx)meer informatie. 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>De StorSimple-adapter voor SharePoint upgraden
1. Verwijder de vorige versie van StorSimple Adapter voor SharePoint.
   
   > [!NOTE]
   > Hierdoor wordt RBS automatisch uitgeschakeld in de inhoudsdatabases. Bestaande BLOB's blijven echter op het StorSimple-apparaat staan. Omdat RBS is uitgeschakeld en de BLOB's niet zijn gemigreerd naar de inhouddatabases, zullen alle aanvragen voor die BLOB's mislukken. 
   > 
   > 
2. Installeer de nieuwe StorSimple-adapter voor SharePoint. De nieuwe adapter herkent automatisch de inhoudsdatabases die eerder voor RBS waren ingeschakeld of uitgeschakeld en gebruikt de vorige instellingen.

