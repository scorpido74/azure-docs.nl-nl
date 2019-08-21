---
title: Apps in de portal configureren-Azure App Service
description: Meer informatie over het configureren van algemene instellingen voor een App Service-app in de Azure Portal.
keywords: Azure app service, Web-app, app-instellingen, omgevings variabelen
services: app-service\web
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 9609a886bdcbf093cffaf888f9aeaff0d61acec1
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636656"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Een App Service-app configureren in het Azure Portal

In dit onderwerp wordt uitgelegd hoe u algemene instellingen configureert voor web-apps, mobiele back-end of API-apps met behulp van de [Azure Portal].

## <a name="configure-app-settings"></a>App-instellingen configureren

In App Service zijn app-instellingen variabelen die als omgevings variabelen worden door gegeven aan de toepassings code. Voor Linux-apps en aangepaste containers app service de app-instellingen door gegeven aan de `--env` container met behulp van de vlag voor het instellen van de omgevings variabele in de container.

Ga in het [Azure Portal]naar de beheer pagina van uw app. Klik in het menu links van de app op **configuratie** > **Toepassings instellingen**.

![Toepassingsinstellingen](./media/configure-common/open-ui.png)

Voor ASP.net-en ASP.net core-ontwikkel aars is het instellen van de app-instellingen `<appSettings>` in app service vergelijkbaar met het instellen hiervan in *Web. config* of *appSettings. json*, maar de waarden in app service overschrijven deze in *Web. config* of  *appSettings. json*. U kunt de ontwikkelings instellingen (bijvoorbeeld lokaal MySQL-wacht woord) in *Web. config* of *appSettings. json*blijven gebruiken, maar productie geheimen (bijvoorbeeld Azure MySQL-database wachtwoord) veilig in app service. Dezelfde code maakt gebruik van uw ontwikkelings instellingen wanneer u lokaal fouten opspoort en uw productie geheimen gebruikt wanneer deze worden geïmplementeerd in Azure.

U kunt ook de app-instellingen ophalen als omgevings variabelen tijdens runtime. Zie voor taalspecifieke stappen voor de taal stack:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Aangepaste containers](containers/configure-custom-container.md#configure-environment-variables)

App-instellingen worden altijd versleuteld wanneer ze worden opgeslagen (versleuteld-at-rest).

> [!NOTE]
> App-instellingen kunnen ook worden omgezet van [Key Vault](/azure/key-vault/) met behulp van [Key Vault-verwijzingen](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Verborgen waarden weer geven

Standaard worden waarden voor app-instellingen in de portal verborgen voor beveiliging. Als u een verborgen waarde van een app-instelling wilt weer geven, klikt u op het veld **waarde** van die instelling. Als u de waarden van alle app-instellingen wilt zien, klikt u op de knop **waarde weer geven** .

### <a name="add-or-edit"></a>Toevoegen of bewerken

Klik op **nieuwe toepassings instelling**om een nieuwe app-instelling toe te voegen. In het dialoog venster kunt u [de instelling op de huidige sleuf plakken](deploy-staging-slots.md#which-settings-are-swapped).

Als u een instelling wilt bewerken, klikt u op de knop **bewerken** aan de rechter kant.

Wanneer u klaar bent, klikt u op **bijwerken**. Vergeet niet om terug te klikken op **Opslaan** op de pagina **configuratie** .

> [!NOTE]
> In een standaard-Linux-container of een aangepaste Linux-container `ApplicationInsights:InstrumentationKey` moet elke geneste JSON-sleutel structuur in de naam van de app-instelling, zoals `ApplicationInsights__InstrumentationKey` voor de sleutel naam, worden geconfigureerd in app service. Met andere woorden, `:` die moeten worden vervangen door `__` (dubbele onderstrepings tekens).
>

### <a name="edit-in-bulk"></a>Bulksgewijs bewerken

Klik op de knop **Geavanceerd bewerken** om de app-instellingen in bulk toe te voegen of te bewerken. Wanneer u klaar bent, klikt u op **bijwerken**. Vergeet niet om terug te klikken op **Opslaan** op de pagina **configuratie** .

App-instellingen hebben de volgende JSON-indeling:

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

Ga in het [Azure Portal]naar de beheer pagina van de app. Klik in het menu links van de app op **configuratie** > **Toepassings instellingen**.

![Toepassingsinstellingen](./media/configure-common/open-ui.png)

Voor ASP.net-en ASP.net core-ontwikkel aars is het instellen van verbindings reeksen in app service `<connectionStrings>` vergelijkbaar met de instelling in in *Web. config*, maar de waarden die u in app service hebt ingesteld, overschrijven deze in *Web. config*. U kunt de ontwikkelings instellingen (bijvoorbeeld een database bestand) in *Web. config* en productie geheimen (bijvoorbeeld SQL database referenties) veilig in app service blijven. Dezelfde code maakt gebruik van uw ontwikkelings instellingen wanneer u lokaal fouten opspoort en uw productie geheimen gebruikt wanneer deze worden geïmplementeerd in Azure.

Voor andere taal stacks is het beter om de [app-instellingen](#configure-app-settings) te gebruiken, omdat verbindings reeksen speciale opmaak in de variabele sleutels nodig hebben om toegang te krijgen tot de waarden. Hier volgt één uitzonde ring: voor bepaalde typen van Azure-data bases wordt samen met de app een back-up gemaakt als u de verbindings reeksen in uw app configureert. Zie [waarvan een back-up wordt gemaakt](manage-backup.md#what-gets-backed-up)voor meer informatie. Als u deze geautomatiseerde back-up niet nodig hebt, gebruikt u de app-instellingen.

In runtime zijn verbindings reeksen beschikbaar als omgevings variabelen, met als voor voegsel de volgende verbindings typen:

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* SQL Database:`SQLAZURECONNSTR_`
* Instel`CUSTOMCONNSTR_`

Bijvoorbeeld, een MySql-connection string met de naam *connectionstring1* kan worden gebruikt als de `MYSQLCONNSTR_connectionString1`omgevings variabele. Zie voor taalspecifieke stappen voor de taal stack:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Aangepaste containers](containers/configure-custom-container.md#configure-environment-variables)

Verbindings reeksen worden altijd versleuteld wanneer ze worden opgeslagen (versleuteld-at-rest).

> [!NOTE]
> Verbindings reeksen kunnen ook worden omgezet van [Key Vault](/azure/key-vault/) met behulp van [Key Vault-verwijzingen](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Verborgen waarden weer geven

Standaard worden waarden voor verbindingsteken reeksen in de portal verborgen voor beveiliging. Als u een verborgen waarde van een connection string wilt weer geven, klikt u op het veld **waarde** van die teken reeks. Als u de waarden van alle verbindings reeksen wilt zien, klikt u op de knop **waarde weer geven** .

### <a name="add-or-edit"></a>Toevoegen of bewerken

Als u een nieuwe connection string wilt toevoegen, klikt u op **nieuw Connection String**. In het dialoog venster kunt u [de Connection String aan de huidige sleuf aansteken](deploy-staging-slots.md#which-settings-are-swapped).

Als u een instelling wilt bewerken, klikt u op de knop **bewerken** aan de rechter kant.

Wanneer u klaar bent, klikt u op **bijwerken**. Vergeet niet om terug te klikken op **Opslaan** op de pagina **configuratie** .

### <a name="edit-in-bulk"></a>Bulksgewijs bewerken

Klik op de knop **Geavanceerd bewerken** om verbindings reeksen in bulk toe te voegen of te bewerken. Wanneer u klaar bent, klikt u op **bijwerken**. Vergeet niet om terug te klikken op **Opslaan** op de pagina **configuratie** .

Verbindings reeksen hebben de volgende JSON-indeling:

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

Ga in het [Azure Portal]naar de beheer pagina van de app. Klik in het menu links van de app op **configuratie** > **Toepassings instellingen**.

![Algemene instellingen](./media/configure-common/open-general.png)

Hier kunt u enkele algemene instellingen voor de app configureren. Voor sommige instellingen moet u [Omhoog schalen naar een hogere prijs categorie](manage-scale-up.md).

- **Stack-instellingen**: De software stack voor het uitvoeren van de app, met inbegrip van de taal-en SDK-versies. Voor Linux-apps en aangepaste container-apps kunt u ook een optionele opstart opdracht of-bestand instellen.
- **Platform instellingen**: Hiermee kunt u instellingen configureren voor het hosting platform, waaronder:
    - **Bitness**: 32-bits of 64-bits.
    - **WebSocket-protocol**: Voor [ASP.net-Signa lering] of [socket.io](https://socket.io/), bijvoorbeeld.
    - **Altijd aan**: Zorg ervoor dat de app wordt geladen, zelfs wanneer er geen verkeer is. Het is vereist voor doorlopende webjobs of voor webjobs die worden geactiveerd met behulp van een CRON-expressie.
    - **Beheerde pijplijn versie**: De IIS-pipeline- [modus]. Stel deze in op **klassiek** als u een verouderde app hebt waarvoor een oudere versie van IIS vereist is.
    - **Http-versie**: Stel deze waarde in op **2,0** om ondersteuning voor [https/2-](https://wikipedia.org/wiki/HTTP/2) protocol in te scha kelen.
    > [!NOTE]
    > De meeste moderne browsers bieden alleen ondersteuning voor HTTP/2-protocollen via TLS, terwijl niet-versleuteld verkeer HTTP/1.1 blijft gebruiken. Als u er zeker van wilt zijn dat client browsers verbinding maken met uw app met HTTP/2, [koopt u een app service Certificate](web-sites-purchase-ssl-web-site.md) voor het aangepaste domein van de app of bindt u [een certificaat van een derde partij](app-service-web-tutorial-custom-ssl.md).
    - **ARR-affiniteit**: Zorg er in een implementatie met meerdere exemplaren voor dat de client voor de levens duur van de sessie wordt doorgestuurd naar hetzelfde exemplaar. U kunt deze optie instellen op **uitschakelen** voor stateless toepassingen.
- **Fout opsporing**: Schakel externe fout opsporing in voor [ASP.net](troubleshoot-dotnet-visual-studio.md#remotedebug)-, [ASP.net core](/visualstudio/debugger/remote-debugging-azure)-of [node. js](containers/configure-language-nodejs.md#debug-remotely) -apps. Deze optie wordt na 48 uur automatisch uitgeschakeld.
- **Binnenkomende client certificaten**: client certificaten in [wederzijdse verificatie](app-service-web-configure-tls-mutual-auth.md)vereisen.

## <a name="configure-default-documents"></a>Standaard documenten configureren

Deze instelling geldt alleen voor Windows-apps.

Ga in het [Azure Portal]naar de beheer pagina van de app. Klik in het menu links op **configuratie** > **standaard documenten**.

![Algemene instellingen](./media/configure-common/open-documents.png)

Het standaard document is de webpagina die wordt weer gegeven op de basis-URL voor een website. Het eerste overeenkomende bestand in de lijst wordt gebruikt. Als u een nieuw standaard document wilt toevoegen, klikt u op **Nieuw document**. Vergeet niet op **Opslaan**te klikken.

Als de app gebruikmaakt van modules die worden gerouteerd op basis van URL in plaats van statische inhoud, is er geen behoefte aan standaard documenten.

## <a name="configure-path-mappings"></a>Paden configureren

Ga in het [Azure Portal]naar de beheer pagina van de app. Klik in het menu links van de app > op**toewijzingen**van configuratiepad.

![Algemene instellingen](./media/configure-common/open-path.png)

Op de pagina **paden toewijzen** worden verschillende dingen weer gegeven op basis van het type besturings systeem.

### <a name="windows-apps-uncontainerized"></a>Windows-apps (niet in container)

Voor Windows-apps kunt u de IIS-handlertoewijzing aanpassen en virtuele toepassingen en mappen.

Met handlertoewijzing kunt u aangepaste script processors toevoegen voor het afhandelen van aanvragen voor specifieke bestands extensies. Als u een aangepaste handler wilt toevoegen, klikt u op **nieuwe handler**. Configureer de handler als volgt:

- **Extensie**. De bestands extensie die u wilt verwerken, zoals  *\*. php* of *handler. fcgi*.
- **Script processor**. Het absolute pad van de script processor naar u. Aanvragen voor bestanden die overeenkomen met de bestands extensie worden verwerkt door de script processor. Gebruik het pad `D:\home\site\wwwroot` om te verwijzen naar de hoofdmap van uw app.
- **Argumenten**. Optionele opdracht regel argumenten voor de script processor.

`/` Aan`D:\home\site\wwwroot`elke app wordt het standaard hoofdpad () toegewezen, waar uw code standaard wordt geïmplementeerd. Als de hoofdmap van de app zich in een andere map bevindt, of als uw opslag plaats meer dan één toepassing heeft, kunt u hier virtuele toepassingen en directory's bewerken of toevoegen. Klik op **nieuwe virtuele toepassing of directory**.

Als u virtuele toepassingen en directory's wilt configureren, geeft u elke virtuele map en het bijbehorende fysieke pad op ten opzichte van de hoofdmap van de website (`D:\home`). U kunt eventueel het selectie vakje van de **toepassing** selecteren om een virtuele map als een toepassing te markeren.

### <a name="containerized-apps"></a>Apps in de container

U kunt [aangepaste opslag voor uw container-app toevoegen](containers/how-to-serve-content-from-azure-storage.md). Apps in containers bevatten alle Linux-apps en ook de aangepaste Windows-en Linux-containers die worden uitgevoerd op App Service. Klik op **nieuw Azure Storage** uw aangepaste opslag als volgt te koppelen en te configureren:

- **Naam**: De weergave naam.
- **Configuratie opties**: **Basis** of **Geavanceerd**.
- **Opslag accounts**: Het opslag account met de gewenste container.
- **Opslag type**: **Azure** -blobs of- **Azure files**.
  > [!NOTE]
  > Windows-container-apps ondersteunen alleen Azure Files.
- **Opslag container**: De gewenste container voor de basis configuratie.
- **Share naam**: De naam van de bestands share voor geavanceerde configuratie.
- **Toegangs sleutel**: De toegangs sleutel voor geavanceerde configuratie.
- **Koppelingspad**: Het absolute pad in uw container om de aangepaste opslag te koppelen.

Zie voor meer informatie [inhoud bezorgen van Azure Storage in app service op Linux](containers/how-to-serve-content-from-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Taal stack-instellingen configureren

Voor Linux-apps raadpleegt u:

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Aangepaste containers configureren

Zie [een aangepaste Linux-container configureren voor Azure app service](containers/configure-custom-container.md)

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste domeinnaam configureren in Azure App Service]
- [Faseringsomgevingen in Azure App Service instellen]
- [HTTPS inschakelen voor een app in Azure App Service]
- [Diagnostische logboeken inschakelen](troubleshoot-diagnostic-logs.md)
- [Een app schalen in Azure App Service]
- [Basis principes controleren in Azure App Service]
- [Wijzig de instellingen van het bestand applicationHost. config met applicationHost. xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET-Signa lering]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Een aangepaste domeinnaam configureren in Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Faseringsomgevingen in Azure App Service instellen]: ./deploy-staging-slots.md
[HTTPS inschakelen voor een app in Azure App Service]: ./app-service-web-tutorial-custom-ssl.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Basis principes controleren in Azure App Service]: ./web-sites-monitor.md
[modus]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Een app schalen in Azure App Service]: ./manage-scale-up.md
