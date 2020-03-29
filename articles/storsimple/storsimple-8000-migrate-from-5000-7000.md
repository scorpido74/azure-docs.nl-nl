---
title: Gegevens migreren op de StorSimple 5000-7000-serie naar apparaten uit de 8000-serie| Microsoft Documenten
description: Biedt een overzicht en de vereisten van de migratiefunctie.
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
ms.date: 08/23/2018
ms.author: alkohli
ms.openlocfilehash: 967c03f3c4201bdcf1529fdda93717b6eb74e771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60631652"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Gegevens migreren van de StorSimple 5000-7000-serie naar een apparaat uit de 8000-serie

> [!IMPORTANT]
> - Op 31 juli 2019 bereikt de StorSimple 5000/7000-serie de status end of support (EOS). We raden klanten uit de StorSimple 5000/7000-serie aan te migreren naar een van de alternatieven die in het document worden beschreven.
> - Migratie is momenteel een ondersteunde bewerking. Als u gegevens van uw StorSimple 5000-7000-serie wilt migreren naar een apparaat uit de 8000-serie, moet u migratie plannen met Microsoft Support. Microsoft Support schakelt uw abonnement vervolgens in voor migratie. Zie voor meer informatie hoe [u een supportticket opent.](storsimple-8000-contact-microsoft-support.md)
> - Nadat u de serviceaanvraag hebt ingediend, kan het enkele weken duren voordat het migratieplan is uitgevoerd en de migratie daadwerkelijk is gestart.
> - Voordat u contact opneemt met Microsoft Support, moet u de [migratievereisten](#migration-prerequisites) controleren en voltooien die in het artikel zijn aangegeven.

## <a name="overview"></a>Overzicht

Dit artikel introduceert de migratiefunctie waarmee klanten uit de StorSimple 5000-7000-serie hun gegevens kunnen migreren naar het fysieke apparaat uit de StorSimple 8000-serie of een 8010/8020-cloudtoestel. In dit artikel wordt ook links gemaakt naar een downloadbare stapsgewijze doorloop van de stappen die nodig zijn om gegevens te migreren van een 5000-7000-serie legacy-apparaat naar een fysiek of cloudtoestel uit de 8000-serie.

Dit artikel is van toepassing op zowel het on-premises apparaat uit de 8000-serie als het StorSimple Cloud Appliance.


## <a name="migration-feature-versus-host-side-migration"></a>Migratiefunctie versus migratie aan hostzijde

U uw gegevens verplaatsen met de migratiefunctie of door een migratie aan hostzijde uit te voeren. In dit gedeelte worden de specifieke kenmerken van elke methode beschreven, inclusief de voor- en nadelen. Gebruik deze informatie om erachter te komen welke methode u wilt nastreven om uw gegevens te migreren.

De migratiefunctie simuleert een DR-proces (disaster recovery) van 7000/5000-serie tot 8000-serie. Met deze functie u de gegevens migreren van de indeling van de 5000/7000-serie naar de indeling van de 8000-serie op Azure. Het migratieproces wordt gestart met de tool StorSimple Migration. De tool start de download en de conversie van back-up metadata op de 8000-serie apparaat en vervolgens maakt gebruik van de nieuwste back-up om de volumes bloot op het apparaat.

|      | Voordelen                                                                                                                                     |Nadelen                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Het migratieproces behoudt de geschiedenis van back-ups die zijn genomen op 5000/7000-serie.                                               | Wanneer gebruikers toegang proberen te krijgen tot de gegevens, worden de gegevens van Azure met deze migratie gedownload, waardoor kosten voor het downloaden van gegevens worden opgevraagd.                                     |
| 2.   | Er worden geen gegevens gemigreerd aan de hostzijde.                                                                                                     | Het proces moet downtime tussen het begin van de back-up en de nieuwste back-up wordt opgedoken op de 8000-serie (kan worden geschat met behulp van de migratie tool). |
| 3.   | Met dit proces worden alle beleidsregels, bandbreedtesjablonen, versleuteling en andere instellingen op apparaten uit de 8000-serie behouden.                      | Gebruikerstoegang brengt alleen de gegevens terug die door de gebruikers worden geopend en zal de hele gegevensset niet hydrateren.                                                  |
| 4.   | Dit proces vereist extra tijd om alle oudere back-ups in Azure om te zetten, wat asynchroon wordt uitgevoerd zonder dat dit gevolgen heeft voor de productie | Migratie kan alleen op cloudconfiguratieniveau worden uitgevoerd.  Afzonderlijke volumes in een cloudconfiguratie kunnen niet afzonderlijk worden gemigreerd                       |

Een host-side migratie maakt het opzetten van 8000-serie onafhankelijk en het kopiëren van de gegevens van 5000/7000-serie apparaat naar 8000-serie apparaat. Dit is gelijk aan het migreren van gegevens van het ene opslagapparaat naar het andere. Een verscheidenheid van hulpmiddelen zoals Diskboss, robocopy worden gebruikt om de gegevens te kopiëren.

|      | Voordelen                                                                                                                      |Nadelen                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Migratie kan gefaseerd worden benaderd op volumebasis.                                               | Eerdere back-ups (genomen op 5000/7000-serie) zijn niet beschikbaar op het apparaat uit de 8000-serie.                                                                                                       |
| 2.   | Hiermee u gegevens in één opslagaccount op Azure consolideren.                                                       | De eerste back-up naar de cloud op de 8000-serie duurt langer, omdat alle gegevens over 8000-series moeten worden geback-upt naar Azure.                                                                     |
| 3.   | Na een succesvolle migratie zijn alle gegevens lokaal op het toestel. Er zijn geen latencies bij het openen van de gegevens. | Het verbruik van Azure-opslag neemt toe totdat de gegevens van het 5000/7000-apparaat zijn verwijderd.                                                                                                        |
| 4.   |                                                                                                                           | Als het apparaat uit de 7000/5000-serie een grote hoeveelheid gegevens heeft, moeten deze gegevens tijdens de migratie worden gedownload van azure, wat kosten en latencies met zich meebrengt in verband met het downloaden van gegevens van Azure |

Dit artikel richt zich alleen op de migratiefunctie van 5000/7000 tot 8000-serie. Ga voor meer informatie over migratie aan hostzijde naar [Migratie van andere opslagapparaten](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Migratievereisten

Hier zijn de migratievereisten voor uw oude apparaat uit de 5000- of 7000-serie en het StorSimple-apparaat uit de 8000-serie.

> [!IMPORTANT]
> Bekijk en voltooi de migratievereisten voordat u een serviceaanvraag indient bij Microsoft Support.

### <a name="for-the-50007000-series-device-source"></a>Voor het apparaat uit de 5000/7000-serie (bron)

Voordat u met migratie begint, moet u ervoor zorgen dat:

* Je hebt je bronapparaat uit de 5000- of 7000-serie; het apparaat kan live of omlaag zijn.

    > [!IMPORTANT]
    > We raden u aan gedurende het migratieproces toegang te hebben tot dit apparaat. Als er apparaatproblemen zijn, kan seriële toegang helpen bij het oplossen van problemen.

* Uw bronapparaat uit de 5000- of 7000-serie draait softwareversie v2.1.1.518 of hoger. Eerdere versies worden niet ondersteund.
* Als u wilt controleren of uw 5000- of 7000-serie wordt uitgevoerd, kijkt u naar de rechterbovenhoek van uw webgebruikersinterface. Hiermoet de softwareversie worden weergegeven die uw apparaat uitvoert. Voor migratie moet uw 5000- of 7000-serie v2.1.1.518 worden uitgevoerd.

    ![Softwareversie controleren op verouderd apparaat](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Als uw live-apparaat geen V2.1.1.518 of hoger draait, u uw systeem upgraden naar de vereiste minimale versie. Raadpleeg uw systeem [upgraden naar v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518)voor gedetailleerde instructies.
    * Als u v2.1.1.518 uitvoert, gaat u naar de webgebruikersinterface om te zien of er meldingen zijn voor fouten bij het herstellen van het register. Als registerherstel is mislukt, voert u registerherstel uit. Ga voor meer informatie naar hoe u [registerherstel uitvoert.](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry)
    * Als u een down-apparaat hebt waarop de V2.1.1.518 niet werd uitgevoerd, voert u een failover uit naar een vervangend apparaat waarop v2.1.1.518 wordt uitgevoerd. Voor gedetailleerde instructies, verwijzen naar DR van uw 5000/7000 serie StorSimple apparaat.
    * Een back-up van de gegevens voor uw apparaat maken door een cloudmomentopname te maken.
    * Controleer op andere actieve back-uptaken die op het bronapparaat worden uitgevoerd. Dit geldt ook voor de taken op de StorSimple Data Protection Console-host. Wacht tot de huidige taken zijn voltooid.


### <a name="for-the-8000-series-physical-device-target"></a>Voor de 8000-serie fysiek apparaat (doel)

Voordat u met migratie begint, moet u ervoor zorgen dat:

* Uw doel 8000-serie apparaat is geregistreerd en wordt uitgevoerd. Zie voor meer informatie hoe [u uw StorSimple-apparaat implementeert met de StorSimple Manager-service.](storsimple-8000-deployment-walkthrough-u2.md)
* Uw 8000-serie apparaat heeft de nieuwste StorSimple 8000-serie Update 5 geïnstalleerd en draait 6.3.9600.17845 of later versie. Als uw apparaat niet de nieuwste updates heeft geïnstalleerd, moet u de nieuwste updates installeren voordat u verder gaan met migratie. Zie voor meer informatie hoe u [de nieuwste update op uw apparaat uit de 8000-serie installeert.](storsimple-8000-install-update-5.md)
* Uw Azure-abonnement is ingeschakeld voor migratie. Als uw abonnement niet is ingeschakeld, neemt u contact op met Microsoft Support om uw abonnement voor migratie in te schakelen.

### <a name="for-the-80108020-cloud-appliance-target"></a>Voor het 8010/8020 cloudtoestel (doel)

Voordat u met de migratie begint, moet u ervoor zorgen:

* Uw doelcloudtoestel is geregistreerd en wordt uitgevoerd. Zie voor meer informatie hoe u [StorSimple Cloud Appliance implementeert en beheert.](storsimple-8000-cloud-appliance-u2.md)
* Uw cloudtoestel draait op de nieuwste StorSimple 8000 Series Update 5-softwareversie 6.3.9600.17845. Als uw cloudtoestel update 5 niet uitvoert, maakt u een nieuw cloudtoestel update 5 voordat u verdergaat met migratie. Zie voor meer informatie hoe [u een 8010/8020-cloudtoestel maakt.](storsimple-8000-cloud-appliance-u2.md)

### <a name="for-the-computer-running-storsimple-migration-tool"></a>Voor de computer met het gereedschap StorSimple-migratie

StorSimple Migration tool is een ui-gebaseerde tool waarmee u gegevens migreren van een StorSimple 5000-7000-serie naar een apparaat uit de 8000-serie. Als u het hulpprogramma StorSimple Migration wilt installeren, gebruikt u een computer die aan de volgende vereisten voldoet.

De computer heeft internetverbinding en:

* Is het volgende besturingssysteem actief
    * Windows 10.
    * Windows Server 2012 R2 (of hoger) om het hulpprogramma Voor StorSimple Migration te installeren.
* Heeft .NET 4.5.2 geïnstalleerd.
* Heeft minimaal 5 GB vrije ruimte om de tool te installeren en te gebruiken.

> [!TIP]
> Als uw StorSimple-apparaat is verbonden met een Windows Server-host, u het migratiehulpprogramma installeren op de Windows Server-hostcomputer.

#### <a name="to-install-storsimple-migration-tool"></a>Het hulpprogramma Voor StorSimple-migratie installeren

Voer de volgende stappen uit om het hulpprogramma Voor StorSimple-migratie op uw computer te installeren.

1. Kopieer de map _StorSimple8000SeriesMigrationTool_ naar uw Windows-computer. Zorg ervoor dat het station waar de software wordt gekopieerd voldoende ruimte heeft.

    Open het gereedschap config-bestand _StorSimple8000SeriesMigrationTool.exe.config_ in de map. Hier is het fragment van het bestand.
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Bewerk de waarden die overeenkomen met de toetsen en vervang door:

    * `UserName`– Gebruikersnaam om in te loggen op Azure-portal.
    * `SubscriptionName and SubscriptionId`– Naam en id voor uw Azure-abonnement. Klik op de bestemmingspagina van de StorSimple Device Manager-service onder **Algemeen**op **Eigenschappen**. Kopieer de abonnementsnaam en abonnements-id die aan uw service zijn gekoppeld.
    * `ResourceName`– Naam van uw StorSimple Device Manager-service in de Azure-portal. Ook weergegeven onder service-eigenschappen.
    * `ResourceGroup`– Naam van de resourcegroep die is gekoppeld aan uw StorSimple Device Manager-service in de Azure-portal. Ook weergegeven onder service-eigenschappen.
    ![Service-eigenschappen controleren op doelapparaat](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId`– Azure Active Directory Tenant ID in Azure portal. Meld u aan bij Microsoft Azure als beheerder. Klik in de Microsoft Azure-portal op **Azure Active Directory**. Klik onder **Beheren** op **Eigenschappen**. De tenant-id wordt weergegeven in het vak **Directory-id.**
    ![Tenant-id controleren op Azure Active Directory](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Sla de wijzigingen in het config-bestand op.
4.  Voer de _StorSimple8000SeriesMigrationTool.exe uit_ om de tool te starten. Geef de referenties op die zijn gekoppeld aan uw abonnement in de Azure-portal wanneer u om referenties wordt gevraagd. 
5.  De gebruikersinterface van het hulpprogramma StorSimple Migration wordt weergegeven.
  

## <a name="next-steps"></a>Volgende stappen
Download de stapsgewijze instructies voor het [migreren van gegevens van een StorSimple 5000-7000-serie naar een apparaat uit de 8000-serie.](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b)
