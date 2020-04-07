---
title: On-premises gegevensgateway installeren
description: Voordat u toegang hebt tot gegevens op locatie vanuit Azure Logic Apps, download en installeer t u de on-premises gegevensgateway
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 12/05/2019
ms.openlocfilehash: f2f8b9f207993c49201d03d3d1fed3c5800e8780
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673819"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>On-premises gegevensgateway installeren voor Azure Logic Apps

Voordat u verbinding [maken met on-premises gegevensbronnen van Azure Logic Apps,](../logic-apps/logic-apps-gateway-connection.md)download en installeer u de [on-premises gegevensgateway](https://aka.ms/on-premises-data-gateway-installer) op een lokale computer. De gateway werkt als een brug die snelle gegevensoverdracht en versleuteling biedt tussen gegevensbronnen op locatie en uw logische apps. U dezelfde gateway-installatie gebruiken met andere cloudservices, zoals Power BI, Power Automate, Power Apps en Azure Analysis Services. Zie de volgende artikelen voor informatie over het gebruik van de gateway met deze services:

* [Microsoft Power Automatiseer on-premises datagateway](/power-automate/gateway-reference)
* [Microsoft Power BI on-premises datagateway](/power-bi/service-gateway-onprem)
* [On-premises microsoft Power Apps-gateway](/powerapps/maker/canvas-apps/gateway-reference)
* [On-premises azure Analysis Services-gateway](../analysis-services/analysis-services-gateway.md)

In dit artikel ziet u hoe u uw on-premises gegevensgateway downloaden, installeren en instellen, zodat u toegang hebt tot on-premises gegevensbronnen van Azure Logic Apps. U later in dit onderwerp ook meer te weten komen over [hoe de gegevensgateway werkt.](#gateway-cloud-service) Zie Wat is een [on-premises gateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem)voor meer informatie over de gateway? Ga naar de PowerShell-galerie voor de [GegevensGateway PowerShell-cmdlets](https://www.powershellgallery.com/packages/DataGateway/3000.15.15)om installatie- en beheertaken van poorten te automatiseren.

<a name="requirements"></a>

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. Als u geen Azure-account met een abonnement hebt, [meldt u zich aan voor een gratis Azure-account.](https://azure.microsoft.com/free/)

  * Uw Azure-account moet deel uitmaken van één [Azure Active Directory -tenant of -map](../active-directory/fundamentals/active-directory-whatis.md#terminology). U moet hetzelfde Azure-account gebruiken voor het installeren en beheren van de gateway op uw lokale computer.

  * Tijdens de installatie van de gateway meldt u zich aan met uw Azure-account, dat uw gateway-installatie koppelt aan uw Azure-account en alleen dat account. Later, in de Azure-portal, moet u hetzelfde Azure-account en Azure AD-tenant gebruiken wanneer u een Azure-gatewaybron maakt die uw gateway-installatie registreert en claimt. In Azure Logic Apps gebruiken on-premises triggers en acties vervolgens de gatewaybron om verbinding te maken met on-premises gegevensbronnen.

    > [!NOTE]
    > U slechts één gateway-installatie en één Azure-gatewaybron aan elkaar koppelen. U dezelfde gateway-installatie niet koppelen aan meerdere Azure-accounts of Azure-gatewaybronnen. Een Azure-account kan echter worden gekoppeld aan meerdere gateway-installaties en Azure-gatewaybronnen. In een on-premises trigger of actie u kiezen uit uw verschillende Azure-abonnementen en vervolgens een bijbehorende gatewaybron selecteren.

  * Je moet inloggen met een werkaccount of schoolaccount, ook wel een `username@contoso.com` *organisatieaccount* genoemd, dat eruit ziet als . U geen Azure B2B-accounts (gast)accounts of @hotmail.com @outlook.compersoonlijke Microsoft-accounts gebruiken, zoals of.

    > [!TIP]
    > Als u zich hebt aangemeld voor een Office 365-aanbieding en uw `username@domain.onmicrosoft.com`werk-e-mailadres niet hebt opgegeven, lijkt uw adres mogelijk op . Uw account wordt opgeslagen in een tenant in een Azure Active Directory (Azure AD). In de meeste gevallen is de UPN (User Principal Name) voor uw Azure AD-account hetzelfde als uw e-mailadres.
    >
    > Als u een [Visual Studio Standard-abonnement](https://visualstudio.microsoft.com/vs/pricing/) wilt gebruiken dat is gekoppeld aan een Microsoft-account, maakt u eerst [een tenant in Azure AD](../active-directory/develop/quickstart-create-new-tenant.md) of gebruikt u de standaardmap. Voeg een gebruiker met een wachtwoord toe aan de map en geef die gebruiker vervolgens toegang tot uw Azure-abonnement. U zich dan aanmelden tijdens de installatie van de gateway met deze gebruikersnaam en wachtwoord.

* Hier zijn vereisten voor uw lokale computer:

  **Minimumeisen**

  * .NET Framework 4.7.2
  * 64-bits versie van Windows 7 of Windows Server 2008 R2 (of hoger)

  **Aanbevolen vereisten**

  * 8-core CPU
  * Geheugen van 8 GB
  * 64-bits versie van Windows Server 2012 R2 of hoger
  * Ssd-opslag (Solid-state drive) voor spoelen

  > [!NOTE]
  > De gateway biedt geen ondersteuning voor Windows Server Core.

* **Verwante overwegingen**

  * Installeer de on-premises gegevensgateway alleen op een lokale computer, niet op een domeincontroller. U hoeft de gateway niet op dezelfde computer te installeren als uw gegevensbron. U hebt slechts één gateway nodig voor al uw gegevensbronnen, zodat u de gateway niet voor elke gegevensbron hoeft te installeren.

    > [!TIP]
    > Om de latentie te minimaliseren, u de gateway zo dicht mogelijk bij uw gegevensbron of op dezelfde computer installeren, ervan uitgaande dat u machtigingen hebt.

  * Installeer de gateway op een computer die zich op een bekabeld netwerk bevindt, verbonden is met internet, altijd ingeschakeld en niet in de slaapstand gaat. Anders kan de gateway niet worden uitgevoerd en kunnen de prestaties lijden onder een draadloos netwerk.

  * Als u Windows-verificatie wilt gebruiken, moet u ervoor zorgen dat u de gateway installeert op een computer die lid is van dezelfde Active Directory-omgeving als uw gegevensbronnen.

  * Het gebied dat u selecteert voor uw gateway-installatie is dezelfde locatie die u moet selecteren wanneer u later de Azure-gatewaybron voor uw logische app maakt. Standaard is dit gebied dezelfde locatie als uw Azure AD-tenant die uw Azure-account beheert. U de locatie echter wijzigen tijdens de installatie van de gateway.

  * Als u de gateway-installatie bijwerkt naar de nieuwste versie, verwijdert u eerst uw huidige gateway voor een schonere ervaring.

  * De gateway heeft twee modi: standaardmodus en persoonlijke modus, die alleen van toepassing is op Power BI. U niet meer dan één gateway in dezelfde modus op dezelfde computer laten uitvoeren.

  * Azure Logic Apps ondersteunt lees- en schrijfbewerkingen via de gateway. Deze bewerkingen hebben echter [beperkingen op hun laadvermogengrootte](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Een gegevensgateway installeren

1. [Download en voer het gateway-installatieprogramma uit op een lokale computer.](https://aka.ms/on-premises-data-gateway-installer)

1. Controleer de minimumvereisten, houd het standaardinstallatiepad, accepteer de gebruiksvoorwaarden en selecteer **Installeren**.

   ![Vereisten controleren en gebruiksvoorwaarden accepteren](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. Nadat de gateway is geïnstalleerd, geeft u het e-mailadres voor uw Azure-account op en selecteert u **Aanmelden,** bijvoorbeeld:

   ![Inloggen met werk- of schoolaccount](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   Uw gateway-installatie kan slechts aan één Azure-account worden gekoppeld.

1. Selecteer **Een nieuwe gateway registreren op deze computer** > **Volgende**. Met deze stap wordt uw gateway-installatie geregistreerd met de [gatewaycloudservice.](#gateway-cloud-service)

   ![Gateway registreren op lokale computer](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Geef deze informatie op voor uw gateway-installatie:

   * Een gatewaynaam die uniek is voor uw Azure AD-tenant
   * De herstelsleutel, die ten minste acht tekens moet hebben, die u wilt gebruiken
   * Bevestiging voor uw herstelsleutel

   ![Informatie verstrekken voor gateway-installatie](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Bewaar en bewaar uw herstelsleutel op een veilige plaats. U hebt deze sleutel nodig als u ooit de locatie wilt wijzigen, een gateway-installatie wilt verplaatsen, herstellen of overnemen.

   Let op de optie **Toevoegen aan een bestaand gatewaycluster**, dat u selecteert wanneer u extra gateways installeert voor [scenario's](#high-availability)met hoge beschikbaarheid .

1. Controleer de regio voor de gatewaycloudservice en [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) die wordt gebruikt door uw gateway-installatie. Standaard is dit gebied dezelfde locatie als de Azure AD-tenant voor uw Azure-account.

   ![Regio bevestigen voor gatewayservice en servicebus](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Als u het standaardgebied wilt accepteren, selecteert **u Configureren**. Als het standaardgebied echter niet het gebied is dat het dichtst bij u staat, u de regio wijzigen.

   *Waarom de regio wijzigen voor uw gateway-installatie?*

   Als u bijvoorbeeld de latentie wilt verminderen, u het gebied van uw gateway wijzigen in dezelfde regio als uw logica-app. U ook het gebied selecteren dat het dichtst bij uw on-premises gegevensbron ligt. Uw *gatewaybron in Azure* en uw logische app kunnen verschillende locaties hebben.

   1. Selecteer Naast het huidige gebied **Regio wijzigen**.

      ![Het huidige gatewaygebied wijzigen](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. Open op de volgende pagina de lijst **Regio selecteren,** selecteer de gewenste regio en selecteer **Gereed**.

      ![Een ander gebied selecteren voor gatewayservice](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Bekijk de informatie in het definitieve bevestigingsvenster. In dit voorbeeld wordt hetzelfde account gebruikt voor Logic Apps, Power BI, Power Apps en Power Automate, zodat de gateway beschikbaar is voor al deze services. Wanneer u klaar bent, selecteert u **Sluiten**.

   ![Gegevensgatewaygegevens bevestigen](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Maak nu [de Azure-bron voor uw gateway-installatie.](../logic-apps/logic-apps-gateway-connection.md)

## <a name="check-or-adjust-communication-settings"></a>Communicatie-instellingen controleren of aanpassen

De on-premises datagateway is afhankelijk van [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) voor cloudconnectiviteit en stelt de bijbehorende uitgaande verbindingen tot het bijbehorende Azure-gebied van de gateway. Als uw werkomgeving vereist dat verkeer via een proxy of firewall gaat om toegang te krijgen tot internet, kan deze beperking voorkomen dat de on-premises gegevensgateway verbinding maakt met de gatewaycloudservice en Azure Service Bus. De gateway heeft verschillende communicatie-instellingen, die u aanpassen. Zie deze onderwerpen voor meer informatie:

* [Communicatie-instellingen voor de on-premises gegevensgateway aanpassen](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)
* [Proxyinstellingen configureren voor de on-premises gegevensgateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Ondersteuning voor hoge beschikbaarheid

Om te voorkomen dat er één storingspunt voor on-premises gegevenstoegang optreedt, u meerdere gateway-installaties (alleen standaardmodus) hebben met elk op een andere computer en deze instellen als een cluster of groep. Op die manier worden gegevensaanvragen, als de primaire gateway niet beschikbaar is, doorgestuurd naar de tweede gateway, enzovoort. Omdat u slechts één standaardgateway op een computer installeren, moet u elke extra gateway die zich in het cluster bevindt op een andere computer installeren. Alle connectors die werken met de on-premises datagateway ondersteunen een hoge beschikbaarheid.

* U moet al ten minste één gateway-installatie hebben met hetzelfde Azure-account als de primaire gateway en de herstelsleutel voor die installatie.

* Uw primaire gateway moet de gateway-update van november 2017 of later uitvoeren.

Nadat u uw primaire gateway hebt ingesteld, selecteert u bij het installeren van een andere gateway de optie **Toevoegen aan een bestaand gatewaycluster,** selecteert u de primaire gateway, de eerste gateway die u hebt geïnstalleerd, en biedt u de herstelsleutel voor die gateway. Zie [clusters met hoge beschikbaarheid voor on-premises gegevensgateway voor](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster)meer informatie.

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Locatie wijzigen, migreren, herstellen of bestaande gateway overnemen

Als u de locatie van uw gateway moet wijzigen, de gateway-installatie naar een nieuwe computer moet verplaatsen, een beschadigde gateway moet herstellen of eigenaar moet worden van een bestaande gateway, hebt u de herstelsleutel nodig die is geleverd tijdens de installatie van de gateway.

1. Voer het gateway-installatieprogramma uit op de computer met de bestaande gateway. Als u niet beschikt over de nieuwste gateway installer, [download de nieuwste gateway versie](https://aka.ms/on-premises-data-gateway-installer).

   > [!NOTE]
   > Voordat u de gateway op de computer met de oorspronkelijke gateway-installatie herstelt, moet u eerst de gateway op die computer verwijderen. Met deze actie wordt de oorspronkelijke gateway losgekoppeld.
   > Als u een gatewaycluster voor een cloudservice verwijdert of verwijdert, u dat cluster niet herstellen.

1. Nadat de installatieprogramma is geopend, meldt u zich aan met hetzelfde Azure-account dat is gebruikt om de gateway te installeren.

1. Selecteer Een bestaande gateway > **vervolgens** **migreren, herstellen of overnemen,** bijvoorbeeld:

   ![Selecteer 'Een bestaande gateway migreren, herstellen of overnemen'](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Kies uit de beschikbare clusters en gateways en voer de herstelsleutel voor de geselecteerde gateway in, bijvoorbeeld:

   ![Gateway selecteren en herstelsleutel bieden](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Als u het gebied wilt wijzigen, selecteert u **Regio wijzigen**en selecteert u het nieuwe gebied.

1. Wanneer u klaar bent, selecteert u **Configureren** zodat u uw taak voltooien.

## <a name="tenant-level-administration"></a>Beheer op tenantniveau

Als u inzicht wilt krijgen in alle on-premises gegevensgateways in een Azure AD-tenant, kunnen globale beheerders in die tenant zich aanmelden bij het [Power Platform Admin center](https://powerplatform.microsoft.com) als tenantbeheerder en de optie **Gegevensgateways** selecteren. Zie [Beheer op tenantniveau voor de on-premises gegevensgateway voor](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)meer informatie.

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Gateway opnieuw opstarten

Standaard wordt de gateway-installatie op uw lokale computer uitgevoerd als een Windows-serviceaccount met de naam 'On-premises datagatewayservice'. De gateway-installatie gebruikt `NT SERVICE\PBIEgwService` echter de naam voor de machtigingen voor het 'Aanmelden als' en heeft machtigingen voor 'Aanmelden als service'.

> [!NOTE]
> Uw Windows-serviceaccount verschilt van het account dat wordt gebruikt voor het maken van verbinding met on-premises gegevensbronnen en van het Azure-account dat u gebruikt wanneer u zich aanmeldt bij cloudservices.

Net als elke andere Windows-service u de gateway op verschillende manieren starten en stoppen. Zie [Een on-premises gegevensgateway opnieuw starten](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart)voor meer informatie.

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Hoe de gateway werkt

Gebruikers in uw organisatie hebben toegang tot on-premises gegevens waarvoor ze al geautoriseerde toegang hebben. Voordat deze gebruikers echter verbinding kunnen maken met uw on-premises gegevensbron, moet u een on-premises gegevensgateway installeren en instellen. Meestal is een beheerder de persoon die een gateway installeert en instelt. Voor deze acties zijn mogelijk machtigingen voor serverbeheerders of speciale kennis over uw on-premises servers vereist.

De gateway helpt bij het sneller en veiliger communiceren achter de schermen. Deze communicatie verloopt tussen een gebruiker in de cloud, de gatewaycloudservice en uw on-premises gegevensbron. De gatewaycloudservice versleutelt en slaat uw gegevensbronreferenties en gatewaygegevens op. De service leidt ook query's en hun resultaten tussen de gebruiker, de gateway en uw on-premises gegevensbron.

De gateway werkt met firewalls en gebruikt alleen uitgaande verbindingen. Al het verkeer is afkomstig van beveiligd uitgaand verkeer van de gatewayagent. De gateway stuurt gegevens van on-premises bronnen door op versleutelde kanalen via [Azure Service Bus.](../service-bus-messaging/service-bus-messaging-overview.md) Deze servicebus maakt een kanaal tussen de gateway en de oproepservice, maar slaat geen gegevens op. Alle gegevens die via de gateway worden verzonden, worden versleuteld.

![Architectuur voor on-premises datagateway](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> Afhankelijk van de cloudservice moet u mogelijk een gegevensbron voor de gateway instellen.

In deze stappen wordt beschreven wat er gebeurt wanneer u een element gebruikt dat is verbonden met een on-premises gegevensbron:

1. De cloudservice maakt een query, samen met de versleutelde referenties voor de gegevensbron. De service stuurt vervolgens de query en referenties naar de gatewaywachtrij voor verwerking.

1. De gatewaycloudservice analyseert de query en pusht het verzoek naar Azure Service Bus.

1. Azure Service Bus stuurt de in behandeling zijnde aanvragen naar de gateway.

1. De gateway krijgt de query, decodeert de referenties en maakt verbinding met een of meer gegevensbronnen met die referenties.

1. De gateway stuurt de query naar de gegevensbron voor het uitvoeren.

1. De resultaten worden vanuit de gegevensbron teruggestuurd naar de gateway en vervolgens naar de gatewaycloudservice. De gatewaycloudservice gebruikt vervolgens de resultaten.

### <a name="authentication-to-on-premises-data-sources"></a>Verificatie voor on-premises gegevensbronnen

Een opgeslagen referentie wordt gebruikt om verbinding te maken vanaf de gateway met on-premises gegevensbronnen. Ongeacht de gebruiker gebruikt de gateway de opgeslagen referenties om verbinding te maken. Er kunnen uitzonderingen zijn voor verificatie voor specifieke services, zoals DirectQuery en LiveConnect voor analyseservices in Power BI.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Microsoft-cloudservices gebruiken [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) om gebruikers te verifiëren. Een Azure AD-tenant bevat gebruikersnamen en beveiligingsgroepen. Het e-mailadres dat u voor aanmelding gebruikt, is doorgaans hetzelfde als de upn (User Principal Name) voor uw account.

### <a name="what-is-my-upn"></a>Wat is mijn UPN?

Als u geen domeinbeheerder bent, kent u uw UPN mogelijk niet. Voer de `whoami /upn` opdracht uit vanaf uw werkstation om de UPN voor uw account te vinden. Hoewel het resultaat lijkt op een e-mailadres, is het resultaat de UPN voor uw lokale domeinaccount.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Een on-premises Active Directory Domain Services synchroniseren met Azure AD

De UPN voor uw on-premises Active Directory-accounts en Azure AD-accounts moeten hetzelfde zijn. Zorg er dus voor dat elk on-premises Active Directory-account overeenkomt met uw Azure AD-account. De cloudservices weten alleen van accounts binnen Azure AD. U hoeft dus geen account toe te voegen aan uw on-premises Active Directory. Als het account niet bestaat in Azure AD, u dat account niet gebruiken.

Hier volgen manieren waarop u uw on-premises Active Directory-accounts koppelen aan Azure AD.

* Accounts handmatig toevoegen aan Azure AD.

  Maak een account in de Azure-portal of in het Microsoft 365-beheercentrum. Controleer of de accountnaam overeenkomt met de UPN voor het on-premises Active Directory-account.

* Lokale accounts synchroniseren met uw Azure AD-tenant met het azure Active Directory Connect-hulpprogramma.

  Het hulpprogramma Azure AD Connect biedt opties voor adreslijstsynchronisatie en verificatie-instelling. Deze opties omvatten wachtwoord hash sync, pass-through authenticatie, en federatie. Als u geen tenantbeheerder of lokale domeinbeheerder bent, neemt u contact op met uw IT-beheerder om Azure AD Connect in te stellen. Azure AD Connect zorgt ervoor dat uw Azure AD UPN overeenkomt met uw lokale Active Directory UPN. Deze aanpassing helpt als u live verbindingen met Analysis Services gebruikt met Power BI- of SSO-mogelijkheden (Single Sign-On).

  > [!NOTE]
  > Als u accounts synchroniseert met het Azure AD Connect-hulpprogramma, worden nieuwe accounts gemaakt in uw Azure AD-tenant.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>Veelgestelde vragen en probleemoplossing

Zie deze onderwerpen voor meer informatie:

* [FAQ voor on-premises gegevensgateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)
* [Problemen met de on-premises gegevensgateway oplossen](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Gatewayprestaties bewaken en optimaliseren](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met on-premises gegevens van logische apps](../logic-apps/logic-apps-gateway-connection.md)
* [Functies voor bedrijfsintegratie](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Connectors voor Azure Logic Apps](../connectors/apis-list.md)
