---
title: Azurite-emulator gebruiken voor lokale Azure Storage-ontwikkeling
description: De Azurite open-source emulator (preview) biedt een gratis lokale omgeving voor het testen van uw Azure-opslagtoepassingen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 5e1fce0852a4e820d7ee0af626ce3fddf6773750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76029924"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development-and-testing-preview"></a>De Azurite-emulator gebruiken voor lokale Azure Storage-ontwikkeling en -tests (voorbeeld)

De Azurite versie 3.2 open-source emulator (preview) biedt een gratis lokale omgeving voor het testen van uw Azure blob en wachtrij opslag toepassingen. Als u tevreden bent over de manier waarop uw toepassing lokaal werkt, schakelt u over naar het gebruik van een Azure Storage-account in de cloud. De emulator biedt cross-platform ondersteuning op Windows, Linux en MacOS. Azurite v3 ondersteunt API's die zijn geïmplementeerd door de Azure Blob-service.

Azurite is het toekomstige opslagemulatorplatform. Azurite vervangt de [Azure Storage Emulator.](storage-use-emulator.md) Azurite blijft worden bijgewerkt om de nieuwste versies van Azure Storage API's te ondersteunen.

Er zijn verschillende manieren om Azurite op uw lokale systeem te installeren en uit te voeren:

  1. [De Azurite Visual Studio Code-extensie installeren en uitvoeren](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Azurite installeren en uitvoeren met NPM](#install-and-run-azurite-by-using-npm)
  1. [De Azurite Docker-afbeelding installeren en uitvoeren](#install-and-run-the-azurite-docker-image)
  1. [Kloon, bouw en voer Azurite uit vanuit de GitHub-repository](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>De Azurite Visual Studio Code-extensie installeren en uitvoeren

Selecteer in Visual Studio Code het deelvenster **EXTENSIES** en zoek naar *Azurite* in de **OPTIE-EXTENSIES:MARKETPLACE**.

![Visual Studio Code-extensies marktplaats](media/storage-use-azurite/azurite-vs-code-extension.png)

U ook naar [de VS-extensiemarkt voor code in](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) uw browser navigeren. Selecteer de knop **Installeren** om Visual Studio Code te openen en ga rechtstreeks naar de uitbreidingspagina van Azurite.

U Azurite snel starten of sluiten door te klikken op **[Azurite Blob Service]** of **[Azurite Queue Service]** in de VS-codestatusbalk of door de volgende opdrachten uit te geven in het vs-code-opdrachtpalet. Druk op **F1** in VS-code om het opdrachtpalet te openen.

De extensie ondersteunt de volgende opdrachten voor Visual Studio Code:

   * **Azurite: Start** - Start alle Azurite diensten
   * **Azurite: Close** - Sluit alle Azurite-diensten
   * **Azurite: Clean** - Reset alle Azurite-services persistency data
   * **Azurite: Blob-service starten** - Blob-service starten
   * **Azurite: Blob-service sluiten** - Blob-service sluiten
   * **Azurite: Clean Blob Service** - Schone blob service
   * **Azurite: Wachtrijservice starten** - Wachtrijservice starten
   * **Azurite: Close Queue Service** - Close queue service
   * **Azurite: Clean Queue Service** - Clean queue service

Als u Azurite wilt configureren in Visual Studio Code, selecteert u het deelvenster Extensies. Selecteer het pictogram **Beheren** (versnelling) voor **Azurite**. Selecteer **Extensie-instellingen configureren**.

![Instellingen voor azurite configureren](media/storage-use-azurite/azurite-configure-extension-settings.png)

De volgende instellingen worden ondersteund:

   * **Azurite: Blob Host** - Het luistereindpunt van de Blob-service. De standaardinstelling is 127.0.0.1.
   * **Azurite: Blob Port** - De Blob-serviceluisterpoort. De standaardpoort is 10000.
   * **Azurite: Debug** - Uitvoer het foutopsporingslogboek naar het Azurite-kanaal. De standaardwaarde is **onwaar**.
   * **Azurite: Locatie** - Het locatiepad van de werkruimte. De standaardinstelling is de map Visual Studio Code working.
   * **Azurite: Queue Host** - Het luistereindpunt van de wachtrijservice. De standaardinstelling is 127.0.0.1.
   * **Azurite: Queue Port** - The Queue service listening port. De standaardpoort is 10001.
   * **Azurite: Silent** - Silent mode schakelt het toegangslogboek uit. De standaardwaarde is **onwaar**.

## <a name="install-and-run-azurite-by-using-npm"></a>Azurite installeren en uitvoeren met NPM

Deze installatiemethode vereist dat [Node.js versie 8.0 of hoger](https://nodejs.org) is geïnstalleerd. **npm** is de package management tool bij elke Node.js installatie. Nadat u Node.js hebt geïnstalleerd, voert u de volgende **npm-opdracht** uit om Azurite te installeren.

```console
npm install -g azurite
```

Na het installeren van Azurite, zie [Run Azurite vanaf een command-line](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>De Azurite Docker-afbeelding installeren en uitvoeren

Gebruik [DockerHub](https://hub.docker.com/) om de [nieuwste Azurite-afbeelding](https://hub.docker.com/_/microsoft-azure-storage-azurite) te trekken met behulp van de volgende opdracht:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Voer de Azurite Docker-afbeelding uit:**

Met de volgende opdracht wordt de afbeelding van Azurite Docker uitgevoerd. De `-p 10000:10000` parameter verwijst aanvragen van de poort 10000 van de hostmachine om naar de instantie Docker.

```console
docker run -p 10000:10000 -p 10001:10001 mcr.microsoft.com/azure-storage/azurite
```

**Geef de locatie van de werkruimte op:**

In het volgende `-v c:/azurite:/data` voorbeeld geeft de parameter *c:/azurite* aan als de Azurite-gegevenslocatie. De map, *c:/azurite,* moet worden gemaakt voordat de opdracht Docker wordt uitgevoerd.

```console
docker run -p 10000:10000 -p 10001:10001 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Alleen de blobservice uitvoeren**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

**Stel alle Azurite-parameters in:**

In dit voorbeeld ziet u hoe u alle opdrachtregelparameters instelt. Alle onderstaande parameters moeten op één opdrachtregel worden geplaatst.

```console
docker run -p 8888:8888
           -p 9999:9999
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
           --queuePort 9999
           --queueHost 0.0.0.0
```

Zie [Opdrachtregelopties](#command-line-options) voor meer informatie over het configureren van Azurite bij het opstarten.

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Kloon, bouw en voer Azurite uit vanuit de GitHub-repository

Deze installatiemethode vereist dat u [Git](https://git-scm.com/) hebt geïnstalleerd. Kloon de [GitHub-repository](https://github.com/azure/azurite) voor het Azurite-project met behulp van de volgende consoleopdracht.

```console
git clone https://github.com/Azure/Azurite.git
```

Na het klonen van de broncode voert u volgende opdrachten uit vanaf de wortel van de gekloonde repo om Azurite te bouwen en te installeren.

```console
npm install
npm run build
npm install -g
```

Na het installeren en bouwen van Azurite, zie [Run Azurite vanaf een command-line](#run-azurite-from-a-command-line).

## <a name="run-azurite-from-a-command-line"></a>Azurite uitvoeren vanaf een opdrachtregel

> [!NOTE]
> Azurite kan niet vanaf de opdrachtregel worden uitgevoerd als u alleen de Visual Studio Code-extensie hebt geïnstalleerd. Gebruik in plaats daarvan het opdrachtpalet VS-code. Zie [De Azurite Visual Studio Code-extensie installeren en uitvoeren](#install-and-run-the-azurite-visual-studio-code-extension)voor meer informatie.

Als u onmiddellijk aan de slag wilt met de opdrachtregel, maakt u een map met de naam **c:\azurite**en start u Azurite door de volgende opdracht uit te geven:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Met deze opdracht moet Azurite alle gegevens opslaan in een bepaalde map, **c:\azurite**. Als de **optie --locatie** wordt weggelaten, wordt de huidige werkmap gebruikt.

## <a name="command-line-options"></a>Opdrachtregelopties

In deze sectie worden de schakels van de opdrachtlijn beschreven die beschikbaar zijn bij het starten van Azurite. Alle command-line switches zijn optioneel.

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>] 
    [-d | --debug <log file path>] [-l | --location <workspace path>]
    [--queueHost <IP address>] [--queuePort <port address>]
    [-s | --silent] [-h | --help]
```

De **-d** is een snelkoppeling voor **--debug**, **-l-schakelaar** is een snelkoppeling voor **--locatie,** **-s** is een snelkoppeling voor **--stil**en **-h** is een snelkoppeling voor **--help**.

### <a name="blob-listening-host"></a>Blob-luisterhost

**Optioneel** Standaard luistert Azurite naar 127.0.0.1 als lokale server. Gebruik de **--blobHost-schakelaar** om het adres in te stellen op uw vereisten.

Accepteer alleen aanvragen op de lokale machine:

```console
azurite --blobHost 127.0.0.1
```

Externe aanvragen toestaan:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> Het toestaan van externe verzoeken kan uw systeem kwetsbaar maken voor externe aanvallen.

### <a name="blob-listening-port-configuration"></a>Blob-luisterpoortconfiguratie

**Optioneel** Azurite luistert standaard naar de Blob-service op poort 10000. Gebruik de **--blobPort-schakelaar** om de gewenste luisterpoort op te geven.

> [!NOTE]
> Nadat u een aangepaste poort hebt gebruikt, moet u de verbindingstekenreeks of de bijbehorende configuratie bijwerken in uw Azure Storage-hulpprogramma's of SDK's.

Pas de luisterpoort van de Blob-service aan:

```console
azurite --blobPort 8888
```

Laat het systeem automatisch een beschikbare poort selecteren:

```console
azurite --blobPort 0
```

De in gebruik zijnde poort wordt weergegeven tijdens het opstarten van Azurite.

### <a name="queue-listening-host"></a>Luisterende wachtrij

**Optioneel** Standaard luistert Azurite naar 127.0.0.1 als lokale server. Gebruik de **schakelaar -wachtrijHost** om het adres in te stellen op uw vereisten.

Accepteer alleen aanvragen op de lokale machine:

```console
azurite --queueHost 127.0.0.1
```

Externe aanvragen toestaan:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> Het toestaan van externe verzoeken kan uw systeem kwetsbaar maken voor externe aanvallen.

### <a name="queue-listening-port-configuration"></a>Configuratie van de luisterpoort in de wachtrij

**Optioneel** Azurite luistert standaard naar de Queue-service op poort 10001. Gebruik de **poortschakelaar --queue** om de gewenste luisterpoort op te geven.

> [!NOTE]
> Nadat u een aangepaste poort hebt gebruikt, moet u de verbindingstekenreeks of de bijbehorende configuratie bijwerken in uw Azure Storage-hulpprogramma's of SDK's.

Pas de luisterpoort van de wachtrijservice aan:

```console
azurite --queuePort 8888
```

Laat het systeem automatisch een beschikbare poort selecteren:

```console
azurite --queuePort 0
```

De in gebruik zijnde poort wordt weergegeven tijdens het opstarten van Azurite.

### <a name="workspace-path"></a>Werkruimtepad

**Optioneel** Azurite slaat gegevens op de lokale schijf tijdens de uitvoering. Gebruik de **--locatieschakelaar** om een pad op te geven als de locatie van de werkruimte. Standaard wordt de huidige werkmap voor processen gebruikt.

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>Toegangslogboek

**Optioneel** Standaard wordt het toegangslogboek weergegeven in het consolevenster. Schakel de weergave van het toegangslogboek uit met behulp van de **stille** schakelaar.

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>Foutopsporingslogboek

**Optioneel** Het foutopsporingslogboek bevat gedetailleerde informatie over elk verzoek en de lijsttracering van uitzonderingen. Schakel het foutopsporingslogboek in door een geldig lokaal bestandspad naar de **foutopsporingsschakelaar** te geven.

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

### <a name="loose-mode"></a>Losse modus

**Optioneel** Azurite past standaard een strikte modus toe om niet-ondersteunde aanvraagkoppen en -parameters te blokkeren. Schakel de strikte modus uit met de **losse** schakelaar.

```console
azurite --loose
```

Let op de kapitaaltoetsschakelaar 'L':

```console
azurite -L
```

## <a name="authorization-for-tools-and-sdks"></a>Autorisatie voor gereedschappen en SDK's

Maak verbinding met Azurite via Azure Storage SDKs of hulpprogramma's, zoals [Azure Storage Explorer,](https://azure.microsoft.com/features/storage-explorer/)met behulp van een verificatiestrategie. Verificatie is vereist. Azurite ondersteunt autorisatie met Shared Key en shared access signatures (SAS). Azurite ondersteunt ook anonieme toegang tot openbare containers.

### <a name="well-known-storage-account-and-key"></a>Bekende opslagrekening en sleutel

U de volgende accountnaam en sleutel met Azurite gebruiken. Dit is dezelfde bekende account en sleutel die wordt gebruikt door de verouderde Azure-opslagemulator.

* Accountnaam:`devstoreaccount1`
* Accountsleutel:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> Naast SharedKey-verificatie ondersteunt Azurite account- en serviceSAS-verificatie. Anonieme toegang is ook beschikbaar wanneer een container is ingesteld om openbare toegang mogelijk te maken.

### <a name="connection-string"></a>Verbindingsreeks

De eenvoudigste manier om verbinding te maken met Azurite vanuit uw toepassing is door een verbindingstekenreeks in het configuratiebestand van uw toepassing te configureren die verwijst naar de snelkoppeling *UseDevelopmentStorage=true.* Hier is een voorbeeld van een verbindingstekenreeks in een *app.config-bestand:*

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Zie [Azure Storage-verbindingstekenreeksen configureren](storage-configure-connection-string.md)voor meer informatie .

### <a name="custom-storage-accounts-and-keys"></a>Aangepaste opslagaccounts en -sleutels

Azurite ondersteunt aangepaste opslagaccountnamen en `AZURITE_ACCOUNTS` -sleutels door de `account1:key1[:key2];account2:key1[:key2];...`omgevingsvariabele in de volgende indeling in te stellen: .

Gebruik bijvoorbeeld een aangepast opslagaccount met één sleutel:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

Of gebruik meerdere opslagaccounts met elk 2 sleutels:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

Azurite vernieuwt standaard standaard standaard aangepaste accountnamen en sleutels uit de omgevingsvariabele elke minuut. Met deze functie u de accountsleutel dynamisch roteren of nieuwe opslagaccounts toevoegen zonder Azurite opnieuw te starten.

> [!NOTE]
> Het `devstoreaccount1` standaardopslagaccount is uitgeschakeld wanneer u aangepaste opslagaccounts instelt.

> [!NOTE]
> Werk de verbindingstekenreeks dienovereenkomstig bij wanneer u aangepaste accountnamen en -sleutels gebruikt.

> [!NOTE]
> Gebruik `export` het trefwoord om omgevingsvariabelen in te `set` stellen in een Linux-omgeving die wordt gebruikt in Windows.

### <a name="storage-explorer"></a>Opslagverkenner

Maak in Azure Storage Explorer verbinding met Azurite door op het pictogram **Account toevoegen** te klikken en vervolgens **Bijvoegen aan een lokale emulator te** selecteren en op Verbinding **maken**te klikken.

## <a name="differences-between-azurite-and-azure-storage"></a>Verschillen tussen Azurite en Azure Storage

Er zijn functionele verschillen tussen een lokaal exemplaar van Azurite en een Azure Storage-account in de cloud.

### <a name="endpoint-and-connection-url"></a>URL voor eindpunt en verbinding

De serviceeindpunten voor Azurite verschillen van de eindpunten van een Azure Storage-account. De lokale computer doet geen domeinnaamresolutie, waarbij Azurite-eindpunten lokale adressen moeten zijn.

Wanneer u een resource in een Azure Storage-account aangeeft, maakt de accountnaam deel uit van de URI-hostnaam. De resource die wordt aangepakt, maakt deel uit van het URI-pad:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

De volgende URI is een geldig adres voor een blob in een Azure Storage-account:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Aangezien de lokale computer geen domeinnaamresolutie doet, maakt de accountnaam deel uit van het URI-pad in plaats van de hostnaam. Gebruik de volgende URI-indeling voor een resource in Azurite:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Het volgende adres kan worden gebruikt voor toegang tot een blob in Azurite:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Schalen en prestaties

Azurite is geen schaalbare opslagservice en ondersteunt geen groot aantal gelijktijdige clients. Er is geen prestatiegarantie. Azurite is bedoeld voor ontwikkelings- en testdoeleinden.

### <a name="error-handling"></a>Foutafhandeling

Azurite is afgestemd op de logica voor foutafhandeling van Azure Storage, maar er zijn verschillen. Foutberichten kunnen bijvoorbeeld verschillen, terwijl foutstatuscodes worden uitgelijnd.

### <a name="ra-grs"></a>RA-GRS

Azurite ondersteunt georedundante replicatie (read-access geo-redundante replicatie). Voor opslagbronnen u de secundaire locatie openen door **-secundair** aan de accountnaam toe te schrijven. Het volgende adres kan bijvoorbeeld worden gebruikt voor toegang tot een blob met het alleen-lezen secundaire in Azurite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite is open-source

Bijdragen en suggesties voor Azurite zijn welkom. Ga naar de Azurite [GitHub-projectpagina](https://github.com/Azure/Azurite/projects) of [GitHub-problemen](https://github.com/Azure/Azurite/issues) voor mijlpalen en werkitems die we bijhouden voor aankomende functies en bugfixes. Gedetailleerde werkitems worden ook bijgehouden in GitHub.

## <a name="next-steps"></a>Volgende stappen

* [Gebruik de Azure-opslagemulator voor het ontwikkelen en testen van](storage-use-emulator.md) documenten de verouderde Azure-opslagemulator, die wordt vervangen door Azurite.
* [Configureer Azure Storage-verbindingstekenreeksen](storage-configure-connection-string.md) hoe u een geldige Azure STorage-verbindingstekenreeks samenstellen.
