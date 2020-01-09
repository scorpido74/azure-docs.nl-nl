---
title: Azure-toepassing inzichten opentellt lokale doorstuur server (preview-versie)
description: Voorwaarts gedistribueerde traceringen en intervallen van de open telling van talen zoals python en ga naar Azure-toepassing Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2018
ms.reviewer: nimolnar
ms.openlocfilehash: 25c6c82fa1179a9173f42c3a5a4e95a371dd49c6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75406609"
---
# <a name="local-forwarder-preview"></a>Lokale doorstuur server (preview-versie)

Lokale doorstuur server is een agent die de telemetrie van Application Insights of [Opentellingen](https://opencensus.io/) verzamelt vanuit diverse sdk's en deze naar Application Insights stuurt. Het kan worden uitgevoerd onder Windows en Linux. U kunt deze ook uitvoeren onder macOS, maar dit wordt op dit moment niet officieel ondersteund.

## <a name="running-local-forwarder"></a>Lokale doorstuur server wordt uitgevoerd

Lokale doorstuur server is een [open-source project op github](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases). Er zijn verschillende manieren om een lokale doorstuur server uit te voeren op meerdere platforms.

### <a name="windows"></a>Windows

#### <a name="windows-service"></a>Windows Service

De eenvoudigste manier om een lokale doorstuur server uit te voeren onder Windows is door deze te installeren als een Windows-service. De release wordt geleverd met een Windows-service-uitvoerbaar bestand (*WindowsServiceHost/Microsoft. LocalForwarder. WindowsServiceHost. exe*), dat eenvoudig kan worden geregistreerd bij het besturings systeem.

> [!NOTE]
> Voor de lokale doorstuur server is mini maal .NET Framework 4,7 vereist. Als u niet beschikt over .NET Framework 4,7, wordt de service niet geïnstalleerd, maar wordt deze niet gestart. Voor toegang tot de meest recente versie van .NET Framework **[gaat u naar de downloadpagina van .NET Framework](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)** .

1. Down load het BF. WindowsServiceHost. zip-bestand van de pagina voor de [lokale doorstuur server](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases) op github.

    ![Scherm afbeelding van de download pagina van de lokale doorstuur server](./media/opencensus-local-forwarder/001-local-forwarder-windows-service-host-zip.png)

2. In dit voor beeld wordt het zip-bestand alleen uitgepakt naar het pad `C:\LF-WindowsServiceHost`.

    Als u de service wilt registreren en deze wilt configureren om te starten bij het opstarten van het systeem, voert u het volgende uit vanaf de opdracht regel als Administrator:

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    U ontvangt een antwoord van het volgende:
    
    `[SC] CreateService SUCCESS`
    
    Als u uw nieuwe service wilt onderzoeken via het GUI-type voor services ``services.msc``
        
     ![Scherm afbeelding van lokale doorstuur server](./media/opencensus-local-forwarder/002-services.png)

3. **Klik** met de rechter muisknop op de nieuwe lokale doorstuur server en selecteer **starten**. Uw service voert nu een actieve status in.

4. De service wordt standaard zonder herstel acties gemaakt. U kunt met de **rechter muisknop klikken** en **Eigenschappen** > **herstel** selecteren om automatische antwoorden op een service fout te configureren.

    Of als u de automatische herstel opties programmatisch wilt instellen voor wanneer er fouten optreden, kunt u het volgende gebruiken:

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. In dezelfde locatie als uw ``Microsoft.LocalForwarder.WindowsServiceHost.exe``-bestand, dat in dit voor beeld ``C:\LF-WindowsServiceHost`` een bestand met de naam ``LocalForwarder.config``. Dit is een XML-bestand waarmee u de configuratie van uw localforwader kunt aanpassen en de instrumentatie sleutel moet opgeven van de Application Insights resource waarvoor uw gedistribueerde tracerings gegevens moeten worden doorgestuurd. 

    Nadat u het ``LocalForwarder.config`` bestand hebt bewerkt om uw instrumentatie sleutel toe te voegen, moet u de **lokale doorstuur server** opnieuw starten om uw wijzigingen toe te passen.
    
6. Controleer het ``LocalForwarder.log`` bestand om te bevestigen dat de gewenste instellingen aanwezig zijn en dat de lokale doorstuur server luistert naar traceer gegevens zoals verwacht. U ziet resultaten die vergelijkbaar zijn met de onderstaande afbeelding onder aan het bestand:

    ![Scherm opname van het bestand LocalForwarder. log](./media/opencensus-local-forwarder/003-log-file.png)

#### <a name="console-application"></a>Console toepassing

Voor bepaalde gebruiks gevallen kan het handig zijn om een lokale doorstuur server uit te voeren als een console toepassing. De release wordt geleverd met de volgende uitvoer bare versies van de console-host:
* een Framework-afhankelijke .NET core binaire */ConsoleHost/Publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Als u dit binaire bestand wilt uitvoeren, moet u een .NET core runtime installeren. Raadpleeg deze download [pagina](https://www.microsoft.com/net/download/dotnet-core/2.1) voor meer informatie.
  ```batchfile
  E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
  ```
* een op zichzelf staande, .NET core-set binaire bestanden voor x86-en x64-platforms. Hiervoor is geen .NET core runtime vereist om te worden uitgevoerd. */ConsoleHost/win-x86/publish/Microsoft.LocalForwarder.ConsoleHost.exe*, */ConsoleHost/win-x64/publish/Microsoft.LocalForwarder.ConsoleHost.exe*.
  ```batchfile
  E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  ```

### <a name="linux"></a>Linux

Net als bij Windows wordt de release geleverd met de volgende uitvoer bare versies van de console-host:
* een Framework-afhankelijke .NET core binaire */ConsoleHost/Publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Als u dit binaire bestand wilt uitvoeren, moet u een .NET core runtime installeren. Raadpleeg deze download [pagina](https://www.microsoft.com/net/download/dotnet-core/2.1) voor meer informatie.

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* een op zichzelf staande .NET-hoofdset binaire bestanden voor Linux-64. Voor deze versie hoeft geen .NET core runtime te worden uitgevoerd. */ConsoleHost/linux-x64/publish/Microsoft.LocalForwarder.ConsoleHost*.

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

Veel Linux-gebruikers willen een lokale doorstuur server als daemon uitvoeren. Linux-systemen worden geleverd met diverse oplossingen voor Service Management, zoals upstart, sysv of gesystemed. Wat uw specifieke versie is, u kunt deze gebruiken om een lokale doorstuur server uit te voeren op een manier die het meest geschikt is voor uw scenario.

U kunt bijvoorbeeld een daemon-service maken met behulp van systemed. We gebruiken de Framework-afhankelijke versie, maar dit kan ook worden gedaan voor een zelfstandig deel van het abonnement.

* Maak het volgende service bestand met de naam *localforwarder. service* en plaats het in */lib/systemd/System*.
In dit voor beeld wordt ervan uitgegaan dat uw gebruikers naam SAMPLE_USER is en dat u lokale binaire bestanden voor doorstuur servers (van */ConsoleHost/Publish*) hebt gekopieerd naar */home/SAMPLE_USER/LOCALFORWARDER_DIR*.

```
# localforwarder.service
# Place this file into /lib/systemd/system/
# Use 'systemctl enable localforwarder' to start the service automatically on each boot
# Use 'systemctl start localforwarder' to start the service immediately

[Unit]
Description=Local Forwarder service
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=SAMPLE_USER
WorkingDirectory=/home/SAMPLE_USER/LOCALFORWARDER_DIR
ExecStart=/usr/bin/env dotnet /home/SAMPLE_USER/LOCALFORWARDER_DIR/Microsoft.LocalForwarder.ConsoleHost.dll noninteractive

[Install]
WantedBy=multi-user.target
```

* Voer de volgende opdracht uit om aan te geven dat de lokale doorstuur server op elke keer moet worden gestart

```
systemctl enable localforwarder
```

* Voer de volgende opdracht uit om ervoor te zorgen dat de lokale doorstuur server onmiddellijk wordt gestart

```
systemctl start localforwarder
```

* Controleer de service door * *. log* -bestanden te controleren in de map/Home/SAMPLE_USER/LOCALFORWARDER_DIR.

### <a name="mac"></a>Mac
Lokale doorstuur servers werken mogelijk met macOS, maar deze worden momenteel niet officieel ondersteund.

### <a name="self-hosting"></a>Zelf hosting
Lokale doorstuur servers worden ook gedistribueerd als een .NET Standard NuGet-pakket, zodat u het kunt hosten in uw eigen .NET-toepassing.

```csharp
using Library;
...
Host host = new Host();

// see section below on configuring local forwarder
string configuration = ...;
    
host.Run(config, TimeSpan.FromSeconds(5));
...
host.Stop();
```

## <a name="configuring-local-forwarder"></a>Lokale doorstuur server configureren

* Bij het uitvoeren van een van de eigen hosts van de lokale doorstuur server (console host of Windows servicehost), vindt u **LocalForwarder. config** , dat naast het binaire bestand is geplaatst.
* Bij het zelf hosten van de lokale doorstuur server NuGet, moet de configuratie van dezelfde indeling worden vermeld in code (Zie de sectie over zelf hosting). Voor de configuratie syntaxis controleert u de [LocalForwarder. config](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config) in de GitHub-opslag plaats. 

> [!NOTE]
> Configuratie kan veranderen van release naar release, dus let op de versie die u gebruikt.

## <a name="monitoring-local-forwarder"></a>Lokale doorstuur server bewaken

Traceringen worden naar het bestands systeem geschreven naast het uitvoer bare bestand dat de lokale doorstuur server uitvoert (zoeken naar * *. log* -bestanden). U kunt een bestand met de naam *NLog. config* naast het uitvoer bare programma plaatsen om uw eigen configuratie op te geven in plaats van de standaard instelling. Raadpleeg de [documentatie](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format) voor de beschrijving van de indeling.

Als er geen configuratie bestand wordt gegeven (dit is de standaard instelling), gebruikt de lokale doorstuur server de standaard configuratie die [hier](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config)kan worden gevonden.

## <a name="next-steps"></a>Volgende stappen

* [Open Census](https://opencensus.io/)
