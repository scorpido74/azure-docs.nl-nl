---
title: On-premises gegevens gateway installeren-Azure Logic Apps | Microsoft Docs
description: U kunt de on-premises gegevens gateway downloaden en installeren voordat u toegang hebt tot gegevens on-Azure Logic Apps premises.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 36fb40dcee010ab68dc87eb6f81c0b2fb8977914
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376385"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>On-premises gegevens gateway voor Azure Logic Apps installeren

Voordat u verbinding kunt maken met on-premises gegevens bronnen vanuit Azure Logic Apps, downloadt en installeert u de on-premises gegevens gateway op een lokale computer. De gateway werkt als een brug die snelle gegevens overdracht en versleuteling biedt tussen gegevens bronnen op locatie (niet in de Cloud) en uw Logic apps. In dit artikel wordt beschreven hoe u uw on-premises gegevens gateway kunt downloaden, installeren en instellen.

U kunt dezelfde Gateway-installatie gebruiken met andere services, zoals Power BI, Microsoft Flow, PowerApps en Azure Analysis Services. Meer informatie over [de werking van de gegevens gateway](#gateway-cloud-service).

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

Zie de volgende artikelen voor meer informatie over het gebruik van de gateway met andere services:

* [On-premises gegevens gateway van micro soft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft PowerApps on-premises gegevens gateway](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Microsoft Flow on-premises gegevens gateway](https://flow.microsoft.com/documentation/gateway-manage/)
* [Azure Analysis Services on-premises gegevens gateway](../analysis-services/analysis-services-gateway.md)

<a name="requirements"></a>

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

  Tijdens de installatie van de gateway meldt u zich aan bij dit account zodat u de gateway-installatie kunt koppelen aan uw Azure-abonnement. U kunt ook hetzelfde account gebruiken wanneer u een Azure-resource maakt voor uw gateway-installatie in de Azure Portal.

* Hier vindt u de vereisten voor uw lokale computer:

  **Minimale vereisten**

  * .NET Framework 4.5.2
  * 64-bits versie van Windows 7 of Windows Server 2008 R2 (of hoger)

  **Aanbevolen vereisten**

  * 8-core CPU
  * 8 GB geheugen
  * 64-bits versie van Windows Server 2012 R2 (of hoger)

  > [!NOTE]
  > De gateway biedt geen ondersteuning voor Windows Server 2016 core.

* **Belang rijke overwegingen**

  * U kunt de on-premises gegevens gateway alleen installeren op een lokale computer, niet op een domein controller. U hoeft de gateway echter niet te installeren op dezelfde computer als uw gegevens bron. Daarnaast hebt u slechts één gateway nodig voor al uw gegevens bronnen. u hoeft de gateway dus niet te installeren voor elke gegevens bron.

    > [!TIP]
    > Als u de latentie wilt minimaliseren, kunt u de gateway zo dicht mogelijk bij de gegevens bron of op dezelfde computer installeren, ervan uitgaande dat u over de juiste machtigingen beschikt.

  * Installeer de gateway op een computer die is verbonden met internet, altijd ingeschakeld en *niet* naar de slaap stand. Anders kan de gateway niet worden uitgevoerd. De prestaties kunnen ook hoger zijn dan een draadloos netwerk.

  * Tijdens de installatie kunt u zich alleen aanmelden met een [werk-of school account](../active-directory/sign-up-organization.md) dat wordt beheerd door Azure Active Directory (Azure AD), bijvoorbeeld @contoso.onmicrosoft.comen geen Azure B2B-account (gast) of persoonlijke @hotmail.com Microsoft-account, zoals of @outlook.com. Zorg ervoor dat u hetzelfde aanmeldings account gebruikt wanneer u uw gateway-installatie registreert in de Azure Portal door een gateway resource te maken. U kunt deze gateway bron vervolgens selecteren wanneer u de verbinding maakt vanuit uw logische app naar uw on-premises gegevens bron. [Waarom moet ik een werk-of school account voor Azure AD gebruiken?](#why-azure-work-school-account)

  > [!TIP]
  > Als u zich hebt geregistreerd voor een Office 365-aanbieding en uw werkelijke werk-e-mail niet hebt geleverd, hebt u mogelijk een aanmeldings adres dat lijkt op dit voor beeld:`username@domain.onmicrosoft.com` 
  >
  > Als u een Microsoft-account met een [standaard abonnement van Visual Studio](https://visualstudio.microsoft.com/vs/pricing/)wilt gebruiken, maakt u eerst [een directory (tenant) in azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md)of gebruikt u de standaard directory met uw Microsoft-account. 
  > Voeg een gebruiker met een wacht woord toe aan de map en geef die gebruiker vervolgens toegang tot uw abonnement. 
  > U kunt zich vervolgens aanmelden tijdens de installatie van de gateway met deze gebruikers naam en dit wacht woord.

  * De regio die u voor de gateway-installatie selecteert, bepaalt de locatie waar u later uw gateway in azure registreert door een Azure-resource te maken. Wanneer u deze gateway bron in azure maakt, moet u *dezelfde* locatie selecteren als voor de gateway-installatie. De standaard regio is dezelfde locatie als uw Azure AD-Tenant, waarmee uw Azure-account wordt beheerd, maar u kunt de locatie wijzigen tijdens de installatie van de gateway.

  * Als u al een gateway hebt die u hebt ingesteld met een installatie programma dat ouder is dan versie 14.16.6317.4, kunt u de locatie van de gateway niet wijzigen door het nieuwste installatie programma uit te voeren. U kunt echter het meest recente installatie programma gebruiken om een nieuwe gateway in te stellen met de gewenste locatie.
  
    Als u een gateway-installatie programma hebt dat ouder is dan versie 14.16.6317.4, maar u uw gateway nog niet hebt geïnstalleerd, kunt u in plaats daarvan het meest recente installatie programma downloaden en gebruiken.

## <a name="high-availability-support"></a>Ondersteuning voor hoge Beschik baarheid

De on-premises gegevens gateway biedt ondersteuning voor hoge Beschik baarheid wanneer u meer dan één gateway-installatie hebt en als clusters hebt ingesteld. Als u een bestaande gateway hebt wanneer u een andere gateway maakt, kunt u optioneel clusters met een hoge Beschik baarheid maken. Met deze clusters worden gateways in groepen ingedeeld die kunnen helpen bij het voor komen van storingen op individuele punten. Daarnaast ondersteunen alle on-premises gegevens gateway-Connectors nu hoge Beschik baarheid.

Als u de on-premises gegevens gateway wilt gebruiken, raadpleegt u deze vereisten en overwegingen:

* U moet al ten minste één gateway installatie hebben binnen hetzelfde Azure-abonnement als de primaire gateway en de herstel sleutel voor die installatie.

* Op uw primaire gateway moet de gateway-update van november 2017 of hoger worden uitgevoerd.

Wanneer u de volgende gateway hebt aan deze vereisten voldoet, selecteert u **toevoegen aan een bestaand gateway cluster**, selecteert u de primaire gateway voor uw cluster en geeft u de herstel sleutel voor de primaire gateway op. Zie [clusters met hoge Beschik baarheid voor on-premises gegevens gateway](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters)voor meer informatie.

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Een gegevensgateway installeren

1. [Het installatie programma voor de gateway downloaden, opslaan en uitvoeren op een lokale computer](https://aka.ms/on-premises-data-gateway-installer).

1. Accepteer het standaardpad voor de installatie of geef de locatie op van de computer waarop u de gateway wilt installeren.

1. Bekijk en accepteer de gebruiksrecht overeenkomst en de privacyverklaring en klik vervolgens op **installeren**.

   ![Gebruiks voorwaarden en privacyverklaring accepteren](./media/logic-apps-gateway-install/accept-terms.png)

1. Nadat de gateway is geïnstalleerd, geeft u het e-mail adres voor uw werk-of school account op en kiest u **Aanmelden**.

   ![Aanmelden met een werk-of school account](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

1. Kies **een nieuwe gateway registreren op deze computer** > **volgende**, waarmee de gateway-installatie wordt geregistreerd bij de [Gateway-Cloud service](#gateway-cloud-service).

   ![Gateway registreren](./media/logic-apps-gateway-install/register-new-gateway.png)

1. Geef deze informatie op voor de gateway-installatie:

   * De naam die u wilt voor de installatie

   * De herstel sleutel die u wilt maken, die ten minste acht tekens moet bevatten

     > [!IMPORTANT]
     > Bewaar uw herstel sleutel op een veilige plaats. U hebt deze sleutel nodig bij het wijzigen van de locatie van de gateway of bij het migreren, herstellen of overnemen van een bestaande gateway.

   * Bevestiging van uw herstel sleutel

     ![Gateway instellen](./media/logic-apps-gateway-install/set-up-gateway.png)

1. Controleer de regio die is geselecteerd voor de gateway-Cloud service en Azure Service Bus die wordt gebruikt door de installatie van de gateway.

   ![Controle regio](./media/logic-apps-gateway-install/check-region.png)

   > [!IMPORTANT]
   > Als u deze regio wilt wijzigen nadat u de installatie van de gateway hebt voltooid, hebt u de herstel sleutel voor die Gateway-installatie nodig. U moet ook de gateway verwijderen en opnieuw installeren. Zie voor meer informatie [locatie wijzigen, migreren, herstellen of overnemen van bestaande gateways](#update-gateway-installation).

   *Waarom wijzigt u de regio voor de installatie van de gateway?*

   Als u bijvoorbeeld de latentie wilt beperken, kunt u de regio van de gateway wijzigen in dezelfde regio als uw logische app. U kunt ook de regio selecteren die het dichtst bij uw on-premises gegevens bron ligt.    Uw *Gateway bron in azure* en uw logische app kunnen verschillende locaties hebben.

1. Als u de standaard regio wilt accepteren, kiest **u configureren**. Als u de standaard regio wilt wijzigen, voert u de volgende stappen uit:

   1. Selecteer **regio wijzigen**naast de huidige regio.

      ![Regio wijzigen](./media/logic-apps-gateway-install/change-region.png)

   1. Open de lijst **regio selecteren** op de volgende pagina, selecteer de gewenste regio en kies **gereed**.

      ![Selecteer een andere regio](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Wanneer de pagina bevestiging wordt weer gegeven, kiest u **sluiten**.

   Het installatie programma bevestigt dat uw gateway nu online is en gereed is voor gebruik.

   ![Voltooide gateway](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Registreer uw gateway nu in azure door [een Azure-resource te maken voor uw gateway-installatie](../logic-apps/logic-apps-gateway-connection.md).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Een bestaande gateway wijzigen, migreren, herstellen of overnemen

Als u de locatie van de gateway moet wijzigen, de installatie van de gateway naar een nieuwe computer verplaatst, een beschadigde gateway herstelt of eigenaar van een bestaande gateway bent, hebt u de herstel sleutel nodig die tijdens de installatie van de gateway werd verschaft. Met deze actie wordt de verbinding met de oude gateway verbroken.

1. Ga in het **configuratie scherm**van uw computer naar **Program ma's en onderdelen**. Selecteer in de lijst Program ma's de optie **on-premises gegevens gateway**en kies vervolgens **verwijderen**.

1. [Installeer de on-premises gegevens Gateway opnieuw](https://aka.ms/on-premises-data-gateway-installer).

1. Nadat het installatie programma is geopend, meldt u zich aan met hetzelfde werk-of school account dat eerder werd gebruikt voor het installeren van de gateway.

1. Selecteer **een bestaande gateway migreren, herstellen of overnemen**en klik vervolgens op **volgende**.

   ![Selecteer een bestaande gateway migreren, herstellen of overnemen](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Selecteer onder **beschik bare gateways** of **beschik bare gateway clusters**de gateway-installatie die u wilt wijzigen. Voer de herstel sleutel in voor de installatie van de gateway.

   ![Primaire gateway selecteren](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Als u de regio wilt wijzigen, selecteert u **regio wijzigen** en de nieuwe regio.

1. Wanneer u klaar bent, kiest **u configureren**.

## <a name="configure-proxy-or-firewall"></a>Proxy of firewall configureren

De on-premises gegevens gateway maakt een uitgaande verbinding met [Azure service bus](https://azure.microsoft.com/services/service-bus/). Als uw werk omgeving vereist dat verkeer via een proxy wordt uitgevoerd voor toegang tot internet, kan deze beperking ertoe leiden dat de gegevens gateway geen verbinding kan maken met de Cloud service van de gateway. Als u wilt bepalen of uw netwerk gebruikmaakt van een proxy, raadpleegt u dit artikel op superuser.com:

[Hoe kan ik weet ik welke proxy server Ik gebruik? (SuperUser.com)](https://superuser.com/questions/346372/how-do-i-know-what-proxy-server-im-using)

Zie [proxy-instellingen configureren](https://docs.microsoft.com/power-bi/service-gateway-proxy)om proxy gegevens voor uw gateway op te geven. Als u wilt controleren of uw proxy of firewall verbindingen kan blok keren, controleert u of de computer verbinding kan maken met internet en de [Azure service bus](https://azure.microsoft.com/services/service-bus/). Voer de volgende opdracht uit vanaf een Power shell-prompt:

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

## <a name="configure-ports"></a>Poorten configureren

De gateway maakt een uitgaande verbinding met [Azure service bus](https://azure.microsoft.com/services/service-bus/) en communiceert via uitgaande poorten: TCP 443 (standaard), 5671, 5672, 9350 tot en met 9354. De gateway vereist geen binnenkomende poorten. Meer informatie over [Azure service bus en hybride oplossingen](../service-bus-messaging/service-bus-messaging-overview.md).

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

Sommige proxy's laten verkeer via poort 80 en 443. Communicatie met Azure Service Bus vindt standaard plaats op andere poorten dan 443. U kunt afdwingen dat de gateway communiceert met de Azure Service Bus via HTTPS in plaats van rechtstreeks TCP, maar dit kan de prestaties aanzienlijk verminderen. Ga als volgt te werk om deze taak uit te voeren:

1. Blader naar de locatie van de on-premises gegevens Gateway-client, die u hier doorgaans kunt vinden:```C:\Program Files\On-premises data gateway\Microsoft.PowerBI.EnterpriseGateway.exe```

   Als u de client locatie wilt vinden, opent u de Services-console op dezelfde computer, zoekt u de **on-premises gegevens Gateway Service**en bekijkt **u het pad naar de eigenschap voor het uitvoer bare bestand** .

1. Dit *configuratie* bestand openen: **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

1. Wijzig de waarde voor **ServiceBusSystemConnectivityModeString** van **Automatische detectie** in **https**:

   ```html
   <setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
      <value>Https</value>
   </setting>
   ```

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Windows-Service account

Op de computer waarop u de on-premises gegevens gateway installeert, wordt de gateway uitgevoerd als een Windows-Service account met de naam ' on-premises gegevens Gateway-Service '. De gateway gebruikt echter de naam ' NT SERVICE\PBIEgwService ' voor de account referenties ' Aanmelden als '. De gateway heeft standaard de machtigingen ' Aanmelden als service ' op de computer waarop u de gateway installeert. Het Windows-Service account voor de gateway verschilt doorgaans van het account dat u gebruikt om verbinding te maken met on-premises gegevens bronnen en van het werk-of school account dat u gebruikt om u aan te melden bij Cloud Services.

Als u de gateway wilt maken en onderhouden in de Azure Portal, moet dit Windows-Service account mini maal **Inzender** machtigingen hebben. Zie [toegang beheren met RBAC en de Azure Portal](../role-based-access-control/role-assignments-portal.md)om deze machtigingen te controleren.

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Gateway opnieuw starten

De gegevens gateway wordt uitgevoerd als een venster Service, net zoals elke andere Windows-service, maar u kunt de gateway op verschillende manieren starten en stoppen. U kunt bijvoorbeeld een opdracht prompt met verhoogde bevoegdheden openen op de computer waarop de gateway wordt uitgevoerd en een van de volgende opdrachten uitvoeren:

* Als u de service wilt stoppen, voert u deze opdracht uit:
  
  `net stop PBIEgwService`

* Als u de service wilt starten, voert u deze opdracht uit:
  
  `net start PBIEgwService`

## <a name="tenant-level-administration"></a>Beheer op Tenant niveau

Er is momenteel geen enkele locatie waar Tenant beheerders alle gateways kunnen beheren die andere gebruikers hebben geïnstalleerd en geconfigureerd. Als u een Tenant beheerder bent, wilt u mogelijk de gebruikers in uw organisatie als beheerder toevoegen voor elke gateway die ze installeren. Op deze manier kunt u alle gateways in uw organisatie beheren via de pagina gateway-instellingen of via [Power shell-opdrachten](/data-integration/gateway/service-gateway-powershell-support).

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-gateway-work"></a>Hoe werkt de gateway?

De gegevens gateway vereenvoudigt snelle en veilige communicatie tussen uw logische app, de gateway-Cloud service en uw on-premises gegevens bron. De gateway-Cloud service versleutelt en slaat uw referenties voor de gegevens bron en de gateway gegevens op. De service routeert ook query's en de bijbehorende resultaten tussen uw logische app, de on-premises gegevens gateway en uw gegevens bron on-premises.

De gateway werkt met firewalls en maakt gebruik van alleen uitgaande verbindingen. Al het verkeer is afkomstig van het beveiligde uitgaande verkeer van de gateway-agent. De gateway doorstuurt gegevens van on-premises bronnen op versleutelde kanalen via de Azure Service Bus. Met deze service bus wordt een kanaal gemaakt tussen de gateway en de aanroepende service, maar worden er geen gegevens opgeslagen. Alle gegevens die via de gateway worden uitgewisseld, worden versleuteld.

![diagram-voor-on-premises-gegevens gateway-stroom](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

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
