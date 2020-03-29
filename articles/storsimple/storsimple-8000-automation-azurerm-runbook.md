---
title: Azure Automation Runbook gebruiken om StorSimple-apparaten te beheren
description: Meer informatie over het gebruik van Azure Automation Runbook om StorSimple-taken te automatiseren
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: 727bebe0c190ed4dff4408884c45fe166ad541a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276960"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Azure Automation-runbooks gebruiken om StorSimple-apparaten te beheren

In dit artikel wordt beschreven hoe Azure Automation-runbooks worden gebruikt om uw StorSimple 8000-serieapparaat in Azure-portal te beheren. Een voorbeeld van runbook is opgenomen om u door de stappen van het configureren van uw omgeving om dit runbook uit te voeren.


## <a name="configure-add-and-run-azure-runbook"></a>Azure-runbook configureren, toevoegen en uitvoeren

In deze sectie wordt een voorbeeld van Windows PowerShell-script voor StorSimple opgenomen en worden de verschillende stappen beschreven die nodig zijn om het script in een runbook te importeren en vervolgens het runbook te publiceren en uit te voeren.

### <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u:

* een actief Azure-abonnement dat is gekoppeld aan uw StorSimple Device Manager-service die is geregistreerd bij een apparaat uit de StorSimple 8000-serie.

* Windows PowerShell 5.0 is geïnstalleerd op uw computer (of uw Windows Server-host voor uw StorSimple als u er een gebruikt).

### <a name="create-automation-runbook-module-in-windows-powershell"></a>Automatiseringsrunbookmodule maken in Windows PowerShell

Voer de volgende stappen uit om een automatiseringsmodule voor het apparaatbeheer van de StorSimple 8000-serie te maken:

1. Start Windows PowerShell. Maak een nieuwe map en wijzig de map in de nieuwe map.

    ```powershell
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```

2. [Download NuGet CLI](https://www.nuget.org/downloads) onder de map die in de vorige stap is gemaakt. Er zijn verschillende versies van _nuget.exe_. Kies de versie die overeenkomt met uw SDK. Elke downloadlink verwijst rechtstreeks naar een _.exe-bestand._ Zorg ervoor dat u met de rechtermuisknop klikt en het bestand op uw computer opslaat in plaats van het vanuit de browser uit te voeren.

    U ook de volgende opdracht uitvoeren om het script te downloaden en op te slaan in dezelfde map die u eerder hebt gemaakt.

    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

3. Download de afhankelijke SDK.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

4. Download het script van het voorbeeld Van GitHub-project.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    ```

5. Maak een Azure Automation Runbook Module voor apparaatbeheer uit de StorSimple 8000-serie. Typ in het Windows Powershell-venster de volgende opdrachten:

    ```powershell
        # set path variables
        $downloadDir = "C:\scripts\StorSimpleSDKTools"
        $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

        #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
        mkdir "$moduleDir"
        Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
        Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

        #Don't change the name of the Archive
        compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

6. Controleer of er een zip-bestand voor automatiseringsmodule is gemaakt in `C:\scripts\StorSimpleSDKTools`.

    ![verify-automation-module](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. De volgende uitvoer wordt gepresenteerd wanneer de automatiseringsmodule wordt gemaakt via de Windows PowerShell.

    ```powershell
    mkdir C:\scripts\StorSimpleSDKTools
    ```

    ```Output
        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools
    ```

    ```powershell
    wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 717.48 ms
    ```

    ```powershell
    wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    # set path variables
    $downloadDir = "C:\scripts\StorSimpleSDKTools"
    $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"
    #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
    mkdir "$moduleDir"
    ```

    ```Output
        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series
    ```

    ```powershell
    Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
    Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    #Don't change the name of the Archive
    compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

### <a name="import-publish-and-run-automation-runbook"></a>Runbook voor automatisering importeren, publiceren en uitvoeren

1. Maak een Azure Run As-automatiseringsaccount in de Azure-portal. Ga hiervoor naar **Azure marketplace > Alles** en zoek vervolgens naar **Automatisering.** Selecteer **Automatiseringsaccounts**.

    ![zoekautomatisering](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. Ga als bedoeld in het blade **Van automatiseringsaccount toevoegen:**

   1. Geef de **naam** van uw automatiseringsaccount op.
   2. Selecteer het **abonnement dat** is gekoppeld aan uw StorSimple Device Manager-service.
   3. Maak een nieuwe resourcegroep of selecteer uit een bestaande resourcegroep.
   4. Selecteer een **locatie** (indien mogelijk hetzelfde als waar uw service wordt uitgevoerd).
   5. Laat de standaard **optie Run as-account maken** geselecteerd.
   6. Schakel optioneel **Vastmaken aan dashboard in.** Klik **op Maken**.

       ![create-automation-account](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      Nadat het automatiseringsaccount is gemaakt, wordt u hiervan op de hoogte gesteld. Ga voor meer informatie over het maken van een Automatiseringsaccount naar [Een Run As-account maken.](https://docs.microsoft.com/azure/automation/automation-create-runas-account)

3. Als u ervoor wilt zorgen dat het gemaakte automatiseringsaccount toegang heeft tot de StorSimple Device Manager-service, moet u de juiste machtigingen toewijzen aan het automatiseringsaccount. Ga naar **Toegangscontrole** in uw StorSimple Device Manager-service. Klik **op + Voeg** de naam van uw Azure Automation-account toe en geef deze op. Sla de instellingen **op.**

    ![add-permissions-automation-account](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. Ga in het nieuw gemaakte account naar **Gedeelde bronnen > Modules** en klik op module toevoegen **.**

5. Blader in het **moduleblad Toevoegen** naar de locatie van de ritsmodule en selecteer en open de module. Klik op **OK**.

    ![add-module](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Ga naar **Procesautomatisering > Runbooks en klik op + Een runbook toevoegen**. Klik in het **runbook-blad toevoegen** op **Een bestaand runbook importeren**. Wijs het Windows PowerShell-scriptbestand aan voor het **Runbook-bestand**. Het type runbook wordt automatisch geselecteerd. Geef een naam en een optionele beschrijving op voor het runbook. Klik **op Maken**.

    ![add-module](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. Het runbook wordt toegevoegd aan de lijst met runbooks. Selecteer en klik op dit runbook.

    ![klik-nieuw-runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Bewerk het runbook en klik op **Deelvenster Testen**. Geef de parameters op, zoals de naam van uw StorSimple Device Manager-service, de naam van het StorSimple-apparaat en het abonnement. **Start** de test. Het rapport wordt gegenereerd wanneer de run is voltooid. Ga voor meer informatie naar [hoe u een runbook testen.](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook)

    ![test-runbook](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Controleer de uitvoer uit het runbook in het testvenster. Sluit indien tevreden het deelvenster. Klik **op Publiceren** en wanneer u om bevestiging wordt gevraagd, bevestig en publiceer het runbook.

    ![publicatie-runbook](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)

## <a name="next-steps"></a>Volgende stappen

[Gebruik de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)
