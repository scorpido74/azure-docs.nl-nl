---
title: Verbinding maken met SAP-systemen
description: Toegang tot en beheer van SAP-bronnen door werk stromen te automatiseren met Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 06/23/2020
tags: connectors
ms.openlocfilehash: 01c1a2b3f9455f19877f1b16b7fff5a7c2e77c76
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85323151"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Verbinding maken met SAP-systemen in Azure Logic Apps

> [!IMPORTANT]
> De eerdere SAP-toepassings server en SAP Message server-connectors zijn afgeschaft op 29 februari 2020. De huidige SAP-connector consolideert deze vorige SAP-connectors zodat u het verbindings type niet hoeft te wijzigen, is volledig compatibel met eerdere connectors, biedt veel extra mogelijkheden en blijft de SAP .net connector Library (SAP NCo) gebruiken.
>
> Voor Logic apps die gebruikmaken van de oudere connectors, [migreert u naar de nieuwste connector](#migrate) vóór de datum van afschaffing. Als dat niet het geval is, kunnen deze Logic apps uitvoerings fouten ondervinden en kan er geen berichten naar uw SAP-systeem worden verzonden.

In dit artikel wordt beschreven hoe u met de SAP-connector toegang kunt krijgen tot uw on-premises SAP-resources vanuit een logische app. De connector werkt met de klassieke versies van SAP, zoals R/3-en ECC-systemen on-premises. De connector biedt ook integratie met de nieuwere, op HANA gebaseerde SAP-systemen van SAP, zoals S/4 HANA, of deze nu on-premises of in de cloud worden gehost. De SAP-connector ondersteunt de integratie van berichten of gegevens van en naar SAP netweave-systemen via tussenliggende documenten (IDoc), Business Application Programming Interface (BAPI) of externe functie aanroep (RFC).

De SAP-connector maakt gebruik van de [SAP .net connector (NCo)-bibliotheek](https://support.sap.com/en/product/connectors/msnet.html) en biedt de volgende acties:

* **Bericht verzenden naar SAP**: IDOC verzenden via TRFC, BAPI-functies aanroepen via RFC of RFC/tRFC aanroepen in SAP-systemen.

* **Wanneer een bericht wordt ontvangen van SAP**: IDOC ontvangen via TRFC, BAPI-functies aanroepen via TRFC of RFC/tRFC aanroepen in SAP-systemen.

* **Schema's genereren**: schema's genereren voor de SAP-artefacten voor IDOC, BAPI of RFC.

Voor deze bewerkingen ondersteunt de SAP-connector basis verificatie via gebruikers namen en wacht woorden. De connector biedt ook ondersteuning voor [beveiligde netwerk communicatie (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). SNC kan worden gebruikt voor SAP NetWeaver single sign-on (SSO) of voor aanvullende beveiligings mogelijkheden van een extern beveiligings product.

In dit artikel wordt uitgelegd hoe u logische apps maakt die met SAP worden geïntegreerd, terwijl u de eerder beschreven integratie scenario's bestrijkt. In dit artikel wordt beschreven hoe u logische apps kunt migreren naar de meest recente SAP-connector voor Logic apps die gebruikmaken van de oudere SAP-connectors.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt volgen, hebt u de volgende items nodig:

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, [meldt u zich aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De logische app die u wilt gebruiken om toegang te krijgen tot uw SAP-systeem en een trigger waarmee de werk stroom van de logische app wordt gestart. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md) en [Quick Start: uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uw [SAP-toepassings server](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) of [SAP-berichten server](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* Bericht inhoud die u naar uw SAP-server verzendt, zoals een voor beeld van een IDoc-bestand, moet de XML-indeling hebben en de naam ruimte bevatten voor de SAP-actie die u wilt gebruiken.

* Als u de trigger **Wanneer een bericht wordt ontvangen van SAP** wilt gebruiken, moet u deze installatie stappen ook uitvoeren:
  
  > [!NOTE]
  > Deze trigger maakt gebruik van dezelfde URI-locatie voor het vernieuwen en afmelden van een webhook-abonnement. Voor de vernieuwings bewerking wordt de HTTP- `PATCH` methode gebruikt, terwijl de afmeldings bewerking de HTTP- `DELETE` methode gebruikt. Dit gedrag kan ertoe leiden dat een vernieuwings bewerking wordt weer gegeven als een afmeldings bewerking in de geschiedenis van uw trigger, maar de bewerking is nog steeds een verlenging omdat de trigger wordt gebruikt `PATCH` als de HTTP-methode, niet `DELETE` .

  * Stel de beveiligings machtigingen voor uw SAP-gateway in met deze instelling:

    `"TP=Microsoft.PowerBI.EnterpriseGateway HOST=<gateway-server-IP-address> ACCESS=*"`

  * Stel de beveiligings logboeken van de SAP-gateway in, waarmee u fouten in de Access Control lijst (ACL) kunt vinden en niet standaard is ingeschakeld. Anders wordt de volgende fout weer geven:

    `"Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed"`

    Zie voor meer informatie het SAP Help-onderwerp, het [instellen van Gateway logboek registratie](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm).

<a name="multi-tenant"></a>

### <a name="multi-tenant-azure-prerequisites"></a>Azure-vereisten voor meerdere tenants

Deze vereisten zijn van toepassing wanneer uw Logic apps worden uitgevoerd in een multi tenant Azure en u de beheerde SAP-connector wilt gebruiken, die niet systeem eigen wordt uitgevoerd in een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Als u gebruikmaakt van een ISE op Premium niveau en u de SAP-connector wilt gebruiken die systeem eigen wordt uitgevoerd in de ISE, raadpleegt u de [vereisten voor Integration service Environment (ISE)](#sap-ise).

De beheerde SAP-connector (non-ISE) kan worden geïntegreerd met on-premises SAP-systemen via de [on-premises gegevens gateway](../logic-apps/logic-apps-gateway-connection.md). Als bijvoorbeeld in scenario's voor het verzenden van berichten een bericht vanuit een logische app naar een SAP-systeem wordt verzonden, fungeert de gegevens gateway als een RFC-client en worden de aanvragen die van de logische app worden ontvangen, doorgestuurd naar SAP. Evenzo fungeert de gegevens gateway in scenario's voor het ontvangen van berichten als een RFC-server die aanvragen van SAP ontvangt en deze doorstuurt naar de logische app.

* [Down load en installeer de on-premises gegevens gateway](../logic-apps/logic-apps-gateway-install.md) op uw lokale computer. Maak vervolgens [een Azure gateway-resource](../logic-apps/logic-apps-gateway-connection.md#create-azure-gateway-resource) voor die gateway in het Azure Portal. De gateway helpt u veilig toegang te krijgen tot on-premises gegevens en bronnen.

  Zorg er als best practice voor dat u een ondersteunde versie van de on-premises gegevens gateway gebruikt. Micro soft brengt elke maand een nieuwe versie uit. Op dit moment ondersteunt micro soft de laatste zes versies. Als u een probleem met uw gateway ondervindt, voert u [een upgrade uit naar de nieuwste versie](https://aka.ms/on-premises-data-gateway-installer), die mogelijk updates bevat om het probleem op te lossen.

* [Down load en installeer de meest recente SAP-client bibliotheek](#sap-client-library-prerequisites) op dezelfde computer als de on-premises gegevens gateway.

<a name="sap-ise"></a>

### <a name="integration-service-environment-ise-prerequisites"></a>Vereisten voor de integratie service omgeving (ISE)

Deze vereisten zijn van toepassing wanneer uw Logic apps worden uitgevoerd in een [integratie service omgeving](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)op Premium-niveau (niet op ontwikkelaars niveau) en u de SAP-connector wilt gebruiken die systeem eigen wordt uitgevoerd in een ISE. Een ISE biedt toegang tot bronnen die worden beveiligd met een virtueel Azure-netwerk en biedt andere ISE-systeem eigen connectors waarmee Logic apps rechtstreeks toegang hebben tot on-premises resources zonder gebruik te maken van een on-premises gegevens gateway.

> [!NOTE]
> Hoewel de SAP ISE-connector zichtbaar is binnen een ISE op ontwikkelaars niveau, mislukt de installatie van de connector.

1. Als u nog geen Azure Storage-account en een BLOB-container hebt, moet u die container maken met behulp van de [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) of de [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

1. [Down load en installeer de meest recente SAP-client bibliotheek](#sap-client-library-prerequisites) op uw lokale computer. U moet de volgende assembly bestanden hebben:

   * libicudecnumber.dll
   * rscp4n.dll
   * sapnco.dll
   * sapnco_utils.dll

1. Maak een zip-bestand dat deze assembly's bevat en upload dit pakket naar uw BLOB-container in Azure Storage.

1. Blader in het Azure Portal of Azure Storage Explorer naar de container locatie waar u het zip-bestand hebt geüpload.

1. Kopieer de URL voor die locatie en zorg ervoor dat u het SAS-token (Shared Access Signature) opneemt.

   Anders wordt het SAS-token niet toegestaan en wordt de implementatie van de SAP ISE-connector mislukt.

1. Voordat u de SAP ISE-connector kunt gebruiken, moet u de connector in uw ISE installeren en implementeren.

   1. Zoek en open uw ISE in de [Azure Portal](https://portal.azure.com).
   
   1. Selecteer in het menu ISE **beheerde connectors**  >  **toevoegen**. Zoek en selecteer **SAP**in de lijst connectors.
   
   1. Plak in het deel venster **een nieuwe beheerde connector toevoegen** , in het vak **SAP package** , de URL voor het zip-bestand dat de SAP-assembly's bevat. *Zorg ervoor dat u het SAS-token opneemt.*

   1. Als u gereed bent, selecteert u **Maken**.

   Zie [ISE-connectors toevoegen](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment)voor meer informatie.

1. Als uw SAP-exemplaar en ISE zich in verschillende virtuele netwerken bevinden, moet u [deze netwerken ook peeren](../virtual-network/tutorial-connect-virtual-networks-portal.md) zodat het virtuele netwerk van uw ISE verbinding heeft met het virtuele netwerk van uw SAP-exemplaar.

<a name="sap-client-library-prerequisites"></a>

### <a name="sap-client-library-prerequisites"></a>Vereisten voor SAP-client bibliotheek

* Zorg ervoor dat u de meest recente versie [van SAP connector (NCo 3,0) installeert voor Microsoft .net 3.0.22.0 gecompileerd met .NET Framework 4,0-Windows 64-bits (x64)](https://softwaredownloads.sap.com/file/0020000001000932019). Eerdere versies kunnen leiden tot compatibiliteits problemen. Zie versies van SAP- [client bibliotheek](#sap-library-versions)voor meer informatie.

* Standaard plaatst het SAP-installatie programma de assembly bestanden in de standaardmap voor installatie. U moet de volgende assembly bestanden naar een andere locatie kopiëren, op basis van uw scenario:

  Voor Logic apps die worden uitgevoerd in een ISE, volgt u de stappen die worden beschreven in de vereisten voor de [integratie service omgeving](#sap-ise). Voor logische apps die worden uitgevoerd in een multi tenant Azure en de on-premises gegevens gateway te gebruiken, kopieert u de assembly bestanden uit de standaardinstallatiemap naar de installatiemap van de gegevens gateway. Als u problemen ondervindt met de gegevens gateway, raadpleegt u de volgende problemen:

  * U moet de 64-bits versie voor de SAP-client bibliotheek installeren omdat de gegevens gateway alleen op 64-bits systemen kan worden uitgevoerd. Anders krijgt u een fout bericht over een onjuiste afbeelding omdat de data gateway-hostservice geen 32-bits-assembly's ondersteunt.

  * Als uw SAP-verbinding mislukt met het fout bericht "Controleer de account gegevens en/of de machtigingen en probeer het opnieuw", de assembly bestanden kunnen zich op de verkeerde locatie bevindt. Zorg ervoor dat u de assembly bestanden hebt gekopieerd naar de installatiemap van de gegevens gateway.

    Om u te helpen problemen op te lossen, [gebruikt u de logboek viewer voor .NET-assembly-binding](https://docs.microsoft.com/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer), waarmee u kunt controleren of de assembly bestanden zich op de juiste locatie bevinden. U kunt eventueel de optie **globale assembly-cache registratie** selecteren wanneer u de SAP-client bibliotheek installeert.

<a name="sap-library-versions"></a>

#### <a name="sap-client-library-versions"></a>SAP-client bibliotheek versies

Eerdere SAP NCo-versies kunnen worden gedeadlockd wanneer meer dan één IDoc-bericht tegelijk wordt verzonden. Deze voor waarde blokkeert alle latere berichten die worden verzonden naar de SAP-bestemming, waardoor er een time-out optreedt voor de berichten.

Dit zijn de relaties tussen de SAP-client bibliotheek, de .NET Framework, de .NET-runtime en de gateway:

* Zowel de micro soft SAP-adapter als de gateway-hostservice gebruiken .NET Framework 4,5.

* De SAP-NCo voor .NET Framework 4,0 werkt met processen die .NET runtime 4,0 gebruiken in 4.7.1.

* De SAP-NCo voor .NET Framework 2,0 werkt met processen die gebruikmaken van .NET runtime 2,0 tot 3,5, maar werkt niet meer met de nieuwste gateway.

### <a name="secure-network-communications-prerequisites"></a>Vereisten voor beveiligde netwerk communicatie

Als u de on-premises gegevens gateway gebruikt met de optionele beveiligde netwerk communicatie (SNC), die alleen wordt ondersteund in azure met meerdere tenants, moet u deze instellingen ook configureren:

* Als u SNC gebruikt met eenmalige aanmelding (SSO), zorg er dan voor dat de gegevens gateway wordt uitgevoerd als een gebruiker die is toegewezen aan de SAP-gebruiker. Als u het standaard account wilt wijzigen, selecteert u **account wijzigen**en voert u de gebruikers referenties in.

  ![Gegevens gateway-account wijzigen](./media/logic-apps-using-sap-connector/gateway-account.png)

* Als u SNC inschakelt met een extern beveiligings product, kopieert u de SNC-bibliotheek of-bestanden op dezelfde computer waarop de gegevens gateway is geïnstalleerd. Enkele voor beelden van SNC-producten zijn [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos en NTLM.

Zie [veilige netwerk communicatie inschakelen](#secure-network-communications)voor meer informatie over het inschakelen van SNC voor de gegevens gateway.

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>Migreren naar de huidige connector

Voer de volgende stappen uit om de migratie uit te voeren vanaf een eerder beheerde SAP-connector (niet ISE) naar de huidige beheerde SAP-connector:

1. Als u dit nog niet hebt gedaan, werkt u de [on-premises gegevens gateway](https://www.microsoft.com/download/details.aspx?id=53127) bij, zodat u over de meest recente versie beschikt. Zie [een on-premises gegevens gateway installeren voor Azure Logic apps](../logic-apps/logic-apps-gateway-install.md)voor meer informatie.

1. Verwijder de actie **verzenden naar SAP** in de logische app die gebruikmaakt van de oudere sap-connector.

1. Voeg de actie **bericht verzenden naar SAP** toe vanuit de meest recente SAP-connector. Voordat u deze actie kunt gebruiken, maakt u de verbinding met uw SAP-systeem opnieuw.

1. Wanneer u klaar bent, slaat u de logische app op.

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>Bericht verzenden naar SAP

In dit voor beeld wordt een logische app gebruikt die u kunt activeren met een HTTP-aanvraag. De logische app verzendt een IDoc naar een SAP-server en retourneert een antwoord naar de aanvrager die de logische app wordt genoemd.

### <a name="add-an-http-request-trigger"></a>Een HTTP-aanvraag trigger toevoegen

In Azure Logic Apps moet elke logische app beginnen met een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), die wordt geactiveerd wanneer een bepaalde gebeurtenis plaatsvindt of wanneer aan een bepaalde voor waarde wordt voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt de Logic Apps-Engine een exemplaar van een logische app en begint de werk stroom van uw app uit te voeren.

> [!NOTE]
> Wanneer een logische app IDoc pakketten van SAP ontvangt, wordt het ' Plain ' XML-schema dat door SAP WE60 IDoc-documentatie is gegenereerd niet door de [aanvraag trigger](https://docs.microsoft.com/azure/connectors/connectors-native-reqres) ondersteund. Het ' Plain ' XML-schema wordt echter ondersteund voor scenario's waarin berichten van Logic apps *naar SAP worden* verzonden. U kunt de aanvraag trigger met SAP IDoc XML gebruiken, maar niet met IDoc via RFC. Of u kunt de XML omzetten in de gewenste indeling. 

In dit voor beeld maakt u een logische app met een eind punt in azure, zodat u *http post-aanvragen* kunt verzenden naar uw logische app. Wanneer uw logische app deze HTTP-aanvragen ontvangt, wordt de trigger geactiveerd en wordt de volgende stap in uw werk stroom uitgevoerd.

1. Maak in de [Azure Portal](https://portal.azure.com)een lege logische app, waarmee de ontwerp functie voor logische apps wordt geopend.

1. Voer in het zoekvak in `http request` als uw filter. Selecteer in de lijst **Triggers** **Wanneer een HTTP-aanvraag wordt ontvangen**.

   ![HTTP-aanvraag trigger toevoegen](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Sla de logische app nu op zodat u een eind punt-URL kunt genereren voor uw logische app. Selecteer **Opslaan**op de werk balk van de ontwerp functie.

   De eind punt-URL wordt nu weer gegeven in de trigger, bijvoorbeeld:

   ![URL voor eind punt genereren](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Een SAP-actie toevoegen

In Azure Logic Apps is een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) een stap in uw werk stroom die volgt op een trigger of een andere actie. Als u nog geen trigger hebt toegevoegd aan uw logische app en u dit voor beeld wilt volgen, [voegt u de in deze sectie beschreven trigger toe](#add-trigger).

1. Selecteer in de Logic app Designer onder de trigger **nieuwe stap**.

   ![Nieuwe stap toevoegen aan logische app](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Voer in het zoekvak in `sap` als uw filter. Selecteer in de lijst **acties** de optie **bericht naar SAP verzenden**.
  
   ![Selecteer de actie bericht verzenden naar SAP](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   U kunt ook het tabblad **onderneming** selecteren en de SAP-actie selecteren.

   ![Selecteer de actie bericht verzenden naar SAP van het tabblad onderneming](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Als uw verbinding al bestaat, gaat u door met de volgende stap, zodat u uw SAP-actie kunt instellen. Als u echter om de verbindings gegevens wordt gevraagd, geeft u de informatie op zodat u een verbinding kunt maken met uw on-premises SAP-server.

   1. Geef een naam op voor de verbinding.

   1. Als u de gegevens gateway gebruikt, voert u de volgende stappen uit:
   
      1. Selecteer in de sectie **gegevens gateway** , onder **abonnement**, eerst het Azure-abonnement voor de gegevens gateway resource die u hebt gemaakt in de Azure portal voor uw data Gateway-installatie.
   
      1. Onder **verbindings gateway**selecteert u uw gegevens gateway resource in Azure.

   1. Ga door met het verstrekken van informatie over de verbinding. Volg voor de eigenschap **aanmeldings type** de stap op basis van het feit of de eigenschap is ingesteld op **toepassings server** of **groep**:
   
      * Voor **toepassings server**zijn deze eigenschappen, die meestal optioneel worden weer gegeven, vereist:

        ![Een SAP-toepassings server verbinding maken](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Voor **groep**zijn deze eigenschappen, die meestal optioneel worden weer gegeven, vereist:

        ![SAP Message server-verbinding maken](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Standaard wordt met sterk typen gecontroleerd op ongeldige waarden door XML-validatie uit te voeren op basis van het schema. Dit gedrag kan u helpen bij het detecteren van eerder gemaakte problemen. De optie voor **veilig typen** is beschikbaar voor achterwaartse compatibiliteit en controleert alleen de lengte van de teken reeks. Meer informatie over de [optie voor veilig typen](#safe-typing).

   1. Wanneer u klaar bent, selecteert u **maken**.

      Logic Apps stelt uw verbinding in en test deze om te controleren of de verbinding goed werkt.

1. Nu vindt u een actie op de SAP-server en selecteert u deze.

   1. Selecteer het mappictogram in het vak **actie van SAP** . Zoek en selecteer in de lijst bestand het SAP-bericht dat u wilt gebruiken. Gebruik de pijlen om door de lijst te navigeren.

      In dit voor beeld wordt een IDoc geselecteerd met het type **Orders** .

      ![Zoek en selecteer IDoc-actie](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Als de gewenste actie niet kan worden gevonden, kunt u hand matig een pad invoeren, bijvoorbeeld:

      ![Hand matig het pad naar de IDoc-actie opgeven](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Geef de waarde voor **SAP-actie** op via de expressie-editor. Op die manier kunt u dezelfde actie voor verschillende bericht typen gebruiken.

      Zie [bericht schema's voor IDOC-bewerkingen](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)voor meer informatie over IDOC-bewerkingen.

   1. Klik in het vak **invoer bericht** zodat de lijst met dynamische inhoud wordt weer gegeven. Selecteer in deze lijst, onder **Wanneer een HTTP-aanvraag wordt ontvangen**, het veld **hoofd tekst** .

      In deze stap wordt de inhoud van de hoofd tekst van uw HTTP-aanvraag trigger opgenomen en wordt die uitvoer naar uw SAP-server verzonden.

      ![Selecteer de eigenschap Body in de trigger](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Wanneer u klaar bent, ziet uw SAP-actie eruit als in dit voor beeld:

      ![De SAP-actie volt ooien](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Sla uw logische app op. Selecteer **Opslaan**op de werk balk van de ontwerp functie.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Een HTTP-antwoord actie toevoegen

Voeg nu een reactie actie toe aan de werk stroom van uw logische app en neem de uitvoer op uit de SAP-actie. Op die manier retourneert uw logische app de resultaten van uw SAP-server naar de oorspronkelijke aanvrager.

1. Selecteer in de ontwerp functie van de Logic app, onder de actie SAP, de optie **nieuwe stap**.

1. Voer in het zoekvak in `response` als uw filter. Selecteer in de lijst **acties** de optie **antwoord**.

1. Klik in het vak **hoofd tekst** zodat de lijst met dynamische inhoud wordt weer gegeven. Selecteer in de lijst onder **bericht naar SAP verzenden**het veld **hoofd tekst** .

   ![SAP-actie volt ooien](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Sla uw logische app op.

### <a name="test-your-logic-app"></a>Uw logische app testen

1. Als uw logische app nog niet is ingeschakeld, selecteert u **overzicht**in het menu van de logische app. Selecteer **inschakelen**op de werk balk.

1. Selecteer **uitvoeren**op de werk balk van de ontwerp functie. Met deze stap wordt de logische app hand matig gestart.

1. Activeer uw logische app door een HTTP POST-aanvraag te verzenden naar de URL in uw HTTP-aanvraag trigger.
Neem uw bericht inhoud op met uw aanvraag. U kunt een hulp programma zoals [postman](https://www.getpostman.com/apps)gebruiken om de aanvraag te verzenden.

   Voor dit artikel verzendt de aanvraag een IDoc-bestand, dat moet de XML-indeling hebben en de naam ruimte bevatten voor de SAP-actie die u gebruikt, bijvoorbeeld:

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Nadat u uw HTTP-aanvraag hebt verzonden, moet u wachten op de reactie van de logische app.

   > [!NOTE]
   > Mogelijk is er een time-out opgetreden voor uw logische app als alle stappen die vereist zijn voor het antwoord niet binnen de [time-outlimiet](./logic-apps-limits-and-config.md)van de aanvraag vallen. Als dit probleem optreedt, worden aanvragen mogelijk geblokkeerd. Lees hoe u [uw Logic apps kunt controleren en controleren](../logic-apps/monitor-logic-apps.md)voor meer informatie over het vaststellen van problemen.

U hebt nu een logische app gemaakt die kan communiceren met uw SAP-server. Nu u een SAP-verbinding hebt ingesteld voor uw logische app, kunt u andere beschik bare SAP-acties, zoals BAPI en RFC, verkennen.

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>Bericht ontvangen van SAP

In dit voor beeld wordt een logische app gebruikt die wordt geactiveerd wanneer de app een bericht van een SAP-systeem ontvangt.

### <a name="add-an-sap-trigger"></a>Een SAP-trigger toevoegen

1. Maak in de Azure Portal een lege logische app, waarmee de ontwerp functie voor logische apps wordt geopend.

1. Voer in het zoekvak in `sap` als uw filter. Selecteer in de lijst **Triggers** **Wanneer een bericht wordt ontvangen van SAP**.

   ![SAP-trigger toevoegen](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   U kunt ook het tabblad **onderneming** selecteren en vervolgens de trigger selecteren:

   ![SAP-trigger toevoegen vanuit het tabblad onderneming](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Als uw verbinding al bestaat, gaat u door met de volgende stap, zodat u uw SAP-actie kunt instellen. Als u echter om de verbindings gegevens wordt gevraagd, geeft u de informatie op zodat u nu een verbinding met uw on-premises SAP-server kunt maken.

   1. Geef een naam op voor de verbinding.

   1. Als u de gegevens gateway gebruikt, voert u de volgende stappen uit:

      1. Selecteer in de sectie **gegevens gateway** , onder **abonnement**, eerst het Azure-abonnement voor de gegevens gateway resource die u hebt gemaakt in de Azure portal voor uw data Gateway-installatie.

      1. Onder **verbindings gateway**selecteert u uw gegevens gateway resource in Azure.

   1. Ga door met het verstrekken van informatie over de verbinding. Volg voor de eigenschap **aanmeldings type** de stap op basis van het feit of de eigenschap is ingesteld op **toepassings server** of **groep**:

      * Voor **toepassings server**zijn deze eigenschappen, die meestal optioneel worden weer gegeven, vereist:

        ![Een SAP-toepassings server verbinding maken](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Voor **groep**zijn deze eigenschappen, die meestal optioneel worden weer gegeven, vereist:

        ![SAP Message server-verbinding maken](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Standaard wordt met sterk typen gecontroleerd op ongeldige waarden door XML-validatie uit te voeren op basis van het schema. Dit gedrag kan u helpen bij het detecteren van eerder gemaakte problemen. De optie voor **veilig typen** is beschikbaar voor achterwaartse compatibiliteit en controleert alleen de lengte van de teken reeks. Meer informatie over de [optie voor veilig typen](#safe-typing).

   1. Wanneer u klaar bent, selecteert u **maken**.

      Logic Apps stelt uw verbinding in en test deze om te controleren of de verbinding goed werkt.

1. Geef de [vereiste para meters](#parameters) op op basis van de configuratie van uw SAP-systeem. 

   U kunt [de berichten die u ontvangt van uw SAP-server filteren door een lijst met SAP-acties op te geven](#filter-with-sap-actions).

   U kunt een SAP-actie selecteren in de bestands kiezer:

   ![SAP-actie toevoegen aan de logische app](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   U kunt ook hand matig een actie opgeven:

   ![SAP-actie hand matig invoeren](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Hier volgt een voor beeld waarin wordt getoond hoe de actie wordt weer gegeven wanneer u de trigger zo instelt dat er meer dan één bericht wordt ontvangen.

   ![Trigger voorbeeld dat meerdere berichten ontvangt](media/logic-apps-using-sap-connector/example-trigger.png)

   Zie [bericht schema's voor IDOC-bewerkingen](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations) voor meer informatie over de SAP-actie.

1. Sla de logische app nu op zodat u berichten van uw SAP-systeem kunt ontvangen. Selecteer **Opslaan**op de werk balk van de ontwerp functie.

Uw logische app is nu klaar om berichten te ontvangen van uw SAP-systeem.

> [!NOTE]
> De SAP-trigger is geen polling-trigger, maar is in plaats daarvan een webhook-trigger. Als u de gegevens gateway gebruikt, wordt de trigger alleen vanuit de gegevens gateway aangeroepen wanneer er een bericht bestaat. er is dus geen polling nodig.

<a name="parameters"></a>

#### <a name="parameters"></a>Parameters

De SAP-connector accepteert samen met eenvoudige teken reeks-en nummer invoer de volgende tabel parameters ( `Type=ITAB` invoer):

* Tabel richting para meters, zowel invoer als uitvoer, voor oudere SAP-releases.
* Para meters wijzigen, waardoor de tabel richtings parameters worden vervangen door nieuwere SAP-releases.
* Hiërarchische tabel parameters

<a name="filter-with-sap-actions"></a>

#### <a name="filter-with-sap-actions"></a>Filteren met SAP-acties

U kunt desgewenst de berichten die uw logische app ontvangt van uw SAP-server filteren door een lijst, of matrix, te leveren met een of meer SAP-acties. Deze matrix is standaard leeg, wat betekent dat uw logische app alle berichten van uw SAP-server ontvangt zonder te filteren. 

Wanneer u het matrix filter instelt, ontvangt de trigger alleen berichten van de opgegeven SAP-actie typen en worden alle andere berichten van uw SAP-server geweigerd. Dit filter is echter niet van invloed op of het type van de ontvangen Payload zwak of sterk is.

SAP-actie filters worden uitgevoerd op het niveau van de SAP-adapter voor uw on-premises gegevens gateway. Zie [test IDocs verzenden naar Logic apps vanuit SAP](#send-idocs-from-sap)voor meer informatie.

Als u geen IDoc-pakketten van SAP kunt verzenden naar de trigger van de logische app, raadpleegt u het afkeurings bericht voor transactionele RFC (tRFC)-oproep in het dialoog venster SAP tRFC (T-code SM58). In de SAP-interface worden mogelijk de volgende fout berichten weer geven die zijn afgekapt als gevolg van de limieten voor de subtekenreeksen van het veld **status tekst** .

* `The RequestContext on the IReplyChannel was closed without a reply being`: Onverwachte fouten treden op wanneer de catch-all-handler voor het kanaal het kanaal beëindigt vanwege een fout en het kanaal opnieuw opbouwt om andere berichten te verwerken.

  * Als u wilt bevestigen dat uw logische app de IDoc heeft ontvangen, [voegt u een reactie actie toe](../connectors/connectors-native-reqres.md#add-a-response-action) die een `200 OK` status code retourneert. De IDoc wordt getransporteerd via tRFC, die geen respons lading toestaat.

  * Als u in plaats daarvan het IDoc moet afwijzen, reageert u met een andere HTTP-status code dan `200 OK` zodat de SAP-adapter een uitzonde ring voor uw naam terugstuurt naar SAP. 

* `The segment or group definition E2EDK36001 was not found in the IDoc meta`: Verwachte fouten worden veroorzaakt door andere fouten, zoals de fout bij het genereren van een IDoc XML-nettolading, omdat de bijbehorende segmenten niet worden vrijgegeven door SAP, waardoor de vereiste meta gegevens voor het segment type voor de conversie ontbreken. 

  * Neem contact op met de ABAP-Engineer voor uw SAP-systeem om deze segmenten te kunnen vrijgeven door SAP.

<a name="find-extended-error-logs"></a>

#### <a name="find-extended-error-logs"></a>Uitgebreide fout logboeken zoeken

Controleer de uitgebreide logboeken van de SAP-adapter voor volledige fout berichten. 

Voor on-premises gegevens gateway releases van juni 2020 en hoger kunt u [Gateway logboeken inschakelen in de app-instellingen](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app).

Voor on-premises gegevens gateway releases van 2020 april en eerder zijn Logboeken standaard uitgeschakeld. Als u uitgebreide logboeken wilt ophalen, voert u de volgende stappen uit:

1. Open het bestand in de installatiemap van de on-premises gegevens gateway `Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config` . 

1. Wijzig voor de instelling **SapExtendedTracing** de waarde van **False** in **waar**.

1. Indien gewenst moet u voor minder gebeurtenissen de waarde van **SapTracingLevel** wijzigen van **Informational** (standaard) in **fout** of **waarschuwing**. Of, voor meer gebeurtenissen, wijzigt u **informatie** in **uitgebreid**.

1. Sla het configuratiebestand op.

1. Start uw gegevens Gateway opnieuw op. Open de installatie-app van uw on-premises gegevens gateway en ga naar het menu **Service-instellingen** . Selecteer onder **de gateway opnieuw opstarten** **nu opnieuw opstarten**.

1. Reproduceer het probleem.

1. Uw gateway logboeken exporteren. Ga in de data Gateway-installatie-app naar het menu **Diagnostische** gegevens. Onder **Gateway logboeken**selecteert u **Logboeken exporteren**. Deze bestanden bevatten SAP-logboeken die op datum zijn ingedeeld. Afhankelijk van de grootte van het logboek, kunnen er meerdere logboek bestanden voor één datum bestaan.

1. In het configuratie bestand herstelt u de instelling **SapExtendedTracing** in **Onwaar**.

1. Start de gateway opnieuw.

### <a name="test-your-logic-app"></a>Uw logische app testen

1. Als u uw logische app wilt activeren, verzendt u een bericht van uw SAP-systeem.

1. Selecteer **overzicht**in het menu van de logische app. Bekijk de **uitvoerings geschiedenis** voor nieuwe uitvoeringen voor uw logische app.

1. Open de meest recente uitvoering, waarin het bericht wordt weer gegeven dat vanuit uw SAP-systeem is verzonden in de sectie trigger uitvoer.

<a name="send-idocs-from-sap"></a>

### <a name="test-sending-idocs-from-sap"></a>Verzenden van IDocs vanuit SAP testen

Als u IDocs van SAP naar uw logische app wilt verzenden, moet u de volgende minimale configuratie configureren:

> [!IMPORTANT]
> Gebruik deze stappen alleen wanneer u uw SAP-configuratie test met uw logische app. Productie omgevingen vereisen aanvullende configuratie.

1. [Een RFC-doel configureren in SAP](#create-rfc-destination)

1. [Een ABAP-verbinding maken met uw RFC-doel](#create-abap-connection)

1. [Een ontvanger poort maken](#create-receiver-port)

1. [Een zender poort maken](#create-sender-port)

1. [Een logische systeem partner maken](#create-logical-system-partner)

1. [Een partner profiel maken](#create-partner-profiles)

1. [Testen van berichten verzenden](#test-sending-messages)

#### <a name="create-rfc-destination"></a>RFC-doel maken

1. Als u de **configuratie van instellingen voor een RFC-verbinding** wilt openen, gebruikt u in uw SAP-interface de **sm59** -transactie code (T code) met het voor voegsel **/n** .

1. Selecteer **TCP/IP-verbindingen**  >  **maken**.

1. Maak een nieuwe RFC-doel met de volgende instellingen:
    
    * Voer een naam in voor uw **RFC-doel**.
    
    * Selecteer op het tabblad **technische instellingen** bij **activerings type**de optie **geregistreerd server programma**. Voer een waarde in voor de **programma-id**. In SAP wordt de trigger van uw logische app geregistreerd met behulp van deze id.
    
    * Op het tabblad **Unicode** , voor **communicatie type met doel systeem**, selecteert u **Unicode**.

1. Sla uw wijzigingen op.

1. Registreer uw nieuwe **programma-id** bij Azure Logic apps.

1. Als u de verbinding wilt testen, selecteert u in de SAP-interface, onder uw nieuwe **RFC-doel**, de optie **verbinding testen**.

#### <a name="create-abap-connection"></a>ABAP-verbinding maken

1. Als u de **configuratie van instellingen voor een RFC-verbinding** wilt openen, gebruikt u in uw SAP-interface de **sm59***-transactie code (T code) met het voor voegsel **/n** .

1. Selecteer **ABAP-verbindingen**  >  **maken**.

1. Voor **RFC-doel**voert u de id in voor [het test-SAP-systeem](#create-rfc-destination).

1. Sla uw wijzigingen op.

1. Als u de verbinding wilt testen, selecteert u **verbindings test** .

#### <a name="create-receiver-port"></a>Ontvanger poort maken

1. Als u de **poorten in de IDOC-verwerkings** instellingen wilt openen, gebruikt u in uw SAP-interface de **we21** -transactie code (T code) met het voor voegsel **/n** .

1. Selecteer **poorten**  >  **transactionele RFC**  >  **Create**.

1. In het dialoog venster instellingen dat wordt geopend, selecteert u **eigen poort naam**. Voer een **naam**in voor de test poort. Sla uw wijzigingen op.

1. In de instellingen voor uw nieuwe ontvanger poort, voor **RFC-doel**, voert u de id in voor [de test-RFC-bestemming](#create-rfc-destination).

1. Sla uw wijzigingen op.

#### <a name="create-sender-port"></a>Poort van afzender maken

1.  Als u de **poorten in de IDOC-verwerkings** instellingen wilt openen, gebruikt u in uw SAP-interface de **we21** -transactie code (T code) met het voor voegsel **/n** .

1. Selecteer **poorten**  >  **transactionele RFC**  >  **Create**.

1. In het dialoog venster instellingen dat wordt geopend, selecteert u **eigen poort naam**. Voer voor uw test poort een **naam** in die begint met **SAP**. Alle poort namen van de afzender moeten beginnen met de letters **SAP**, bijvoorbeeld **SAPTEST**. Sla uw wijzigingen op.

1. Voer in de instellingen voor de nieuwe zender poort voor **RFC-doel**de id in voor [uw ABAP-verbinding](#create-abap-connection).

1. Sla uw wijzigingen op.

#### <a name="create-logical-system-partner"></a>Een logische systeem partner maken

1. Als u de **wijzigings weergave logische systemen wilt openen: overzichts** instellingen in uw SAP-interface, gebruikt u de **bd54** -transactie code (T-code).

1. Accepteer het waarschuwings bericht dat wordt weer gegeven: **Waarschuwing: de tabel is kruislings-client**

1. Selecteer in de lijst met de bestaande logische systemen **nieuwe vermeldingen**.

1. Voer voor het nieuwe logische systeem een **Log.System** -id en een korte **naam** beschrijving in. Sla uw wijzigingen op.

1. Wanneer de **prompt voor Workbench** wordt weer gegeven, maakt u een nieuwe aanvraag door een beschrijving op te geven, of als u al een aanvraag hebt gemaakt, slaat u deze stap over.

1. Nadat u de workbench-aanvraag hebt gemaakt, koppelt u die aanvraag aan de tabel update-aanvraag. Sla de wijzigingen op om te bevestigen dat de tabel is bijgewerkt.

#### <a name="create-partner-profiles"></a>Partner profielen maken

Voor productie omgevingen moet u twee partner profielen maken. Het eerste profiel is voor de afzender, dat wil zeggen uw organisatie en het SAP-systeem. Het tweede profiel is voor de ontvanger, de logische app.

1. Als u de instellingen van de **partner profielen** wilt openen, gebruikt u in uw SAP-interface de **we20** -transactie code (T code) met het voor voegsel **/n** .

1. Onder **partner profielen**selecteert u **partner type LS**  >  **Create**.

1. Maak een nieuw Partner profiel met de volgende instellingen:

    * Voer [de id van uw logische systeem partner](#create-logical-system-partner)in als **partner nummer**.

    * Voor **Parts. Typ** **ls**.

    * Voer voor **agent**de id in voor het SAP-gebruikers account dat moet worden gebruikt wanneer u programma-id's registreert voor Azure Logic apps of andere niet-SAP-systemen.

1. Sla uw wijzigingen op. Als u [de logische systeem partner](#create-logical-system-partner)nog niet hebt gemaakt, krijgt u de fout melding **een geldig partner nummer**.

1. Selecteer in de instellingen van uw partner profiel onder **uitgaande parmtrs.** de optie **uitgaande para meter maken**.

1. Maak een nieuwe uitgaande para meter met de volgende instellingen:

    * Voer uw **bericht type**in, bijvoorbeeld **CREMAS**.

    * Voer de [id van de poort van de ontvanger](#create-receiver-port)in.

    * Voer een IDoc-grootte in voor het **pakket. Grootte**. Als u [IDocs een voor een wilt verzenden vanuit SAP](#receive-idoc-packets-from-sap), selecteert u **onmiddellijk door geven IDOC**.

1. Sla uw wijzigingen op.

#### <a name="test-sending-messages"></a>Testen van berichten verzenden

1. Als u het **test programma voor IDOC-verwerkings** instellingen wilt openen, gebruikt u in uw SAP-interface de **we19** -transactie code (T code) met het voor voegsel **/n** .

1. Selecteer onder **sjabloon voor testen**de optie **via bericht type**en voer uw bericht type in, bijvoorbeeld **CREMAS**. Selecteer **Maken**.

1. Bevestig het **IDOC-type?** bericht door **door gaan**te selecteren.

1. Selecteer het knoop punt **EDIDC** . Voer de juiste waarden in voor uw ontvanger en de poorten van de afzender. Selecteer **Doorgaan**.

1. Selecteer **standaard uitgaande verwerking**.

1. Selecteer **door gaan**als u de verwerking van uitgaande IDOC wilt starten. Wanneer de verwerking is voltooid, wordt de **IDOC verzonden naar het SAP-systeem of het externe programma** bericht weer gegeven.

1.  Als u de verwerkings fouten wilt controleren, gebruikt u de **SM58** -transactie code (T code) met het voor voegsel **/n** .

## <a name="receive-idoc-packets-from-sap"></a>IDoc pakketten ontvangen van SAP

U kunt SAP instellen voor het [verzenden van IDocs in pakketten](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html), die batches of groepen van IDocs zijn. Voor het ontvangen van IDoc-pakketten, de SAP-connector en de specifiek de trigger, hebt u geen extra configuratie nodig. Als u elk item in een IDoc-pakket echter wilt verwerken nadat de trigger het pakket heeft ontvangen, zijn er aanvullende stappen vereist om het pakket te splitsen in afzonderlijke IDocs.

Hier volgt een voor beeld waarin wordt uitgelegd hoe u afzonderlijke IDocs uit een pakket kunt ophalen met behulp van de [ `xpath()` functie](./workflow-definition-language-functions-reference.md#xpath):

1. Voordat u begint, hebt u een logische app met een SAP-trigger nodig. Als u deze logische app nog niet hebt, volgt u de vorige stappen in dit onderwerp om [een logische app in te stellen met een SAP-trigger](#receive-from-sap).

   Bijvoorbeeld:

   ![SAP-trigger toevoegen aan de logische app](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Haal de hoofd naam ruimte op uit de XML-IDoc die uw logische app van SAP ontvangt. Als u deze naam ruimte uit het XML-document wilt extra heren, voegt u een stap toe die een lokale teken reeks variabele maakt en slaat die naam ruimte op met behulp van een `xpath()` expressie:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Hoofd naam ruimte ophalen van IDoc](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Als u een afzonderlijke IDoc wilt extra heren, voegt u een stap toe waarmee een matrix variabele wordt gemaakt en wordt de IDoc-verzameling opgeslagen met behulp van een andere `xpath()` expressie:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Matrix van items ophalen](./media/logic-apps-using-sap-connector/get-array.png)

   De matrix variabele maakt elk IDoc beschikbaar voor uw logische app door het inventariseren van de verzameling. In dit voor beeld stuurt de logische app elke IDoc naar een SFTP-server met behulp van een lus:

   ![IDoc naar SFTP-server verzenden](./media/logic-apps-using-sap-connector/loop-batch.png)

   Elke IDoc moet de hoofd naam ruimte bevatten. Dit is de reden waarom de bestands inhoud in een- `<Receive></Receive` element samen met de hoofd naam ruimte wordt verpakt voordat de IDOC naar de downstream-app of de sftp-server in dit geval wordt verzonden.

U kunt de Quick Start-sjabloon voor dit patroon gebruiken door deze sjabloon te selecteren in de ontwerp functie voor logische apps wanneer u een nieuwe logische app maakt.

![Sjabloon voor batch Logic app selecteren](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Schema's genereren voor artefacten in SAP

In dit voor beeld wordt een logische app gebruikt die u kunt activeren met een HTTP-aanvraag. Voor het genereren van de schema's voor de opgegeven IDoc en BAPI, verzendt het SAP-actie **schema** een aanvraag naar een SAP-systeem.

Deze SAP-actie retourneert een XML-schema, niet de inhoud of gegevens van het XML-document zelf. Schema's die in het antwoord worden geretourneerd, worden geüpload naar een integratie account met behulp van de Azure Resource Manager-connector. Schema's bevatten de volgende onderdelen:

* De structuur van het aanvraag bericht. Gebruik deze informatie om uw BAPI- `get` lijst te maken.
* De structuur van het antwoord bericht. Gebruik deze informatie voor het parseren van het antwoord. 

Als u het aanvraag bericht wilt verzenden, gebruikt u de algemene SAP-actie **bericht verzenden naar SAP**of de BAPI-acties van de doel **oproep** .

### <a name="add-an-http-request-trigger"></a>Een HTTP-aanvraag trigger toevoegen

1. Maak in de Azure Portal een lege logische app, waarmee de ontwerp functie voor logische apps wordt geopend.

1. Voer in het zoekvak in `http request` als uw filter. Selecteer in de lijst **Triggers** **Wanneer een HTTP-aanvraag wordt ontvangen**.

   ![HTTP-aanvraag trigger toevoegen](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Sla de logische app nu op zodat u een eind punt-URL kunt genereren voor uw logische app.
Selecteer **Opslaan**op de werk balk van de ontwerp functie.

   De eind punt-URL wordt nu weer gegeven in de trigger, bijvoorbeeld:

   ![URL voor eind punt genereren](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Een SAP-actie toevoegen om schema's te genereren

1. Selecteer in de Logic app Designer onder de trigger **nieuwe stap**.

   ![Nieuwe stap toevoegen aan logische app](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Voer in het zoekvak in `sap` als uw filter. Selecteer in de lijst **acties** de optie **schema's genereren**.
  
   ![Actie ' schema's genereren ' toevoegen aan logische app](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   U kunt ook het tabblad **onderneming** selecteren en de SAP-actie selecteren.

   ![SAP-verzend actie selecteren op het tabblad onderneming](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Als uw verbinding al bestaat, gaat u door met de volgende stap, zodat u uw SAP-actie kunt instellen. Als u echter om de verbindings gegevens wordt gevraagd, geeft u de informatie op zodat u nu een verbinding met uw on-premises SAP-server kunt maken.

   1. Geef een naam op voor de verbinding.

   1. Selecteer in de sectie **gegevens gateway** , onder **abonnement**, eerst het Azure-abonnement voor de gegevens gateway resource die u hebt gemaakt in de Azure portal voor uw data Gateway-installatie. 
   
   1. Onder **verbindings gateway**selecteert u uw gegevens gateway resource in Azure.

   1. Ga door met het verstrekken van informatie over de verbinding. Volg voor de eigenschap **aanmeldings type** de stap op basis van het feit of de eigenschap is ingesteld op **toepassings server** of **groep**:
   
      * Voor **toepassings server**zijn deze eigenschappen, die meestal optioneel worden weer gegeven, vereist:

        ![Een SAP-toepassings server verbinding maken](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Voor **groep**zijn deze eigenschappen, die meestal optioneel worden weer gegeven, vereist:

        ![SAP Message server-verbinding maken](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Standaard wordt met sterk typen gecontroleerd op ongeldige waarden door XML-validatie uit te voeren op basis van het schema. Dit gedrag kan u helpen bij het detecteren van eerder gemaakte problemen. De optie voor **veilig typen** is beschikbaar voor achterwaartse compatibiliteit en controleert alleen de lengte van de teken reeks. Meer informatie over de [optie voor veilig typen](#safe-typing).

   1. Wanneer u klaar bent, selecteert u **maken**.

      Logic Apps stelt uw verbinding in en test deze om te controleren of de verbinding goed werkt.

1. Geef het pad op naar het artefact waarvoor u het schema wilt genereren.

   U kunt de SAP-actie selecteren via de bestands kiezer:

   ![SAP-actie selecteren](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   U kunt de actie ook hand matig invoeren:

   ![SAP-actie hand matig invoeren](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Als u schema's wilt genereren voor meer dan één artefact, geeft u de SAP-actie Details voor elk artefact op, bijvoorbeeld:

   ![Selecteer Nieuw item toevoegen](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Twee items weer geven](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Zie [bericht schema's voor IDOC-bewerkingen](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)voor meer informatie over de SAP-actie.

1. Sla uw logische app op. Selecteer **Opslaan**op de werk balk van de ontwerp functie.

### <a name="test-your-logic-app"></a>Uw logische app testen

1. Selecteer op de werk balk van de ontwerp functie **uitvoeren** om een uitvoering voor uw logische app te activeren.

1. Open de uitvoering en controleer de uitvoer voor de actie **Schema's genereren** .

   In de uitvoer worden de gegenereerde schema's voor de opgegeven lijst met berichten weer gegeven.

### <a name="upload-schemas-to-an-integration-account"></a>Schema's uploaden naar een integratie account

U kunt de gegenereerde schema's ook downloaden of opslaan in opslag plaatsen, zoals een blob-, opslag-of integratie account. Integratie accounts bieden een eersteklas ervaring met andere XML-acties, dus in dit voor beeld ziet u hoe u schema's uploadt naar een integratie account voor dezelfde logische app met behulp van de Azure Resource Manager-connector.

1. Selecteer in de Logic app Designer onder de trigger **nieuwe stap**.

1. Voer in het zoekvak in `Resource Manager` als uw filter. Selecteer **een resource maken of bijwerken**.

   ![Azure Resource Manager actie selecteren](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Voer de Details voor de actie in, met inbegrip van uw Azure-abonnement, Azure-resource groep en integratie account. Om SAP-tokens toe te voegen aan de velden, klikt u in de vakken voor die velden en selecteert u in de lijst met dynamische inhoud die wordt weer gegeven.

   1. Open de lijst **nieuwe para meter toevoegen** en selecteer de velden **locatie** en **Eigenschappen** .

   1. Geef details op voor deze nieuwe velden, zoals wordt weer gegeven in dit voor beeld.

      ![Details invoeren voor Azure Resource Manager actie](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   Met de SAP-actie **Schema's genereren** worden schema's gegenereerd als een verzameling, zodat de ontwerp functie automatisch een **voor elke** lus toevoegt aan de actie. Hier volgt een voor beeld waarin wordt getoond hoe deze actie wordt weer gegeven:

   ![Azure Resource Manager actie met ' voor elke ' lus](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > De schema's maken gebruik van base64-gecodeerde indeling. Als u de schema's naar een integratie account wilt uploaden, moeten ze worden gedecodeerd met behulp van de `base64ToString()` functie. Hier volgt een voor beeld waarin de code voor het element wordt weer gegeven `"properties"` :
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Sla uw logische app op. Selecteer **Opslaan**op de werk balk van de ontwerp functie.

### <a name="test-your-logic-app"></a>Uw logische app testen

1. Selecteer op de werk balk van de ontwerp functie **uitvoeren** om de logische app hand matig te activeren.

1. Als de uitvoering is geslaagd, gaat u naar het integratie account en controleert u of de gegenereerde schema's bestaan.

<a name="secure-network-communications"></a>

## <a name="enable-secure-network-communications"></a>Veilige netwerk communicatie inschakelen

Voordat u begint, moet u ervoor zorgen dat u voldoet aan de eerder vermelde [vereisten](#pre-reqs), die alleen van toepassing zijn wanneer u de gegevens gateway en Logic apps uitvoert in een multi tenant-Azure:

* Zorg ervoor dat de on-premises gegevens gateway is geïnstalleerd op een computer die zich in hetzelfde netwerk bevindt als uw SAP-systeem.

* Voor eenmalige aanmelding (SSO) wordt de gegevens gateway uitgevoerd als een gebruiker die is toegewezen aan een SAP-gebruiker.

* De SNC-bibliotheek die de aanvullende beveiligings functies biedt, wordt geïnstalleerd op dezelfde computer als de gegevens gateway. Enkele voor beelden zijn [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos en NTLM.

   Als u SNC voor uw aanvragen naar of van het SAP-systeem wilt inschakelen, schakelt u het selectie vakje **SNC gebruiken** in de SAP-verbinding in en geeft u de volgende eigenschappen op:

   ![SAP-SNC configureren in verbinding](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Eigenschap | Beschrijving |
   |----------| ------------|
   | **Pad naar SNC-bibliotheek** | De naam of het pad van de SNC-bibliotheek ten opzichte van de NCo-installatie locatie of het absolute pad. Voor beelden zijn `sapsnc.dll` of `.\security\sapsnc.dll` of `c:\security\sapsnc.dll` . |
   | **SNC SSO** | Wanneer u verbinding maakt via SNC, wordt de SNC-identiteit meestal gebruikt voor het verifiëren van de oproepende functie. Een andere optie is om te overschrijven zodat gebruikers-en wachtwoord gegevens kunnen worden gebruikt voor het verifiëren van de oproepende functie, maar de regel wordt nog steeds versleuteld. |
   | **Mijn naam SNC** | In de meeste gevallen kan deze eigenschap worden wegge laten. De geïnstalleerde SNC-oplossing kent meestal een eigen SNC-naam. Alleen voor oplossingen die ondersteuning bieden voor meerdere identiteiten, moet u mogelijk de identiteit opgeven die moet worden gebruikt voor deze specifieke bestemming of server. |
   | **Naam van SNC-partner** | De naam voor de back-end-SNC. |
   | **SNC-kwaliteit van beveiliging** | De Quality of service die moet worden gebruikt voor SNC-communicatie van deze specifieke bestemming of server. De standaard waarde wordt gedefinieerd door het back-end-systeem. De maximum waarde wordt gedefinieerd door het beveiligings product dat voor SNC wordt gebruikt. |
   |||

   > [!NOTE]
   > Stel de omgevings variabelen SNC_LIB en SNC_LIB_64 niet in op de computer waar u de gegevens gateway en de SNC-bibliotheek hebt. Als deze instelling is ingesteld, hebben ze voor rang op de waarde van de SNC-bibliotheek die via de connector is door gegeven.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Veilig typen

Wanneer u uw SAP-verbinding maakt, wordt standaard een sterk type gebruikt om te controleren op ongeldige waarden door XML-validatie uit te voeren op basis van het schema. Dit gedrag kan u helpen bij het detecteren van eerder gemaakte problemen. De optie voor **veilig typen** is beschikbaar voor achterwaartse compatibiliteit en controleert alleen de lengte van de teken reeks. Als u kiest voor **veilig typen**, worden het type DATS en het type TIM'S in SAP behandeld als teken reeksen in plaats van als XML-equivalenten, `xs:date` en `xs:time` , waar `xmlns:xs="http://www.w3.org/2001/XMLSchema"` . Een veilige type is van invloed op het gedrag voor het genereren van het schema, het bericht verzenden voor de payload ' verzonden ' en het antwoord ' is ontvangen ', en de trigger. 

Wanneer sterk type wordt gebruikt (**veilig typen** is niet ingeschakeld), wijst het schema de DATS-en Tim's-typen toe aan meer eenvoudige XML-typen:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Wanneer u berichten verzendt met een sterk type, voldoet het DATS-en TIM'S-antwoord aan de overeenkomende XML-type-indeling:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Wanneer **veilig typen** is ingeschakeld, wijst het schema de typen DATS en Tim's toe aan XML-teken reeks velden met alleen lengte beperkingen, bijvoorbeeld:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

Wanneer berichten worden verzonden met een **veilig type** ingeschakeld, ziet het DATS-en Tim's-antwoord er als volgt uit:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Geavanceerde scenario's

### <a name="change-language-headers"></a>Taal headers wijzigen

Wanneer u verbinding maakt met SAP vanuit Logic Apps, is de standaard taal voor de verbinding Engels. U kunt de taal voor uw verbinding instellen met behulp van [de standaard `Accept-Language` -http-header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4) met uw inkomende aanvragen.

> [!TIP]
> De meeste webbrowsers voegen een `Accept-Language` koptekst toe op basis van de instellingen van de gebruiker. De webbrowser past deze header toe wanneer u een nieuwe SAP-verbinding maakt in de Logic Apps Designer. Als u geen SAP-verbindingen in de voorkeurs taal van uw webbrowser wilt maken, moet u de instellingen van uw webbrowser bijwerken om uw voorkeurs taal te gebruiken of uw SAP-verbinding maken met behulp van Azure Resource Manager in plaats van de Logic Apps Designer. 

U kunt bijvoorbeeld een aanvraag met de `Accept-Language` koptekst naar uw logische app verzenden met behulp van de **HTTP-aanvraag** trigger. Alle acties in uw logische app ontvangen de header. SAP gebruikt vervolgens de opgegeven talen in de bijbehorende systeem berichten, zoals BAPI-fout berichten.

De SAP-verbindings parameters voor een logische app hebben geen taal eigenschap. Als u de `Accept-Language` header gebruikt, wordt mogelijk de volgende fout weer geven: **Controleer de account gegevens en/of de machtigingen en probeer het opnieuw.** In dit geval controleert u in plaats daarvan de fout logboeken van het SAP-onderdeel. De fout treedt eigenlijk op in het SAP-onderdeel dat gebruikmaakt van de-header, waardoor u mogelijk een van deze fout berichten krijgt:

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`
* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### <a name="confirm-transaction-explicitly"></a>Trans actie expliciet bevestigen

Wanneer u trans acties verzendt naar SAP vanuit Logic Apps, vindt deze uitwisseling plaats in twee stappen zoals beschreven in het SAP-document, [transactionele RFC server-Program ma's](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). De actie **verzenden naar SAP** verwerkt standaard zowel de stappen voor de functie overdracht als voor de transactie bevestiging in één aanroep. De SAP-connector biedt u de mogelijkheid om deze stappen uit te voeren. U kunt een IDoc verzenden en in plaats van de trans actie automatisch te bevestigen, kunt u de actie voor de expliciete **trans actie-id bevestigen** gebruiken.

Deze mogelijkheid om de trans actie-ID-bevestiging te ontkoppelen is handig wanneer u trans acties niet wilt dupliceren in SAP, bijvoorbeeld in scenario's waarin storingen optreden vanwege oorzaken van problemen met het netwerk. Door de trans actie-ID afzonderlijk te bevestigen, wordt de trans actie slechts één keer in uw SAP-systeem uitgevoerd.

Hier volgt een voor beeld waarin dit patroon wordt weer gegeven:

1. Maak een lege logische app en voeg een HTTP-trigger toe.

1. Voeg de actie **IDOC verzenden** toe vanuit de SAP-connector. Geef de details op voor de IDoc die u naar uw SAP-systeem verzendt.

1. Als u de trans actie-ID expliciet wilt bevestigen in een afzonderlijke stap, selecteert u **Nee**in het veld **TID bevestigen** . Voor het optionele **trans actie-ID-GUID** veld kunt u de waarde hand matig opgeven of de connector automatisch genereren en deze GUID retour neren in de reactie van de actie IDOC verzenden.

   ![Eigenschappen van IDOC-actie verzenden](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Als u de trans actie-ID expliciet wilt bevestigen, voegt u de actie **trans actie-id bevestigen** toe. Klik in het vak **trans actie-id** zodat de lijst met dynamische inhoud wordt weer gegeven. Selecteer in de lijst de waarde voor de **trans actie-id** die wordt geretourneerd door de actie **IDOC verzenden** .

   ![Bewerking trans actie-ID bevestigen](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Nadat deze stap is uitgevoerd, wordt de huidige trans actie als voltooid aan beide uiteinden, aan de zijde van de SAP-connector en aan SAP-systeem zijde gemarkeerd.

## <a name="known-issues-and-limitations"></a>Bekende problemen en beperkingen

Dit zijn de bekende problemen en beperkingen voor de beheerde SAP-connector (niet ISE):

* De SAP-trigger biedt geen ondersteuning voor gegevens gateway clusters. In sommige failover-gevallen kan het knoop punt van de gegevens gateway dat met het SAP-systeem communiceert, afwijken van het actieve knoop punt, wat leidt tot onverwacht gedrag. Voor scenario's voor verzenden worden gegevens gateway clusters ondersteund.

* SAP-router teken reeksen worden momenteel niet ondersteund door de SAP-connector. De on-premises gegevens gateway moet bestaan op hetzelfde LAN als het SAP-systeem dat u wilt verbinden.

## <a name="connector-reference"></a>Connector-verwijzing

Voor meer technische informatie over deze connector, zoals triggers, acties en limieten, zoals beschreven in het Swagger-bestand van de connector, raadpleegt u de [referentie pagina van de connector](https://docs.microsoft.com/connectors/sap/).

> [!NOTE]
> Voor Logic apps in een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), maakt de ISE-versie van deze connector gebruik van de [ISE-bericht limieten](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) in plaats daarvan.

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met on-premises systemen](../logic-apps/logic-apps-gateway-connection.md) vanuit Azure Logic apps.
* Meer informatie over het valideren, transformeren en gebruiken van andere bericht bewerkingen met de [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).
* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md).
