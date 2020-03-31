---
title: .NET installeren op Azure Cloud Services-rollen | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u het .NET Framework handmatig installeren op uw web- en werknemersrollen voor cloudservices
services: cloud-services
documentationcenter: .net
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 06/22/2018
ms.author: tagore
ms.openlocfilehash: c830dc0ee38ad808579a62274e3db87d0696e099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214711"
---
# <a name="install-net-on-azure-cloud-services-roles"></a>.NET installeren op Azure Cloud Services-rollen
In dit artikel wordt beschreven hoe u versies van .NET Framework installeert die niet bij het Azure Guest OS worden geleverd. U .NET op het besturingssysteem van de gast gebruiken om uw web- en werknemersrollen voor cloudservices te configureren.

U bijvoorbeeld .NET Framework 4.6.2 installeren op de Guest OS-familie 4, die niet wordt geleverd met een release van .NET Framework 4.6. (De Guest OS familie 5 komt met .NET Framework 4.6.) Zie het persbericht van [Azure Guest OS](cloud-services-guestos-update-matrix.md)voor de meest recente informatie over de azure guest os-releases. 

>[!IMPORTANT]
>De Azure SDK 2.9 bevat een beperking voor het implementeren van .NET Framework 4.6 op de GastOS-familie 4 of eerder. Er is een oplossing voor de beperking beschikbaar op de [Microsoft Docs-site.](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9)

Als u .NET wilt installeren op uw web- en werknemersrollen, neemt u de webinstaller .NET op als onderdeel van uw cloudserviceproject. Start de installatieinstallatie als onderdeel van de opstarttaken van de rol. 

## <a name="add-the-net-installer-to-your-project"></a>De .NET-installer toevoegen aan uw project
Als u het webprogramma voor het .NET Framework wilt downloaden, kiest u de versie die u wilt installeren:

* [.NET Framework 4.8 webinstaller](https://dotnet.microsoft.com/download/thank-you/net48)
* [.NET Framework 4.7.2 webinstaller](https://go.microsoft.com/fwlink/?LinkId=863262)
* [.NET Framework 4.6.2 webinstaller](https://www.microsoft.com/download/details.aspx?id=53345)

Ga als lid van het installatieprogramma voor een *webrol:*
  1. Klik in **Solution Explorer**onder **Rollen** in uw cloudserviceproject met de rechtermuisknop op uw *webrol* en selecteer Nieuwe map **toevoegen.** > **New Folder** Een map met de naam **bin .**
  2. Klik met de rechtermuisknop op de map met de opslaglocatie en selecteer**Bestaand item** **toevoegen** > . Selecteer het .NET-installatieprogramma en voeg het toe aan de map met opslaglocatie.
  
Ga als lid van de installatiefunctie voor een *werkrol* als gevolg van:
* Klik met de rechtermuisknop op de *rol van uw werknemer* en selecteer Bestaand**item** **toevoegen** > . Selecteer het .NET-installatieprogramma en voeg deze toe aan de rol. 

Wanneer bestanden op deze manier worden toegevoegd aan de map met rolinhoud, worden ze automatisch toegevoegd aan uw cloudservicepakket. De bestanden worden vervolgens geïmplementeerd op een consistente locatie op de virtuele machine. Herhaal dit proces voor elke web- en werknemersrol in uw cloudservice, zodat alle rollen een kopie van het installatieprogramma hebben.

> [!NOTE]
> U moet .NET Framework 4.6.2 installeren op uw cloudservicerol, zelfs als uw toepassing zich richt op .NET Framework 4.6. Het gastbesturingssysteem bevat de Knowledge [Base-update 3098779](https://support.microsoft.com/kb/3098779) en [update 3097997](https://support.microsoft.com/kb/3097997). Problemen kunnen optreden wanneer u uw .NET-toepassingen uitvoert als .NET Framework 4.6 bovenop de Knowledge Base-updates is geïnstalleerd. Om deze problemen te voorkomen, installeert u .NET Framework 4.6.2 in plaats van versie 4.6. Zie voor meer informatie het [Knowledge Base-artikel 3118750](https://support.microsoft.com/kb/3118750) en [4340191](https://support.microsoft.com/kb/4340191).
> 
> 

![Rolinhoud met installatiebestanden][1]

## <a name="define-startup-tasks-for-your-roles"></a>Opstarttaken voor uw rollen definiëren
U opstarttaken gebruiken om bewerkingen uit te voeren voordat een rol wordt gestart. Het installeren van het .NET Framework als onderdeel van de opstarttaak zorgt ervoor dat het framework wordt geïnstalleerd voordat een toepassingscode wordt uitgevoerd. Zie [Opstarttaken uitvoeren in Azure](cloud-services-startup-tasks.md)voor meer informatie over opstarttaken. 

1. Voeg de volgende inhoud toe aan het bestand ServiceDefinition.csdef onder het knooppunt **WebRole** of **WorkerRole** voor alle rollen:
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    In de vorige configuratie `install.cmd` wordt de opdracht console uitgevoerd met beheerdersbevoegdheden om het .NET Framework te installeren. De configuratie maakt ook een **LocalStorage-element** met de naam **NETFXInstall**. In het opstartscript wordt de tijdelijke map ingesteld om deze lokale opslagbron te gebruiken. 
    
    > [!IMPORTANT]
    > Als u wilt zorgen voor een correcte installatie van het framework, stelt u de grootte van deze resource in op ten minste 1.024 MB.
    
    Zie [Algemene opstarttaken van Azure Cloud Services](cloud-services-startup-tasks-common.md)voor meer informatie over opstarttaken van Azure Cloud Services.

2. Maak een bestand met de naam **install.cmd** en voeg het volgende installatiescript toe aan het bestand.

   Het script controleert of de opgegeven versie van het .NET-framework al op de machine is geïnstalleerd door het register op te vragen. Als de .NET Framework-versie niet is geïnstalleerd, wordt de webinstaller .NET Framework geopend. Om eventuele problemen op te lossen, worden alle activiteiten in het bestand opgeslagentaaklog(huidige datum en tijd).txt die is opgeslagen in local storage **installLogs.**
   
   > [!IMPORTANT]
   > Gebruik een basisteksteditor zoals Windows Notepad om het bestand install.cmd te maken. Als u Visual Studio gebruikt om een tekstbestand te maken en de extensie te wijzigen in .cmd, kan het bestand nog steeds een utf-8-doorte-ordermarkering bevatten. Dit merk kan een fout veroorzaken wanneer de eerste regel van het script wordt uitgevoerd. Als u deze fout wilt voorkomen, maakt u van de eerste regel van het script een REM-instructie die kan worden overgeslagen door de verwerking van de byteorder. 
   > 
   >
   
   ```cmd
   REM Set the value of netfx to install appropriate .NET Framework. 
   REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
   REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
   REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" ***** https://go.microsoft.com/fwlink/?LinkId=671729
   REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" ***** https://www.microsoft.com/download/details.aspx?id=53345
   REM ***** To install .NET 4.7 set the variable netfx to "NDP47" ***** 
   REM ***** To install .NET 4.7.1 set the variable netfx to "NDP471" ***** https://go.microsoft.com/fwlink/?LinkId=852095
   REM ***** To install .NET 4.7.2 set the variable netfx to "NDP472" ***** https://go.microsoft.com/fwlink/?LinkId=863262
   set netfx="NDP472"
   REM ***** To install .NET 4.8 set the variable netfx to "NDP48" ***** https://dotnet.microsoft.com/download/thank-you/net48
      
   REM ***** Set script start timestamp *****
   set timehour=%time:~0,2%
   set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
   set "log=install.cmd started %timestamp%."
   
   REM ***** Exit script if running in Emulator *****
   if "%ComputeEmulatorRunning%"=="true" goto exit
   
   REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
   set TMP=%PathToNETFXInstall%
   set TEMP=%PathToNETFXInstall%
   
   REM ***** Setup .NET filenames and registry keys *****
   if %netfx%=="NDP472" goto NDP472
   if %netfx%=="NDP471" goto NDP471
   if %netfx%=="NDP47" goto NDP47
   if %netfx%=="NDP462" goto NDP462
   if %netfx%=="NDP461" goto NDP461
   if %netfx%=="NDP46" goto NDP46
   
   set "netfxinstallfile=NDP452-KB2901954-Web.exe"
   set netfxregkey="0x5cbf5"
   goto logtimestamp
   
   :NDP46
   set "netfxinstallfile=NDP46-KB3045560-Web.exe"
   set netfxregkey="0x6004f"
   goto logtimestamp
   
   :NDP461
   set "netfxinstallfile=NDP461-KB3102438-Web.exe"
   set netfxregkey="0x6040e"
   goto logtimestamp
   
   :NDP462
   set "netfxinstallfile=NDP462-KB3151802-Web.exe"
   set netfxregkey="0x60632"
   goto logtimestamp
   
   :NDP47
   set "netfxinstallfile=NDP47-KB3186500-Web.exe"
   set netfxregkey="0x707FE"
   goto logtimestamp
   
   :NDP471
   set "netfxinstallfile=NDP471-KB4033344-Web.exe"
   set netfxregkey="0x709fc"
   goto logtimestamp
   
   :NDP472
   set "netfxinstallfile=NDP472-KB4054531-Web.exe"
   set netfxregkey="0x70BF6"
   goto logtimestamp
   
   :logtimestamp
   REM ***** Setup LogFile with timestamp *****
   md "%PathToNETFXInstall%\log"
   set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
   set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
   echo %log% >> %startuptasklog%
   echo Logfile generated at: %startuptasklog% >> %startuptasklog%
   echo TMP set to: %TMP% >> %startuptasklog%
   echo TEMP set to: %TEMP% >> %startuptasklog%
   
   REM ***** Check if .NET is installed *****
   echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
   set /A netfxregkeydecimal=%netfxregkey%
   set foundkey=0
   FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
   echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
   if %foundkey% GEQ %netfxregkeydecimal% goto installed
   
   REM ***** Installing .NET *****
   echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
   start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
   if %ERRORLEVEL%== 0 goto installed
       echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
       if %ERRORLEVEL%== 3010 goto restart
       if %ERRORLEVEL%== 1641 goto restart
       echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
       goto exit
       
   :restart
   echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
   shutdown.exe /r /t 5 /c "Installed .NET framework" /f /d p:2:4
   
   :installed
   echo .NET (%netfx%) is installed >> %startuptasklog%
   
   :end
   echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
   
   :exit
   EXIT /B 0
   ```

3. Voeg het bestand install.cmd toe aan elke rol met**Bestaand item** **toevoegen** > in **Solution Explorer** zoals eerder in dit onderwerp is beschreven. 

    Nadat deze stap is voltooid, moeten alle rollen het .NET-installatiebestand en het bestand install.cmd hebben.

   ![Rolinhoud met alle bestanden][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Diagnostische gegevens configureren om opstartlogboeken over te zetten naar Blob-opslag
Om het oplossen van installatieproblemen te vereenvoudigen, u Azure Diagnostics configureren om logboekbestanden die zijn gegenereerd door het opstartscript of het .NET-installatieprogramma over te zetten naar Azure Blob-opslag. Met deze benadering u de logboeken bekijken door de logboekbestanden uit Blob-opslag te downloaden in plaats van extern bureaublad in de rol te hoeven gebruiken.


Als u Diagnostics wilt configureren, opent u het bestand diagnostics.wadcfgx en voegt u de volgende inhoud toe onder het knooppunt **Mappen:** 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Deze XML configureert Diagnostics om de bestanden in de logboekmap in de **NETFXInstall-bron** over te zetten naar het Diagnostisch-opslagaccount in de **blobcontainer die netfx installeert.**

## <a name="deploy-your-cloud-service"></a>Uw cloudservice implementeren
Wanneer u uw cloudservice implementeert, installeren de opstarttaken het .NET Framework als deze nog niet is geïnstalleerd. Uw cloudservicerollen bevinden zich in de *drukke* status terwijl het framework wordt geïnstalleerd. Als de framework-installatie een herstart vereist, kunnen de servicerollen ook opnieuw worden opgestart. 

## <a name="additional-resources"></a>Aanvullende bronnen
* [Het .NET-framework installeren][Installing the .NET Framework]
* [Bepalen welke .NET Framework-versies zijn geïnstalleerd][How to: Determine Which .NET Framework Versions Are Installed]
* [Probleemoplossing .NET Framework-installaties][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



