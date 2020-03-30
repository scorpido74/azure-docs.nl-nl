---
title: Met Visual Studio schakelt u Extern bureaublad in voor een rol (Azure Cloud Services)
description: Uw Azure-cloudservicetoepassing configureren om verbinding met extern bureaublad toe te staan
services: cloud-services
author: ghogen
manager: jillfra
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.workload: azure-vs
ms.date: 03/06/2018
ms.author: ghogen
ms.openlocfilehash: f4622e44c795182ee68c617f335c9e1651d3adcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294391"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-visual-studio"></a>Verbinding met extern bureaublad inschakelen voor een rol in Azure Cloud Services met Visual Studio

> [!div class="op_single_selector"]
> * [Azure-portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Powershell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Met Extern bureaublad heeft u toegang tot het bureaublad van een rol die in Azure wordt uitgevoerd. U een verbinding met Extern bureaublad gebruiken om problemen met uw toepassing op te lossen en te diagnosticeren terwijl deze wordt uitgevoerd.

De publicatiewizard die Visual Studio biedt voor cloudservices bevat een optie om Extern bureaublad in te schakelen tijdens het publicatieproces, met behulp van referenties die u verstrekt. Het gebruik van deze optie is geschikt bij het gebruik van Visual Studio 2017 versie 15.4 en eerder.

Met Visual Studio 2017 versie 15.5 en hoger wordt echter aangeraden om Extern bureaublad niet in te schakelen via de wizard Publiceren, tenzij u slechts als één ontwikkelaar werkt. Voor elke situatie waarin het project kan worden geopend door andere ontwikkelaars, schakelt u extern bureaublad in via de Azure-portal, via PowerShell of vanuit een releasepijplijn in een continue implementatiewerkstroom. Deze aanbeveling is te wijten aan een wijziging in de manier waarop Visual Studio communiceert met Remote Desktop op de cloudservice VM, zoals wordt uitgelegd in dit artikel.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Extern bureaublad configureren via Visual Studio 2017 versie 15.4 en eerder

Wanneer u Visual Studio 2017 versie 15.4 en eerder gebruikt, u de optie **Extern bureaublad inschakelen voor alle rollen** in de wizard Publiceren gebruiken. U de wizard nog steeds gebruiken met Visual Studio 2017 versie 15.5 en hoger, maar gebruik de optie Extern bureaublad niet.

1. Start in Visual Studio de wizard Publiceren door met de rechtermuisknop op uw cloudserviceproject in Solution Explorer te klikken en **Publiceren**te kiezen.

2. Meld u indien nodig aan bij uw Azure-abonnement en selecteer **Volgende**.

3. Selecteer **op** de pagina Instellingen **Extern bureaublad inschakelen voor alle rollen**en selecteer vervolgens de koppeling **Instellingen...** om het dialoogvenster **Extern bureaublad-configuratie** te openen.

4. Selecteer onder aan het dialoogvenster **meer opties**. Met deze opdracht wordt een vervolgkeuzelijst weergegeven waarin u een certificaat maakt of kiest, zodat u referentieskunt versleutelen wanneer u verbinding maakt via een extern bureaublad.

   > [!Note]
   > De certificaten die u nodig hebt voor een verbinding met extern bureaublad verschillen van de certificaten die u voor andere Azure-bewerkingen gebruikt. Het externe toegangscertificaat moet een privésleutel hebben.

5. Selecteer een certificaat in de lijst of kies ** &lt;Maken... &gt;**. Als u een nieuw certificaat maakt, geeft u een vriendelijke naam op voor het nieuwe certificaat wanneer daarom wordt gevraagd en selecteert u **OK**. Het nieuwe certificaat wordt weergegeven in de vervolgkeuzelijst.

6. Geef een gebruikersnaam en een wachtwoord op. U geen bestaand account gebruiken. Gebruik 'Administrator' niet als gebruikersnaam voor het nieuwe account.

7. Kies een datum waarop het account verloopt en waarna verbindingen voor extern bureaublad worden geblokkeerd.

8. Nadat u alle vereiste informatie hebt verstrekt, selecteert u **OK**. Visual Studio voegt de instellingen voor `.cscfg` Extern `.csdef` bureaublad toe aan de instellingen en bestanden van uw project, inclusief het wachtwoord dat is versleuteld met het gekozen certificaat.

9. Voer de resterende stappen uit met de knop **Volgende** en selecteer **Publiceren** wanneer u klaar bent om uw cloudservice te publiceren. Als u nog niet klaar bent om te publiceren, selecteert u **Annuleren** en antwoord **ja** wanneer u wordt gevraagd wijzigingen op te slaan. U uw cloudservice later publiceren met deze instellingen.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Extern bureaublad configureren wanneer u Visual Studio 2017-versie 15.5 en hoger gebruikt

Met Visual Studio 2017 versie 15.5 en hoger u de wizard publiceren nog steeds gebruiken met een cloudserviceproject. U de optie **Extern bureaublad inschakelen ook voor alle rollen** gebruiken als u slechts als één ontwikkelaar werkt.

Als u als onderdeel van een team werkt, moet u in plaats daarvan extern bureaublad op de Azure-cloudservice inschakelen met behulp van de [Azure-portal](cloud-services-role-enable-remote-desktop-new-portal.md) of [PowerShell.](cloud-services-role-enable-remote-desktop-powershell.md)

Deze aanbeveling is te wijten aan een wijziging in de manier waarop Visual Studio 2017 versie 15.5 en later communiceert met de cloudservice VM. Wanneer u Extern bureaublad inschakelt via de wizard Publiceren, communiceren eerdere versies van Visual Studio met de VM via de zogenaamde 'RDP-plug-in'. Visual Studio 2017 versie 15.5 en later communiceert in plaats daarvan met behulp van de "RDP-extensie" die veiliger en flexibeler is. Deze wijziging sluit ook aan bij het feit dat de Azure-portal en PowerShell-methoden om Extern bureaublad in te schakelen ook de RDP-extensie gebruiken.

Wanneer Visual Studio communiceert met de RDP-extensie, wordt een wachtwoord met platte tekst via TLS verzonden. De configuratiebestanden van het project slaan echter alleen een versleuteld wachtwoord op, dat alleen kan worden gedecodeerd tot platte tekst met het lokale certificaat dat oorspronkelijk werd gebruikt om het te versleutelen.

Als u het cloudserviceproject telkens vanaf dezelfde ontwikkelcomputer implementeert, is dat lokale certificaat beschikbaar. In dit geval u nog steeds de optie **Extern bureaublad inschakelen voor alle rollen** in de wizard Publiceren gebruiken.

Als u of andere ontwikkelaars het cloudserviceproject echter vanaf verschillende computers willen implementeren, hebben die andere computers niet het benodigde certificaat om het wachtwoord te decoderen. Als gevolg hiervan ziet u het volgende foutbericht:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

U het wachtwoord elke keer wijzigen wanneer u de cloudservice implementeert, maar die actie wordt lastig voor iedereen die Extern bureaublad moet gebruiken.

Als u het project deelt met een team, u het beste de optie in de wizard Publiceren wissen wissen wissen en in plaats daarvan Extern bureaublad rechtstreeks inschakelen via de [Azure-portal](cloud-services-role-enable-remote-desktop-new-portal.md) of met [PowerShell.](cloud-services-role-enable-remote-desktop-powershell.md)

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Implementeren vanaf een buildserver met Visual Studio 2017 versie 15.5 en hoger

U een cloudserviceproject implementeren vanaf een buildserver (bijvoorbeeld met Azure DevOps Services) waarop Visual Studio 2017 versie 15.5 of hoger is geïnstalleerd in de buildagent. Met deze regeling vindt implementatie plaats vanaf dezelfde computer waarop het versleutelingscertificaat beschikbaar is.

Als u de RDP-extensie van Azure DevOps Services wilt gebruiken, neemt u de volgende details op in uw buildpijplijn:

1. Neem `/p:ForceRDPExtensionOverPlugin=true` in uw MSBuild-argumenten op om ervoor te zorgen dat de implementatie werkt met de RDP-extensie in plaats van met de RDP-plug-in. Bijvoorbeeld:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Voeg na uw bouwstappen de stap **Azure Cloud Service Deployment** toe en stelt de eigenschappen ervan in.

1. Voeg na de implementatiestap een **Azure Powershell-stap** toe, stel de eigenschap **Weergavenaam** in op 'Azure Deployment: Enable RDP Extension' (of een andere geschikte naam) en selecteer het juiste Azure-abonnement.

1. Stel **Scripttype** in op 'Inline' en plak de onderstaande code in het veld **Inline Script.** (U ook `.ps1` een bestand in uw project maken met dit script, **Scripttype** instellen op 'Scriptbestandspad' en **scriptpad** instellen om naar het bestand te wijzen.)

    ```ps
    Param(
        [Parameter(Mandatory=$True)]
        [string]$username,

        [Parameter(Mandatory=$True)]
        [string]$password,

        [Parameter(Mandatory=$True)]
        [string]$serviceName,

        [Datetime]$expiry = ($(Get-Date).AddYears(1))
    )

    Write-Host "Service Name: $serviceName"
    Write-Host "User Name: $username"
    Write-Host "Expiry: $expiry"

    $securepassword = ConvertTo-SecureString -String $password -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential $username,$securepassword

    # Try to remote existing RDP Extensions
    try
    {
        $existingRDPExtension = Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
        if ($existingRDPExtension -ne $null)
        {
            Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
        }
    }
    catch
    {
    }

    Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry -Verbose
    ```

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Verbinding maken met een Azure-rol met Extern bureaublad

Nadat u uw cloudservice op Azure hebt gepubliceerd en Extern bureaublad hebt ingeschakeld, u Visual Studio Server Explorer gebruiken om u aan te melden bij de VM van de cloudservice:

1. Vouw in Server Explorer het **Azure-knooppunt** uit en vouw vervolgens het knooppunt uit voor een cloudservice en een van de rollen ervan om een lijst met instanties weer te geven.

2. Klik met de rechtermuisknop op een instantieknooppunt en selecteer **Verbinding maken met Extern bureaublad**.

3. Voer de gebruikersnaam en het wachtwoord in die u eerder hebt gemaakt. U bent nu aangemeld bij uw externe sessie.

## <a name="additional-resources"></a>Aanvullende bronnen

[Cloud Services configureren](cloud-services-how-to-configure-portal.md)
