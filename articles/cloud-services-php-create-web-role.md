---
title: Azure-web-en-werk rollen maken voor PHP
description: Een hand leiding voor het maken van PHP-web-en-werk rollen in een Azure-Cloud service en het configureren van de PHP-runtime.
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
ms.openlocfilehash: 82bb5f153a2c70d3b26f295925f8e48693bc49b9
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146873"
---
# <a name="create-php-web-and-worker-roles"></a>PHP-web- en -werkrollen maken

## <a name="overview"></a>Overzicht

In deze hand leiding wordt uitgelegd hoe u een PHP-web-of-werk rollen maakt in een Windows-ontwikkel omgeving, een specifieke versie van PHP kiest uit de ' ingebouwde ' beschik bare versies, de PHP-configuratie wijzigen, uitbrei dingen inschakelt en tot slot naar Azure implementeert. Ook wordt beschreven hoe u een web-of worker-rol configureert voor het gebruik van een PHP-runtime (met aangepaste configuratie en uitbrei dingen) die u opgeeft.

Azure biedt drie rekenmodellen voor het uitvoeren van toepassingen: Azure App Service, Azure Virtual Machines en Azure Cloud Services. Alle drie de modellen ondersteunen PHP. Cloud Services, dat web-en werk rollen bevat, biedt *platform as a Service (PaaS)* . Binnen een Cloud service biedt een webrol een speciale Internet Information Services (IIS) webserver voor het hosten van front-end webtoepassingen. Een werknemersrol kan asynchrone, langdurige of permanente taken uitvoeren, onafhankelijk van de interactie of invoer van de gebruiker.

Zie [Compute hosting Options van Azure](cloud-services/cloud-services-choose-me.md)(Engelstalig) voor meer informatie over deze opties.

## <a name="download-the-azure-sdk-for-php"></a>De Azure SDK voor PHP downloaden

De [Azure SDK voor php](https://github.com/Azure/azure-sdk-for-php) bestaat uit verschillende onderdelen. In dit artikel worden twee hiervan gebruikt: Azure PowerShell en de Azure-emulators. Deze twee onderdelen kunnen worden geïnstalleerd via het installatie programma voor het micro soft-webplatform. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) voor meer informatie.

## <a name="create-a-cloud-services-project"></a>Een Cloud Services project maken

De eerste stap bij het maken van een PHP-web-of-werk functie is het maken van een Azure-service project. een Azure-service project fungeert als een logische container voor web-en werk rollen en bevat de [Service definitie (. csdef)] en [Service configuratie bestanden (. cscfg)] van het project.

Als u een nieuw Azure-service project wilt maken, voert u Azure PowerShell uit als beheerder en voert u de volgende opdracht uit:

    PS C:\>New-AzureServiceProject myProject

Met deze opdracht wordt een nieuwe map (`myProject`) gemaakt waaraan u web-en werk rollen kunt toevoegen.

## <a name="add-php-web-or-worker-roles"></a>PHP-web-of-werk rollen toevoegen

Als u een PHP-webfunctie wilt toevoegen aan een project, voert u de volgende opdracht uit vanuit de hoofdmap van het project:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Voor een worker-rol gebruikt u deze opdracht:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> De `roleName` para meter is optioneel. Als de naam wordt wegge laten, wordt de rolnaam automatisch gegenereerd. De eerste webfunctie die wordt `WebRole1`gemaakt, is de tweede `WebRole2`, enzovoort. De eerste werk rollen die worden gemaakt `WorkerRole1`, zijn de tweede `WorkerRole2`, enzovoort.
>
>

## <a name="specify-the-built-in-php-version"></a>De ingebouwde PHP-versie opgeven

Wanneer u een PHP-web-of worker-rol aan een project toevoegt, worden de configuratie bestanden van het project zodanig gewijzigd dat PHP wordt geïnstalleerd op elk web-of worker-exemplaar van uw toepassing wanneer deze wordt geïmplementeerd. Als u wilt zien welke versie van PHP standaard wordt geïnstalleerd, voert u de volgende opdracht uit:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

De uitvoer van de bovenstaande opdracht ziet er ongeveer uit zoals hieronder wordt weer gegeven. In dit voor beeld is `IsDefault` de vlag ingesteld op `true` voor PHP 5.3.17, waarmee wordt aangegeven dat de standaard versie van PHP is geïnstalleerd.

```
Runtime Version     PackageUri                      IsDefault
------- -------     ----------                      ---------
Node 0.6.17         http://nodertncu.blob.core...   False
Node 0.6.20         http://nodertncu.blob.core...   True
Node 0.8.4          http://nodertncu.blob.core...   False
IISNode 0.1.21      http://nodertncu.blob.core...   True
Cache 1.8.0         http://nodertncu.blob.core...   True
PHP 5.3.17          http://nodertncu.blob.core...   True
PHP 5.4.0           http://nodertncu.blob.core...   False
```

U kunt de PHP-runtime versie instellen op een van de PHP-versies die worden weer gegeven. Als u bijvoorbeeld de PHP-versie (voor een rol met de naam `roleName`) wilt instellen op 5.4.0, gebruikt u de volgende opdracht:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [!NOTE]
> Beschik bare PHP-versies kunnen in de toekomst worden gewijzigd.
>
>

## <a name="customize-the-built-in-php-runtime"></a>De ingebouwde PHP-runtime aanpassen

U hebt volledige controle over de configuratie van de PHP-runtime die wordt geïnstalleerd wanneer u de bovenstaande stappen uitvoert, met inbegrip van het wijzigen van `php.ini` instellingen en het inschakelen van uitbrei dingen.

Voer de volgende stappen uit om de ingebouwde PHP-runtime aan te passen:

1. Voeg een nieuwe map met de `php`naam toe aan `bin` de directory van uw webrole. Voor een werk rollen voegt u deze toe aan de hoofdmap van de rol.
2. Maak in `php` de map een andere map met `ext`de naam. Plaats alle `.dll` extensie bestanden (bijvoorbeeld `php_mongo.dll`) die u in deze map wilt inschakelen.
3. Voeg een `php.ini` bestand toe aan `php` de map. Schakel alle aangepaste uitbrei dingen in en stel PHP-instructies in dit bestand in. Als u de `php_mongo.dll` uitbrei ding bijvoorbeeld wilt `display_errors` inschakelen en inschakelen `php.ini` , zou de inhoud van het bestand er als volgt uitzien:

        display_errors=On
        extension=php_mongo.dll

> [!NOTE]
> Instellingen die u niet expliciet instelt in het `php.ini` bestand dat u opgeeft, worden automatisch ingesteld op de standaard waarden. Denk echter eraan dat u een volledig `php.ini` bestand kunt toevoegen.
>
>

## <a name="use-your-own-php-runtime"></a>Uw eigen PHP-runtime gebruiken

In sommige gevallen, in plaats van een geïntegreerde PHP-runtime te selecteren en deze te configureren zoals hierboven beschreven, kunt u uw eigen PHP-runtime opgeven. U kunt bijvoorbeeld dezelfde PHP-runtime gebruiken in een web-of worker-rol die u in uw ontwikkel omgeving gebruikt. Dit maakt het gemakkelijker om ervoor te zorgen dat de toepassing niet verandert in uw productie omgeving.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Een webrole configureren om uw eigen PHP-runtime te gebruiken

Voer de volgende stappen uit om een webrole te configureren voor het gebruik van een PHP-runtime die u opgeeft:

1. Maak een Azure-service project en voeg een PHP-webfunctie toe zoals eerder in dit onderwerp wordt beschreven.
2. Maak een `php` map in de `bin` map die zich in de hoofdmap van uw webfunctie bevindt en voeg vervolgens de PHP-runtime (alle binaire bestanden, configuratie bestanden, submappen, enzovoort `php` ) toe aan de map.
3. Beschrijving Als uw PHP-runtime gebruikmaakt van de [micro soft-Stuur Programma's voor php voor SQL Server][sqlsrv drivers], moet u uw webrole configureren om [SQL Server Native Client 2012][sql native client] te installeren wanneer het is ingericht. Als u dit wilt doen, voegt u het [installatie programma SQLNCLI. msi x64] toe aan de `bin` map in de hoofdmap van uw webrol. Het opstart script dat in de volgende stap wordt beschreven, voert het installatie programma op de achtergrond uit wanneer de rol is ingericht. Als uw PHP-runtime geen gebruikmaakt van de micro soft-Stuur Programma's voor PHP voor SQL Server, kunt u de volgende regel verwijderen uit het script dat wordt weer gegeven in de volgende stap:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definieer een opstart taak waarmee [Internet Information Services (IIS)][iis.net] wordt geconfigureerd om uw PHP-runtime te gebruiken voor het `.php` afhandelen van aanvragen voor pagina's. Hiertoe opent u het `setup_web.cmd` bestand (in het `bin` bestand met de hoofdmap van de webfunctie) in een tekst editor en vervangt u de inhoud door het volgende script:

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
5. Voeg uw toepassings bestanden toe aan de hoofdmap van uw web-rol. Dit is de hoofdmap van de webserver.
6. Publiceer uw toepassing zoals beschreven in de sectie [uw toepassing publiceren](#publish-your-application) hieronder.

> [!NOTE]
> Het `download.ps1` script (in de `bin` map van de hoofdmap van de webfunctie) kan worden verwijderd nadat u de stappen hebt gevolgd die hierboven worden beschreven voor het gebruik van uw eigen PHP-runtime.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Een worker-rol configureren voor het gebruik van uw eigen PHP-runtime

Als u een werknemersrol wilt configureren voor het gebruik van een PHP-runtime die u opgeeft, voert u de volgende stappen uit:

1. Maak een Azure-service project en voeg een PHP-worker-rol toe zoals eerder in dit onderwerp wordt beschreven.
2. Maak een `php` map in de hoofdmap van de werk rollen en voeg vervolgens de PHP-runtime (alle binaire bestanden, configuratie bestanden, submappen, enzovoort) toe aan `php` de map.
3. Beschrijving Als uw PHP-runtime gebruikmaakt [van micro soft-Stuur Programma's voor php voor SQL Server][sqlsrv drivers], moet u uw werknemersrol configureren om [SQL Server Native Client 2012][sql native client] te installeren wanneer het is ingericht. U doet dit door het [installatie programma SQLNCLI. msi x64] toe te voegen aan de hoofdmap van de werk rollen. Het opstart script dat in de volgende stap wordt beschreven, voert het installatie programma op de achtergrond uit wanneer de rol is ingericht. Als uw PHP-runtime geen gebruikmaakt van de micro soft-Stuur Programma's voor PHP voor SQL Server, kunt u de volgende regel verwijderen uit het script dat wordt weer gegeven in de volgende stap:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definieer een opstart taak waarmee uw `php.exe` uitvoer bare bestand wordt toegevoegd aan de omgevings variabele PATH van de werk rollen wanneer de rol wordt ingericht. Als u dit wilt doen, `setup_worker.cmd` opent u het bestand (in de hoofdmap van de werk rollen) in een tekst editor en vervangt u de inhoud door het volgende script:

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
5. Voeg uw toepassings bestanden toe aan de hoofdmap van uw werk rollen.
6. Publiceer uw toepassing zoals beschreven in de sectie [uw toepassing publiceren](#publish-your-application) hieronder.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Uw toepassing uitvoeren in de berekenings-en opslag emulators

De Azure-emulators bieden een lokale omgeving waarin u uw Azure-toepassing kunt testen voordat u deze in de Cloud implementeert. Er zijn enkele verschillen tussen de emulators en de Azure-omgeving. Zie [de Azure-opslag emulator gebruiken voor ontwikkeling en testen](storage/common/storage-use-emulator.md)om dit beter te begrijpen.

U moet PHP lokaal hebben geïnstalleerd om de compute-emulator te kunnen gebruiken. De compute emulator maakt gebruik van uw lokale PHP-installatie om uw toepassing uit te voeren.

Als u uw project wilt uitvoeren in de emulators, voert u de volgende opdracht uit vanuit de hoofdmap van het project:

    PS C:\MyProject> Start-AzureEmulator

De uitvoer ziet er ongeveer als volgt uit:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

U kunt zien dat uw toepassing wordt uitgevoerd in de emulator door een webbrowser te openen en te bladeren naar het lokale adres dat wordt`http://127.0.0.1:81` weer gegeven in de uitvoer (in de voorbeeld uitvoer hierboven).

Als u de emulators wilt stoppen, voert u deze opdracht uit:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Uw toepassing publiceren

Als u uw toepassing wilt publiceren, moet u eerst de publicatie-instellingen importeren met behulp van de cmdlet [import-AzurePublishSettingsFile](https://docs.microsoft.com/powershell/module/servicemanagement/azure/import-azurepublishsettingsfile) . Vervolgens kunt u uw toepassing publiceren met behulp van de cmdlet [Publish-AzureServiceProject](https://docs.microsoft.com/powershell/module/servicemanagement/azure/publish-azureserviceproject) . Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview)voor informatie over het aanmelden.

## <a name="next-steps"></a>Volgende stappen

Zie het [PHP-ontwikkelaars centrum](https://azure.microsoft.com/develop/php/)voor meer informatie.

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[Service definitie (. csdef)]: https://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[Service configuratie bestanden (. cscfg)]: https://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: https://www.iis.net/
[sql native client]: https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[installatie programma SQLNCLI. msi x64]: https://go.microsoft.com/fwlink/?LinkID=239648
