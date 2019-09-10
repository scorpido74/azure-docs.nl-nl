---
title: On-premises gegevens gateway installeren-Azure Logic Apps
description: U kunt de on-premises gegevens gateway downloaden en installeren voordat u toegang hebt tot gegevens on-Azure Logic Apps premises.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: 657bc704e33e89b1646dffa6123a27169e6c317a
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860807"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>On-premises gegevens gateway voor Azure Logic Apps installeren

Voordat u verbinding kunt maken met on-premises gegevens bronnen vanuit Azure Logic Apps, downloadt en installeert u de on-premises gegevens gateway op een lokale computer. De gateway werkt als een brug die snelle gegevens overdracht en versleuteling biedt tussen gegevens bronnen op locatie (niet in de Cloud) en uw Logic apps. U kunt dezelfde Gateway-installatie gebruiken met andere Cloud Services, zoals Power BI, Microsoft Flow, PowerApps en Azure Analysis Services. Zie de volgende artikelen voor meer informatie over het gebruik van de gateway met deze services:

* [On-premises gegevens gateway van micro soft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft PowerApps on-premises gegevens gateway](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Microsoft Flow on-premises gegevens gateway](https://flow.microsoft.com/documentation/gateway-manage/)
* [Azure Analysis Services on-premises gegevens gateway](../analysis-services/analysis-services-gateway.md)

In dit artikel wordt beschreven hoe u uw on-premises gegevens gateway kunt downloaden, installeren en instellen, zodat u toegang hebt tot on-premises gegevens bronnen van Azure Logic Apps. U kunt ook meer te weten komen over [de manier waarop de gegevens gateway](#gateway-cloud-service) verderop in dit onderwerp werkt.

<a name="supported-connections"></a>

De gateway ondersteunt [on-premises connectors](../connectors/apis-list.md#on-premises-connectors) in azure Logic apps voor deze gegevens bronnen:

* BizTalk Server 2016
* Bestandssysteem
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Hoewel de gateway alleen extra kosten in rekening brengt, is het [Logic apps prijs model](../logic-apps/logic-apps-pricing.md) van toepassing op deze connectors en andere bewerkingen in azure Logic apps.

<a name="requirements"></a>

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

  * U moet hetzelfde Azure-account gebruiken om de gateway te installeren en te beheren. Tijdens de installatie gebruikt u dit Azure-account om de gateway op uw computer te koppelen aan een Azure-abonnement. Later gebruikt u hetzelfde Azure-account wanneer u een Azure-resource maakt in de Azure Portal voor uw gateway-installatie. 

  * U moet zich aanmelden met een werk account of school account, ook wel bekend als een *organisatie* account, die er als `username@contoso.com`volgt uitziet. U kunt geen Azure B2B-accounts of persoonlijke micro soft-accounts gebruiken, zoals @hotmail.com of @outlook.com.

    > [!TIP]
    > Als u zich hebt geregistreerd voor een Office 365-aanbieding en uw zakelijke e-mail adres niet hebt verstrekt, `username@domain.onmicrosoft.com`kan uw adres er als volgt uitzien. Uw account wordt opgeslagen in een Tenant in een Azure Active Directory (Azure AD). In de meeste gevallen is de UPN (User Principal Name) voor uw Azure AD-account hetzelfde als uw e-mail adres.
    >
    > Als u een [Visual Studio Standard-abonnement](https://visualstudio.microsoft.com/vs/pricing/) wilt gebruiken dat is gekoppeld aan een Microsoft-account, maakt u eerst [een TENANT in azure AD](../active-directory/develop/quickstart-create-new-tenant.md)of gebruikt u de standaard directory. Voeg een gebruiker met een wacht woord toe aan de map en geef die gebruiker vervolgens toegang tot uw abonnement. 
    > U kunt zich vervolgens aanmelden tijdens de installatie van de gateway met deze gebruikers naam en dit wacht woord.

* Hier vindt u de vereisten voor uw lokale computer:

  **Minimale vereisten**

  * .NET Framework 4.6
  * 64-bits versie van Windows 7 of Windows Server 2008 R2 (of hoger)

  **Aanbevolen vereisten**

  * 8-core CPU
  * 8 GB geheugen
  * 64-bits versie van Windows Server 2012 R2 of hoger
  * SSD-opslag (Solid-State Drive) voor spooling

  > [!NOTE]
  > De gateway biedt geen ondersteuning voor Windows Server 2016 core.

* **Verwante overwegingen**

  * U kunt de on-premises gegevens gateway alleen installeren op een lokale computer, niet op een domein controller. U hoeft de gateway echter niet te installeren op dezelfde computer als uw gegevens bron. U hebt slechts één gateway nodig voor al uw gegevens bronnen, dus u hoeft de gateway niet voor elke gegevens bron te installeren.

    > [!TIP]
    > Als u de latentie wilt minimaliseren, kunt u de gateway zo dicht mogelijk bij de gegevens bron of op dezelfde computer installeren, ervan uitgaande dat u over de juiste machtigingen beschikt.

  * Installeer de gateway op een computer die zich op een bekabeld netwerk bevindt, verbonden met internet, altijd ingeschakeld en niet naar de slaap stand. Anders kan de gateway niet worden uitgevoerd en is het mogelijk dat de prestaties van een draadloos netwerk afnemen.

  * Als u van plan bent om Windows-verificatie te gebruiken, moet u ervoor zorgen dat u de gateway installeert op een computer die lid is van dezelfde Active Directory omgeving als uw gegevens bronnen.

  * De regio die u voor de gateway-installatie selecteert, is dezelfde locatie die u moet selecteren wanneer u later de Azure-gateway resource voor uw logische app maakt. Deze regio is standaard dezelfde locatie als uw Azure AD-Tenant voor het beheren van uw Azure-account. U kunt de locatie echter wijzigen tijdens de installatie van de gateway.

  * De gateway heeft twee modi: standaard modus en persoonlijke modus, die alleen van toepassing is op Power BI. U kunt niet meer dan één gateway in dezelfde modus op dezelfde computer uitvoeren.

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Een gegevensgateway installeren

1. [Down load en voer het installatie programma van de gateway op een lokale computer uit](https://aka.ms/on-premises-data-gateway-installer).

1. Wanneer het installatie programma wordt geopend, selecteert u **volgende**.

   ![Introductie van het installatie programma](./media/logic-apps-gateway-install/gateway-intro-screen.png)

1. Selecteer **on-premises gegevens gateway (aanbevolen)** , de standaard modus, en selecteer vervolgens **volgende**.

   ![Gateway modus selecteren](./media/logic-apps-gateway-install/select-gateway-mode.png)

1. Bekijk de minimale vereisten, behoud de standaard installatie, accepteer de gebruiksrecht overeenkomst en selecteer vervolgens **installeren**.

   ![Bekijk de vereisten en accepteer de gebruiks voorwaarden](./media/logic-apps-gateway-install/accept-terms.png)

1. Nadat de gateway is geïnstalleerd, geeft u het e-mail adres voor uw organisatie account op en selecteert u **Aanmelden**, bijvoorbeeld:

   ![Aanmelden met een werk-of school account](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   U bent nu aangemeld bij uw account.

1. Selecteer **een nieuwe gateway registreren op deze computer** > **volgende**. Met deze stap wordt de gateway-installatie geregistreerd bij de [Gateway-Cloud service](#gateway-cloud-service).

   ![Gateway registreren](./media/logic-apps-gateway-install/register-gateway.png)

1. Geef deze informatie op voor de gateway-installatie:

   * Een gateway naam die uniek is binnen uw Azure AD-Tenant
   * De herstel sleutel, die ten minste acht tekens moet bevatten die u wilt gebruiken
   * Bevestiging van uw herstel sleutel

   ![Gateway instellen](./media/logic-apps-gateway-install/set-up-gateway.png)

   > [!IMPORTANT]
   > Bewaar uw herstel sleutel op een veilige plaats. U hebt deze sleutel nodig als u ooit de locatie wilt wijzigen, verplaatsen, herstellen of overnemen van een gateway-installatie.

   Let op de optie om toe **te voegen aan een bestaand gateway cluster**, dat u selecteert wanneer u extra gateways voor [scenario's met hoge Beschik baarheid](#high-availability)installeert.

1. Controleer de regio voor de gateway-Cloud service en [Azure service bus](https://azure.microsoft.com/services/service-bus/) die wordt gebruikt door de installatie van de gateway. Standaard is deze regio dezelfde locatie als de Azure AD-Tenant voor uw Azure-account.

   ![Controle regio](./media/logic-apps-gateway-install/check-region.png)

1. Selecteer **configureren**om de standaard regio te accepteren. Als de standaard regio echter niet het meest overeenkomt met u, kunt u de regio wijzigen.

   *Waarom wijzigt u de regio voor de installatie van de gateway?*

   Als u bijvoorbeeld de latentie wilt beperken, kunt u de regio van de gateway wijzigen in dezelfde regio als uw logische app. U kunt ook de regio selecteren die het dichtst bij uw on-premises gegevens bron ligt. Uw *Gateway bron in azure* en uw logische app kunnen verschillende locaties hebben.

   1. Selecteer **regio wijzigen**naast de huidige regio.

      ![Regio wijzigen](./media/logic-apps-gateway-install/change-region.png)

   1. Open de lijst **regio selecteren** op de volgende pagina, selecteer de gewenste regio en selecteer **gereed**.

      ![Selecteer een andere regio](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Bekijk de informatie in het laatste bevestigings venster. In dit voor beeld wordt hetzelfde account gebruikt voor Logic Apps, Power BI, PowerApps en Microsoft Flow, zodat de gateway beschikbaar is voor al deze services. Wanneer u klaar bent, selecteert u **sluiten**.

   ![Voltooide gateway](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. [Maak nu de Azure-resource voor de gateway-installatie](../logic-apps/logic-apps-gateway-connection.md).

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Ondersteuning voor hoge Beschik baarheid

Als u storingen wilt voor komen op individuele punten van uitval voor on-premises gegevens toegang, kunt u meerdere gateway-installaties (alleen de standaard modus) hebben met elk op een andere computer en deze instellen als een cluster of groep. Op die manier, als de primaire gateway niet beschikbaar is, worden gegevens aanvragen doorgestuurd naar de tweede gateway, enzovoort. Omdat u slechts één standaard gateway op een computer kunt installeren, moet u elke extra gateway in het cluster op een andere computer installeren. Alle connectors die samen werken met de on-premises gegevens gateway ondersteunen hoge Beschik baarheid. 

* U moet al ten minste één gateway installatie hebben binnen hetzelfde Azure-abonnement als de primaire gateway en de herstel sleutel voor die installatie.

* Op uw primaire gateway moet de gateway-update van november 2017 of hoger worden uitgevoerd.

Wanneer u na het instellen van de primaire gateway een andere gateway installeert, selecteert u **toevoegen aan een bestaand gateway cluster**, selecteert u de primaire gateway, de eerste gateway die u hebt geïnstalleerd, en geeft u de herstel sleutel voor die gateway op. Zie [clusters met hoge Beschik baarheid voor on-premises gegevens gateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster)voor meer informatie.

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Een bestaande gateway wijzigen, migreren, herstellen of overnemen

Als u de locatie van de gateway moet wijzigen, de installatie van de gateway naar een nieuwe computer verplaatst, een beschadigde gateway herstelt of eigenaar van een bestaande gateway bent, hebt u de herstel sleutel nodig die tijdens de installatie van de gateway werd verschaft.

1. Voer het installatie programma van de gateway uit op de computer met de bestaande gateway. Als u niet beschikt over het nieuwste Gateway-installatie programma, [downloadt u de nieuwste gateway versie](https://aka.ms/on-premises-data-gateway-installer).

   > [!NOTE]
   > Voordat u de gateway op de computer met de oorspronkelijke gateway installatie herstelt, moet u eerst de gateway op die computer verwijderen. Met deze actie wordt de verbinding van de oorspronkelijke gateway verbroken.
   > Als u een gateway cluster verwijdert of verwijdert voor een Cloud service, kunt u dat cluster niet herstellen.

1. Nadat het installatie programma is geopend, meldt u zich aan met hetzelfde Azure-account dat is gebruikt om de gateway te installeren.

1.  > Selecteer **een bestaande gateway migreren, herstellen of overnemen** **, bijvoorbeeld**:

   ![Selecteer een bestaande gateway migreren, herstellen of overnemen](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Selecteer een van de beschik bare clusters en gateways en voer de herstel sleutel voor de geselecteerde gateway in, bijvoorbeeld:

   ![Gateway selecteren](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Als u de regio wilt wijzigen, selecteert u **regio wijzigen**en selecteert u de nieuwe regio.

1. Wanneer u klaar bent, selecteert u **configureren** zodat u de taak kunt volt ooien.

## <a name="configure-proxy-or-firewall"></a>Proxy of firewall configureren

Als uw werk omgeving vereist dat verkeer via een proxy wordt uitgevoerd voor toegang tot internet, kan deze beperking ertoe leiden dat de on-premises gegevens gateway geen verbinding kan maken met de gateway-Cloud service en [Azure service bus](../service-bus-messaging/service-bus-messaging-overview.md). Zie [proxy-instellingen configureren voor de on-premises gegevens gateway](https://docs.microsoft.com/power-bi/service-gateway-proxy)voor meer informatie.

Als u wilt controleren of uw proxy of firewall verbindingen kan blok keren, controleert u of de computer verbinding kan maken met internet en Azure Service Bus. Voer de volgende opdracht uit vanaf een Power shell-prompt:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> Met deze opdracht worden alleen de netwerk verbinding en de verbinding met de Azure Service Bus getest. De opdracht doet niets met de gateway of de gateway-Cloud service die uw referenties en gateway gegevens versleutelt en opslaat. 
>
> Deze opdracht is ook alleen beschikbaar op Windows Server 2012 R2 of hoger en Windows 8,1 of hoger. In eerdere versies van het besturings systeem kunt u Telnet gebruiken om de connectiviteit te testen. Meer informatie over [Azure service bus en hybride oplossingen](../service-bus-messaging/service-bus-messaging-overview.md).

De resultaten moeten er ongeveer uitzien als in dit voor beeld waarbij **TcpTestSucceeded** is ingesteld op **True**:

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Als **TcpTestSucceeded** niet is ingesteld op **True**, wordt de gateway mogelijk geblokkeerd door een firewall. Als u uitgebreid wilt zijn, vervangt u de waarden **ComputerName** en **Port** door de waarden die worden vermeld onder [poorten configureren](#configure-ports) in dit artikel.

De firewall blokkeert mogelijk ook de verbindingen die de Azure Service Bus maakt in de Azure-data centers. Als dit het geval is, moet u alle IP-adressen voor deze data centers in uw regio goed keuren (deblokkeren). Voor deze IP-adressen kunt u [hier de lijst met Azure IP-adressen ophalen](https://www.microsoft.com/download/details.aspx?id=41653).

<a name="configure-ports"></a>

## <a name="configure-ports"></a>Poorten configureren

De gateway maakt een uitgaande verbinding met Azure Service Bus en communiceert via uitgaande poorten: TCP 443 (standaard), 5671, 5672, 9350 tot en met 9354. De gateway vereist geen binnenkomende poorten. Meer informatie over [Azure service bus en hybride oplossingen](../service-bus-messaging/service-bus-messaging-overview.md).

De gateway maakt gebruik van de volgende volledig gekwalificeerde domein namen:

| Domeinnamen | Uitgaande poorten | Description |
| ------------ | -------------- | ----------- |
| *.analysis.windows.net | 443 | HTTPS |
| *.core.windows.net | 443 | HTTPS |
| *.frontend.clouddatahub.net | 443 | HTTPS |
| *.login.windows.net | 443 | HTTPS |
| *.microsoftonline-p.com | 443 | Wordt gebruikt voor verificatie, afhankelijk van de configuratie. |
| *.msftncsi.com | 443 | Wordt gebruikt om de Internet verbinding te testen wanneer de gateway onbereikbaar is voor de Power BI-service. |
| *.servicebus.windows.net | 443, 9350-9354 | Listeners op Service Bus Relay via TCP (vereist 443 voor de aanschaf van Access Control-token) |
| *.servicebus.windows.net | 5671-5672 | Advanced Message Queuing Protocol (AMQP) |
| login.microsoftonline.com | 443 | HTTPS |
||||

In sommige gevallen worden er Azure Service Bus verbindingen gemaakt met IP-adressen in plaats van volledig gekwalificeerde domein namen. Daarom wilt u de IP-adressen voor uw gegevens regio in uw firewall blok keren. Als u toegang wilt verlenen voor IP-adressen in plaats van domeinen, kunt u de [lijst met IP-adresbereiken van Microsoft Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653)downloaden en gebruiken. De IP-adressen in deze lijst bevinden zich in een [CIDR-notatie (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

### <a name="force-https-communication-with-azure-service-bus"></a>HTTPS-communicatie met Azure Service Bus forceren

Sommige proxy's laten verkeer via poort 80 en 443. Communicatie met Azure Service Bus vindt standaard plaats op andere poorten dan 443. U kunt afdwingen dat de gateway communiceert met de Azure Service Bus via HTTPS in plaats van rechtstreeks TCP, maar dit kan de prestaties aanzienlijk verminderen. Zie [https-communicatie met Azure service bus forceren](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication#force-https-communication-with-azure-service-bus)voor meer informatie.

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Windows-Service account

Standaard wordt de installatie van de gateway op uw lokale computer uitgevoerd als een Windows-Service account met de naam ' on-premises gegevens Gateway-Service '. De gateway-installatie gebruikt echter de `NT SERVICE\PBIEgwService` naam voor de account referenties aanmelden als en heeft de machtigingen aanmelden als service.

> [!NOTE]
> Uw Windows-Service account wijkt af van het account dat wordt gebruikt om verbinding te maken met on-premises gegevens bronnen en van het Azure-account dat u gebruikt wanneer u zich aanmeldt bij Cloud Services.

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Gateway opnieuw starten

De gegevens gateway wordt uitgevoerd als een venster Service, net zoals elke andere Windows-service, maar u kunt de gateway op verschillende manieren starten en stoppen. Zie [een on-premises gegevens Gateway opnieuw starten](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart)voor meer informatie.

## <a name="tenant-level-administration"></a>Beheer op Tenant niveau

Om inzicht te krijgen in alle on-premises gegevens gateways in een Azure AD-Tenant, kunnen globale beheerders in die Tenant zich aanmelden bij het [Power platform-beheer centrum](https://powerplatform.microsoft.com) als Tenant beheerder en de optie **gegevens gateways** selecteren. Zie [beheer op Tenant niveau voor de on-premises gegevens gateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)voor meer informatie.

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Hoe de gateway werkt

De gegevens gateway vereenvoudigt snelle en veilige communicatie tussen uw logische app, de gateway-Cloud service en uw on-premises gegevens bron. De gateway-Cloud service versleutelt en slaat uw referenties voor de gegevens bron en de gateway gegevens op. De service routeert ook query's en de bijbehorende resultaten tussen uw logische app, de on-premises gegevens gateway en uw gegevens bron on-premises.

De gateway werkt met firewalls en maakt gebruik van alleen uitgaande verbindingen. Al het verkeer is afkomstig van het beveiligde uitgaande verkeer van de gateway-agent. De gateway doorstuurt gegevens van on-premises bronnen op versleutelde kanalen via Azure Service Bus. Met deze service bus wordt een kanaal gemaakt tussen de gateway en de aanroepende service, maar worden er geen gegevens opgeslagen. Alle gegevens die via de gateway worden uitgewisseld, worden versleuteld.

![Architectuur voor on-premises gegevens gateway](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

In deze stappen wordt beschreven wat er gebeurt wanneer een gebruiker in de Cloud communiceert met een-element dat is verbonden met uw on-premises gegevens Bron:

1. De gateway-Cloud service maakt een query, samen met de versleutelde referenties voor de gegevens bron, en verzendt de query naar de wachtrij om de gateway te verwerken.

1. De gateway-Cloud service analyseert de query en duwt de aanvraag naar het Azure Service Bus.

1. De on-premises gegevens gateway pollt de Azure Service Bus voor in behandeling zijnde aanvragen.

1. De gateway haalt de query op, ontsleutelt de referenties en maakt verbinding met de gegevens bron met deze referenties.

1. De Gateway verzendt de query naar de gegevens bron voor uitvoering.

1. De resultaten worden vanuit de gegevens bron teruggezonden naar de gateway en vervolgens naar de gateway-Cloud service. De gateway-Cloud service gebruikt vervolgens de resultaten.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="general"></a>Algemeen

**V**: Heb ik een gateway nodig voor gegevens bronnen in de Cloud, zoals Azure SQL Database? <br/>
**A**: Nee, de gateway maakt alleen verbinding met on-premises gegevens bronnen.

**V**: Moet de gateway worden geïnstalleerd op dezelfde computer als de gegevens bron? <br/>
**A**: Nee, de gateway maakt verbinding met de gegevens bron met behulp van de verstrekte verbindings gegevens. Beschouw de gateway als een client toepassing in deze zin. De gateway heeft alleen de mogelijkheid nodig om verbinding te maken met de naam van de server die is ingesteld.

<a name="why-azure-work-school-account"></a>

**V**: Waarom moet ik een werk-of school account gebruiken om je aan te melden? <br/>
**A**: U kunt alleen een werk-of school account gebruiken wanneer u de on-premises gegevens gateway installeert. Uw aanmeldings account wordt opgeslagen in een Tenant die wordt beheerd door Azure Active Directory (Azure AD). Normaal gesp roken komt de user principal name (UPN) van uw Azure AD-account overeen met het e-mail adres.

**V**: Waar worden mijn referenties opgeslagen? <br/>
**A**: De referenties die u voor een gegevens bron invoert, worden versleuteld en opgeslagen in de gateway-Cloud service. De referenties worden ontsleuteld op de on-premises gegevens gateway.

**V**: Zijn er vereisten voor de netwerk bandbreedte? <br/>
**A**: Controleer of de netwerk verbinding een goede door Voer heeft. Elke omgeving is anders en de hoeveelheid verzonden gegevens kan van invloed zijn op de resultaten. Probeer [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)om een doorvoer niveau tussen uw on-premises gegevens bron en de Azure-data centers te garanderen. Om uw door voer te meten, probeert u een extern hulp programma zoals Azure Speed Test.

**V**: Wat is de latentie voor het uitvoeren van query's op een gegevens bron van de gateway? Wat is de beste architectuur? <br/>
**A**: Als u de netwerk latentie wilt beperken, installeert u de gateway zo dicht mogelijk bij de gegevens bron. Als u de gateway op de daad werkelijke gegevens bron kunt installeren, wordt deze nabijheid van de latentie geminimaliseerd. Houd ook rekening met de buurt van Azure-data centers. Als uw service bijvoorbeeld gebruikmaakt van het VS-West-Data Center en u SQL Server gehost in een Azure-VM, wilt u mogelijk ook uw Azure VM in de regio vs-West. Deze nabijheid beperkt latentie en voor komt dat er uitstaande kosten worden berekend op de virtuele machine van Azure.

**V**: Hoe worden resultaten teruggezonden naar de Cloud? <br/>
**A**: De resultaten worden verzonden via Azure Service Bus.

**V**: Zijn er inkomende verbindingen met de gateway vanuit de Cloud? <br/>
**A**: Nee, de gateway gebruikt uitgaande verbindingen met Azure Service Bus.

**V**: Wat gebeurt er als ik uitgaande verbindingen blok keer? Wat moet ik openen? <br/>
**A**: Bekijk de poorten en hosts die de gateway gebruikt.

**V**: Wat is de daad werkelijke Windows-service aangeroepen? <br/>
**A**: Op het tabblad Services van taak beheer is de service naam PBIEgwService of Power BI Enter prise Gateway Service. In de Services-console is de service naam on-premises gegevens Gateway Service. De Windows-service gebruikt NT SERVICE\PBIEgwService als de service-SID (SSID).

**V**: Kan de Windows-service van de gateway worden uitgevoerd met een Azure Active Directory-account? <br/>
**A**: Nee, de Windows-service moet een geldig Windows-account hebben.

### <a name="disaster-recovery"></a>Herstel na noodgeval

**V**: Welke opties zijn er beschikbaar voor herstel na nood gevallen? <br/>
**A**: U kunt de herstel sleutel gebruiken om een gateway te herstellen of te verplaatsen. Wanneer u de gateway installeert, geeft u de herstel sleutel op.

**V**: Wat is het voor deel van de herstel sleutel? <br/>
**A**: De herstel sleutel biedt een manier om uw gateway-instellingen te migreren of te herstellen na een nood geval.

## <a name="troubleshooting"></a>Problemen oplossen

In deze sectie worden enkele veelvoorkomende problemen besproken die u mogelijk hebt tijdens het instellen en gebruiken van de on-premises gegevens gateway.

**V**: Waarom is de installatie van mijn gateway mislukt? <br/>
**A**: Dit probleem kan zich voordoen als de antivirus software op de doel computer verouderd is. U kunt de antivirus software bijwerken of de antivirus software uitschakelen, maar alleen tijdens de installatie van de gateway en de software vervolgens opnieuw inschakelen.

**V**: Waarom zie ik mijn Gateway-installatie niet bij het maken van de gateway bron in azure? <br/>
**A**: Dit probleem kan de volgende oorzaken hebben:

* De installatie van de gateway is al geregistreerd en geclaimd door een andere gateway bron in Azure. Gateway-installaties worden niet weer gegeven in de lijst instanties nadat er gateway bronnen voor zijn gemaakt. Als u uw gateway registraties wilt controleren in de Azure Portal, controleert u alle Azure-resources met het type **on-premises gegevens gateways** voor *alle* Azure-abonnementen.

* De Azure AD-identiteit voor de persoon die de gateway heeft geïnstalleerd, verschilt van de persoon die zich heeft aangemeld bij de Azure Portal. Controleer of u bent aangemeld met dezelfde identiteit waarmee de gateway is geïnstalleerd.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**V**: Waar bevinden de gateway logboeken zich? <br/>
**A**: Zie de [sectie **logs** ](#logs) verderop in dit artikel.

**V**: Hoe kan ik zien welke query's worden verzonden naar de on-premises gegevens bron? <br/>
**A**: U kunt query tracering inschakelen, inclusief de query's die worden verzonden. Vergeet niet om de query-tracering terug te zetten naar de oorspronkelijke waarde wanneer u het probleem hebt gemaakt. Als u query tracering ingeschakeld laat, worden grotere logboeken gemaakt.

U kunt ook de hulpprogram ma's bekijken die uw gegevens bron heeft voor het traceren van query's. U kunt bijvoorbeeld Extended Events of SQL Profiler gebruiken voor SQL Server en Analysis Services.

### <a name="outdated-gateway-version"></a>Verouderde gateway versie

Veel problemen kunnen zich voordoen als de gateway versie verouderd wordt. Zorg ervoor dat u de meest recente versie hebt. Als u de gateway voor een maand of langer niet hebt bijgewerkt, kunt u overwegen de nieuwste versie van de gateway te installeren en te controleren of u het probleem kunt reproduceren.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Fout: Kan de gebruiker niet toevoegen aan de groep. (-2147463168 PBIEgwService prestatie logboek gebruikers)

Deze fout kan optreden als u probeert de gateway te installeren op een domein controller, wat niet wordt ondersteund. Zorg ervoor dat u de gateway implementeert op een computer die geen domein controller is.

<a name="logs"></a>

### <a name="logs"></a>Logboeken

Om u te helpen problemen op te lossen, moet u altijd eerst de gateway-logboeken verzamelen en controleren. U kunt de logboeken op verschillende manieren verzamelen, maar de eenvoudigste optie na de installatie van de gateway is via de gebruikers interface van het gateway-installatie programma.

1. Open het installatie programma van de on-premises gegevens gateway op uw computer.

1. Selecteer in het menu links de optie **Diagnostische gegevens**.

1. Onder **Gateway logboeken**selecteert u **Logboeken exporteren**.

   ![Logboeken exporteren vanuit het installatie programma van de gateway](./media/logic-apps-gateway-install/export-logs.png)

Hier vindt u andere locaties waar u verschillende logboeken kunt vinden:

| Logboektype | Location |
|----------|----------|
| **Installatie logboeken** | %localappdata%\Temp\On-premises_data_gateway_<*yyyymmdd*>.<*number*>.log |
| **Configuratie logboeken** | C:\Users\<*gebruikers naam*> \AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator <*jjjmmdd*>. <*nummer*>. log |
| **Service logboeken van de Enter prise gateway** | C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\Gateway <*jjjmmdd*>. <*nummer*>. log |
|||

**Gebeurtenis logboeken**

Voer de volgende stappen uit om de gebeurtenis logboeken voor de gateway te vinden:

1. Open de **Logboeken**op de computer met de gateway installatie.

1. Vouw **Logboeken** > -**Logboeken toepassingen en services**uit.

1. Selecteer de **on-premises gegevens Gateway Service**.

   ![Gebeurtenis logboeken voor de gateway weer geven](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="review-slow-query-performance"></a>Trage query prestaties controleren

Als u merkt dat query's langzaam worden uitgevoerd via de gateway, kunt u extra logboek registratie inschakelen waarmee query's en de duur ervan worden gestart. Met deze logboeken kunt u vinden welke query's langzaam of langdurig worden uitgevoerd. Als u de query prestaties wilt afstemmen, moet u mogelijk de gegevens bron wijzigen, bijvoorbeeld indexen aanpassen voor SQL Server query's.

Voer de volgende stappen uit om de duur van een query te bepalen:

1. Blader naar dezelfde locatie als de Gateway-client, die u hier doorgaans kunt vinden:```C:\Program Files\On-premises data gateway```

   Als u de client locatie wilt vinden, opent u de Services-console op dezelfde computer, zoekt u de **on-premises gegevens Gateway Service**en bekijkt **u het pad naar de eigenschap voor het uitvoer bare bestand** .

1. Deze configuratie bestanden openen en bewerken, zoals wordt beschreven:

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     Wijzig de waarde van **EmitQueryTraces** in dit **bestand in** **waar** , zodat de gateway query's kan registreren die vanaf de gateway worden verzonden naar een gegevens Bron:

     ```html
     <setting name="EmitQueryTraces" serializeAs="String">
        <value>true</value>
     </setting>
     ```

     > [!IMPORTANT]
     > Als u de instelling EmitQueryTraces inschakelt, kan de logboek grootte aanzienlijk toenemen op basis van het gebruik van de gateway. Nadat u klaar bent met het controleren van de logboeken, moet u EmitQueryTraces opnieuw instellen op **Onwaar** , in plaats van deze instelling op de lange termijn te laten staan.

   * **Microsoft.PowerBI.DataMovement.Pipeline.Diagnostics.dll.config**

     Als u wilt dat uw gateway logboek uitgebreide vermeldingen bevat, inclusief vermeldingen die de duur tonen, wijzigt u de **traceverbosity** -waarde van **4** in **5** door een van de volgende stappen uit te voeren:

     * Wijzig in dit configuratie bestand de **traceverbosity** -waarde van **4** in **5**

       ```html
       <setting name="TracingVerbosity" serializeAs="String">
          <value>5</value>
       </setting>
       ```

     * Open het installatie programma van de gateway, selecteer **Diagnostische gegevens**, Schakel **extra logboek registratie**in en kies vervolgens **Toep assen**:

       ![Aanvullende logboek registratie inschakelen](./media/logic-apps-gateway-install/turn-on-additional-logging.png)

     > [!IMPORTANT]
     > Als u de instelling Traceverbosity inschakelt, kan de logboek grootte aanzienlijk toenemen op basis van het gebruik van de gateway. Nadat u klaar bent met het controleren van de logboeken, moet u **extra logboek registratie** uitschakelen in het installatie programma van de gateway of traceverbosity opnieuw instellen op **4** in het configuratie bestand, in plaats van deze instelling op de lange termijn te laten staan.

1. Voer de volgende stappen uit om de duur van een query te zoeken:

   1. [Exporteer](#logs) het gateway logboek en open het.

   1. Als u een query wilt zoeken, zoekt u naar een type activiteit, bijvoorbeeld:

      | Type activiteit | Description |
      |---------------|-------------|
      | MGEQ | Query's die worden uitgevoerd via ADO.NET |
      | MGEO | Query's die worden uitgevoerd via OLEDB |
      | MGEM | Query's die worden uitgevoerd vanuit de mashup-engine |
      |||

   1. Let op de tweede GUID, de aanvraag-ID.

   1. Ga verder met zoeken naar het activiteitstype totdat u een vermelding met de naam ' FireActivityCompletedSuccessfullyEvent ' hebt gevonden met een duur in milliseconden. Bevestig dat de invoer dezelfde aanvraag-ID heeft, bijvoorbeeld:

      ```text
      DM.EnterpriseGateway Verbose: 0 : 2016-09-26T23:08:56.7940067Z DM.EnterpriseGateway    baf40f21-2eb4-4af1-9c59-0950ef11ec4a    5f99f566-106d-c8ac-c864-c0808c41a606    MGEQ    21f96cc4-7496-bfdd-748c-b4915cb4b70c    B8DFCF12 [DM.Pipeline.Common.TracingTelemetryService] Event: FireActivityCompletedSuccessfullyEvent (duration=5004)
      ```

      > [!NOTE]
      > De vermelding ' FireActivityCompletedSuccessfullyEvent ' is een uitgebreide vermelding en wordt niet geregistreerd tenzij de instelling ' Traceverbosity ' zich op niveau 5 bevindt.

### <a name="trace-traffic-with-fiddler"></a>Verkeer traceren met Fiddler

[Fiddler](https://www.telerik.com/fiddler) is een gratis hulp programma van Telerik dat http-verkeer bewaakt. U kunt dit verkeer bekijken met de Power BI-service van de client computer. Deze service kan fouten en andere gerelateerde informatie weer geven.

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met on-premises gegevens vanuit Logic apps](../logic-apps/logic-apps-gateway-connection.md)
* [Functies voor ondernemings integratie](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Connectors voor Azure Logic Apps](../connectors/apis-list.md)
