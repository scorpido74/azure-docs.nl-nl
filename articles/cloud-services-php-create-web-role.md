---
title: Azure-web- en werknemersrollen voor PHP maken
description: Een handleiding voor het maken van PHP-web- en werknemersrollen in een Azure-cloudservice en het configureren van de PHP-runtime.
services: ''
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 54410e1e70a2ec0d3a9e2f853dc9556cd05996ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297251"
---
# <a name="create-php-web-and-worker-roles"></a>PHP-web- en -werkrollen maken

## <a name="overview"></a>Overzicht

In deze handleiding ziet u hoe u PHP-web- of werknemersrollen maken in een Windows-ontwikkelomgeving, een specifieke versie van PHP kiezen uit de beschikbare ingebouwde versies, de PHP-configuratie wijzigen, extensies inschakelen en uiteindelijk implementeren in Azure. Het beschrijft ook hoe u een web- of werknemersrol configureert om een PHP-runtime te gebruiken (met aangepaste configuratie en extensies) die u verstrekt.

Azure biedt drie rekenmodellen voor het uitvoeren van toepassingen: Azure App Service, Azure Virtual Machines en Azure Cloud Services. Alle drie de modellen ondersteunen PHP. Cloud Services, dat web- en werknemersrollen omvat, biedt *platform as a service (PaaS).* Binnen een cloudservice biedt een webrol een speciale IIS-webserver (Internet Information Services) voor het hosten van front-end webtoepassingen. Een werkrol kan asynchrone, langlopende of eeuwigdurende taken uitvoeren, onafhankelijk van gebruikersinteractie of -invoer.

Zie [Compute-hostingopties van Azure](cloud-services/cloud-services-choose-me.md)voor meer informatie over deze opties.

## <a name="download-the-azure-sdk-for-php"></a>De Azure SDK voor PHP downloaden

De [Azure SDK voor PHP](https://github.com/Azure/azure-sdk-for-php) bestaat uit verschillende componenten. In dit artikel worden er twee gebruikt: Azure PowerShell en de Azure-emulators. Deze twee componenten kunnen worden geïnstalleerd via het Microsoft Web Platform Installer. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) voor meer informatie.

## <a name="create-a-cloud-services-project"></a>Een Cloud Services-project maken

De eerste stap bij het maken van een PHP-web- of werknemersrol is het maken van een Azure Service-project. een Azure Service-project dient als een logische container voor web- en werknemersrollen en bevat de [servicedefinitie (.csdef)] en [serviceconfiguratiebestanden van] het project.

Als u een nieuw Azure Service-project wilt maken, voert u Azure PowerShell uit als beheerder en voert u de volgende opdracht uit:

    PS C:\>New-AzureServiceProject myProject

Met deze opdracht wordt`myProject`een nieuwe map ( ) gemaakt waaraan u web- en werknemersrollen toevoegen.

## <a name="add-php-web-or-worker-roles"></a>PHP-web- of werknemersrollen toevoegen

Als u een PHP-webrol aan een project wilt toevoegen, voert u de volgende opdracht uit vanuit de hoofdmap van het project:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Gebruik de opdracht voor een werkrol:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> De `roleName` parameter is optioneel. Als deze wordt weggelaten, wordt de rolnaam automatisch gegenereerd. De eerste webrol `WebRole1`gemaakt zal zijn, de tweede zal zijn, `WebRole2`en ga zo maar door. De eerste werknemer rol `WorkerRole1`gemaakt zal `WorkerRole2`zijn , de tweede zal zijn , en ga zo maar door.
>
>

## <a name="use-your-own-php-runtime"></a>Gebruik je eigen PHP runtime

In sommige gevallen, in plaats van het selecteren van een ingebouwde PHP runtime en configureren zoals hierboven beschreven, wilt u misschien uw eigen PHP runtime bieden. U bijvoorbeeld dezelfde PHP-runtime gebruiken in een web- of werknemersrol die u in uw ontwikkelomgeving gebruikt. Dit maakt het gemakkelijker om ervoor te zorgen dat de toepassing het gedrag in uw productieomgeving niet verandert.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Een webrol configureren om uw eigen PHP-runtime te gebruiken

Voer de volgende stappen uit om een webrol te configureren om een PHP-runtime te gebruiken die u opgeeft:

1. Maak een Azure Service-project en voeg een PHP-webrol toe zoals eerder beschreven in dit onderwerp.
2. Maak `php` een map `bin` in de map die zich in de hoofdmap van uw webrol bevindt en voeg vervolgens uw `php` PHP-runtime (alle binaire bestanden, configuratiebestanden, submappen, enz.) toe aan de map.
3. (OPTIONEEL) Als uw PHP-runtime de [Microsoft Drivers for PHP voor SQL Server][sqlsrv drivers]gebruikt, moet u uw webrol configureren om SQL Server Native Client [2012][sql native client] te installeren wanneer deze is ingericht. Voeg hiervoor het [sqlncli.msi x64-installatieprogramma] toe aan de map in de `bin` hoofdmap van uw webrol. Het opstartscript dat in de volgende stap wordt beschreven, wordt in stilte uitgevoerd wanneer de rol is ingericht. Als uw PHP-runtime de Microsoft Drivers voor PHP voor SQL Server niet gebruikt, u de volgende regel uit het script verwijderen dat in de volgende stap wordt weergegeven:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definieer een opstarttaak die [IIS (Internet Information Services)][iis.net] configureert `.php` om uw PHP-runtime te gebruiken om aanvragen voor pagina's af te handelen. Open hiervoor het `setup_web.cmd` bestand (in `bin` het bestand van de hoofdmap van uw webrol) in een teksteditor en vervang de inhoud ervan door het volgende script:

    ```cmd
    @ECHO ON
    cd "%~dp0"

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
    SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"
    ```
5. Voeg uw toepassingsbestanden toe aan de hoofdmap van uw webrol. Dit is de hoofdmap van de webserver.
6. Publiceer uw aanvraag zoals beschreven in de sectie [Uw aanvraag](#publish-your-application) publiceren hieronder.

> [!NOTE]
> Het `download.ps1` script (in de `bin` map van de hoofdmap van de webrol) kan worden verwijderd nadat u de hierboven beschreven stappen hebt gevolgd voor het gebruik van uw eigen PHP-runtime.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Een werkrol configureren om uw eigen PHP-runtime te gebruiken

Voer de volgende stappen uit om een werkrol te configureren om een PHP-runtime te gebruiken die u opgeeft:

1. Maak een Azure Service-project en voeg een PHP-werkrol toe zoals eerder beschreven in dit onderwerp.
2. Maak `php` een map in de hoofdmap van de werkrol en voeg vervolgens uw PHP-runtime (alle `php` binaire bestanden, configuratiebestanden, submappen, enz.) toe aan de map.
3. (OPTIONEEL) Als uw PHP-runtime [Microsoft Drivers voor PHP voor SQL Server][sqlsrv drivers]gebruikt, moet u uw werkrol configureren om SQL Server Native Client [2012][sql native client] te installeren wanneer deze is ingericht. Voeg hiervoor het [sqlncli.msi x64-installer toe] aan de rootdirectory van de werknemerrol. Het opstartscript dat in de volgende stap wordt beschreven, wordt in stilte uitgevoerd wanneer de rol is ingericht. Als uw PHP-runtime de Microsoft Drivers voor PHP voor SQL Server niet gebruikt, u de volgende regel uit het script verwijderen dat in de volgende stap wordt weergegeven:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definieer een opstarttaak `php.exe` die uw uitvoerbare toevoegt aan de PATH-omgevingsvariabele van de werknemerrol wanneer de rol is ingericht. Open hiervoor het `setup_worker.cmd` bestand (in de hoofdmap van de werkrol) in een teksteditor en vervang de inhoud ervan door het volgende script:

    ```cmd
    @echo on

    cd "%~dp0"

    echo Granting permissions for Network Service to the web root directory...
    icacls ..\ /grant "Network Service":(OI)(CI)W
    if %ERRORLEVEL% neq 0 goto error
    echo OK

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    setx Path "%PATH%;%~dp0php" /M

    if %ERRORLEVEL% neq 0 goto error

    echo SUCCESS
    exit /b 0

    :error

    echo FAILED
    exit /b -1
    ```
5. Voeg uw toepassingsbestanden toe aan de hoofdmap van uw werknemersrol.
6. Publiceer uw aanvraag zoals beschreven in de sectie [Uw aanvraag](#publish-your-application) publiceren hieronder.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Uw toepassing uitvoeren in de compute- en opslagemulators

De Azure-emulators bieden een lokale omgeving waarin u uw Azure-toepassing testen voordat u deze implementeert in de cloud. Er zijn enkele verschillen tussen de emulators en de Azure-omgeving. Zie [De Azure-opslagemulator gebruiken voor ontwikkeling en testen](storage/common/storage-use-emulator.md)om dit beter te begrijpen.

Houd er rekening mee dat PHP lokaal moet zijn geïnstalleerd om de compute emulator te kunnen gebruiken. De compute emulator gebruikt uw lokale PHP-installatie om uw toepassing uit te voeren.

Als u uw project in de emulators wilt uitvoeren, voert u de volgende opdracht uit in de hoofdmap van uw project:

    PS C:\MyProject> Start-AzureEmulator

U ziet uitvoer die lijkt op de volgende: 

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

U uw toepassing in de emulator zien draaien door een webbrowser te`http://127.0.0.1:81` openen en te bladeren naar het lokale adres dat in de uitvoer wordt weergegeven (in de bovenstaande voorbeelduitvoer).

Voer de opdracht uit om de emulators te stoppen:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Uw toepassing publiceren

Als u uw toepassing wilt publiceren, moet u eerst uw publicatie-instellingen importeren met de cmdlet [Import-AzurePublishSettingsFile.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/import-azurepublishsettingsfile) Vervolgens u uw toepassing publiceren met de cmdlet [Publish-AzureServiceProject.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/publish-azureserviceproject) Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview)voor informatie over aanmelden.

## <a name="next-steps"></a>Volgende stappen

Zie het PHP [Developer Center](https://azure.microsoft.com/develop/php/)voor meer informatie.

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[servicedefinitie (.csdef)]: https://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[serviceconfiguratie (.cscfg)]: https://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: https://www.iis.net/
[sql native client]: https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[sqlncli.msi x64 installer]: https://go.microsoft.com/fwlink/?LinkID=239648
