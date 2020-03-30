---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f84fe995e65d2b67aaaf4ff9acc4a6a44ce607dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176075"
---
> [!NOTE]
> Wanneer u wijzigingen aanbrengt in de StorSimple-adapter voor SharePoint RBS-configuratie, moet u zijn aangemeld met een gebruikersaccount dat deel uitmaakt van de groep Domeinadministrators. Bovendien moet u toegang krijgen tot de configuratiepagina vanuit een browser die op dezelfde host wordt uitgevoerd als Central Administration.
> 
> 

#### <a name="to-configure-rbs"></a>RBS configureren
1. Open de pagina Centraal Beheer van SharePoint en blader naar **Systeeminstellingen**. 
2. Klik in de sectie **Azure StorSimple** op **StorSimple-adapter configureren**.
   
    ![De StorSimple-adapter configureren](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. Ga als een op de pagina **StorSimple-adapter configureren:**
   
   1. Controleer of het selectievakje **Bewerkingspad inschakelen** is ingeschakeld.
   2. Typ in het tekstvak het UNC-pad (Universal Naming Convention) van het BLOB-archief.
      
      > [!NOTE]
      > Het blob-winkelvolume moet worden gehost op een iSCSI-volume dat is geconfigureerd op het StorSimple-apparaat.

   3. Klik **op** de knop Inschakelen onder elk van de inhoudsdatabases die u wilt configureren voor externe opslag.
      
      > [!NOTE]
      > De BLOB-winkel moet worden gedeeld en bereikbaar zijn voor alle WFE-servers (Web Front-End) en het gebruikersaccount dat is geconfigureerd voor de SharePoint-serverfarm moet toegang hebben tot het aandeel.
      
      ![De RBS-provider inschakelen](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      Wanneer u RBS in- of uitschakelt, ziet u ook het volgende bericht.
      
      ![StorSimple-adapter uitschakelen configureren](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Klik **op** de knop Bijwerken om de configuratie toe te passen. Wanneer u op de knop **Bijwerken** klikt, wordt de RBS-configuratiestatus bijgewerkt op alle WFE-servers en is de hele farm rbs-ingeschakeld. Het volgende bericht wordt weergegeven.
      
      ![Configuratiebericht adapter](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Als u RBS configureert voor een SharePoint-farm met een zeer groot aantal databases (meer dan 200), kan de webpagina van SharePoint Central Administration een time-out krijgen. Als dat gebeurt, vernieuwt u de pagina. Dit heeft geen invloed op het configuratieproces.

4. Controleer de configuratie:
   
   1. Meld u aan bij de sharepoint central administration-website en blader naar de pagina **StorSimple-adapter configureren.**
   2. Controleer de configuratiegegevens om te controleren of deze overeenkomen met de instellingen die u hebt ingevoerd. 
5. Controleer of RBS correct werkt:
   
   1. Een document uploaden naar SharePoint. 
   2. Blader naar het UNC-pad dat u hebt geconfigureerd. Zorg ervoor dat de RBS-mapstructuur is gemaakt en dat het geüploade object is opgenomen.
6. (Optioneel) U de Microsoft `Migrate()` RBS PowerShell-cmdlet die bij SharePoint is meegeleverd, gebruiken om bestaande BLOB-inhoud te migreren naar het StorSimple-apparaat. Zie [Inhoud migreren naar of uit RBS in SharePoint 2013][6] of Inhoud migreren naar of uit [RBS (SharePoint Foundation 2010)][7]voor meer informatie.
7. (Optioneel) Op testinstallaties u controleren of de BLOB's als volgt uit de inhoudsdatabase zijn gehaald: 
   
   1. Start SQL Management Studio.
   2. Voer de query ListBlobsInDB_2010.sql of ListBlobsInDB_2013.sql als volgt uit.
      
      ```
      **ListBlobsInDB_2013.sql**
      
        USE WSS_Content
        GO
      
        SELECT DocStreams.DocId,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               DocStreams.RbsId,
               TimeLastModified
      
        FROM DocStreams
      
             INNER JOIN AllDocs ON DocStreams.DocId = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      
      **ListBlobsInDB_2010.sql**
      
        USE WSS_Content
        GO
      
        SELECT AllDocStreams.Id,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               RbsId,
               TimeLastModified
        FROM AllDocStreams
      
             INNER JOIN AllDocs ON AllDocStreams.Id = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      ```
      
      Als RBS correct is geconfigureerd, moet een NULL-waarde worden weergegeven in de kolom SizeOfContentInDB voor elk object dat is geüpload en met succes is geexternaliseerd met RBS.
8. (Optioneel) Nadat u RBS hebt geconfigureerd en alle BLOB-inhoud naar het StorSimple-apparaat hebt verplaatst, u de inhoudsdatabase naar het apparaat verplaatsen. Als u ervoor kiest de inhoudsdatabase te verplaatsen, raden we u aan de opslag van inhoudsgegevensop het apparaat als primair volume te configureren. Gebruik vervolgens gevestigde SQL Server-aanbevolen procedures om de inhoudsdatabase te migreren naar het StorSimple-apparaat. 
   
   > [!NOTE]
   > Het verplaatsen van de inhouddatabase naar het apparaat wordt alleen ondersteund voor de StorSimple 8000-serie (deze wordt niet ondersteund voor de 5000- of 7000-serie).
   
   Als u BLOB's en de inhoudsdatabase in afzonderlijke volumes op het StorSimple-apparaat opslaat, raden we u aan deze in dezelfde volumecontainer te configureren. Dit zorgt ervoor dat er samen een back-up van wordt gemaakt.
   
   > [!WARNING]
   > Als u RBS niet hebt ingeschakeld, raden we u aan de inhoudsdatabase naar het StorSimple-apparaat te verplaatsen. Dit is een niet-geteste configuratie.
   
9. Ga naar de volgende stap: [Garbage Collection configureren.](#configure-garbage-collection)

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx
