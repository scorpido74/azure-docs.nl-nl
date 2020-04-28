---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 00d292b3ba2d1b6c7c425d4c9f89188e660ac80d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73182203"
---
In deze procedure gaat u als volgt te werk:

1. [Voorbereiden op het uitvoeren van het uitvoer bare bestand van de onderhouds programma](#to-prepare-to-run-the-maintainer) .
2. [Bereid de inhouds database en Prullenbak voor voor onmiddellijke verwijdering van zwevende blobs](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).
3. [Voer maintainer. exe uit](#to-run-the-maintainer).
4. [De instellingen voor de inhouds database en de Prullenbak herstellen](#to-revert-the-content-database-and-recycle-bin-settings).

#### <a name="to-prepare-to-run-the-maintainer"></a>Voorbereiden om de onderhouds programma uit te voeren
1. Open de share point 2013-beheer shell op de front-endwebserver van de web-server als beheerder.
2. Ga naar het *opstart station*van de map: \Program Files\Microsoft SQL Remote Blob Storage 10,50 \ maintainer\.
3. Wijzig de naam van **micro soft. data. SqlRemoteBlobs. maintainer. exe. config** in **Web. config**.
4. Gebruiken `aspnet_regiis -pdf connectionStrings` om het bestand Web. config te ontsleutelen.
5. Voeg in het ontsleutelde web. config-bestand `connectionStrings` onder het knoop punt de Connection String voor uw SQL Server-exemplaar en de naam van de inhouds database toe. Zie het volgende voorbeeld
   
    `<add name="RBSMaintainerConnectionWSSContent" connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. Gebruiken `aspnet_regiis –pef connectionStrings` om het bestand Web. config opnieuw te versleutelen. 
7. Wijzig de naam van web. config in micro soft. data. SqlRemoteBlobs. maintainer. exe. config. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>De inhouds database en Prullenbak voorbereiden om zwevende BLOBs onmiddellijk te verwijderen
1. Voer op het SQL Server in SQL Management Studio de volgende bijwerk query's uit voor de data base met doel inhoud: 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. Op de front-end-webserver, onder **Centraal beheer**, bewerkt u de **algemene instellingen voor de webtoepassing** voor de gewenste inhouds database om de Prullenbak tijdelijk uit te scha kelen. Met deze actie wordt ook de Prullenbak voor alle gerelateerde site verzamelingen leeg gelaten. Hiertoe klikt u op **Centraal beheer** -> **toepassings beheer** -> **webtoepassingen (webtoepassingen beheren)** -> **share point-80** -> **algemene toepassings instellingen**. Stel de **status** van de Prullenbak in op **uit**.
   
    ![Algemene instellingen voor webtoepassingen](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>De maintainer uitvoeren
* Voer op de front-end-webserver in de share point 2013-beheer shell de maintainer als volgt uit:
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Op dit `GarbageCollection` moment wordt alleen de bewerking ondersteund voor StorSimple. Houd er ook rekening mee dat de para meters die zijn uitgegeven voor micro soft. data. SqlRemoteBlobs. maintainer. exe hoofdletter gevoelig zijn. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>De instellingen voor de inhouds database en de Prullenbak herstellen
1. Voer op het SQL Server in SQL Management Studio de volgende bijwerk query's uit voor de data base met doel inhoud:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. Bewerk op de front-end-webserver in **Centraal beheer**de **algemene instellingen voor de webtoepassing** voor de gewenste inhouds database om de Prullenbak weer in te scha kelen. Hiertoe klikt u op **Centraal beheer** -> **toepassings beheer** -> **webtoepassingen (webtoepassingen beheren)** -> **share point-80** -> **algemene toepassings instellingen**. Stel de status van de Prullenbak **in op aan**.

