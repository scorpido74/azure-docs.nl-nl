---
title: Apps configureren in de portal
description: Meer informatie over het configureren van algemene instellingen voor een App Service-app in de Azure-portal. App-instellingen, verbindingstekenreeksen, platform, taalstack, container, enz.
keywords: azure app-service, web-app, app-instellingen, omgevingsvariabelen
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 08/13/2019
ms.custom: seodec18
ms.openlocfilehash: 18469c94b66acab27b58243e8d15eb924843319b
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811118"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Een App Service-app configureren in de Azure-portal

In dit onderwerp wordt uitgelegd hoe u algemene instellingen voor web-apps, mobiele back-end of API-app configureert met behulp van de [Azure-portal.]

## <a name="configure-app-settings"></a>App-instellingen configureren

In App Service zijn app-instellingen variabelen die worden doorgegeven als omgevingsvariabelen naar de toepassingscode. Voor Linux-apps en aangepaste containers geeft App Service `--env` app-instellingen door aan de container met behulp van de vlag om de omgevingsvariabele in de container in te stellen.

Zoek in de [Azure-portal]naar en selecteer **App Services**en selecteer vervolgens uw app. 

![Zoeken naar App-services](./media/configure-common/search-for-app-services.png)

Selecteer in het linkermenu van de app de**optie Configuratietoepassingsinstellingen** **Configuration** > .

![Toepassingsinstellingen](./media/configure-common/open-ui.png)

Voor ASP.NET en ASP.NET Core-ontwikkelaars zijn het instellen van `<appSettings>` app-instellingen in App Service als het instellen ervan in *Web.config* of *appsettings.json,* maar de waarden in App Service overschrijven de instellingen in *Web.config* of *appsettings.json*. U ontwikkelinstellingen (bijvoorbeeld lokaal MySQL-wachtwoord) in *Web.config* of *appsettings.json*behouden, maar productiegeheimen (bijvoorbeeld Azure MySQL-databasewachtwoord) veilig in App Service. Dezelfde code gebruikt uw ontwikkelingsinstellingen wanneer u lokaal debugt en gebruikt uw productiegeheimen wanneer deze worden geïmplementeerd in Azure.

Andere taalstapels krijgen ook de app-instellingen als omgevingsvariabelen bij runtime. Zie voor specifieke stappen voor taalstapel, zie:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Aangepaste containers](containers/configure-custom-container.md#configure-environment-variables)

App-instellingen worden altijd versleuteld wanneer ze worden opgeslagen (versleuteld-at-rest).

> [!NOTE]
> App-instellingen kunnen ook worden opgelost vanuit [Key Vault](/azure/key-vault/) met behulp van [Key Vault-referenties.](app-service-key-vault-references.md)

### <a name="show-hidden-values"></a>Verborgen waarden weergeven

Waarden voor app-instellingen worden standaard verborgen in de portal voor beveiliging. Als u een verborgen waarde van een app-instelling wilt zien, klikt u op het veld **Waarde** van die instelling. Als u de waarden van alle app-instellingen wilt weergeven, klikt u op de knop **Waarde weergeven.**

### <a name="add-or-edit"></a>Toevoegen of bewerken

Als u een nieuwe app-instelling wilt toevoegen, klikt u op **Nieuwe toepassingsinstelling**. In het dialoogvenster u [de instelling aan de huidige sleuf plakken.](deploy-staging-slots.md#which-settings-are-swapped)

Als u een instelling wilt bewerken, klikt u op de knop **Bewerken** aan de rechterkant.

Klik op **Bijwerken**als u klaar bent. Vergeet niet op **Opslaan** terug te klikken op de pagina **Configuratie.**

> [!NOTE]
> In een standaard Linux-container of een aangepaste Linux-container moet elke `ApplicationInsights:InstrumentationKey` geneste JSON-sleutelstructuur `ApplicationInsights__InstrumentationKey` in de naam van de app-instelling worden geconfigureerd in App Service als voor de sleutelnaam. Met andere woorden, elke `:` `__` moet worden vervangen door (dubbele underscore).
>

### <a name="edit-in-bulk"></a>In bulk bewerken

Als u app-instellingen in bulk wilt toevoegen of bewerken, klikt u op de knop **Geavanceerd bewerken.** Klik op **Bijwerken**als u klaar bent. Vergeet niet op **Opslaan** terug te klikken op de pagina **Configuratie.**

App-instellingen hebben de volgende JSON-opmaak:

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

## <a name="configure-connection-strings"></a>Verbindingsreeksen configureren

Zoek in de [Azure-portal]naar en selecteer **App Services**en selecteer vervolgens uw app. Selecteer in het linkermenu van de app de**optie Configuratietoepassingsinstellingen** **Configuration** > .

![Toepassingsinstellingen](./media/configure-common/open-ui.png)

Voor ASP.NET- en ASP.NET Core-ontwikkelaars zijn het instellen `<connectionStrings>` van verbindingstekenreeksen in App Service als het instellen ervan in *Web.config,* maar de waarden die u in App Service instelt, overschrijven de tekenreeksen in *Web.config.* U ontwikkelinstellingen (bijvoorbeeld een databasebestand) in *Web.config* en productiegeheimen (bijvoorbeeld SQL Database-referenties) veilig bewaren in App Service. Dezelfde code gebruikt uw ontwikkelingsinstellingen wanneer u lokaal debugt en gebruikt uw productiegeheimen wanneer deze worden geïmplementeerd in Azure.

Voor andere taalstapels is het beter om in plaats daarvan [app-instellingen](#configure-app-settings) te gebruiken, omdat verbindingstekenreeksen speciale opmaak in de variabele toetsen vereisen om toegang te krijgen tot de waarden. Hier is echter één uitzondering: er wordt een back-up gemaakt van bepaalde Azure-databasetypen samen met de app als u de verbindingstekenreeksen in uw app configureert. Zie [Wat er een back-up wordt van wat er wordt opgenomen](manage-backup.md#what-gets-backed-up)voor meer informatie. Als u deze geautomatiseerde back-up niet nodig hebt, gebruikt u app-instellingen.

Bij runtime zijn verbindingstekenreeksen beschikbaar als omgevingsvariabelen, vooraf gekoppeld aan de volgende verbindingstypen:

* Sqlserver:`SQLCONNSTR_`  
* Mysql:`MYSQLCONNSTR_` 
* SQLAzure:`SQLAZURECONNSTR_` 
* Aangepaste:`CUSTOMCONNSTR_`
* Postgresql:`POSTGRESQLCONNSTR_`  

Een MySql-verbindingstekenreeks met de naam *connectionstring1* is `MYSQLCONNSTR_connectionString1`bijvoorbeeld toegankelijk als omgevingsvariabele . Zie voor specifieke stappen voor taalstapel, zie:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Aangepaste containers](containers/configure-custom-container.md#configure-environment-variables)

Verbindingstekenreeksen worden altijd versleuteld wanneer ze worden opgeslagen (versleuteld-at-rest).

> [!NOTE]
> Verbindingstekenreeksen kunnen ook worden opgelost vanuit [Key Vault](/azure/key-vault/) met behulp van [Key Vault-referenties.](app-service-key-vault-references.md)

### <a name="show-hidden-values"></a>Verborgen waarden weergeven

Waarden voor verbindingstekenreeksen worden standaard verborgen in de portal voor beveiliging. Als u een verborgen waarde van een verbindingstekenreeks wilt zien, klikt u op het veld **Waarde** van die tekenreeks. Als u de waarden van alle verbindingstekenreeksen wilt weergeven, klikt u op de knop **Waarde weergeven.**

### <a name="add-or-edit"></a>Toevoegen of bewerken

Als u een nieuwe verbindingstekenreeks wilt toevoegen, klikt u op **Nieuwe verbindingstekenreeks**. In het dialoogvenster u [de verbindingstekenreeks aan de huidige sleuf plakken.](deploy-staging-slots.md#which-settings-are-swapped)

Als u een instelling wilt bewerken, klikt u op de knop **Bewerken** aan de rechterkant.

Klik op **Bijwerken**als u klaar bent. Vergeet niet op **Opslaan** terug te klikken op de pagina **Configuratie.**

### <a name="edit-in-bulk"></a>In bulk bewerken

Als u verbindingstekenreeksen in bulk wilt toevoegen of bewerken, klikt u op de knop **Geavanceerd bewerken.** Klik op **Bijwerken**als u klaar bent. Vergeet niet op **Opslaan** terug te klikken op de pagina **Configuratie.**

Verbindingstekenreeksen hebben de volgende JSON-opmaak:

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>Algemene instellingen configureren

Zoek in de [Azure-portal]naar en selecteer **App Services**en selecteer vervolgens uw app. Selecteer in het linkermenu van de app de optie**Algemene configuratie-instellingen** **Configuration** > .

![Algemene instellingen](./media/configure-common/open-general.png)

Hier u enkele algemene instellingen voor de app configureren. Voor sommige instellingen moet u [opschalen naar hogere prijsniveaus.](manage-scale-up.md)

- **Stackinstellingen**: de softwarestack om de app uit te voeren, inclusief de taal- en SDK-versies. Voor Linux-apps en aangepaste container-apps u ook een optionele opstartopdracht of -bestand instellen.
- **Platforminstellingen:** hiermee u instellingen configureren voor het hostingplatform, waaronder:
    - **Bitness**: 32-bits of 64-bits.
    - **WebSocket protocol**: Voor [ASP.NET SignalR] of [socket.io,](https://socket.io/)bijvoorbeeld.
    - **Altijd aan:** Houd de app geladen, zelfs als er geen verkeer is. Het is vereist voor continue WebJobs of voor WebJobs die worden geactiveerd met behulp van een CRON-expressie.
      > [!NOTE]
      > Met de functie Altijd aan hebt u geen controle over het eindpunt. Het stuurt altijd een verzoek naar de toepassingswortel.
    - **Beheerde pijplijnversie**: de IIS-pijplijnmodus . [pipeline mode] Stel deze in op **Klassiek** als u een verouderde app hebt waarvoor een oudere versie van IIS vereist is.
    - **HTTP-versie**: Ingesteld op **2.0** om ondersteuning voor [HTTPS/2-protocol](https://wikipedia.org/wiki/HTTP/2) in te schakelen.
    > [!NOTE]
    > De meeste moderne browsers ondersteunen alleen http/2-protocol via TLS, terwijl niet-versleuteld verkeer HTTP/1.1 blijft gebruiken. Als u ervoor wilt zorgen dat clientbrowsers verbinding maken met uw app met HTTP/2, beveiligt u uw aangepaste DNS-naam. Zie [Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service](configure-ssl-bindings.md)voor meer informatie.
    - **ARR-affiniteit**: Zorg er in een implementatie met meerdere instanties voor dat de client wordt doorgestuurd naar hetzelfde exemplaar voor de levensduur van de sessie. U deze optie instellen op **Uit** voor stateloze toepassingen.
- **Foutopsporing:** Externe foutopsporing inschakelen voor [ASP.NET,](troubleshoot-dotnet-visual-studio.md#remotedebug) [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)of [Node.js-apps.](containers/configure-language-nodejs.md#debug-remotely) Deze optie wordt na 48 uur automatisch uitgeschakeld.
- **Binnenkomende clientcertificaten:** vereisen clientcertificaten in [wederzijdse verificatie](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Standaarddocumenten configureren

Deze instelling is alleen voor Windows-apps.

Zoek in de [Azure-portal]naar en selecteer **App Services**en selecteer vervolgens uw app. Selecteer in het linkermenu van de app de optie **Standaarddocumenten** > **configureren**.

![Standaarddocumenten](./media/configure-common/open-documents.png)

Het standaarddocument is de webpagina die wordt weergegeven op de hoofd-URL voor een website. Het eerste overeenkomende bestand in de lijst wordt gebruikt. Als u een nieuw standaarddocument wilt toevoegen, klikt u op **Nieuw document**. Vergeet niet op **Opslaan**te klikken.

Als de app modules gebruikt die route op basis van URL in plaats van het weergeven van statische inhoud, is er geen noodzaak voor standaarddocumenten.

## <a name="configure-path-mappings"></a>Padtoewijzingen configureren

Zoek in de [Azure-portal]naar en selecteer **App Services**en selecteer vervolgens uw app. **Selecteer** > **configuratiepadtoewijzingen**in het linkermenu van de app.

![Padtoewijzingen](./media/configure-common/open-path.png)

Op de pagina **Padtoewijzingen** ziet u verschillende dingen op basis van het type BE.

### <a name="windows-apps-uncontainerized"></a>Windows-apps (niet gecontaineriseerd)

Voor Windows-apps u de IIS-handlertoewijzingen en virtuele toepassingen en mappen aanpassen.

Met handlertoewijzingen u aangepaste scriptprocessors toevoegen om aanvragen voor specifieke bestandsextensies af te handelen. Als u een aangepaste handler wilt toevoegen, klikt u op **Nieuwe handler**. Configureer de handler als volgt:

- **Uitbreiding**. De bestandsextensie die u wilt verwerken, zoals * \*.php* of *handler.fcgi*.
- **Scriptprocessor**. Het absolute pad van de scriptprocessor voor u. Aanvragen voor bestanden die overeenkomen met de bestandsextensie worden verwerkt door de scriptprocessor. Gebruik het `D:\home\site\wwwroot` pad om naar de hoofdmap van uw app te verwijzen.
- **Argumenten**. Optionele opdrachtregelargumenten voor de scriptprocessor.

Elke app heeft het`/`standaard hoofdpad `D:\home\site\wwwroot`( ) toegewezen aan , waar uw code standaard wordt geïmplementeerd. Als uw app-root zich in een andere map bevindt of als uw repository meer dan één toepassing heeft, u hier virtuele toepassingen en mappen bewerken of toevoegen. Klik **op Nieuwe virtuele toepassing of map**.

Als u virtuele toepassingen en mappen wilt configureren, geeft u elke`D:\home`virtuele map en het bijbehorende fysieke pad op ten opzichte van de hoofdmap van de website ( ). Optioneel u het selectievakje **Toepassing** selecteren om een virtuele map als toepassing te markeren.

### <a name="containerized-apps"></a>Gecontaineriseerde apps

U [aangepaste opslag toevoegen voor uw containerte app.](containers/how-to-serve-content-from-azure-storage.md) Gecontaineriseerde apps bevatten alle Linux-apps en ook de aangepaste Windows- en Linux-containers die op App Service draaien. Klik **op Nieuwe Azure Storage Mount** en configureer de aangepaste opslag als volgt:

- **Naam:** De weergavenaam.
- **Configuratieopties**: **Basic** of **Advanced**.
- **Opslagaccounts:** het opslagaccount bij de container die u wilt.
- **Opslagtype:** **Azure Blobs** of **Azure Files**.
  > [!NOTE]
  > Windows-containerapps ondersteunen alleen Azure Files.
- **Opslagcontainer:** voor de basisconfiguratie, de gewenste container.
- **Naam delen:** Voor geavanceerde configuratie wordt de naam van het bestandsaandeel gedeeld.
- **Toegangssleutel:** voor geavanceerde configuratie wordt de toegangssleutel geopend.
- **Mount pad:** Het absolute pad in uw container om de aangepaste opslag te monteren.

Zie [Inhoud uit Azure Storage leveren in App Service voor Linux](containers/how-to-serve-content-from-azure-storage.md) voor meer informatie.

## <a name="configure-language-stack-settings"></a>Instellingen voor taalstapel configureren

Zie voor Linux-apps:

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Aangepaste containers configureren

Zie [Een aangepaste Linux-container configureren voor Azure App Service](containers/configure-custom-container.md)

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste domeinnaam configureren in Azure App Service]
- [Faseringsomgevingen in Azure App Service instellen]
- [Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service](configure-ssl-bindings.md)
- [Diagnostische logboeken inschakelen](troubleshoot-diagnostic-logs.md)
- [Een app schalen in Azure App Service]
- [Basisbeginselen in Azure App-service bewaken]
- [ApplicationHost.config-instellingen wijzigen met applicationHost.xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure-portal]: https://portal.azure.com/
[Een aangepaste domeinnaam configureren in Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Faseringsomgevingen in Azure App Service instellen]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Basisbeginselen in Azure App-service bewaken]: ./web-sites-monitor.md
[pijplijnmodus]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Een app schalen in Azure App Service]: ./manage-scale-up.md
