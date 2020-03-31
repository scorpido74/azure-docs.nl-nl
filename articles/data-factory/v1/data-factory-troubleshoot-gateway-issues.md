---
title: Problemen met gegevensbeheergateway oplossen
description: Geeft tips om problemen met betrekking tot Data Management Gateway op te lossen.
services: data-factory
author: nabhishek
manager: anandsub
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 09d51de3ae0bd4baca585d2abdd936b1a29567d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065033"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Problemen oplossen met behulp van Data Management Gateway
In dit artikel vindt u informatie over het oplossen van problemen met het gebruik van Data Management Gateway.

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Azure Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [zelf gehoste runtime voor integratie in Data Factory](../create-self-hosted-integration-runtime.md).

Zie het artikel [Data Management Gateway](data-factory-data-management-gateway.md) voor gedetailleerde informatie over de gateway. Zie de [gegevens verplaatsen tussen on-premises en cloudartikel](data-factory-move-data-between-onprem-and-cloud.md) voor een walkthrough van het verplaatsen van gegevens van een on-premises SQL Server-database naar Microsoft Azure Blob-opslag met behulp van de gateway.

## <a name="failed-to-install-or-register-gateway"></a>Kan gateway niet installeren of registreren
### <a name="1-problem"></a>1. Probleem
U ziet dit foutbericht bij het installeren en registreren van een gateway, in het bijzonder, tijdens het downloaden van het gateway-installatiebestand.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Oorzaak
De machine waarop u de gateway probeert te installeren, heeft het nieuwste gateway-installatiebestand niet gedownload van het downloadcentrum vanwege een netwerkprobleem.

#### <a name="resolution"></a>Oplossing
Controleer de instellingen van uw firewallproxyserver om te zien of de instellingen de netwerkverbinding blokkeren van de computer naar het [downloadcentrum](https://download.microsoft.com/)en de instellingen dienovereenkomstig bij te werken.

U het installatiebestand voor de nieuwste gateway ook downloaden vanuit het [downloadcentrum](https://www.microsoft.com/download/details.aspx?id=39717) op andere machines die toegang hebben tot het downloadcentrum. U het installatiebestand vervolgens naar de gatewayhostcomputer kopiëren en handmatig uitvoeren om de gateway te installeren en bij te werken.

### <a name="2-problem"></a>2. Probleem
Deze fout wordt weergegeven wanneer u een gateway probeert te installeren door **rechtstreeks op deze computer** in de Azure-portal te klikken.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Oorzaak
Er is al een gateway op de machine geïnstalleerd.

#### <a name="resolution"></a>Oplossing
Verwijder de bestaande gateway op de machine en klik opnieuw op de **installatie op deze computerkoppeling.**

### <a name="3-problem"></a>3. Probleem
Mogelijk ziet u deze fout bij het registreren van een nieuwe gateway.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Oorzaak
Mogelijk ziet u dit bericht om een van de volgende redenen:

* De indeling van de gatewaysleutel is ongeldig.
* De gatewaysleutel is ongeldig verklaard.
* De gatewaysleutel is geregenereerd vanaf het portaal.  

#### <a name="resolution"></a>Oplossing
Controleer of u de juiste gatewaysleutel van de portal gebruikt. Regenereert indien nodig een sleutel en gebruik de sleutel om de gateway te registreren.

### <a name="4-problem"></a>4. Probleem
Mogelijk ziet u het volgende foutbericht wanneer u een gateway registreert.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Inhoud of indeling van de sleutel is ongeldig](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Oorzaak
De inhoud of indeling van de invoergatewaysleutel is onjuist. Een van de redenen kan zijn dat u slechts een deel van de sleutel van de portal hebt gekopieerd of dat u een ongeldige sleutel gebruikt.

#### <a name="resolution"></a>Oplossing
Genereer een gatewaysleutel in de portal en gebruik de kopieerknop om de hele sleutel te kopiëren. Plak het vervolgens in dit venster om de gateway te registreren.

### <a name="5-problem"></a>5. Probleem
Mogelijk ziet u het volgende foutbericht wanneer u een gateway registreert.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Gatewaysleutel is ongeldig of leeg](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Oorzaak
De gatewaysleutel is geregenereerd of de gateway is verwijderd in de Azure-portal. Het kan ook gebeuren als de opstelling van de Data Management Gateway niet de laatste is.

#### <a name="resolution"></a>Oplossing
Controleer of de instellingen van de Data Management Gateway de nieuwste versie zijn, u de nieuwste versie vinden in het [Microsoft-downloadcentrum.](https://go.microsoft.com/fwlink/p/?LinkId=271260)

Als de installatie actueel/ laatste is en de gateway nog steeds bestaat op Portal, regenereert u de gatewaysleutel in de Azure-portal en gebruikt u de kopieerknop om de hele sleutel te kopiëren en vervolgens in dit venster te plakken om de gateway te registreren. Anders maakt u de gateway opnieuw en begint u opnieuw.

### <a name="6-problem"></a>6. Probleem
Mogelijk ziet u het volgende foutbericht wanneer u een gateway registreert.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![Gatewaysleutel is ongeldig of leeg](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Oorzaak
Deze fout kan optreden omdat de gateway is verwijderd of de bijbehorende gatewaysleutel is geregenereerd.

#### <a name="resolution"></a>Oplossing
Als de gateway is verwijderd, maakt u de gateway opnieuw vanuit de portal, klikt u op **Registreren,** kopieert u de sleutel van de portal, plakt u deze en probeert u de gateway te registreren.

Als de gateway nog steeds bestaat, maar de sleutel is geregenereerd, gebruikt u de nieuwe sleutel om de gateway te registreren. Als u de sleutel niet hebt, u de sleutel opnieuw regenereren van het portaal.

### <a name="7-problem"></a>7. Probleem
Wanneer u een gateway registreert, moet u mogelijk pad en wachtwoord invoeren voor een certificaat.

![Certificaat opgeven](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Oorzaak
De gateway is al eerder geregistreerd op andere machines. Tijdens de eerste registratie van een gateway is een versleutelingscertificaat gekoppeld aan de gateway. Het certificaat kan zelf worden gegenereerd door de gateway of door de gebruiker worden geleverd.  Dit certificaat wordt gebruikt om referenties van het gegevensarchief (gekoppelde service) te versleutelen.  

![Certificaat exporteren](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Wanneer u de gateway op een andere hostmachine herstelt, vraagt de registratiewizard om dit certificaat om referenties te decoderen die eerder met dit certificaat waren versleuteld.  Zonder dit certificaat kunnen de referenties niet worden gedecodeerd door de nieuwe gateway en mislukken latere uitvoeringen van kopieeractiviteiten die aan deze nieuwe gateway zijn gekoppeld.  

#### <a name="resolution"></a>Oplossing
Als u het referentiecertificaat hebt geëxporteerd vanaf de oorspronkelijke gatewaymachine met de knop **Exporteren** op het tabblad **Instellingen** in Configuratiebeheer voor gegevensbeheergateway, gebruikt u het certificaat hier.

U deze fase niet overslaan bij het herstellen van een gateway. Als het certificaat ontbreekt, moet u de gateway uit de portal verwijderen en een nieuwe gateway opnieuw maken.  Werk bovendien alle gekoppelde services bij die gerelateerd zijn aan de gateway door hun referenties opnieuw in te voeren.

### <a name="8-problem"></a>8. Probleem
Mogelijk ziet u het volgende foutbericht.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Oorzaak
Deze fout treedt op wanneer uw gateway zich in een omgeving bevindt waarvoor een HTTP-proxy nodig is om toegang te krijgen tot internetbronnen of het verificatiewachtwoord van uw proxy wordt gewijzigd, maar deze gateway niet dienovereenkomstig wordt bijgewerkt.

#### <a name="resolution"></a>Oplossing
Volg de instructies in het gedeelte Proxyserver overwegingen van dit artikel en configureer proxy-instellingen met Data Management Gateway Configuration Manager.

## <a name="gateway-is-online-with-limited-functionality"></a>Gateway is online met beperkte functionaliteit
### <a name="1-problem"></a>1. Probleem
U ziet de status van de gateway als online met beperkte functionaliteit.

#### <a name="cause"></a>Oorzaak
U ziet de status van de gateway als online met beperkte functionaliteit om een van de volgende redenen:

* Gateway kan geen verbinding maken met cloudservice via Azure Service Bus.
* Cloudservice kan geen verbinding maken met gateway via Service Bus.

Wanneer de gateway online is met beperkte functionaliteit, u mogelijk de wizard Gegevensfabriekskopie niet gebruiken om gegevenspijplijnen te maken voor het kopiëren van gegevens naar of van on-premises gegevensarchieven. Als tijdelijke oplossing u Data Factory Editor gebruiken in de portal, Visual Studio of Azure PowerShell.

#### <a name="resolution"></a>Oplossing
Oplossing voor dit probleem (online met beperkte functionaliteit) is gebaseerd op de vraag of de gateway geen verbinding kan maken met de cloudservice of op de andere manier. In de volgende secties worden deze resoluties verstrekt.

### <a name="2-problem"></a>2. Probleem
U ziet de volgende fout.

`Error: Gateway cannot connect to cloud service through service bus`

![Gateway kan geen verbinding maken met cloudservice](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Oorzaak
Gateway kan geen verbinding maken met de cloudservice via Service Bus.

#### <a name="resolution"></a>Oplossing
Volg deze stappen om de gateway weer online te krijgen:

1. Sta uitgaande IP-adresregels toe op de gatewaymachine en de bedrijfsfirewall. U IP-adressen vinden in het Windows-gebeurtenislogboek (ID == 401): er is geprobeerd toegang te krijgen tot een socket op een manier die verboden is door de toegangsmachtigingen XX. Xx. Xx. XX:9350.
1. Proxy-instellingen configureren op de gateway. Zie de sectie Proxyserveroverwegingen voor meer informatie.
1. Schakel uitgaande poorten 5671 en 9350-9354 in op zowel de Windows Firewall op de gatewaymachine als de bedrijfsfirewall. Zie de sectie Poorten en firewall voor meer informatie. Deze stap is optioneel, maar we raden deze aan voor prestatieoverweging.

### <a name="3-problem"></a>3. Probleem
U ziet de volgende fout.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Oorzaak
Een tijdelijke fout in de netwerkverbinding.

#### <a name="resolution"></a>Oplossing
Volg deze stappen om de gateway weer online te krijgen:

1. Wacht een paar minuten, de verbinding wordt automatisch hersteld wanneer de fout is verdwenen.
1. Als de fout blijft bestaan, start u de gatewayservice opnieuw.

## <a name="failed-to-author-linked-service"></a>Kan geen gekoppelde service maken
### <a name="problem"></a>Probleem
Deze fout wordt mogelijk weergegeven wanneer u Credential Manager in de portal probeert te gebruiken om referenties voor een nieuwe gekoppelde service in te voeren of referenties voor een bestaande gekoppelde service bij te werken.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Wanneer u deze fout ziet, ziet de instellingenpagina van Data Management Gateway Configuration Manager er mogelijk uit als de volgende schermafbeelding.

![Database kan niet worden bereikt](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Oorzaak
Het TLS/SSL-certificaat is mogelijk verloren gegaan op de gatewaymachine. De gatewaycomputer kan het certificaat dat momenteel wordt gebruikt voor TLS-versleuteling niet laden. Mogelijk ziet u ook een foutbericht in het gebeurtenislogboek dat vergelijkbaar is met het volgende bericht.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Oplossing
Volg de volgende stappen om het probleem op te lossen:

1. Start Data Management Gateway Configuration Manager.
2. Ga naar het tabblad **Instellingen.**  
3. Klik **op** de knop Wijzigen om het TLS/SSL-certificaat te wijzigen.

   ![Knop Certificaat wijzigen](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Selecteer een nieuw certificaat als TLS/SSL-certificaat. U elk TLS/SSL-certificaat gebruiken dat door u of een organisatie wordt gegenereerd.

   ![Certificaat opgeven](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Kopieeractiviteit mislukt
### <a name="problem"></a>Probleem
Mogelijk ziet u de volgende fout van 'UserErrorFailedToConnectToSqlserver' nadat u een pijplijn in de portal hebt ingesteld.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Oorzaak
Dit kan om verschillende redenen gebeuren en mitigatie varieert dienovereenkomstig.

#### <a name="resolution"></a>Oplossing
Uitgaande TCP-verbindingen toestaan via poort TCP/1433 aan de clientzijde van De Gegevensbeheergateway voordat u verbinding maakt met een SQL-database.

Als de doeldatabase een Azure SQL-database is, schakelt u ook sql server-firewall-instellingen voor Azure in.

Zie de volgende sectie om de verbinding met het on-premises gegevensarchief te testen.

## <a name="data-store-connection-or-driver-related-errors"></a>Gegevensarchiefverbinding of stuurprogrammagerelateerde fouten
Als u de verbinding met het gegevensarchief of stuurprogrammagerelateerde fouten ziet, voert u de volgende stappen uit:

1. Start Data Management Gateway Configuration Manager op de gatewaymachine.
2. Ga naar het tabblad **Diagnostische gegevens.**
3. Voeg in **Testconnection**de waarden van de gatewaygroep toe.
4. Klik **op Testen** om te zien of u verbinding maken met de on-premises gegevensbron van de gatewaymachine met behulp van de verbindingsgegevens en -referenties. Als de testverbinding ook niet lukt nadat u een stuurprogramma hebt geïnstalleerd, start u de gateway opnieuw op om de meest recente wijziging op te halen.

![Verbinding testen in het tabblad Diagnostische gegevens](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Gatewaylogboeken
### <a name="send-gateway-logs-to-microsoft"></a>Gatewaylogboeken naar Microsoft verzenden
Wanneer u contact opneemt met Microsoft Support om hulp te krijgen bij het oplossen van gatewayproblemen, wordt u mogelijk gevraagd om uw gatewaylogboeken te delen. Met de release van de gateway u vereiste gatewaylogboeken delen met twee klikken op de knop in Data Management Gateway Configuration Manager.    

1. Ga naar het tabblad **Diagnostische gegevens** in Data Management Gateway Configuration Manager.

    ![Tabblad Gegevensbeheergatewaydiagnostiek](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Klik **op Logboeken verzenden** om het volgende dialoogvenster te bekijken.

    ![Logboeken voor gegevensbeheergateway verzenden](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (Optioneel) Klik **op logboeken bekijken** om logboeken in de gebeurtenisviewer te bekijken.
4. (Optioneel) Klik **op privacy** om de privacyverklaring van Microsoft Web Services te bekijken.
5. Wanneer u tevreden bent met wat u gaat uploaden, klikt u op **Logboeken verzenden** om de logboeken van de afgelopen zeven dagen daadwerkelijk naar Microsoft te verzenden voor het oplossen van problemen. U ziet de status van de bewerking send-logs zoals weergegeven in de volgende schermafbeelding.

    ![Status gegevensbeheergateway send-logboeken](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. Nadat de bewerking is voltooid, ziet u een dialoogvenster zoals weergegeven in de volgende schermafbeelding.

    ![Status gegevensbeheergateway send-logboeken](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Sla de **rapport-id op** en deel deze met Microsoft Support. De rapport-id wordt gebruikt om de gatewaylogboeken te vinden die u hebt geüpload voor het oplossen van problemen.  De rapport-ID wordt ook opgeslagen in de gebeurtenisviewer.  U het vinden door te kijken naar het evenement ID 25, en controleer de datum en tijd.

    ![Rapport-ID van logboeken voor gegevensbeheergatewaysend](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Archiefgatewaylogboeken op gatewayhostmachine
Er zijn enkele scenario's waarin u gatewayproblemen hebt en u gatewaylogboeken niet rechtstreeks delen:

* U installeert de gateway handmatig en registreert de gateway.
* U probeert de gateway te registreren met een geregenereerdsleutel in Data Management Gateway Configuration Manager.
* U probeert logboeken te verzenden en de gatewayhostservice kan niet worden verbonden.

Voor deze scenario's u gatewaylogboeken opslaan als een zip-bestand en deze delen wanneer u contact opneemt met Microsoft-ondersteuning. Als u bijvoorbeeld een fout ontvangt terwijl u de gateway registreert, zoals in de volgende schermafbeelding wordt weergegeven.   

![Gegevensbeheergatewayregistratie: fout](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Klik op de koppeling **Archiefgatewaylogboeken** om logboeken te archiveren en op te slaan en deel het zip-bestand vervolgens met Microsoft-ondersteuning.

![Logboeken van gegevensbeheergatewayarchief](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Gatewaylogboeken zoeken
U vindt gedetailleerde informatie over gatewaylogboeken in de logboeken van Windows-gebeurtenissen.

1. **Windows-logboeken starten**.
2. Zoek logboeken in de map **Application and Services Logs** > **Data Management Gateway.**

   Wanneer u problemen met gatewayproblemen oplost, zoekt u naar gebeurtenissen op foutniveau in de gebeurtenisviewer.

![Gegevensbeheergatewaylogboeken in gebeurtenisviewer](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
