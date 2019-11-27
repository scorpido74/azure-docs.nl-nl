---
title: Azurite-emulator gebruiken voor het ontwikkelen van lokale Azure Storage
description: De Azurite open-source emulator (preview) biedt een gratis lokale omgeving voor het testen van uw Azure Storage-toepassingen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 0421f49b31eba688542adc0a5b62e1cf75028836
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74269459"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development-and-testing-preview"></a>De Azurite-emulator gebruiken voor het ontwikkelen en testen van lokale Azure Storage (preview-versie)

De Azurite-versie 3,2 open-source emulator (preview) biedt een gratis lokale omgeving voor het testen van uw Azure Blob-en Queue Storage-toepassingen. Wanneer u tevreden bent over de manier waarop uw toepassing lokaal werkt, schakelt u over naar het gebruik van een Azure Storage-account in de Cloud. De emulator biedt ondersteuning voor meerdere platforms voor Windows, Linux en MacOS. Azurite v3 ondersteunt Api's die door Azure Blob service worden geïmplementeerd.

Azurite is het toekomstige opslag emulator-platform. Azurite vervangt de [Azure Storage-emulator](storage-use-emulator.md). Azurite zal nog steeds worden bijgewerkt ter ondersteuning van de nieuwste versies van Azure Storage-Api's.

Er zijn verschillende manieren om Azurite te installeren en uit te voeren op uw lokale systeem:

  1. [De Azurite Visual Studio code-extensie installeren en uitvoeren](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Azurite installeren en uitvoeren met behulp van NPM](#install-and-run-azurite-by-using-npm)
  1. [De Azurite docker-installatie kopie installeren en uitvoeren](#install-and-run-the-azurite-docker-image)
  1. [Azurite klonen, bouwen en uitvoeren vanuit de GitHub-opslag plaats](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>De Azurite Visual Studio code-extensie installeren en uitvoeren

Selecteer in Visual Studio code het deel venster **uitbrei dingen** en zoek naar *Azurite* in de **modules extensies: marketplace**.

![Marketplace voor Visual Studio-code uitbreidingen](media/storage-use-azurite/azurite-vs-code-extension.png)

U kunt ook naar de [VS code-uitbreidings markt](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) in uw browser navigeren. Selecteer de knop **installeren** om Visual Studio code te openen en direct naar de uitbrei ding pagina Azurite te gaan.

U kunt Azurite snel starten of sluiten door te klikken op **[Azurite BLOB Service]** of **[Azurite Queue Service]** in de status balk VS-code of door de volgende opdrachten uit te geven in het palet met de VS code-opdracht. Druk op **F1** in VS code om het opdracht palet te openen.

De extensie ondersteunt de volgende Visual Studio-code opdrachten:

   * **Azurite: start** -alle Azurite-Services starten
   * **Azurite: sluiten** -alle Azurite-Services sluiten
   * **Azurite:** alle Azurite Services opereren-gegevens opnieuw instellen
   * **Azurite: de BLOB-service starten** -BLOB-service starten
   * **Azurite: de BLOB-service sluiten** -BLOB-service sluiten
   * **Azurite: de BLOB-service opschonen** -BLOB-service opschonen
   * **Azurite: wachtrij service starten** -wachtrij service starten
   * **Azurite: de wachtrij service sluiten** -wachtrij service sluiten
   * **Azurite: wachtrij service opschonen** -wachtrij service wissen

Als u Azurite in Visual Studio code wilt configureren, selecteert u het deel venster uitbrei dingen. Selecteer het pictogram **beheren** (vistuig) voor **Azurite**. Selecteer **extensie-instellingen configureren**.

![Azurite configureren](media/storage-use-azurite/azurite-configure-extension-settings.png)

De volgende instellingen worden ondersteund:

   * **Azurite: BLOB host** : het BLOB service luisterende eind punt. De standaard instelling is 127.0.0.1.
   * **Azurite: BLOB-poort** -de BLOB service Luister poort. De standaard poort is 10000.
   * **Azurite: debug** -het logboek voor fout opsporing uitvoeren op het Azurite-kanaal. De standaard waarde is **False**.
   * **Azurite: locatie** -het pad naar de locatie van de werk ruimte. De standaard waarde is de werkmap Visual Studio-code.
   * **Azurite: wachtrij-host** -het Queue-service luisterende eind punt. De standaard instelling is 127.0.0.1.
   * **Azurite: wachtrij poort** : de Queue-service Luister poort. De standaard poort is 10001.
   * **Azurite:** in de Stille modus wordt het toegangs logboek uitgeschakeld. De standaard waarde is **False**.

## <a name="install-and-run-azurite-by-using-npm"></a>Azurite installeren en uitvoeren met behulp van NPM

Deze installatie methode vereist dat [node. js versie 8,0 of hoger](https://nodejs.org) is geïnstalleerd. **NPM** is het hulp programma voor pakket beheer dat bij elke node. js-installatie is opgenomen. Na de installatie van node. js voert u de volgende **NPM** -opdracht uit om Azurite te installeren.

```console
npm install -g azurite
```

Nadat u Azurite hebt geïnstalleerd, raadpleegt u [Azurite uitvoeren vanaf een opdracht regel](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>De Azurite docker-installatie kopie installeren en uitvoeren

Gebruik [DockerHub](https://hub.docker.com/) voor het ophalen van de [meest recente Azurite-installatie kopie](https://hub.docker.com/_/microsoft-azure-storage-azurite) met behulp van de volgende opdracht:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Voer de Azurite docker-installatie kopie uit**:

Met de volgende opdracht wordt de Azurite docker-installatie kopie uitgevoerd. Met de para meter `-p 10000:10000` worden aanvragen van de poort 10000 van de hostcomputer omgeleid naar het docker-exemplaar.

```console
docker run -p 10000:10000 -p 10001:10001 mcr.microsoft.com/azure-storage/azurite
```

**De locatie van de werk ruimte opgeven**:

In het volgende voor beeld specificeert de para meter `-v c:/azurite:/data` *c:/Azurite* als de Azurite persistente gegevens locatie. De map, *c:/Azurite*, moet worden gemaakt voordat u de opdracht docker uitvoert.

```console
docker run -p 10000:10000 -p 10001:10001 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Alleen de BLOB-service uitvoeren**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

**Alle Azurite-para meters instellen**:

In dit voor beeld ziet u hoe u alle opdracht regel parameters kunt instellen. Alle onderstaande para meters moeten op één opdracht regel worden geplaatst.

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

Zie [opdracht regel opties](#command-line-options) voor meer informatie over het configureren van Azurite bij het opstarten.

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Azurite klonen, bouwen en uitvoeren vanuit de GitHub-opslag plaats

Voor deze installatie methode moet [Git](https://git-scm.com/) zijn geïnstalleerd. Kloon de [github-opslag plaats](https://github.com/azure/azurite) voor het Azurite-project met behulp van de volgende console opdracht.

```console
git clone https://github.com/Azure/Azurite.git
```

Nadat u de bron code hebt gekloond, voert u de volgende opdrachten uit vanuit de hoofdmap van de gekloonde opslag plaats om Azurite te bouwen en te installeren.

```console
npm install
npm run build
npm install -g
```

Ga na het installeren en maken van Azurite naar [Azurite uitvoeren vanaf een opdracht regel](#run-azurite-from-a-command-line).

## <a name="run-azurite-from-a-command-line"></a>Azurite uitvoeren vanaf een opdracht regel

> [!NOTE]
> Azurite kan niet worden uitgevoerd vanaf de opdracht regel als u de Visual Studio code extension alleen hebt geïnstalleerd. Gebruik in plaats daarvan het opdracht palet VS code. Zie [Install and run the Azurite Visual Studio code extension](#install-and-run-the-azurite-visual-studio-code-extension)(Engelstalig) voor meer informatie.

Als u direct aan de slag wilt gaan met de opdracht regel, maakt u een map met de naam **c:\azurite**en start u Azurite door de volgende opdracht te geven:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Met deze opdracht wordt Azurite om alle gegevens op te slaan in een bepaalde map, **c:\azurite**. Als de optie **--locatie** wordt wegge laten, wordt de huidige werkmap gebruikt.

## <a name="command-line-options"></a>Opdracht regel opties

In deze sectie vindt u meer informatie over de opdracht regel opties die beschikbaar zijn bij het starten van Azurite. Alle opdracht regel opties zijn optioneel.

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>] 
    [-d | --debug <log file path>] [-l | --location <workspace path>]
    [--queueHost <IP address>] [--queuePort <port address>]
    [-s | --silent] [-h | --help]
```

De **-d** is een snelkoppeling voor **--** de schakel optie- **l** is een snelkoppeling voor **--locatie**, **-s** is een snelkoppeling voor **--Silent**en **-h** is een snelkoppeling voor **--Help**.

### <a name="blob-listening-host"></a>BLOB luistert host

**Optioneel** Azurite wordt standaard naar 127.0.0.1 geluisterd als de lokale server. Gebruik de schakel optie **--blobHost** om het adres in te stellen op uw vereisten.

Alleen aanvragen op de lokale computer accepteren:

```console
azurite --blobHost 127.0.0.1
```

Externe aanvragen toestaan:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> Het toestaan van externe aanvragen kan uw systeem kwetsbaar maken voor externe aanvallen.

### <a name="blob-listening-port-configuration"></a>Poort configuratie voor BLOB-Luister

**Optioneel** Azurite luistert standaard naar de Blob service op poort 10000. Gebruik de schakel optie **--blobPort** om de luisterende poort op te geven die u nodig hebt.

> [!NOTE]
> Nadat u een aangepaste poort hebt gebruikt, moet u de connection string of de bijbehorende configuratie bijwerken in uw Azure Storage-hulpprogram ma's of Sdk's.

De Blob service-Luister poort aanpassen:

```console
azurite --blobPort 8888
```

Het systeem automatisch een beschik bare poort laten selecteren:

```console
azurite --blobPort 0
```

De poort die in gebruik is, wordt weer gegeven tijdens het opstarten van Azurite.

### <a name="queue-listening-host"></a>Luisterende host in wachtrij plaatsen

**Optioneel** Azurite wordt standaard naar 127.0.0.1 geluisterd als de lokale server. Gebruik de schakel optie **--queueHost** om het adres in te stellen op uw vereisten.

Alleen aanvragen op de lokale computer accepteren:

```console
azurite --queueHost 127.0.0.1
```

Externe aanvragen toestaan:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> Het toestaan van externe aanvragen kan uw systeem kwetsbaar maken voor externe aanvallen.

### <a name="queue-listening-port-configuration"></a>Poort configuratie voor Luis teren in wachtrij

**Optioneel** Azurite luistert standaard naar de Queue-service op poort 10001. Gebruik de schakel optie **--queuePort** om de luisterende poort op te geven die u nodig hebt.

> [!NOTE]
> Nadat u een aangepaste poort hebt gebruikt, moet u de connection string of de bijbehorende configuratie bijwerken in uw Azure Storage-hulpprogram ma's of Sdk's.

De Queue-service-Luister poort aanpassen:

```console
azurite --queuePort 8888
```

Het systeem automatisch een beschik bare poort laten selecteren:

```console
azurite --queuePort 0
```

De poort die in gebruik is, wordt weer gegeven tijdens het opstarten van Azurite.

### <a name="workspace-path"></a>Pad naar werk ruimte

**Optioneel** Azurite slaat gegevens op de lokale schijf op tijdens de uitvoering. Gebruik de schakel optie **--locatie** om een pad op te geven als locatie van de werk ruimte. Standaard wordt de huidige werk directory van het proces gebruikt.

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>Access-logboek

**Optioneel** Het toegangs logboek wordt standaard weer gegeven in het console venster. Schakel de weer gave van het toegangs logboek uit met behulp van de **--Silent-** switch.

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>Logboek voor fout opsporing

**Optioneel** Het logboek voor fout opsporing bevat gedetailleerde informatie over elke aanvraag en uitzonderings Stack tracering. Schakel het logboek voor fout opsporing in door een geldig lokaal bestandspad op te geven bij de schakel optie voor het **opsporen van fouten** .

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

## <a name="authorization-for-tools-and-sdks"></a>Autorisatie voor hulpprogram ma's en Sdk's

Maak verbinding met Azurite van Azure Storage Sdk's of hulpprogram ma's, zoals [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), met behulp van een verificatie strategie. Verificatie is vereist. Azurite ondersteunt verificatie met gedeelde sleutel en Shared Access signatures (SAS). Azurite biedt ook ondersteuning voor anonieme toegang tot open bare containers.

### <a name="well-known-storage-account-and-key"></a>Bekende opslag account en-sleutel

U kunt de volgende account naam en-sleutel gebruiken met Azurite. Dit is dezelfde bekende account en sleutel die wordt gebruikt door de verouderde Azure-opslag emulator.

* Account naam: `devstoreaccount1`
* Account sleutel: `Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> Naast SharedKey-verificatie ondersteunt Azurite ondersteuning voor account-en service-SAS-verificatie. Anonieme toegang is ook beschikbaar wanneer een container is ingesteld om open bare toegang toe te staan.

### <a name="connection-string"></a>Verbindingsreeks

De eenvoudigste manier om verbinding te maken met Azurite vanuit uw toepassing is het configureren van een connection string in het configuratie bestand van uw toepassing dat verwijst naar de snelkoppeling *UseDevelopmentStorage = True*. Hier volgt een voor beeld van een connection string in een *app. config* -bestand:

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Zie [Azure Storage-verbindings reeksen configureren](storage-configure-connection-string.md)voor meer informatie.

### <a name="storage-explorer"></a>Opslagverkenner

In Azure Storage Explorer maakt u verbinding met Azurite door op het pictogram **account toevoegen** te klikken en vervolgens **koppelen aan een lokale emulator te** selecteren. Klik vervolgens op **verbinding maken**.

## <a name="differences-between-azurite-and-azure-storage"></a>Verschillen tussen Azurite en Azure Storage

Er zijn functionele verschillen tussen een lokaal exemplaar van Azurite en een Azure Storage-account in de Cloud.

### <a name="endpoint-and-connection-url"></a>Eind punt en verbindings-URL

De service-eind punten voor Azurite verschillen van de eind punten van een Azure Storage-account. De lokale computer voert geen domeinnaam omzetting uit en vereist dat Azurite-eind punten lokale adressen hebben.

Wanneer u een resource in een Azure Storage-account adresseert, is de account naam onderdeel van de URI-hostnaam. De bron die wordt geadresseerd, maakt deel uit van het URI-pad:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

De volgende URI is een geldig adres voor een BLOB in een Azure Storage-account:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Omdat de lokale computer geen domein naam omzetting heeft, maakt de account naam deel uit van het URI-pad in plaats van de hostnaam. Gebruik de volgende URI-indeling voor een resource in Azurite:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Het volgende adres kan worden gebruikt voor toegang tot een BLOB in Azurite:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Schalen en prestaties

Azurite is geen schaal bare opslag service en biedt geen ondersteuning voor een groot aantal gelijktijdige clients. Er is geen prestatie garantie. Azurite is bedoeld voor ontwikkelings-en test doeleinden.

### <a name="error-handling"></a>Foutafhandeling

Azurite is uitgelijnd met Azure Storage fout bij het verwerken van fouten, maar er zijn verschillen. Fout berichten kunnen bijvoorbeeld verschillen, terwijl fout status codes worden uitgelijnd.

### <a name="ra-grs"></a>RA-GRS

Azurite ondersteunt geo-redundante replicatie met lees toegang (RA-GRS). Voor opslag bronnen opent u de secundaire locatie door deze toe te voegen **-secundair** aan de account naam. Het volgende adres kan bijvoorbeeld worden gebruikt voor toegang tot een blob met behulp van de alleen-lezen secundair in Azurite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite is open source

Bijdragen en suggesties voor Azurite zijn welkom. Ga naar de Azurite [github-project](https://github.com/Azure/Azurite/projects) pagina of [github problemen](https://github.com/Azure/Azurite/issues) voor mijl palen en werk items die we volgen op aanstaande functies en oplossingen voor problemen. Gedetailleerde werk items worden ook bijgehouden in GitHub.

## <a name="next-steps"></a>Volgende stappen

* [Gebruik de Azure-opslag emulator voor het ontwikkelen en testen van](storage-use-emulator.md) documenten de verouderde Azure-opslag emulator, die wordt vervangen door Azurite.
* [Azure Storage verbindings reeksen configureren](storage-configure-connection-string.md) wordt uitgelegd hoe u een geldige Azure Storage-Connection String kunt samen stellen.
