---
title: Verbinding maken met SAP-systemen
description: Toegang tot en beheer van SAP-bronnen door werk stromen te automatiseren met Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/27/2020
tags: connectors
ms.openlocfilehash: 36e22fd92d937271a3859d03367e2a7ef80ef3d2
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118671"
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

De SAP-connector integreert met on-premises SAP-systemen via de [on-premises gegevens gateway](../logic-apps/logic-apps-gateway-connection.md). Bij het verzenden van scenario's, bijvoorbeeld wanneer een bericht wordt verzonden vanuit een logische app naar een SAP-systeem, fungeert de gegevens gateway als een RFC-client en worden de aanvragen doorgestuurd van de logische app naar SAP. In ontvangst scenario's fungeert de gegevens gateway ook als een RFC-server die aanvragen van SAP ontvangt en deze doorstuurt naar de logische app.

In dit artikel wordt uitgelegd hoe u logische apps maakt die met SAP worden geïntegreerd, terwijl u de eerder beschreven integratie scenario's bestrijkt. In dit artikel wordt beschreven hoe u logische apps kunt migreren naar de meest recente SAP-connector voor Logic apps die gebruikmaken van de oudere SAP-connectors.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt volgen, hebt u de volgende items nodig:

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, [meldt u zich aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De logische app die u wilt gebruiken om toegang te krijgen tot uw SAP-systeem en een trigger waarmee de werk stroom van de logische app wordt gestart. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md) en [Quick Start: uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uw [SAP-toepassings server](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) of [SAP-berichten server](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* [Down load en installeer de on-premises gegevens gateway](../logic-apps/logic-apps-gateway-install.md) op uw lokale computer. Maak vervolgens [een Azure gateway-resource](../logic-apps/logic-apps-gateway-connection.md#create-azure-gateway-resource) voor die gateway in het Azure Portal. De gateway helpt u veilig toegang te krijgen tot on-premises gegevens en bronnen. 

  * Zorg er als best practice voor dat u een ondersteunde versie van de on-premises gegevens gateway gebruikt. Micro soft brengt elke maand een nieuwe versie uit. Op dit moment ondersteunt micro soft de laatste zes versies. Als u een probleem met uw gateway ondervindt, voert u [een upgrade uit naar de nieuwste versie](https://aka.ms/on-premises-data-gateway-installer), die mogelijk updates bevat om het probleem op te lossen.

* [Down load, installeer en configureer de meest recente SAP-client bibliotheek](#sap-client-library-prerequisites) op dezelfde computer als de on-premises gegevens gateway.

* Bericht inhoud die u kunt verzenden naar uw SAP-server, zoals een voor beeld van een IDoc-bestand, moet de XML-indeling hebben en de naam ruimte bevatten voor de SAP-actie die u wilt gebruiken.

### <a name="sap-client-library-prerequisites"></a>Vereisten voor SAP-client bibliotheek

* Standaard plaatst het SAP-installatie programma de assembly bestanden in de standaardmap voor installatie. Kopieer de assembly bestanden uit de standaardmap voor installatie naar de installatiemap van de gateway.

    * Als uw SAP-verbinding mislukt met het fout bericht "Controleer de account gegevens en/of de machtigingen en probeer het opnieuw", de assembly bestanden kunnen zich op de verkeerde locatie bevindt. Zorg ervoor dat u de assembly bestanden hebt gekopieerd naar de installatiemap van de gateway. Gebruik vervolgens [de logboek viewer voor .NET-assembly-bindingen voor het oplossen van problemen](https://docs.microsoft.com/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer), waarmee u kunt controleren of de assembly bestanden zich op de juiste locatie bevinden.

    * U kunt eventueel de optie **globale assembly-cache registratie** selecteren wanneer u de SAP-client bibliotheek installeert.

* Zorg ervoor dat u de nieuwste versie, [SAP connector (NCo 3,0) voor Microsoft .net 3.0.22.0 die is gecompileerd met .NET Framework 4,0-Windows 64-bits (x64)](https://softwaredownloads.sap.com/file/0020000001000932019), om de volgende redenen hebt geïnstalleerd:

    * Eerdere SAP NCo-versies kunnen worden gedeadlockd wanneer meer dan één IDoc-bericht tegelijk wordt verzonden. Deze voor waarde blokkeert alle latere berichten die worden verzonden naar de SAP-bestemming, waardoor er een time-out optreedt voor de berichten.
    * De on-premises gegevens gateway wordt alleen uitgevoerd op 64-bits systemen. Anders krijgt u een fout bericht over een onjuiste afbeelding omdat de data gateway-hostservice geen 32-bits-assembly's ondersteunt.

    * Zowel de data gateway-hostservice als de micro soft SAP-adapter gebruiken .NET Framework 4,5. De SAP-NCo voor .NET Framework 4,0 werkt met processen die .NET runtime 4,0 gebruiken in 4.7.1. De SAP-NCo voor .NET Framework 2,0 werkt met processen die gebruikmaken van .NET runtime 2,0 tot 3,5, maar werkt niet meer met de meest recente on-premises gegevens gateway.

### <a name="snc-prerequisites"></a>Vereisten voor SNC

Configureer deze instellingen als u SNC (optioneel) gebruikt:

* Als u SNC gebruikt met SSO, moet u ervoor zorgen dat de gateway wordt uitgevoerd als een gebruiker die is toegewezen aan de SAP-gebruiker. Als u het standaard account wilt wijzigen, selecteert u **account wijzigen**en voert u de gebruikers referenties in.

  ![Gateway account wijzigen](./media/logic-apps-using-sap-connector/gateway-account.png)

* Als u SNC inschakelt met een extern beveiligings product, kopieert u de SNC-bibliotheek of-bestanden op dezelfde computer waarop de gateway is geïnstalleerd. Enkele voor beelden van SNC-producten zijn [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos en NTLM.

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>Migreren naar de huidige connector

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

1. Als uw verbinding al bestaat, gaat u door met de volgende stap, zodat u uw SAP-actie kunt instellen. Als u echter om de verbindings gegevens wordt gevraagd, geeft u de informatie op zodat u nu een verbinding met uw on-premises SAP-server kunt maken.

   1. Geef een naam op voor de verbinding.

   1. Selecteer in de sectie **gegevens gateway** , onder **abonnement**, eerst het Azure-abonnement voor de gateway resource die u hebt gemaakt in de Azure portal voor de installatie van de gateway. 
   
   1. Selecteer uw gateway resource onder **verbindings gateway**.

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

   1. Selecteer in de sectie **gegevens gateway** , onder **abonnement**, eerst het Azure-abonnement voor de gateway resource die u hebt gemaakt in de Azure portal voor de installatie van de gateway. 

   1. Selecteer uw gateway resource onder **verbindings gateway**.

   1. Ga door met het verstrekken van informatie over de verbinding. Volg voor de eigenschap **aanmeldings type** de stap op basis van het feit of de eigenschap is ingesteld op **toepassings server** of **groep**:

      * Voor **toepassings server**zijn deze eigenschappen, die meestal optioneel worden weer gegeven, vereist:

        ![Een SAP-toepassings server verbinding maken](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Voor **groep**zijn deze eigenschappen, die meestal optioneel worden weer gegeven, vereist:

        ![SAP Message server-verbinding maken](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Standaard wordt met sterk typen gecontroleerd op ongeldige waarden door XML-validatie uit te voeren op basis van het schema. Dit gedrag kan u helpen bij het detecteren van eerder gemaakte problemen. De optie voor **veilig typen** is beschikbaar voor achterwaartse compatibiliteit en controleert alleen de lengte van de teken reeks. Meer informatie over de [optie voor veilig typen](#safe-typing).

   1. Wanneer u klaar bent, selecteert u **maken**.

      Logic Apps stelt uw verbinding in en test deze om te controleren of de verbinding goed werkt.

1. Geef de [vereiste para meters](#parameters) op op basis van de configuratie van uw SAP-systeem.

   U kunt desgewenst een of meer SAP-acties opgeven. Met deze lijst met acties worden de berichten opgegeven die de trigger via de gegevens gateway van uw SAP-server ontvangt. Een lege lijst geeft aan dat de trigger alle berichten ontvangt. Als de lijst meer dan één bericht bevat, ontvangt de trigger alleen de berichten die in de lijst zijn opgegeven. Alle andere berichten die vanaf uw SAP-server worden verzonden, worden geweigerd door de gateway.

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
> De SAP-trigger is geen polling-trigger, maar is in plaats daarvan een webhook-trigger. De trigger wordt alleen aangeroepen vanuit de gateway als er een bericht bestaat, dus er is geen polling nodig.

<a name="parameters"></a>

#### <a name="parameters"></a>Parameters

De SAP-connector accepteert samen met eenvoudige teken reeks-en nummer invoer de volgende tabel parameters ( `Type=ITAB` invoer):

* Tabel richting para meters, zowel invoer als uitvoer, voor oudere SAP-releases.
* Para meters wijzigen, waardoor de tabel richtings parameters worden vervangen door nieuwere SAP-releases.
* Hiërarchische tabel parameters

### <a name="test-your-logic-app"></a>Uw logische app testen

1. Als u uw logische app wilt activeren, verzendt u een bericht van uw SAP-systeem.

1. Selecteer **overzicht**in het menu van de logische app. Bekijk de **uitvoerings geschiedenis** voor nieuwe uitvoeringen voor uw logische app.

1. Open de meest recente uitvoering, waarin het bericht wordt weer gegeven dat vanuit uw SAP-systeem is verzonden in de sectie trigger uitvoer.

## <a name="receive-idoc-packets-from-sap"></a>IDOC pakketten ontvangen van SAP

U kunt SAP instellen voor het [verzenden van IDOCs in pakketten](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html), die batches of groepen van IDOCs zijn. Voor het ontvangen van IDOC-pakketten, de SAP-connector en de specifiek de trigger, hebt u geen extra configuratie nodig. Als u elk item in een IDOC-pakket echter wilt verwerken nadat de trigger het pakket heeft ontvangen, zijn er aanvullende stappen vereist om het pakket te splitsen in afzonderlijke IDOCs.

Hier volgt een voor beeld waarin wordt uitgelegd hoe u afzonderlijke IDOCs uit een pakket kunt ophalen met behulp van de [ `xpath()` functie](./workflow-definition-language-functions-reference.md#xpath):

1. Voordat u begint, hebt u een logische app met een SAP-trigger nodig. Als u deze logische app nog niet hebt, volgt u de vorige stappen in dit onderwerp om [een logische app in te stellen met een SAP-trigger](#receive-from-sap).

   Bijvoorbeeld:

   ![SAP-trigger toevoegen aan de logische app](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Haal de hoofd naam ruimte op uit de XML-IDOC die uw logische app van SAP ontvangt. Als u deze naam ruimte uit het XML-document wilt extra heren, voegt u een stap toe die een lokale teken reeks variabele maakt en slaat die naam ruimte op met behulp van een `xpath()` expressie:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Hoofd naam ruimte ophalen van IDOC](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Als u een afzonderlijke IDOC wilt extra heren, voegt u een stap toe waarmee een matrix variabele wordt gemaakt en wordt de IDOC-verzameling opgeslagen met behulp van een andere `xpath()` expressie:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Matrix van items ophalen](./media/logic-apps-using-sap-connector/get-array.png)

   De matrix variabele maakt elk IDOC beschikbaar voor uw logische app door het inventariseren van de verzameling. In dit voor beeld stuurt de logische app elke IDOC naar een SFTP-server met behulp van een lus:

   ![IDOC naar SFTP-server verzenden](./media/logic-apps-using-sap-connector/loop-batch.png)

   Elke IDOC moet de hoofd naam ruimte bevatten. Dit is de reden waarom de bestands inhoud in een- `<Receive></Receive` element samen met de hoofd naam ruimte wordt verpakt voordat de IDOC naar de downstream-app of de sftp-server in dit geval wordt verzonden.

U kunt de Quick Start-sjabloon voor dit patroon gebruiken door deze sjabloon te selecteren in de ontwerp functie voor logische apps wanneer u een nieuwe logische app maakt.

![Sjabloon voor batch Logic app selecteren](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Schema's genereren voor artefacten in SAP

In dit voor beeld wordt een logische app gebruikt die u kunt activeren met een HTTP-aanvraag. De SAP-actie verzendt een aanvraag naar een SAP-systeem om de schema's voor opgegeven IDoc en BAPI te genereren. Schema's die in het antwoord retour neren, worden geüpload naar een integratie account met behulp van de Azure Resource Manager-connector.

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

   1. Selecteer in de sectie **gegevens gateway** , onder **abonnement**, eerst het Azure-abonnement voor de gateway resource die u hebt gemaakt in de Azure portal voor de installatie van de gateway. 
   
   1. Selecteer uw gateway resource onder **verbindings gateway**.

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

## <a name="enable-secure-network-communications"></a>Veilige netwerk communicatie inschakelen

Voordat u begint, moet u ervoor zorgen dat u voldoet aan de eerder genoemde [vereisten](#pre-reqs):

* De on-premises gegevens gateway is geïnstalleerd op een computer die zich in hetzelfde netwerk bevindt als uw SAP-systeem.
* Voor SSO wordt de gateway uitgevoerd als een gebruiker die is toegewezen aan een SAP-gebruiker.
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

### <a name="confirm-transaction-explicitly"></a>Trans actie expliciet bevestigen

Wanneer u trans acties verzendt naar SAP vanuit Logic Apps, vindt deze uitwisseling plaats in twee stappen zoals beschreven in het SAP-document, [transactionele RFC server-Program ma's](https://help.sap.com/doc/saphelp_nwpi71/7.1/en-US/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). De actie **verzenden naar SAP** verwerkt standaard zowel de stappen voor de functie overdracht als voor de transactie bevestiging in één aanroep. De SAP-connector biedt u de mogelijkheid om deze stappen uit te voeren. U kunt een IDOC verzenden en in plaats van de trans actie automatisch te bevestigen, kunt u de actie voor de expliciete **trans actie-id bevestigen** gebruiken.

Deze mogelijkheid om de trans actie-ID-bevestiging te ontkoppelen is handig wanneer u trans acties niet wilt dupliceren in SAP, bijvoorbeeld in scenario's waarin storingen optreden vanwege oorzaken van problemen met het netwerk. Door de trans actie-ID afzonderlijk te bevestigen, wordt de trans actie slechts één keer in uw SAP-systeem uitgevoerd.

Hier volgt een voor beeld waarin dit patroon wordt weer gegeven:

1. Maak een lege logische app en voeg een HTTP-trigger toe.

1. Voeg de actie **IDOC verzenden** toe vanuit de SAP-connector. Geef de details op voor de IDOC die u naar uw SAP-systeem verzendt.

1. Als u de trans actie-ID expliciet wilt bevestigen in een afzonderlijke stap, selecteert u **Nee**in het veld **TID bevestigen** . Voor het optionele **trans actie-ID-GUID** veld kunt u de waarde hand matig opgeven of de connector automatisch genereren en deze GUID retour neren in de reactie van de actie IDOC verzenden.

   ![Eigenschappen van IDOC-actie verzenden](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Als u de trans actie-ID expliciet wilt bevestigen, voegt u de actie **trans actie-id bevestigen** toe. Klik in het vak **trans actie-id** zodat de lijst met dynamische inhoud wordt weer gegeven. Selecteer in de lijst de waarde voor de **trans actie-id** die wordt geretourneerd door de actie **IDOC verzenden** .

   ![Bewerking trans actie-ID bevestigen](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Nadat deze stap is uitgevoerd, wordt de huidige trans actie als voltooid aan beide uiteinden, aan de zijde van de SAP-connector en aan SAP-systeem zijde gemarkeerd.

## <a name="known-issues-and-limitations"></a>Bekende problemen en beperkingen

Dit zijn de bekende problemen en beperkingen voor de SAP-connector:

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
