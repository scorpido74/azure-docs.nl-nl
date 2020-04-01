---
title: Data Management Gateway voor gegevensfabriek
description: Stel een gegevensgateway in om gegevens te verplaatsen tussen on-premises en de cloud. Gebruik Data Management Gateway in Azure Data Factory om uw gegevens te verplaatsen.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 1340c205477b256e3d96ff7ccacb64e575725c2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065406"
---
# <a name="data-management-gateway"></a>Gegevensbeheergateway
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [zelf gehoste runtime voor integratie in](../create-self-hosted-integration-runtime.md).

> [!NOTE]
> Data Management Gateway is nu omgedoopt tot Self-hosted Integration Runtime.

De Data management gateway is een clientagent die u in uw on-premises omgeving moet installeren om gegevens tussen cloud- en on-premises gegevensopslag te kopiëren. De on-premises gegevensopslag die door Data Factory wordt ondersteund, worden vermeld in de sectie [Ondersteunde gegevensbronnen.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)

Dit artikel vormt een aanvulling op de walkthrough in het artikel [Move-gegevens tussen on-premises en cloudgegevensstores.](data-factory-move-data-between-onprem-and-cloud.md) In de walkthrough maakt u een pijplijn die de gateway gebruikt om gegevens van een on-premises SQL Server-database naar een Azure-blob te verplaatsen. Dit artikel bevat gedetailleerde diepgaande informatie over de data management gateway.

U een datamanagementgateway uitschalen door meerdere on-premises machines aan de gateway te koppelen. U worden opgeschaald door het aantal taken voor gegevensverplaatsing te verhogen die gelijktijdig op een knooppunt kunnen worden uitgevoerd. Deze functie is ook beschikbaar voor een logische gateway met één knooppunt. Zie [Scaling data management gateway in Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) artikel voor meer informatie.

> [!NOTE]
> Momenteel ondersteunt gateway alleen de kopieeractiviteit en opgeslagen procedureactiviteit in Data Factory. Het is niet mogelijk om de gateway te gebruiken van een aangepaste activiteit om toegang te krijgen tot on-premises gegevensbronnen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht
### <a name="capabilities-of-data-management-gateway"></a>Mogelijkheden van data management gateway
Data management gateway biedt de volgende mogelijkheden:

* Modelleer on-premises gegevensbronnen en cloudgegevensbronnen binnen dezelfde gegevensfabriek en verplaats gegevens.
* Heb één ruit voor bewaking en beheer met inzicht in de gatewaystatus van de pagina Gegevensfabriek.
* Beheer de toegang tot on-premises gegevensbronnen veilig.
  * Geen wijzigingen nodig in bedrijfsfirewall. Gateway maakt alleen uitgaande HTTP-gebaseerde verbindingen om internet te openen.
  * Versleutel referenties voor uw on-premises gegevensarchieven met uw certificaat.
* Gegevens efficiënt verplaatsen - gegevens worden parallel overgedragen en bestand tegen intermitterende netwerkproblemen met logica voor automatisch opnieuw proberen.

### <a name="command-flow-and-data-flow"></a>Opdrachtstroom en gegevensstroom
Wanneer u een kopieeractiviteit gebruikt om gegevens tussen on-premises en cloud te kopiëren, gebruikt de activiteit een gateway om gegevens over te zetten van on-premises gegevensbron naar de cloud en vice versa.

Hier is de gegevensstroom op hoog niveau voor en ![samenvatting van stappen voor kopiëren met gegevensgateway: gegevensstroom met behulp van gateway](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. Gegevensontwikkelaar maakt een gateway voor een Azure Data Factory met behulp van de [Azure-portal](https://portal.azure.com) of [PowerShell Cmdlet.](https://docs.microsoft.com/powershell/module/az.datafactory/)
2. Gegevensontwikkelaar maakt een gekoppelde service voor een on-premises gegevensarchief door de gateway op te geven. Als onderdeel van het instellen van de gekoppelde service gebruikt gegevensontwikkelaar de toepassing Referenties instellen om verificatietypen en -referenties op te geven. Het dialoogvenster Referenties instellen wordt gecommuniceerd met het gegevensarchief om de verbinding en de gateway te testen om referenties op te slaan.
3. Gateway versleutelt de referenties met het certificaat dat is gekoppeld aan de gateway (geleverd door gegevensontwikkelaar), voordat de referenties in de cloud worden opgeslagen.
4. De service Data Factory communiceert met de gateway voor het plannen van & beheer van taken via een beheerkanaal dat gebruikmaakt van een gedeelde Azure-servicebuswachtrij. Wanneer een kopieeractiviteitstaak moet worden afgetrapt, klanget Data Factory de aanvraag samen met referentiegegevens. Gateway start de taak na het peilen van de wachtrij.
5. De gateway decodeert de referenties met hetzelfde certificaat en maakt vervolgens verbinding met het on-premises gegevensarchief met het juiste verificatietype en referenties.
6. De gateway kopieert gegevens van een on-premises winkel naar een cloudopslag, of omgekeerd, afhankelijk van hoe de kopieeractiviteit is geconfigureerd in de gegevenspijplijn. Voor deze stap communiceert de gateway rechtstreeks met cloudgebaseerde opslagservices zoals Azure Blob Storage via een beveiligd HTTPS-kanaal.

### <a name="considerations-for-using-gateway"></a>Overwegingen voor het gebruik van gateway
* Eén exemplaar van de datamanagementgateway kan worden gebruikt voor meerdere on-premises gegevensbronnen. Eén **gateway-exemplaar is echter gekoppeld aan slechts één Azure-gegevensfabriek** en kan niet worden gedeeld met een andere gegevensfabriek.
* U **slechts één exemplaar van de gegevensbeheergateway** op één machine installeren. Stel dat u twee gegevensfabrieken hebt die toegang moeten krijgen tot on-premises gegevensbronnen, u moet gateways installeren op twee on-premises computers. Met andere woorden, een gateway is gekoppeld aan een specifieke gegevensfabriek
* De **gateway hoeft niet op dezelfde machine te staan als de gegevensbron.** Echter, met gateway dichter bij de gegevensbron vermindert de tijd voor de gateway om verbinding te maken met de gegevensbron. We raden u aan de gateway te installeren op een machine die anders is dan de server die on-premises gegevensbron host. Wanneer de gateway en gegevensbron zich op verschillende machines bevinden, concurreert de gateway niet om bronnen met gegevensbron.
* U **meerdere gateways op verschillende machines hebben die verbinding maken met dezelfde on-premises gegevensbron.** U bijvoorbeeld twee gateways hebben die twee gegevensfabrieken bedienen, maar dezelfde on-premises gegevensbron wordt geregistreerd bij beide gegevensfabrieken.
* Als u al een gateway op uw computer hebt geïnstalleerd die een **Power BI-scenario** biedt, installeert u een **afzonderlijke gateway voor Azure Data Factory** op een andere machine.
* Gateway moet zelfs worden gebruikt wanneer u **ExpressRoute**gebruikt.
* Behandel uw gegevensbron als een on-premises gegevensbron (die zich achter een firewall bevindt) zelfs wanneer u **ExpressRoute**gebruikt. Gebruik de gateway om de verbinding tussen de service en de gegevensbron tot stand te brengen.
* U moet **de gateway gebruiken,** zelfs als het gegevensarchief zich in de cloud bevindt op een **Azure IaaS VM.**

## <a name="installation"></a>Installeren
### <a name="prerequisites"></a>Vereisten
* De ondersteunde **versies van het besturingssysteem** zijn Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. De installatie van de datamanagementgateway op een domeincontroller wordt momenteel niet ondersteund.
* .NET Framework 4.5.1 of hoger is vereist. Als u gateway installeert op een Windows 7-machine, installeert u .NET Framework 4.5 of hoger. Zie [.NET Framework System Requirements](https://msdn.microsoft.com/library/8z6watww.aspx) for details.
* De aanbevolen **configuratie** voor de gatewaymachine is ten minste 2 GHz, 4 cores, 8 GB RAM en 80 GB schijf.
* Als de hostmachine overwintert, reageert de gateway niet op gegevensverzoeken. Configureer daarom een geschikt **energiebeheerschema** op de computer voordat u de gateway installeert. Als de machine is geconfigureerd om te overwinteren, wordt met de gateway-installatie een bericht gevraagd.
* U moet een beheerder op de machine zijn om de gegevensbeheergateway te installeren en te configureren. U extra gebruikers toevoegen aan de lokale **Windows-groep voor gegevensbeheergebruikers.** De leden van deze groep kunnen het hulpprogramma **Gegevensbeheergateway Configuratiebeheer** gebruiken om de gateway te configureren.

Als kopieeractiviteit wordt uitgevoerd op een specifieke frequentie, het gebruik van de bron (CPU, geheugen) op de machine volgt ook hetzelfde patroon met piek-en idle tijden. Het gebruik van resources is ook sterk afhankelijk van de hoeveelheid gegevens die wordt verplaatst. Wanneer er meerdere kopieertaken worden uitgevoerd, ziet u het gebruik van resources tijdens piekuren stijgen.

### <a name="installation-options"></a>Installatieopties
De gateway voor gegevensbeheer kan op de volgende manieren worden geïnstalleerd:

* Door een MSI-installatiepakket te downloaden vanuit het [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). De MSI kan ook worden gebruikt om bestaande datamanagementgateway te upgraden naar de nieuwste versie, waarbij alle instellingen behouden blijven.
* Door te klikken op **Gegevensgatewaykoppeling downloaden en installeren** onder HANDMATIG instellen of **direct installeren op deze computer** onder EXPRESS SETUP. Zie [Gegevens verplaatsen tussen on-premises en cloudartikel](data-factory-move-data-between-onprem-and-cloud.md) voor stapsgewijze instructies over het gebruik van express-instellingen. De handmatige stap brengt u naar het downloadcentrum. De instructies voor het downloaden en installeren van de gateway van download center zijn in de volgende sectie.

### <a name="installation-best-practices"></a>Aanbevolen procedures voor installatie:
1. Configureer het energiebeheerschema op de hostmachine voor de gateway, zodat de machine niet overwintert. Als de hostmachine overwintert, reageert de gateway niet op gegevensverzoeken.
2. Een back-up maken van het certificaat dat aan de gateway is gekoppeld.

### <a name="install-the-gateway-from-download-center"></a>De gateway installeren vanuit downloadcentrum
1. Navigeer naar [de downloadpagina van Microsoft Data Management Gateway.](https://www.microsoft.com/download/details.aspx?id=39717)
2. Klik **op Downloaden,** selecteer de **64-bits** versie (32-bits wordt niet meer ondersteund) en klik op **Volgende**.
3. Voer de **MSI** direct uit of sla deze op uw harde schijf op en voer deze uit.
4. Selecteer **op** de welkomstpagina een **taal** klik op **Volgende**.
5. **Accepteer** de licentieovereenkomst voor eindgebruikers en klik op **Volgende**.
6. Selecteer **map** om de gateway te installeren en klik op **Volgende**.
7. Klik **op** de pagina Klaar om te installeren op **Installeren**.
8. Klik **op Voltooien** om de installatie te voltooien.
9. Haal de sleutel uit de Azure-portal. Zie de volgende sectie voor stapsgewijze instructies.
10. Voer op de pagina **Gateway Register** van Data Management Gateway **Configuration Manager** die op uw machine wordt uitgevoerd, de volgende stappen uit:
    1. Plak de sleutel in de tekst.
    2. Klik optioneel op **Gateway-toets weergeven** om de sleuteltekst weer te geven.
    3. Klik **op Registreren**.

### <a name="register-gateway-using-key"></a>Gateway registreren met sleutel
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Als u nog geen logische gateway in de portal hebt gemaakt
Als u een gateway in de portal wilt maken en de sleutel wilt ophalen van de pagina **Configureren,** volgt U stappen van walkthrough in de [gegevens verplaatsen tussen on-premises en cloudartikel.](data-factory-move-data-between-onprem-and-cloud.md)

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Als u de logische gateway in de portal al hebt gemaakt
1. Navigeer in Azure-portal naar de pagina **Gegevensfabriek** en klik op de tegel **Gekoppelde services.**

    ![Pagina Gegevensfabriek](media/data-factory-data-management-gateway/data-factory-blade.png)
2. Selecteer op de pagina **Gekoppelde Services** de logische **gateway** die u in de portal hebt gemaakt.

    ![logische gateway](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
3. Klik op de pagina **Gegevensgateway** op **Gegevensgateway downloaden en installeren.**

    ![Koppeling downloaden in de portal](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
4. Klik **op** de pagina Configureren op **Toets opnieuw maken**. Klik op Ja op het waarschuwingsbericht nadat u het zorgvuldig hebt gelezen.

    ![Toets opnieuw maken](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Klik op de knop Kopiëren naast de sleutel. De sleutel wordt gekopieerd naar het klembord.

    ![Sleutel kopiëren](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Pictogrammen/meldingen van systeemlade
In de volgende afbeelding ziet u enkele van de ladepictogrammen die u ziet.

![pictogrammen voor systeemlade](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Als u cursor over het systeemladepictogram/meldingsbericht beweegt, ziet u details over de status van de gateway/update-bewerking in een pop-upvenster.

### <a name="ports-and-firewall"></a>Poorten en firewall
Er zijn twee firewalls waar u rekening mee moet houden: **bedrijfsfirewall** die wordt uitgevoerd op de centrale router van de organisatie en **Windows-firewall** geconfigureerd als een daemon op de lokale machine waar de gateway is geïnstalleerd.

![Firewalls](./media/data-factory-data-management-gateway/firewalls2.png)

Op bedrijfsfirewallniveau moet u de volgende domeinen en uitgaande poorten configureren:

| Domeinnamen | Poorten | Beschrijving |
| --- | --- | --- |
| *.servicebus.windows.net |443 |Wordt gebruikt voor communicatie met back-end van Data Movement Service |
| *.core.windows.net |443 |Wordt gebruikt voor Gefaseerde kopie met Azure Blob (indien geconfigureerd)|
| *.frontend.clouddatahub.net |443 |Wordt gebruikt voor communicatie met back-end van Data Movement Service |
| *.servicebus.windows.net |9350-9354, 5671 |Optioneel servicebusrelais via TCP dat wordt gebruikt door de wizard Kopiëren |

Op Windows-firewallniveau zijn deze uitgaande poorten normaal gesproken ingeschakeld. Als dit niet het zo is, u de domeinen en poorten dienovereenkomstig configureren op de gatewaymachine.

> [!NOTE]
> 1. Op basis van uw bron/ sinks moet u mogelijk extra domeinen en uitgaande poorten in uw bedrijfs/Windows-firewall op de witte lijst zetten.
> 2. Voor sommige clouddatabases (bijvoorbeeld: [Azure SQL Database,](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings) [Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access), enz.) moet u mogelijk ip-adres van gatewaymachine op hun firewallconfiguratie op de witte lijst zetten.
>
>

#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Gegevens uit een brongegevensarchief kopiëren naar een gootsteengegevensarchief
Zorg ervoor dat de firewallregels correct zijn ingeschakeld op de bedrijfsfirewall, Windows-firewall op de gatewaymachine en het gegevensarchief zelf. Als u deze regels inschakelt, kan de gateway verbinding maken met zowel de bron als de gootsteen. Regels inschakelen voor elk gegevensarchief dat betrokken is bij de kopieerbewerking.

Als u bijvoorbeeld wilt kopiëren vanuit **een on-premises gegevensarchief naar een Azure SQL Database-sink of een Azure SQL Data Warehouse-sink,** gaat u de volgende stappen uitvoeren:

* Laat uitgaande **TCP-communicatie** op poort **1433** toe voor zowel Windows firewall als bedrijfsfirewall.
* Configureer de firewall-instellingen van Azure SQL Server om het IP-adres van de gatewaymachine toe te voegen aan de lijst met toegestane IP-adressen.

> [!NOTE]
> Als uw firewall uitgaande poort 1433 niet toestaat, heeft Gateway geen directe toegang tot Azure SQL. In dit geval u [Gefaseerde kopie](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy) gebruiken voor SQL Azure Database/ SQL Azure DW. In dit scenario hebt u alleen HTTPS (poort 443) nodig voor de gegevensverplaatsing.
>
>

### <a name="proxy-server-considerations"></a>Overwegingen proxyserver
Als uw bedrijfsnetwerkomgeving een proxyserver gebruikt om toegang te krijgen tot internet, configureert u de gateway voor gegevensbeheer om de juiste proxy-instellingen te gebruiken. U de proxy instellen tijdens de eerste registratiefase.

![Proxy instellen tijdens registratie](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Gateway gebruikt de proxyserver om verbinding te maken met de cloudservice. Klik op **Koppeling wijzigen** tijdens de eerste installatie. U ziet het dialoogvenster **proxy-instelling.**

![Proxy instellen met config-manager](media/data-factory-data-management-gateway/SetProxySettings.png)

Er zijn drie configuratieopties:

* **Gebruik geen proxy:** Gateway gebruikt niet expliciet een proxy om verbinding te maken met cloudservices.
* **Systeemproxy gebruiken:** Gateway gebruikt de proxy-instelling die is geconfigureerd in diahost.exe.config en diawp.exe.config. Als er geen proxy is geconfigureerd in diahost.exe.config en diawp.exe.config, maakt gateway rechtstreeks verbinding met cloudservice zonder proxy te doorlopen.
* **Aangepaste proxy**gebruiken: Configureer de HTTP-proxy-instelling die u wilt gebruiken voor gateway, in plaats van configuraties te gebruiken in diahost.exe.config en diawp.exe.config. Adres en poort zijn vereist. Gebruikersnaam en wachtwoord zijn optioneel, afhankelijk van de verificatie-instelling van uw proxy. Alle instellingen worden versleuteld met het referentiecertificaat van de gateway en lokaal opgeslagen op de gatewayhostmachine.

De hostservice voor gegevensbeheergateway wordt automatisch opnieuw opgestart nadat u de bijgewerkte proxy-instellingen hebt opgeslagen.

Nadat de gateway is geregistreerd, gebruikt u Gegevensbeheer Gateway Configuration Manager als u proxy-instellingen wilt weergeven of bijwerken.

1. Configuratiebeheer **voor gegevensbeheergateway starten**.
2. Ga naar het tabblad **Instellingen.**
3. Klik **op Koppeling wijzigen** in de sectie **HTTP-proxy** om het dialoogvenster **HTTP-proxy instellen** te starten.
4. Nadat u op de knop **Volgende** hebt geklikt, ziet u een waarschuwingsdialoogvenster waarin u om uw toestemming vraagt om de proxy-instelling op te slaan en de Gateway Host Service opnieuw te starten.

U HTTP-proxy bekijken en bijwerken met het hulpprogramma Configuratiebeheer.

![Proxy instellen met config-manager](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Als u een proxyserver met NTLM-verificatie instelt, wordt Gateway Host Service uitgevoerd onder het domeinaccount. Als u het wachtwoord voor het domeinaccount later wijzigt, moet u de configuratie-instellingen voor de service bijwerken en deze opnieuw starten. Vanwege deze vereiste raden we u aan een speciaal domeinaccount te gebruiken om toegang te krijgen tot de proxyserver waarvoor u het wachtwoord niet vaak hoeft bij te werken.
>
>

### <a name="configure-proxy-server-settings"></a>Proxyserverinstellingen configureren
Als u **systeemproxy-instelling gebruiken** selecteert voor de HTTP-proxy, gebruikt gateway de proxy-instelling in diahost.exe.config en diawp.exe.config. Als er geen proxy is opgegeven in diahost.exe.config en diawp.exe.config, maakt gateway rechtstreeks verbinding met cloudservice zonder door proxy te gaan. De volgende procedure bevat instructies voor het bijwerken van het bestand diahost.exe.config.

1. Maak in Verkenner een veilige kopie van *C:\\\\Program\\Files\\\\Microsoft Data Management Gateway\\2.0 Shared diahost.exe.config* om een back-up van het oorspronkelijke bestand te maken.
2. Start Notepad.exe als administrator en open tekstbestand *C:\\\\\\Programmabestanden\\Microsoft\\Data Management Gateway\\2.0 Gedeelde diahost.exe.config*. U vindt de standaardtag voor system.net zoals weergegeven in de volgende code:

    ```
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    U vervolgens proxyservergegevens toevoegen zoals in het volgende voorbeeld wordt weergegeven:

    ```
    <system.net>
        <defaultProxy enabled="true">
            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Extra eigenschappen zijn toegestaan in de proxytag om de vereiste instellingen zoals scriptLocatie op te geven. Raadpleeg [proxy-element (netwerkinstellingen)](https://msdn.microsoft.com/library/sa91de1e.aspx) op syntaxis.

    ```
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Sla het configuratiebestand op de oorspronkelijke locatie op en start vervolgens de service Data Management Gateway Host op, die de wijzigingen oppikt. Als u de service opnieuw wilt starten: gebruik services applet vanuit het configuratiescherm of via het **Configuratiebeheer voor gegevensbeheergateway** > op de knop **Service stoppen** klikt en vervolgens op de **service start**. Als de service niet wordt gestart, is het waarschijnlijk dat een onjuiste XML-tagsyntaxis is toegevoegd aan het configuratiebestand van de toepassing dat is bewerkt.

> [!IMPORTANT]
> Vergeet niet **om zowel** diahost.exe.config als diawp.exe.config bij te werken.

Naast deze punten moet u er ook voor zorgen dat Microsoft Azure op de witte lijst van uw bedrijf staat. De lijst met geldige Microsoft Azure IP-adressen kan worden gedownload van het [Microsoft Download Center.](https://www.microsoft.com/download/details.aspx?id=41653)

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Mogelijke symptomen voor firewall- en proxyservergerelateerde problemen
Als u fouten tegenkomt die vergelijkbaar zijn met de volgende fouten, is dit waarschijnlijk te wijten aan een onjuiste configuratie van de firewall of proxyserver, waardoor de gateway wordt blokkeert om verbinding te maken met Gegevensfabriek om zichzelf te verifiëren. Raadpleeg de vorige sectie om ervoor te zorgen dat uw firewall en proxyserver correct zijn geconfigureerd.

1. Wanneer u de gateway probeert te registreren, ontvangt u de volgende fout: "Kan de gatewaysleutel niet registreren. Voordat u de gatewaysleutel opnieuw probeert te registreren, moet u controleren of de gegevensbeheergateway zich in een verbonden status bevindt en dat de Data Management Gateway Host Service wordt gestart."
2. Wanneer u Configuratiebeheer opent, ziet u de status als 'Verbroken' of 'Verbinding maken'. Bij het bekijken van Windows-gebeurtenislogboeken, onder 'Logboeken' > 'Logboeken van toepassingen en services' > 'Data Management Gateway', ziet u foutmeldingen zoals de volgende fout:`Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Poort 8050 openen voor referentieversleuteling
De toepassing **Referenties instellen** gebruikt de binnenkomende poort **8050** om referenties door te geven aan de gateway wanneer u een on-premises gekoppelde service in de Azure-portal instelt. Tijdens het instellen van de gateway wordt de gateway-installatie standaard geopend op de gatewaymachine.

Als u een firewall van derden gebruikt, u de poort 8050 handmatig openen. Als u tijdens de gateway-instelling een firewallprobleem tegenkomt, u proberen de volgende opdracht te gebruiken om de gateway te installeren zonder de firewall te configureren.

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Als u ervoor kiest de poort 8050 op de gatewaymachine niet te openen, gebruikt u andere mechanismen dan de toepassing **Referenties instellen** om referenties voor gegevensopslag te configureren. U bijvoorbeeld de cmdlet [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) PowerShell gebruiken. Zie De sectie Referenties en Beveiliging instellen over de manier waarop gegevensarchiefreferenties kunnen worden ingesteld.

## <a name="update"></a>Update
Standaard wordt de gegevensbeheergateway automatisch bijgewerkt wanneer er een nieuwere versie van de gateway beschikbaar is. De gateway wordt pas bijgewerkt als alle geplande taken zijn uitgevoerd. Er worden geen verdere taken verwerkt door de gateway totdat de updatebewerking is voltooid. Als de update mislukt, wordt de gateway teruggerold naar de oude versie.

U ziet de geplande updatetijd op de volgende plaatsen:

* De pagina gateway-eigenschappen in de Azure-portal.
* Startpagina van de Data Management Gateway Configuration Manager
* Systeemlademeldingbericht.

Op het tabblad Start van het Configuratiebeheer voor gegevensbeheergateway wordt het updateschema weergegeven en de laatste keer dat de gateway is geïnstalleerd/bijgewerkt.

![Updates plannen](media/data-factory-data-management-gateway/UpdateSection.png)

U de update meteen installeren of wachten tot de gateway automatisch wordt bijgewerkt op het geplande tijdstip. In de volgende afbeelding ziet u bijvoorbeeld het meldingsbericht dat wordt weergegeven in gatewayconfiguratiebeheer, samen met de knop Bijwerken waarop u klikken om het onmiddellijk te installeren.

![Bijwerken in DMG-configuratiebeheer](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

Het meldingsbericht in de systeemlade ziet er zoals weergegeven in de volgende afbeelding:

![Systeemladebericht](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

U ziet de status van de updatebewerking (handmatig of automatisch) in de systeemlade. Wanneer u Gateway Configuration Manager de volgende keer start, ziet u een bericht op de meldingsbalk dat de gateway is bijgewerkt samen met een koppeling naar [het nieuwe onderwerp.](data-factory-gateway-release-notes.md)

### <a name="to-disableenable-auto-update-feature"></a>Functie automatische update uitschakelen/inschakelen
U de functie voor automatisch bijwerken uitschakelen/inschakelen door de volgende stappen uit te voeren:

[Voor één knooppuntgateway]
1. Start Windows PowerShell op de gatewaymachine.
2. Overschakelen naar de map *\\\\C:\\Program\\Files\\Microsoft Integration Runtime 3.0 PowerShellScript.\\ *
3. Voer de volgende opdracht uit om de functie Automatisch bijwerken uit te schakelen (uitschakelen).

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off
    ```
4. Om het weer in te schakelen:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on
    ```
   [Voor multi-node zeer beschikbare en schaalbare gateway](data-factory-data-management-gateway-high-availability-scalability.md)
1. Start Windows PowerShell op de gatewaymachine.
2. Overschakelen naar de map *\\\\C:\\Program\\Files\\Microsoft Integration Runtime 3.0 PowerShellScript.\\ *
3. Voer de volgende opdracht uit om de functie Automatisch bijwerken uit te schakelen (uitschakelen).

    Voor gateway met hoge beschikbaarheid functie, een extra AuthKey param is vereist.
    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off -AuthKey <your auth key>
    ```
4. Om het weer in te schakelen:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on -AuthKey <your auth key>
    ```

## <a name="configuration-manager"></a>Configuratiebeheer
Zodra u de gateway hebt geïnstalleerd, u Data Management Gateway Configuration Manager op een van de volgende manieren starten:

1. Typ **Gegevensbeheergateway** **in het venster Zoeken** om toegang te krijgen tot dit hulpprogramma.
2. Voer de uitvoerbare *ConfigManager.exe* uit in de map: *C:\\\\Program Files\\Microsoft Data Management Gateway\\2.0\\Shared*.

### <a name="home-page"></a>Startpagina
Op de startpagina u de volgende acties uitvoeren:

* De status van de gateway weergeven (verbonden met de cloudservice, enz.).
* **Registreer** met behulp van een sleutel van de portal.
* **Stop** en start de **Data Management Gateway Host-service** op de gatewaymachine.
* **Plan updates** op een bepaald tijdstip van de dagen.
* Bekijk de datum waarop de gateway voor het laatst is **bijgewerkt.**

### <a name="settings-page"></a>De pagina Instellingen
Op de pagina Instellingen u de volgende acties uitvoeren:

* Weergave-, wijzigings- en **exportcertificaat** dat door de gateway wordt gebruikt. Dit certificaat wordt gebruikt om gegevensbronreferenties te versleutelen.
* Wijzig **DE HTTPS-poort** voor het eindpunt. De gateway opent een poort voor het instellen van de gegevensbronreferenties.
* **Status** van het eindpunt
* Het **SSL-certificaat weergeven** wordt gebruikt voor TLS/SSL-communicatie tussen portal en de gateway om referenties voor gegevensbronnen in te stellen.

### <a name="remote-access-from-intranet"></a>Externe toegang vanaf intranet
Deze functionaliteit zal in de toekomst worden ingeschakeld. In de komende updates (v3.4 of hoger) laten we u alle externe connectiviteit inschakelen of uitschakelen die vandaag plaatsvindt met poort 8050 (zie sectie hierboven) terwijl u PowerShell- of Credential Manager-toepassing gebruikt voor het versleutelen van referenties.

### <a name="diagnostics-page"></a>Pagina Diagnostische gegevens
Op de pagina Diagnostische gegevens u de volgende acties uitvoeren:

* Schakel **verboselogging**in, bekijk logboeken in gebeurtenisviewer en stuur logboeken naar Microsoft als er een storing is.
* **Test verbinding met** een gegevensbron.

### <a name="help-page"></a>Help-pagina
Op de Help-pagina worden de volgende gegevens weergegeven:

* Korte beschrijving van de gateway
* Versienummer
* Links naar online help, privacyverklaring en licentieovereenkomst.

## <a name="monitor-gateway-in-the-portal"></a>Gateway in de portal bewaken
In de Azure-portal u bijna realtime momentopnamevan resourcegebruik (CPU, geheugen, netwerk(in/uit), enz.) bekijken op een gatewaymachine.

1. Navigeer in Azure-portal naar de startpagina voor uw gegevensfabriek en klik op De tegel **Gekoppelde services.**

    ![Startpagina van de gegevensfactory](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png)
2. Selecteer de **gateway** op de pagina **Gekoppelde services.**

    ![Pagina Gekoppelde services](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. Op de **gatewaypagina** ziet u het geheugen en het CPU-gebruik van de gateway.

    ![CPU- en geheugengebruik van gateway](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png)
4. Geavanceerde **instellingen** inschakelen om meer details te bekijken, zoals het netwerkgebruik.
    
    ![Geavanceerde bewaking van gateway](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

In de volgende tabel vindt u beschrijvingen van kolommen in de lijst **Gatewayknooppunten:**

Controle eigenschap | Beschrijving
:------------------ | :----------
Name | Naam van de logische gateway en knooppunten die zijn gekoppeld aan de gateway. Node is een on-premises Windows-machine waarop de gateway is geïnstalleerd. Zie [Data Management Gateway - hoge beschikbaarheid en schaalbaarheid](data-factory-data-management-gateway-high-availability-scalability.md)voor informatie over het hebben van meer dan één knooppunt (maximaal vier knooppunten) in één logische gateway.
Status | Status van de logische gateway en de gatewayknooppunten. Voorbeeld: Online/Offline/Limited/etc. Zie sectie [Gatewaystatus](#gateway-status) voor informatie over deze statussen.
Versie | Toont de versie van de logische gateway en elk gatewayknooppunt. De versie van de logische gateway wordt bepaald op basis van de versie van de meerderheid van de knooppunten in de groep. Als er knooppunten met verschillende versies in de logische gateway-instelling zijn, zijn alleen de knooppunten met hetzelfde versienummer als de logische gatewayfunctie naar behoren. Anderen bevinden zich in de beperkte modus en moeten handmatig worden bijgewerkt (alleen voor het geval de automatische update mislukt).
Beschikbaar geheugen | Beschikbaar geheugen op een gatewayknooppunt. Deze waarde is een bijna realtime momentopname.
CPU-gebruik | CPU-gebruik van een gatewayknooppunt. Deze waarde is een bijna realtime momentopname.
Netwerken (In/Uit) | Netwerkgebruik van een gatewayknooppunt. Deze waarde is een bijna realtime momentopname.
Gelijktijdige taken (lopend/limiet) | Aantal taken of taken die op elk knooppunt worden uitgevoerd. Deze waarde is een bijna realtime momentopname. Limit betekent de maximale gelijktijdige taken voor elk knooppunt. Deze waarde wordt gedefinieerd op basis van de grootte van de machine. U de limiet verhogen om gelijktijdige taakuitvoering op te schalen in geavanceerde scenario's, waarbij CPU/geheugen/netwerk onderbenut is, maar activiteiten worden uitgevoerd. Deze mogelijkheid is ook beschikbaar met één node gateway (zelfs als de schaalbaarheids- en beschikbaarheidsfunctie niet is ingeschakeld).
Rol | Er zijn twee soorten rollen in een gateway met meerdere nodes - Dispatcher en werknemer. Alle knooppunten zijn werknemers, wat betekent dat ze allemaal kunnen worden gebruikt om taken uit te voeren. Er is slechts één dispatcherknooppunt, dat wordt gebruikt om taken/taken uit cloudservices te halen en deze naar verschillende werknemersknooppunten (inclusief zichzelf) te verzenden.

Op deze pagina ziet u enkele instellingen die logischer zijn wanneer er twee of meer knooppunten (uitschalenscenario) in de gateway staan. Zie [Data Management Gateway - hoge beschikbaarheid en schaalbaarheid](data-factory-data-management-gateway-high-availability-scalability.md) voor meer informatie over het instellen van een multi-node gateway.

### <a name="gateway-status"></a>Gatewaystatus
De volgende tabel biedt mogelijke statussen van een **gatewayknooppunt:**

Status  | Opmerkingen/scenario's
:------- | :------------------
Online | Knooppunt verbonden met de Data Factory-service.
Offline | Knooppunt is offline.
Upgraden | Het knooppunt wordt automatisch bijgewerkt.
Beperkt | Vanwege het probleem met de connectiviteit. Mogelijk als gevolg van het probleem met de HTTP-poort 8050, het probleem met de verbindingsverbinding van de servicebus of het synchronisatieprobleem van de referentie.
Niet-actief | Node is in een andere configuratie dan de configuratie van andere meerderheidsknooppunten.<br/><br/> Een knooppunt kan inactief zijn wanneer het geen verbinding kan maken met andere knooppunten.

De volgende tabel biedt mogelijke statussen van een **logische gateway.** De status van de gateway is afhankelijk van de status van de gatewayknooppunten.

Status | Opmerkingen
:----- | :-------
Registratie nodig | Er is nog geen knooppunt geregistreerd op deze logische gateway
Online | Gatewayknooppunten zijn online
Offline | Geen knooppunt in online status.
Beperkt | Niet alle knooppunten in deze gateway zijn in gezonde staat. Deze status is een waarschuwing dat een knooppunt mogelijk naar beneden is! <br/><br/>Kan te wijten zijn aan de referentie synchronisatie probleem op dispatcher / worker node.

## <a name="scale-up-gateway"></a>Gateway opschalen
U het aantal **gelijktijdige gegevensverplaatsingstaken** configureren dat op een knooppunt kan worden uitgevoerd om de mogelijkheid op te schalen om gegevens te verplaatsen tussen on-premises en cloudgegevensopslag.

Wanneer het beschikbare geheugen en de CPU niet goed worden gebruikt, maar de niet-actieve capaciteit 0 is, moet u opschalen door het aantal gelijktijdige taken te verhogen dat op een knooppunt kan worden uitgevoerd. U ook opschalen wanneer activiteiten worden getimed omdat de gateway overbelast is. In de geavanceerde instellingen van een gatewayknooppunt u de maximale capaciteit voor een knooppunt verhogen.

## <a name="troubleshooting-gateway-issues"></a>Problemen met gatewayproblemen oplossen
Zie [Artikel Probleemoplossing voor gatewayproblemen](data-factory-troubleshoot-gateway-issues.md) artikel voor informatie/tips voor het oplossen van problemen met het gebruik van de gateway voor gegevensbeheer.

## <a name="move-gateway-from-one-machine-to-another"></a>Gateway van de ene machine naar de andere verplaatsen
In deze sectie vindt u stappen voor het verplaatsen van gatewayclient van de ene machine naar de andere machine.

1. Navigeer in de portal naar de **startpagina van Data Factory**en klik op de tegel **Gekoppelde services.**

    ![Koppeling gegevensgateways](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Selecteer uw gateway in het gedeelte **DATA GATEWAYS** van de pagina **Linked Services.**

    ![Linked Services-pagina met gateway geselecteerd](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. Klik op de pagina **Gegevensgateway** op **Gegevensgateway downloaden en installeren.**

    ![Gatewaykoppeling downloaden](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. Klik **op** de pagina Configureren op **Gegevensgateway downloaden en installeren**en volg instructies om de gegevensgateway op de machine te installeren.

    ![Pagina configureren](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Houd het **Configuratiebeheer van microsoft Data Management Gateway** open.

    ![Configuratiebeheer](./media/data-factory-data-management-gateway/ConfigurationManager.png)
6. Klik **op** de pagina Configureren in de portal op **De toets opnieuw maken** op de opdrachtbalk en klik op **Ja** voor het waarschuwingsbericht. Klik **op de knop Kopiëren** naast de sleuteltekst die de sleutel kopieert naar het klembord. De gateway op de oude machine werkt niet meer zodra u de sleutel opnieuw maakt.

    ![Toets opnieuw maken](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Plak de **sleutel** in tekstvak op de pagina **Register Gateway** van de Data Management Gateway **Configuration Manager** op uw machine. (facultatief) Klik op **Het selectievakje Gateway-toets weergeven** om de sleuteltekst weer te geven.

    ![Sleutel kopiëren en registreren](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Klik **op Registreren** om de gateway te registreren bij de cloudservice.
9. Klik op het tabblad **Instellingen** op **Wijzigen** om hetzelfde certificaat te selecteren dat met de oude gateway is gebruikt, voer het **wachtwoord**in en klik op **Voltooien**.

   ![Certificaat opgeven](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   U een certificaat exporteren vanuit de oude gateway door de volgende stappen uit te voeren: start Data Management Gateway Configuration Manager op de oude machine, ga over naar het tabblad **Certificaat,** klik op De knop **Exporteren** en volg de instructies.
10. Na een succesvolle registratie van de gateway ziet u de **registratie** ingesteld op **Geregistreerd** en **Status** ingesteld op **Gestart** op de startpagina van gatewayconfiguratiebeheer.

## <a name="encrypting-credentials"></a>Referenties versleutelen
Ga als volgt te werk om referenties in de Data Factory Editor te versleutelen:

1. Start de webbrowser op de **gatewaymachine,** navigeer naar [Azure-portal.](https://portal.azure.com) Zoek indien nodig naar uw gegevensfabriek, open de gegevensfabriek op de pagina **DATA FACTORY** en klik vervolgens op Auteur **& implementeren** om de Data Factory Editor te starten.
2. Klik op een bestaande **gekoppelde service** in de structuurweergave om de JSON-definitie te bekijken of maak een gekoppelde service waarvoor een datamanagementgateway vereist is (bijvoorbeeld SQL Server of Oracle).
3. Voer in de JSON-editor voor de eigenschap **gatewayName** de naam van de gateway in.
4. Voer de servernaam in voor de eigenschap **Gegevensbron** in de **verbindingString**.
5. Voer de naam van de database in voor de eigenschap **Initiële catalogus** in de **verbindingstekenreeks**.
6. Klik **op de** knop Versleutelen op de opdrachtbalk waarmee de toepassing Click-Once Credential **Manager** wordt gestart. U moet het dialoogvenster **Referenties instellen** weergeven.

    ![Dialoogvenster Referenties instellen](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. Ga in het dialoogvenster **Referenties instellen** de volgende stappen uit:
   1. Selecteer **verificatie** die u wilt dat de Service Data Factory gebruikt om verbinding te maken met de database.
   2. Voer de naam in van de gebruiker die toegang heeft tot de database voor de **gebruikersnaaminstelling.**
   3. Voer het wachtwoord voor de gebruiker in voor de **instelling WACHTWOORD.**
   4. Klik op **OK** om referenties te versleutelen en het dialoogvenster te sluiten.
8. U moet nu een **eigenschap versleuteldReferentie zien** in de **connectionString.**

    ```JSON
    {
        "name": "SqlServerLinkedService",
        "properties": {
            "type": "OnPremisesSqlServer",
            "description": "",
            "typeProperties": {
                "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
                "gatewayName": "adftutorialgateway"
            }
        }
    }
    ```
   Als u de portal opent vanaf een machine die verschilt van de gatewaymachine, moet u ervoor zorgen dat de toepassing Credentials Manager verbinding kan maken met de gatewaymachine. Als de toepassing de gatewaymachine niet kan bereiken, u geen referenties instellen voor de gegevensbron en de verbinding met de gegevensbron testen.

Wanneer u de toepassing **Referenties instellen** gebruikt, versleutelt de portal de referenties met het certificaat dat is opgegeven op het tabblad **Certificaat** van **gatewayconfiguratiebeheer** op de gatewaymachine.

Als u op zoek bent naar een API-gebaseerde aanpak voor het versleutelen van de referenties, u de [nieuwe-AzDataFactoryValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) PowerShell-cmdlet gebruiken om referenties te versleutelen. De cmdlet gebruikt het certificaat dat gateway is geconfigureerd om te gebruiken om de referenties te versleutelen. U voegt versleutelde referenties toe aan het **element EncryptedCredential** van de **connectionString** in de JSON. U gebruikt de JSON met de [cmdlet New-AzDataFactoryLinkedService](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactorylinkedservice) of in de Data Factory Editor.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Er is nog een aanpak voor het instellen van referenties met behulp van Data Factory Editor. Als u een SQL Server-gekoppelde service maakt met behulp van de editor en u referenties in platte tekst invoert, worden de referenties versleuteld met behulp van een certificaat waarvan de Data Factory-service eigenaar is. Het maakt geen gebruik van het certificaat dat gateway is geconfigureerd om te gebruiken. Hoewel deze aanpak in sommige gevallen misschien iets sneller is, is het minder veilig. Daarom raden we u aan deze aanpak alleen te volgen voor ontwikkelings-/testdoeleinden.

## <a name="powershell-cmdlets"></a>PowerShell-cmdlets
In deze sectie wordt beschreven hoe u een gateway maakt en registreert met Azure PowerShell-cmdlets.

1. Start **Azure PowerShell** in de beheerdersmodus.
2. Meld u aan bij uw Azure-account door de volgende opdracht uit te voeren en uw Azure-referenties in te voeren.

    ```powershell
    Connect-AzAccount
    ```
3. Gebruik de cmdlet **New-AzDataFactoryGateway** om een logische gateway te maken:

    ```powershell
    $MyDMG = New-AzDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Voorbeeldopdracht en uitvoer:**

    ```
    PS C:\> $MyDMG = New-AzDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description "gateway for walkthrough"

    Name              : MyGateway
    Description       : gateway for walkthrough
    Version           :
    Status            : NeedRegistration
    VersionStatus     : None
    CreateTime        : 9/28/2014 10:58:22
    RegisterTime      :
    LastConnectTime   :
    ExpiryTime        :
    ProvisioningState : Succeeded
    Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI
    ```

1. Schakel in Azure PowerShell over naar de map: *\\\\C:\\Program Files\\\\Microsoft Integration Runtime\\3.0 PowerShellScript*. Voer *RegisterGateway.ps1* uit die is gekoppeld aan de lokale variabele **$Key** zoals weergegeven in de volgende opdracht. Dit script registreert de clientagent die op uw machine is geïnstalleerd met de logische gateway die u eerder hebt gemaakt.

    ```powershell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    U de gateway op een externe machine registreren met behulp van de parameter IsRegisterOnRemoteMachine. Voorbeeld:

    ```powershell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. U de **cmdlet Get-AzDataFactoryGateway** gebruiken om de lijst met gateways in uw gegevensfabriek te krijgen. Wanneer de **status** **online**wordt weergegeven, betekent dit dat uw gateway klaar is voor gebruik.

    ```powershell        
    Get-AzDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
   U een gateway verwijderen met de **cmdlet Remove-AzDataFactoryGateway** en de beschrijving bijwerken voor een gateway met behulp van de cmdlets **Set-AzDataFactoryGateway.** Zie Gegevensfabriek Cmdlet Naslaginformatie voor syntaxis en andere details over deze cmdlets.  

### <a name="list-gateways-using-powershell"></a>Gateways weergeven met PowerShell

```powershell
Get-AzDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Gateway verwijderen met PowerShell

```powershell
Remove-AzDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```

## <a name="next-steps"></a>Volgende stappen
* Zie Gegevens verplaatsen tussen het artikel [over on-premises en cloudgegevensstores.](data-factory-move-data-between-onprem-and-cloud.md) In de walkthrough maakt u een pijplijn die de gateway gebruikt om gegevens van een on-premises SQL Server-database naar een Azure-blob te verplaatsen.
