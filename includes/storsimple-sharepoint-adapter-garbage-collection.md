---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 00d292b3ba2d1b6c7c425d4c9f89188e660ac80d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73182203"
---
In deze procedure zult u:

1. [Bereid u voor om de executerexecuter uitvoerbaar uit te voeren.](#to-prepare-to-run-the-maintainer)
2. [Bereid de inhoudsdatabase en prullenbak voor op onmiddellijke verwijdering van verweesde BLOB's.](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs)
3. [Run Maintainer.exe](#to-run-the-maintainer).
4. [De inhoudsdatabase en de instellingen van de Prullenbak terugdraaien](#to-revert-the-content-database-and-recycle-bin-settings).

#### <a name="to-prepare-to-run-the-maintainer"></a>Om de Onderhoudser voor te bereiden
1. Open de SharePoint 2013 Management Shell op de front-endserver van het web als beheerder.
2. Navigeer naar het *opstartstation*voor mappen :\Program Files\Microsoft SQL Remote Blob Storage 10.50\Maintainer\.
3. Wijzig de naam **van Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** naar **web.config**.
4. Met `aspnet_regiis -pdf connectionStrings` gebruiken om het web.config-bestand te decoderen.
5. Voeg in het gedecodeerde web.config-bestand onder het `connectionStrings` knooppunt de verbindingstekenreeks voor uw SQL-server-instantie en de naam van de inhoudsdatabase toe. Zie het volgende voorbeeld
   
    `<add name="RBSMaintainerConnectionWSSContent" connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. Met `aspnet_regiis –pef connectionStrings` gebruiken om het web.config-bestand opnieuw te versleutelen. 
7. Wijzig de naam van web.config naar Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>De inhoudsdatabase en prullenbak voorbereiden om verweesde BLOB's onmiddellijk te verwijderen
1. Voer op de SQL Server in SQL Management Studio de volgende updatequery's uit voor de doelinhoudsdatabase: 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. Bewerk op de front-endserver van het web onder **Centraal beheer**de **algemene instellingen voor webtoepassingen** voor de gewenste inhoudsdatabase om de Prullenbak tijdelijk uit te schakelen. Met deze actie wordt ook de Prullenbak leeggemaakt voor gerelateerde siteverzamelingen. Klik hiervoor op **Webtoepassingen** -> **voor beheertoepassingbeheer** -> **(Webtoepassingen beheren)** -> **SharePoint - 80** -> algemene**toepassingsinstellingen**. Stel de **status van de prullenbak** in op **UIT**.
   
    ![Algemene instellingen voor webtoepassingen](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>De Onderhouder uitvoeren
* Voer op de front-endserver van het web in de SharePoint 2013 Management Shell de Onderhouder als volgt uit:
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Alleen `GarbageCollection` de bewerking wordt op dit moment ondersteund voor StorSimple. Houd er ook rekening mee dat de parameters die zijn uitgegeven voor Microsoft.Data.SqlRemoteBlobs.Maintainer.exe hoofdlettergevoelig zijn. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>De inhoudsdatabase en de instellingen van de Prullenbak terugzetten
1. Voer op de SQL Server in SQL Management Studio de volgende updatequery's uit voor de doelinhoudsdatabase:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. Bewerk op de front-endserver van het web in **Central Administration**de **algemene instellingen voor webtoepassingen** voor de gewenste inhoudsdatabase om de Prullenbak opnieuw in te schakelen. Klik hiervoor op **Webtoepassingen** -> **voor beheertoepassingbeheer** -> **(Webtoepassingen beheren)** -> **SharePoint - 80** -> algemene**toepassingsinstellingen**. De status prullenbak instellen op **AAN**.

