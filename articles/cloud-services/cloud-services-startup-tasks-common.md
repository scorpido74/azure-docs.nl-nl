---
title: Veelvoorkomende opstarttaken voor Cloud Services | Microsoft Documenten
description: Geeft enkele voorbeelden van veelvoorkomende opstarttaken die u mogelijk wilt uitvoeren in uw webrol of werknemersrol van cloudservices.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: 4fe1ee3ccf2849943959889838ba0f22fb64bb9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273057"
---
# <a name="common-cloud-service-startup-tasks"></a>Opstarttaken van Common Cloud Service
In dit artikel vindt u enkele voorbeelden van veelvoorkomende opstarttaken die u mogelijk wilt uitvoeren in uw cloudservice. U opstarttaken gebruiken om bewerkingen uit te voeren voordat een rol wordt gestart. Bewerkingen die u mogelijk wilt uitvoeren, zijn het installeren van een component, het registreren van COM-onderdelen, het instellen van registersleutels of het starten van een langlopend proces. 

Zie [dit artikel](cloud-services-startup-tasks.md) om te begrijpen hoe opstarttaken werken en specifiek hoe u de vermeldingen maakt die een opstarttaak definiëren.

> [!NOTE]
> Opstarttaken zijn niet van toepassing op virtuele machines, alleen op rollen van Cloud Service Web en Worker.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Omgevingsvariabelen definiëren voordat een rol begint
Als u omgevingsvariabelen nodig hebt die zijn gedefinieerd voor een specifieke taak, gebruikt u het element [Omgeving] in het [element Taak.]

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Variabelen kunnen ook een [geldige Azure XPath-waarde](cloud-services-role-config-xpath.md) gebruiken om naar iets over de implementatie te verwijzen. In plaats `value` van het kenmerk te gebruiken, definieert u een onderliggend element [RoleInstanceValue.]

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>IIS-opstart configureren met AppCmd.exe
Het opdrachtregelgereedschap [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) kan worden gebruikt om IIS-instellingen te beheren bij het opstarten op Azure. *AppCmd.exe* biedt handige, opdrachtregeltoegang tot configuratie-instellingen voor gebruik in opstarttaken op Azure. Met Behulp van *AppCmd.exe*kunnen website-instellingen worden toegevoegd, gewijzigd of verwijderd voor toepassingen en sites.

Er zijn echter een paar dingen om op te letten in het gebruik van *AppCmd.exe* als opstarttaak:

* Opstarttaken kunnen meer dan één keer worden uitgevoerd tussen reboots. Bijvoorbeeld wanneer een rol wordt gerecycled.
* Als een *appcmd.exe-actie* meerdere tijd wordt uitgevoerd, kan dit een fout genereren. Als u bijvoorbeeld twee keer een sectie aan *Web.config* wilt toevoegen, kan er een fout worden gegenereerd.
* Opstarttaken mislukken als ze een niet-nulexitcode of **foutniveau retourneren.** Bijvoorbeeld wanneer *AppCmd.exe* een fout genereert.

Het is een goede gewoonte om het **foutenniveau** te controleren na het aanroepen van *AppCmd.exe,* wat gemakkelijk is om te doen als u de oproep naar *AppCmd.exe* met een *.cmd-bestand* inpakt. Als u een bekende **reactie op het foutenniveau** detecteert, u deze negeren of doorgeven.

Het foutenpercentage dat door *AppCmd.exe* wordt geretourneerd, wordt vermeld in het winerror.h-bestand en is ook te zien op [MSDN.](/windows/desktop/Debug/system-error-codes--0-499-)

### <a name="example-of-managing-the-error-level"></a>Voorbeeld van het beheren van het foutniveau
In dit voorbeeld wordt een compressiesectie en een compressievermelding voor JSON toegevoegd aan het *web.config-bestand,* met foutafhandeling en logboekregistratie.

De relevante secties van het [bestand ServiceDefinition.csdef] worden hier weergegeven, waaronder het instellen van het kenmerk [executionContext](/previous-versions/azure/reference/gg557552(v=azure.100)#task) om `elevated` *AppCmd.exe* voldoende machtigingen te geven om de instellingen in het *web.config-bestand* te wijzigen:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Het *batchbestand Opstarten.cmd* gebruikt *AppCmd.exe* om een compressiesectie en een compressievermelding voor JSON toe te voegen aan het *web.config-bestand.* Het verwachte **foutenniveau** van 183 is ingesteld op nul met behulp van de VERIFY. EXE-opdrachtregelprogramma. Onverwachte foutenworden geregistreerd bij StartupErrorLog.txt.

```cmd
REM   *** Add a compression section to the Web.config file. ***
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in an Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 VERIFY > NUL

REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Add compression for json. ***
%windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
IF %ERRORLEVEL% EQU 183 VERIFY > NUL
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Exit batch file. ***
EXIT /b 0

REM   *** Log error and exit ***
:ErrorExit
REM   Report the date, time, and ERRORLEVEL of the error.
DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT %ERRORLEVEL%
```

## <a name="add-firewall-rules"></a>Firewallregels toevoegen
In Azure zijn er effectief twee firewalls. De eerste firewall regelt de verbindingen tussen de virtuele machine en de buitenwereld. Deze firewall wordt beheerd door het [Element EndPoints] in het [bestand ServiceDefinition.csdef.]

De tweede firewall regelt de verbindingen tussen de virtuele machine en de processen binnen die virtuele machine. Deze firewall kan worden `netsh advfirewall firewall` bediend door het command-line tool.

Azure maakt firewallregels voor de processen die binnen uw rollen zijn gestart. Wanneer u bijvoorbeeld een service of programma start, maakt Azure automatisch de benodigde firewallregels om die service met internet te laten communiceren. Als u echter een service maakt die wordt gestart door een proces buiten uw rol (zoals een COM+-service of een geplande Windows-taak), moet u handmatig een firewallregel maken om toegang tot die service toe te staan. Deze firewallregels kunnen worden gemaakt met behulp van een opstarttaak.

Een opstarttaak waarmee een firewallregel wordt gemaakt, moet een [executionContexttaak][Task] van **opgeheven**hebben. Voeg de volgende opstarttaak toe aan het bestand [ServiceDefinition.csdef.]

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Als u de firewallregel wilt `netsh advfirewall firewall` toevoegen, moet u de juiste opdrachten in uw opstartbatchbestand gebruiken. In dit voorbeeld vereist de opstarttaak beveiliging en versleuteling voor TCP-poort 80.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Een specifiek IP-adres blokkeren
U de toegang tot een Azure-webrol beperken tot een set opgegeven IP-adressen door uw **IIS-web.config-bestand** te wijzigen. U moet ook een opdrachtbestand gebruiken dat het **ipSecurity-gedeelte** van het **bestand ApplicationHost.config** ontgrendelt.

Als u het **ipSecurity-gedeelte** van het **bestand ApplicationHost.config wilt ontgrendelen,** maakt u een opdrachtbestand dat wordt uitgevoerd bij het starten van de rol. Maak een map op het hoofdniveau van uw webrol genaamd **opstarten** en maak in deze map een batchbestand met de naam **startup.cmd**. Voeg dit bestand toe aan uw Visual Studio-project en stel de eigenschappen in **op Altijd kopiëren** om ervoor te zorgen dat het in uw pakket wordt opgenomen.

Voeg de volgende opstarttaak toe aan het bestand [ServiceDefinition.csdef.]

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Voeg deze opdracht toe aan het **bestand startup.cmd:**

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

Deze taak zorgt ervoor dat het **batchbestand startup.cmd** wordt uitgevoerd elke keer dat de webrol wordt geïnitialiseerd, zodat de vereiste **ipSecurity-sectie** wordt ontgrendeld.

Wijzig ten slotte de [sectie system.webServer](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) het **web.config-bestand** van uw webrol om een lijst met IP-adressen toe te voegen die toegang krijgen, zoals in het volgende voorbeeld wordt weergegeven:

Met deze voorbeeldconfig **hebben** alle IP's toegang tot de server, behalve de twee gedefinieerde

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

Dit voorbeeld config **ontkent** alle IP's van de toegang tot de server, behalve voor de twee gedefinieerd.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## <a name="create-a-powershell-startup-task"></a>Een PowerShell-opstarttaak maken
Windows PowerShell-scripts kunnen niet rechtstreeks vanuit het bestand [ServiceDefinition.csdef] worden aangeroepen, maar ze kunnen wel worden aangeroepen vanuit een opstartbatchbestand.

PowerShell (standaard) voert geen niet-ondertekende scripts uit. Tenzij u uw script ondertekent, moet u PowerShell configureren om niet-ondertekende scripts uit te voeren. Als u niet-ondertekende scripts wilt uitvoeren, moet het **uitvoeringsbeleid** worden ingesteld op **Onbeperkt**. De instelling **Uitvoeringsbeleid** die u gebruikt, is gebaseerd op de versie van Windows PowerShell.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Als u een gastbesturingssysteem gebruikt dat PowerShell 2.0 of 1.0 uitvoert, u versie 2 dwingen om uit te voeren en als u niet beschikbaar bent, versie 1 gebruiken.

```cmd
REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
IF %ERRORLEVEL% EQU -393216 (
   PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
   PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
)

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Bestanden maken in lokale opslag vanuit een opstarttaak
U een lokale opslagbron gebruiken om bestanden op te slaan die zijn gemaakt door uw opstarttaak die later door uw toepassing wordt geopend.

Als u de lokale opslagbron wilt maken, voegt u een sectie [LocalResources] toe aan het bestand [ServiceDefinition.csdef] en voegt u het onderliggende element [LocalStorage] toe. Geef de lokale opslagbron een unieke naam en een geschikte grootte voor uw opstarttaak.

Als u een lokale opslagbron wilt gebruiken in uw opstarttaak, moet u een omgevingsvariabele maken om te verwijzen naar de locatie van de lokale opslagbron. Vervolgens kunnen de opstarttaak en de toepassing bestanden lezen en schrijven naar de lokale opslagbron.

De relevante secties van het **bestand ServiceDefinition.csdef** worden hier weergegeven:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">
    ...

    <LocalResources>
      <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
    </LocalResources>

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="PathToStartupStorage">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
  </WorkerRole>
</ServiceDefinition>
```

Als voorbeeld gebruikt dit **batchbestand Startup.cmd** de omgevingsvariabele **PathToStartupStorage** om het bestand **MyTest.txt** op de lokale opslaglocatie te maken.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

U hebt toegang tot de lokale opslagmap vanuit de Azure SDK met behulp van de [getlocalresource-methode.](/previous-versions/azure/reference/ee772845(v=azure.100))

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Uitvoeren in de emulator of cloud
U uw opstarttaak verschillende stappen laten uitvoeren wanneer deze in de cloud werkt in vergelijking met wanneer deze zich in de compute emulator bevindt. U bijvoorbeeld alleen een nieuwe kopie van uw SQL-gegevens gebruiken wanneer u in de emulator wordt uitgevoerd. Of u wilt misschien een aantal prestatieoptimalisaties voor de cloud uitvoeren die u niet hoeft te doen wanneer u in de emulator wordt uitgevoerd.

Deze mogelijkheid om verschillende acties uit te voeren op de compute emulator en de cloud kan worden bereikt door een omgevingsvariabele te maken in het [Bestand ServiceDefinition.csdef.] Vervolgens test u die omgevingsvariabele op een waarde in uw opstarttaak.

Als u de omgevingsvariabele wilt maken, voegt u het `/RoleEnvironment/Deployment/@emulated`element [Variable]/[RoleInstanceValue] toe en maakt u een XPath-waarde van . De waarde van de omgevingsvariabele **%ComputeEmulatorRunning%** is `true` wanneer `false` u op de compute emulator wordt uitgevoerd en wanneer deze op de cloud wordt uitgevoerd.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">

    ...

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>

  </WorkerRole>
</ServiceDefinition>
```

De taak kan nu de omgevingsvariabele **%ComputeEmulatorRunning%** controleren om verschillende acties uit te voeren op basis van of de rol wordt uitgevoerd in de cloud of de emulator. Hier is een .cmd shell script dat controleert op die omgevingsvariabele.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Detecteren of uw taak al is uitgevoerd
De rol kan worden gerecycled zonder een herstart waardoor uw opstarttaken opnieuw worden uitgevoerd. Er is geen vlag om aan te geven dat een taak al is uitgevoerd op de hosting VM. U sommige taken hebben waar het niet uitmaakt dat ze meerdere keren worden uitgevoerd. U echter een situatie tegenkomen waarin u moet voorkomen dat een taak meerdere keer wordt uitgevoerd.

De eenvoudigste manier om te detecteren dat een taak al is uitgevoerd, is door een bestand te maken in de map **%TEMP%wanneer** de taak is geslaagd en deze aan het begin van de taak te zoeken. Hier is een voorbeeld cmd shell script dat dat voor u doet.

```cmd
REM   If Task1_Success.txt exists, then Application 1 is already installed.
IF EXIST "%PathToApp1Install%\Task1_Success.txt" (
  ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
  GOTO Finish
)

REM   Run your real exe task
ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (
  REM   The application installed without error. Create a file to indicate that the task
  REM   does not need to be run again.

  ECHO This line will create a file to indicate that Application 1 installed correctly. > "%PathToApp1Install%\Task1_Success.txt"

) ELSE (
  REM   An error occurred. Log the error and exit with the error code.

  DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
  TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
  ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

  EXIT %ERRORLEVEL%
)

:Finish

REM   Exit normally.
EXIT /B 0
```

## <a name="task-best-practices"></a>Best practices voor taken voor taken
Hier volgen enkele aanbevolen procedures die u moet volgen bij het configureren van taken voor uw web- of werknemersrol.

### <a name="always-log-startup-activities"></a>Startactiviteiten altijd registreren
Visual Studio biedt geen debugger om batchbestanden te doorlopen, dus het is goed om zoveel mogelijk gegevens over de werking van batchbestanden te krijgen. Het registreren van de uitvoer van batchbestanden, zowel **stdout** als **stderr,** kan u belangrijke informatie geven wanneer u batchbestanden probeert te debuggen en oplossen. Als u zowel **stdout** als **stderr** wilt aanmelden bij het bestand StartupLog.txt in `>>  "%TEMP%\\StartupLog.txt" 2>&1` de map waarnaar wordt aangegeven door de omgevingsvariabele **%TEMP%,** voegt u de tekst toe aan het einde van specifieke regels die u wilt aanmelden. Ga bijvoorbeeld als volgt te werk om setup.exe uit te voeren in de map **%PathToApp1Install%:**

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Om uw xml te vereenvoudigen, u een wrapper *cmd-bestand* maken dat al uw opstarttaken aanroept, samen met logboekregistratie en ervoor zorgt dat elke onderliggende taak dezelfde omgevingsvariabelen deelt.

U het misschien `>> "%TEMP%\StartupLog.txt" 2>&1` vervelend vinden om te gebruiken op het einde van elke opstarttaak. U taaklogboekregistratie afdwingen door een wrapper te maken waarmee u logboekregistratie verwerkt. Deze wrapper noemt het echte batchbestand dat u wilt uitvoeren. Elke uitvoer uit het doelbatchbestand wordt doorgestuurd naar het *bestand Startuplog.txt.*

In het volgende voorbeeld ziet u hoe u alle uitvoer van een opstartbatchbestand omleiden. In dit voorbeeld maakt het bestand ServerDefinition.csdef een opstarttaak die *logwrap.cmd aanroept.* *logwrap.cmd* calls *Startup2.cmd*, redirecting all output to **%TEMP%\\StartupLog.txt**.

ServiceDefinition.cmd:

```xml
<Startup>
    <Task commandLine="logwrap.cmd startup2.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

**logwrap.cmd:**

```cmd
@ECHO OFF

REM   logwrap.cmd calls passed in batch file, redirecting all output to the StartupLog.txt log file.

ECHO [%date% %time%] == START logwrap.cmd ============================================== >> "%TEMP%\StartupLog.txt" 2>&1
ECHO [%date% %time%] Running %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Call the child command batch file, redirecting all output to the StartupLog.txt log file.
START /B /WAIT %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Log the completion of child command.
ECHO [%date% %time%] Done >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (

   REM   No errors occurred. Exit logwrap.cmd normally.
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B 0

) ELSE (

   REM   Log the error.
   ECHO [%date% %time%] An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B %ERRORLEVEL%

)
```

**Startup2.cmd:**

```cmd
@ECHO OFF

REM   This is the batch file where the startup steps should be performed. Because of the
REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
REM   calling batch file.

ECHO [%date% %time%] Some log information about this task
ECHO [%date% %time%] Some more log information about this task

EXIT %ERRORLEVEL%
```

Voorbeelduitvoer in het bestand **StartupLog.txt:**

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> Het bestand **StartupLog.txt** bevindt zich in de map *C:\Resources\temp\\{role identifier}\RoleTemp.*
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>ExecutionContext op de juiste manier instellen voor opstarttaken
Stel bevoegdheden op de juiste manier in voor de opstarttaak. Soms moeten opstarttaken worden uitgevoerd met verhoogde bevoegdheden, ook al wordt de rol uitgevoerd met normale bevoegdheden.

Met het kenmerk [executionContexttaak][Task] wordt het bevoegdhedenniveau van de opstarttaak ingesteld. Met `executionContext="limited"` behulp van betekent dat de opstarttaak hetzelfde bevoegdhedenniveau heeft als de rol. Met `executionContext="elevated"` behulp van betekent dat de opstarttaak beheerdersbevoegdheden heeft, waardoor de opstarttaak beheerderstaken kan uitvoeren zonder beheerdersrechten aan uw rol te geven.

Een voorbeeld van een opstarttaak waarvoor verhoogde bevoegdheden vereist zijn, is een opstarttaak waarmee **AppCmd.exe** wordt gebruikt om IIS te configureren. **AppCmd.exe** `executionContext="elevated"`vereist .

### <a name="use-the-appropriate-tasktype"></a>Het juiste taaktype gebruiken
Het kenmerk [taaktypetaak][Task] bepaalt de manier waarop de opstarttaak wordt uitgevoerd. Er zijn drie waarden: **eenvoudig,** **achtergrond**en **voorgrond**. De achtergrond- en voorgrondtaken worden asynchroon gestart en vervolgens worden de eenvoudige taken één voor één synchroon uitgevoerd.

Met **eenvoudige** opstarttaken u de volgorde instellen waarin de taken worden uitgevoerd door de volgorde waarin de taken worden weergegeven in het bestand ServiceDefinition.csdef. Als een **eenvoudige** taak eindigt met een niet-nulexitcode, stopt de opstartprocedure en wordt de rol niet gestart.

Het verschil tussen opstarttaken op **de achtergrond** en **opstarttaken op de voorgrond** is dat **voorgrondtaken** de rol blijven uitvoeren totdat de **voorgrondtaak** is beëindigd. Dit betekent ook dat als de **voorgrondtaak** vastloopt of vastloopt, de rol niet wordt gerecycled totdat de **voorgrondtaak** wordt gesloten. Daarom worden **achtergrondtaken** aanbevolen voor asynchrone opstarttaken, tenzij u die functie van de **voorgrondtaak** nodig hebt.

### <a name="end-batch-files-with-exit-b-0"></a>Batchbestanden beëindigen met EXIT /B 0
De rol wordt alleen gestart als het **foutenpercentage** van elk van uw eenvoudige opstarttaak nul is. Niet alle programma's stellen het **foutenniveau** (exitcode) correct `EXIT /B 0` in, dus het batchbestand moet eindigen met een als alles correct is uitgevoerd.

Een `EXIT /B 0` ontbrekenaan aan het einde van een batchbestand is een veelvoorkomende oorzaak van rollen die niet worden gestart.

> [!NOTE]
> Ik heb gemerkt dat geneste batch bestanden `/B` soms hangen bij het gebruik van de parameter. U ervoor zorgen dat dit probleem met hangen niet gebeurt als een ander batchbestand uw huidige batchbestand aanroept, bijvoorbeeld als u de [logwrapper](#always-log-startup-activities)gebruikt. U de `/B` parameter in dit geval weglaten.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Verwacht dat opstarttaken meer dan één keer worden uitgevoerd
Niet alle rolrecyclen bevatten een reboot, maar alle rolrecycles omvatten het uitvoeren van alle opstarttaken. Dit betekent dat opstarttaken zonder problemen meerdere keren tussen reboots moeten kunnen worden uitgevoerd. Dit wordt besproken in [de vorige sectie](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Lokale opslag gebruiken om bestanden op te slaan die in de rol moeten worden geopend
Als u een bestand wilt kopiëren of maken tijdens uw opstarttaak dat vervolgens toegankelijk is voor uw rol, moet dat bestand in lokale opslag worden geplaatst. Zie de [vorige sectie](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Volgende stappen
Bekijk het [cloudservicemodel en -pakket](cloud-services-model-and-package.md)

Meer informatie over hoe [taken](cloud-services-startup-tasks.md) werken.

[Maak en implementeer](cloud-services-how-to-create-deploy-portal.md) uw cloudservicepakket.

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Taak]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Omgeving]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variabele]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceWaarde]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Eindpunten]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LokaalOpslag]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources (LocalResources)]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceWaarde]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue



