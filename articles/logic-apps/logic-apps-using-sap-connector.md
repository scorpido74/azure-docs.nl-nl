---
title: Verbinding maken met SAP-systemen
description: SAP-resources openen en beheren door werkstromen te automatiseren met Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/30/2019
tags: connectors
ms.openlocfilehash: 39ab222f64d964e95b16e043c9cdeccd8170ace3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651012"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Verbinding maken met SAP-systemen in Azure Logic Apps

> [!IMPORTANT]
> De eerdere SAP Application Server- en SAP Message Server-connectors worden op 29 februari 2020 afgeschaft. De huidige SAP-connector consolideert deze eerdere SAP-connectors, zodat u het verbindingstype niet hoeft te wijzigen, volledig compatibel is met eerdere connectors, veel extra mogelijkheden biedt en de SAP .Net-connectorbibliotheek blijft gebruiken ( SAP NCo).
>
> Voor logische apps die de oudere connectors gebruiken, [migreert](#migrate) u naar de nieuwste connector vóór de afschrijvingsdatum. Anders ondervinden deze logische apps uitvoeringsfouten en kunnen ze geen berichten naar uw SAP-systeem verzenden.

In dit artikel ziet u hoe u uw on-premises SAP-bronnen vanuit een logische app openen met behulp van de SAP-connector. De connector werkt met de klassieke releases van SAP, zoals R/3- en ECC-systemen on-premises. De connector maakt ook integratie mogelijk met SAP's nieuwere HANA-gebaseerde SAP-systemen, zoals S/4 HANA, of ze nu on-premises of in de cloud worden gehost. De SAP-connector ondersteunt bericht- of gegevensintegratie van en naar SAP NetWeaver-systemen via Intermediate Document (IDoc), Business Application Programming Interface (BAPI) of Remote Function Call (RFC).

De SAP-connector maakt gebruik van de [SAP .NET Connector (NCo) bibliotheek](https://support.sap.com/en/product/connectors/msnet.html) en biedt deze acties:

* **Stuur bericht naar SAP**: Stuur IDoc via tRFC, roep BAPI-functies op via RFC of bel RFC/tRFC in SAP-systemen.
* **Wanneer een bericht wordt ontvangen van SAP**: IDoc ontvangen via tRFC, call BAPI-functies via tRFC of bel RFC/tRFC in SAP-systemen.
* **Schema's genereren:** schema's genereren voor de SAP-artefacten voor IDoc, BAPI of RFC.

Voor deze bewerkingen ondersteunt de SAP-connector basisverificatie via gebruikersnamen en wachtwoorden. De connector ondersteunt ook [Secure Network Communications (SNC).](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true) SNC kan worden gebruikt voor SAP NetWeaver single sign-on (SSO) of voor extra beveiligingsmogelijkheden die worden geleverd door een extern beveiligingsproduct.

De SAP-connector integreert met on-premises SAP-systemen via de [on-premises datagateway.](../logic-apps/logic-apps-gateway-connection.md) In verzendscenario's, bijvoorbeeld wanneer een bericht wordt verzonden van een logische app naar een SAP-systeem, fungeert de gegevensgateway als een RFC-client en stuurt de aanvragen die van de logische app zijn ontvangen door naar SAP. In ontvangstscenario's fungeert de gegevensgateway ook als een RFC-server die aanvragen van SAP ontvangt en doorstuurt naar de logische app.

In dit artikel ziet u hoe u voorbeeldlogische apps maakt die met SAP worden geïntegreerd en tegelijkertijd de eerder beschreven integratiescenario's bestrijken. Voor logische apps die de oudere SAP-connectors gebruiken, wordt in dit artikel weergegeven hoe u uw logische apps migreert naar de nieuwste SAP-connector.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Vereisten

Om mee te gaan met dit artikel heb je deze items nodig:

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, [meldt u zich aan voor een gratis Azure-account.](https://azure.microsoft.com/free/)

* De logische app van waaruit u toegang wilt krijgen tot uw SAP-systeem en een trigger waarmee de workflow van uw logische app wordt gestart. Zie Wat is Azure Logic [Apps?](../logic-apps/logic-apps-overview.md) en [Quickstart: Uw eerste logische app maken als](../logic-apps/quickstart-create-first-logic-app-workflow.md)u nieuw bent in logische apps.

* Uw [SAP-toepassingsserver](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) of [SAP-berichtenserver.](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)

* Download en installeer de nieuwste [on-premises datagateway](https://www.microsoft.com/download/details.aspx?id=53127) op elke on-premises computer. Zorg ervoor dat u uw gateway in de Azure-portal instelt voordat u verdergaat. De gateway helpt u veilig toegang te krijgen tot on-premises gegevens en bronnen. Zie [Een on-premises gegevensgateway voor Azure Logic Apps installeren voor](../logic-apps/logic-apps-gateway-install.md)meer informatie.

* Als u SNC met SSO gebruikt, controleert u of de gateway wordt uitgevoerd als een gebruiker die is toegewezen ten opzichte van de SAP-gebruiker. Als u het standaardaccount wilt wijzigen, selecteert u **Account wijzigen**en voert u de gebruikersreferenties in.

  ![Gateway-account wijzigen](./media/logic-apps-using-sap-connector/gateway-account.png)

* Als u SNC inschakelt met een extern beveiligingsproduct, kopieert u de SNC-bibliotheek of bestanden op dezelfde machine waar de gateway is geïnstalleerd. Enkele voorbeelden van SNC-producten zijn [sapseculib,](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)Kerberos en NTLM.

* Download en installeer de nieuwste SAP-clientbibliotheek, die momenteel [SAP Connector (NCo 3.0) is voor Microsoft .NET 3.0.22.0 gecompileerd met .NET Framework 4.0 - Windows 64-bits (x64)](https://softwaredownloads.sap.com/file/0020000001000932019), op dezelfde computer als de on-premises datagateway. Installeer deze versie of hoger om deze redenen:

  * Eerdere SAP NCo-versies kunnen vastlopen wanneer meer dan één IDoc-bericht tegelijkertijd wordt verzonden. Deze voorwaarde blokkeert alle latere berichten die naar de SAP-bestemming worden verzonden, waardoor de berichten een time-out krijgen.
  
  * De on-premises datagateway wordt alleen uitgevoerd op 64-bits systemen. Anders krijgt u een fout met een slecht imago omdat de statusservice van de gegevensgateway geen 32-bits verzamelingen ondersteunt.
  
  * Zowel de datagatewayhostservice als de Microsoft SAP Adapter gebruiken .NET Framework 4.5. De SAP NCo voor .NET Framework 4.0 werkt met processen die .NET runtime 4.0 tot 4.7.1 gebruiken. De SAP NCo voor .NET Framework 2.0 werkt met processen die .NET runtime 2.0 tot 3.5 gebruiken, maar werkt niet meer met de nieuwste on-premises datagateway.

* Berichtinhoud die u naar uw SAP-server verzenden, zoals een voorbeeld-IDoc-bestand, moet in XML-indeling zijn en de naamruimte bevatten voor de SAP-actie die u wilt gebruiken.

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>Migreren naar huidige connector

1. Als u dit nog niet hebt gedaan, werkt u uw [on-premises gegevensgateway bij](https://www.microsoft.com/download/details.aspx?id=53127) zodat u de nieuwste versie hebt. Zie [Een on-premises gegevensgateway voor Azure Logic Apps installeren voor](../logic-apps/logic-apps-gateway-install.md)meer informatie.

1. Verwijder in de logische app die de oudere SAP-connector gebruikt de actie **Verzenden naar SAP.**

1. Voeg in de nieuwste SAP-connector de actie **Bericht verzenden naar SAP** toe. Voordat u deze actie gebruiken, maakt u de verbinding met uw SAP-systeem opnieuw.

1. Wanneer u klaar bent, slaat u uw logica-app op.

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>Bericht verzenden naar SAP

In dit voorbeeld wordt een logische app gebruikt die u activeren met een HTTP-aanvraag. De logische app stuurt een IDoc naar een SAP-server en retourneert een antwoord op de aanvrager die de logische app heeft genoemd.

### <a name="add-an-http-request-trigger"></a>Een HTTP-aanvraagtrigger toevoegen

In Azure Logic Apps moet elke logische app beginnen met een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), die wordt geactiveerd wanneer een specifieke gebeurtenis plaatsvindt of wanneer aan een specifieke voorwaarde is voldaan. Elke keer dat de trigger wordt geactiveerd, maakt de Logic Apps-engine een logische app-instantie en wordt de werkstroom van uw app gestart.

In dit voorbeeld maakt u een logische app met een eindpunt in Azure, zodat u *HTTP POST-aanvragen* naar uw logische app verzenden. Wanneer uw logische app deze HTTP-aanvragen ontvangt, wordt de trigger geactiveerd en wordt de volgende stap in uw werkstroom uitgevoerd.

1. Maak in de [Azure-portal](https://portal.azure.com)een lege logische app, waarmee de Logic App Designer wordt geopend.

1. Voer in het `http request` zoekvak in als filter. Selecteer in de lijst **Triggers** de optie **Wanneer een HTTP-aanvraag is ontvangen**.

   ![Http-aanvraagtrigger toevoegen](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Sla nu uw logische app op, zodat u een eindpunt-URL voor uw logische app genereren. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

   De URL van het eindpunt wordt nu weergegeven in uw trigger, bijvoorbeeld:

   ![URL genereren voor eindpunt](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Een SAP-actie toevoegen

In Azure Logic Apps is een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) een stap in uw werkstroom die een trigger of een andere actie volgt. Als u nog geen trigger aan uw logische app hebt toegevoegd en dit voorbeeld wilt volgen, [voegt u de trigger toe die in deze sectie wordt beschreven.](#add-trigger)

1. Selecteer in de Logic App Designer onder de trigger de optie **Nieuwe stap**.

   ![Nieuwe stap toevoegen aan de logische app](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Voer in het `sap` zoekvak in als filter. Selecteer Bericht verzenden **naar SAP**in de lijst **Acties** .
  
   ![Selecteer actie 'Bericht verzenden naar SAP'](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   U ook het tabblad **Onderneming** selecteren en de SAP-actie selecteren.

   ![Selecteer actie 'Bericht verzenden naar SAP' op het tabblad Onderneming](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Als uw verbinding al bestaat, gaat u verder met de volgende stap, zodat u uw SAP-actie instellen. Als u echter om verbindingsgegevens wordt gevraagd, geeft u de informatie op zodat u nu een verbinding maken met uw on-premises SAP-server.

   1. Geef een naam op voor de verbinding.

   1. Selecteer in de sectie **Gegevensgateway** onder **Abonnement**eerst het Azure-abonnement voor de gatewaybron die u hebt gemaakt in de Azure-portal voor uw gateway-installatie. 
   
   1. Selecteer **onder Verbindingsgateway**uw gatewaybron.

   1. Doorgaan met het verstrekken van informatie over de verbinding. Volg voor de eigenschap **Logon type** de stap op basis van de vraag of de eigenschap is ingesteld op **Application Server** of **Groep:**
   
      * Voor **Application Server**zijn deze eigenschappen, die meestal optioneel lijken, vereist:

        ![SAP-toepassingsserververbinding maken](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Voor **Groep**zijn deze eigenschappen, die meestal optioneel lijken, vereist:

        ![Verbinding met SAP-berichtenserver maken](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Standaard wordt sterk typen gebruikt om te controleren op ongeldige waarden door XML-validatie uit te voeren op basis van het schema. Dit gedrag kan u helpen problemen eerder op te sporen. De optie **Veilig typen** is beschikbaar voor achterwaartse compatibiliteit en controleert alleen de tekenreekslengte. Meer informatie over de [optie Veilig typen](#safe-typing).

   1. Wanneer u klaar bent, selecteert u **Maken**.

      Logic Apps stelt uw verbinding in en test deze om ervoor te zorgen dat de verbinding goed werkt.

1. Zoek en selecteer nu een actie van uw SAP-server.

   1. Selecteer in het vak **SAP Action** het mappictogram. Zoek en selecteer in de lijst met bestanden het SAP-bericht dat u wilt gebruiken. Als u door de lijst wilt navigeren, gebruikt u de pijlen.

      In dit voorbeeld wordt een IDoc met het type **Orders** geselecteerd.

      ![IDoc-actie zoeken en selecteren](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Als u de gewenste actie niet vinden, u handmatig een pad invoeren, bijvoorbeeld:

      ![Handmatig pad naar IDoc-actie geven](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Geef de waarde voor **SAP Action** op via de expressieeditor. Op die manier u dezelfde actie gebruiken voor verschillende berichttypen.

      Zie [Berichtenschema's voor IDOC-bewerkingen voor](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)meer informatie over IDoc-bewerkingen .

   1. Klik in het vak **Invoerbericht** zodat de lijst met dynamische inhoud wordt weergegeven. Selecteer in die lijst onder **Wanneer een HTTP-aanvraag wordt ontvangen,** het veld **Lichaam.**

      Deze stap omvat de hoofdinhoud van uw HTTP-aanvraagtrigger en verzendt die uitvoer naar uw SAP-server.

      ![Selecteer de eigenschap 'Lichaam' van trigger](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Wanneer u klaar bent, ziet uw SAP-actie er als volgt uit:

      ![De SAP-actie voltooien](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Sla uw logische app op. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Een HTTP-antwoordactie toevoegen

Voeg nu een reactieactie toe aan de werkstroom van uw logische app en neem de uitvoer van de SAP-actie op. Op die manier retourneert uw logische app de resultaten van uw SAP-server naar de oorspronkelijke aanvrager.

1. Selecteer in de Logic App Designer onder de SAP-actie de optie **Nieuwe stap**.

1. Voer in het `response` zoekvak in als filter. Selecteer **Antwoord**in de lijst **Acties** .

1. Klik in het vak **Hoofdtekst** zodat de lijst met dynamische inhoud wordt weergegeven. Selecteer in die lijst onder **Bericht verzenden naar SAP**het veld **Lichaam.**

   ![Voer SAP-actie uit](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Sla uw logische app op.

### <a name="test-your-logic-app"></a>Uw logische app testen

1. Als uw logische app nog niet is ingeschakeld, selecteert u **overzicht**in het menu van de logische app . Selecteer op de werkbalk **Inschakelen**.

1. Selecteer Uitvoeren op de werkbalk **van**de ontwerper . Met deze stap wordt uw logica-app handmatig gestart.

1. Activeer uw logische app door een HTTP POST-verzoek naar de URL in uw HTTP-aanvraagtrigger te verzenden.
Voeg de inhoud van uw bericht toe aan uw verzoek. Om het verzoek te verzenden, u een tool zoals [Postman](https://www.getpostman.com/apps)gebruiken.

   Voor dit artikel verzendt de aanvraag een IDoc-bestand, dat in XML-indeling moet zijn en de naamruimte moet bevatten voor de SAP-actie die u gebruikt, bijvoorbeeld:

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Nadat u uw HTTP-verzoek hebt verzonden, wacht u op het antwoord van uw logische app.

   > [!NOTE]
   > Uw logische app kan een time-out krijgen als alle stappen die nodig zijn voor het antwoord niet binnen de [termijn voor het time-out van](./logic-apps-limits-and-config.md)de aanvraag worden voltooid. Als deze voorwaarde optreedt, kunnen aanvragen worden geblokkeerd. Om u te helpen problemen te diagnosticeren, leest u hoe u uw logische apps [controleren en controleren.](../logic-apps/monitor-logic-apps.md)

U hebt nu een logische app gemaakt die met uw SAP-server kan communiceren. Nu u een SAP-verbinding voor uw logische app hebt ingesteld, u andere beschikbare SAP-acties verkennen, zoals BAPI en RFC.

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>Ontvang bericht van SAP

In dit voorbeeld wordt een logische app gebruikt die wordt geactiveerd wanneer de app een bericht ontvangt van een SAP-systeem.

### <a name="add-an-sap-trigger"></a>Een SAP-trigger toevoegen

1. Maak in de Azure-portal een lege logische app, waarmee de Logic App Designer wordt geopend.

1. Voer in het `sap` zoekvak in als filter. Selecteer in de lijst **Triggers** de optie **Wanneer een bericht van SAP wordt ontvangen**.

   ![SAP-trigger toevoegen](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   U ook het tabblad **Onderneming** selecteren en vervolgens de trigger selecteren:

   ![SAP-trigger toevoegen vanaf het tabblad Onderneming](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Als uw verbinding al bestaat, gaat u verder met de volgende stap, zodat u uw SAP-actie instellen. Als u echter om verbindingsgegevens wordt gevraagd, geeft u de informatie op zodat u nu een verbinding maken met uw on-premises SAP-server.

   1. Geef een naam op voor de verbinding.

   1. Selecteer in de sectie **Gegevensgateway** onder **Abonnement**eerst het Azure-abonnement voor de gatewaybron die u hebt gemaakt in de Azure-portal voor uw gateway-installatie. 

   1. Selecteer **onder Verbindingsgateway**uw gatewaybron.

   1. Doorgaan met het verstrekken van informatie over de verbinding. Volg voor de eigenschap **Logon type** de stap op basis van de vraag of de eigenschap is ingesteld op **Application Server** of **Groep:**

      * Voor **Application Server**zijn deze eigenschappen, die meestal optioneel lijken, vereist:

        ![SAP-toepassingsserververbinding maken](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Voor **Groep**zijn deze eigenschappen, die meestal optioneel lijken, vereist:

        ![Verbinding met SAP-berichtenserver maken](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Standaard wordt sterk typen gebruikt om te controleren op ongeldige waarden door XML-validatie uit te voeren op basis van het schema. Dit gedrag kan u helpen problemen eerder op te sporen. De optie **Veilig typen** is beschikbaar voor achterwaartse compatibiliteit en controleert alleen de tekenreekslengte. Meer informatie over de [optie Veilig typen](#safe-typing).

   1. Wanneer u klaar bent, selecteert u **Maken**.

      Logic Apps stelt uw verbinding in en test deze om ervoor te zorgen dat de verbinding goed werkt.

1. Geef de vereiste parameters op basis van uw SAP-systeemconfiguratie.

   U optioneel een of meer SAP-acties uitvoeren. In deze lijst met acties worden de berichten opgegeven die de trigger van uw SAP-server ontvangt via de gegevensgateway. Een lege lijst geeft aan dat de trigger alle berichten ontvangt. Als de lijst meer dan één bericht heeft, ontvangt de trigger alleen de berichten die in de lijst zijn opgegeven. Alle andere berichten die vanaf uw SAP-server worden verzonden, worden door de gateway geweigerd.

   U een SAP-actie selecteren in de bestandenkiezer:

   ![SAP-actie toevoegen aan logische app](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   U ook handmatig een actie opgeven:

   ![Voer handmatig SAP-actie in](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Hier ziet u een voorbeeld waarin wordt weergegeven hoe de actie wordt weergegeven wanneer u de trigger instelt om meer dan één bericht te ontvangen.

   ![Voorbeeld van trigger waarin meerdere berichten worden ontvangen](media/logic-apps-using-sap-connector/example-trigger.png)

   Zie [Berichtenschema's voor IDOC-bewerkingen voor](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations) meer informatie over de SAP-actie

1. Sla nu uw logische app op, zodat u berichten ontvangen van uw SAP-systeem. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

Uw logische app is nu klaar om berichten van uw SAP-systeem te ontvangen.

> [!NOTE]
> De SAP-trigger is geen polling trigger, maar is een webhook-gebaseerde trigger plaats. De trigger wordt alleen vanaf de gateway aangeroepen wanneer een bericht bestaat, dus er is geen polling nodig.

### <a name="test-your-logic-app"></a>Uw logische app testen

1. Als u uw logische app wilt activeren, stuurt u een bericht vanuit uw SAP-systeem.

1. Selecteer **Overzicht**in het menu van de logische app . Bekijk de **geschiedenis Van Uitvoering** voor nieuwe uitvoeringen voor uw logische app.

1. Open de meest recente run, waarin het bericht wordt weergegeven dat vanuit uw SAP-systeem wordt verzonden in de sectie triggeroutputs.

## <a name="receive-idoc-packets-from-sap"></a>IDOC-pakketten ontvangen van SAP

U SAP instellen om [IDOCs te verzenden in pakketten](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html), die batches of groepen IDOCs zijn. Om IDOC-pakketten te ontvangen, heeft de SAP-connector, en met name de trigger, geen extra configuratie nodig. Om elk item in een IDOC-pakket te verwerken nadat de trigger het pakket heeft ontvangen, zijn echter enkele extra stappen nodig om het pakket in afzonderlijke IDO's te splitsen.

Hier is een voorbeeld dat laat zien hoe u afzonderlijke IDOCs uit een pakket extraheren met behulp van de [ `xpath()` functie:](./workflow-definition-language-functions-reference.md#xpath)

1. Voordat u begint, hebt u een logische app met een SAP-trigger nodig. Als u deze logische app nog niet hebt, volgt u de vorige stappen in dit onderwerp om [een logische app met een SAP-trigger in](#receive-from-sap)te stellen.

   Bijvoorbeeld:

   ![SAP-trigger toevoegen aan logische app](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Haal de hoofdnaamruimte uit de XML IDOC die uw logica-app ontvangt van SAP. Als u deze naamruimte uit het XML-document wilt extraheren, voegt u een `xpath()` stap toe die een variabele voor lokale tekenreeksen maakt en die naamruimte opslaat met behulp van een expressie:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Hoofdnaamruimte ophalen bij IDOC](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Als u een afzonderlijke IDOC wilt extraheren, voegt u een stap `xpath()` toe die een matrixvariabele maakt en de IDOC-verzameling opslaat met een andere expressie:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Array met items opmaken](./media/logic-apps-using-sap-connector/get-array.png)

   Met de arrayvariabele is elke IDOC beschikbaar voor uw logische app om afzonderlijk te verwerken door de verzameling op te sommen. In dit voorbeeld brengt de logische app elke IDOC over naar een SFTP-server met behulp van een lus:

   ![IDOC naar SFTP-server verzenden](./media/logic-apps-using-sap-connector/loop-batch.png)

   Elke IDOC moet de hoofdnaamruimte bevatten, wat de reden is `<Receive></Receive` waarom de bestandsinhoud in een element is gewikkeld samen met de hoofdnaamruimte voordat u de IDOC naar de downstream-app of SFTP-server in dit geval verzendt.

U de sjabloon snel start voor dit patroon gebruiken door deze sjabloon te selecteren in de Logic App Designer wanneer u een nieuwe logische app maakt.

![Sjabloon batchlogische app selecteren](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Schema's genereren voor artefacten in SAP

In dit voorbeeld wordt een logische app gebruikt die u activeren met een HTTP-aanvraag. De SAP-actie stuurt een verzoek naar een SAP-systeem om de schema's voor bepaalde IDoc en BAPI te genereren. Schema's die terugkeren in het antwoord worden geüpload naar een integratieaccount met behulp van de Azure Resource Manager-connector.

### <a name="add-an-http-request-trigger"></a>Een HTTP-aanvraagtrigger toevoegen

1. Maak in de Azure-portal een lege logische app, waarmee de Logic App Designer wordt geopend.

1. Voer in het `http request` zoekvak in als filter. Selecteer in de lijst **Triggers** de optie **Wanneer een HTTP-aanvraag is ontvangen**.

   ![Http-aanvraagtrigger toevoegen](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Sla nu uw logische app op, zodat u een eindpunt-URL voor uw logische app genereren.
Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

   De URL van het eindpunt wordt nu weergegeven in uw trigger, bijvoorbeeld:

   ![URL genereren voor eindpunt](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Een SAP-actie toevoegen om schema's te genereren

1. Selecteer in de Logic App Designer onder de trigger de optie **Nieuwe stap**.

   ![Nieuwe stap toevoegen aan de logische app](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Voer in het `sap` zoekvak in als filter. Selecteer **schema's** **genereren**in de lijst Acties .
  
   ![Actie 'Schema's genereren' toevoegen aan logische app](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   U ook het tabblad **Onderneming** selecteren en de SAP-actie selecteren.

   ![Sap-verzendactie selecteren op het tabblad Onderneming](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Als uw verbinding al bestaat, gaat u verder met de volgende stap, zodat u uw SAP-actie instellen. Als u echter om verbindingsgegevens wordt gevraagd, geeft u de informatie op zodat u nu een verbinding maken met uw on-premises SAP-server.

   1. Geef een naam op voor de verbinding.

   1. Selecteer in de sectie **Gegevensgateway** onder **Abonnement**eerst het Azure-abonnement voor de gatewaybron die u hebt gemaakt in de Azure-portal voor uw gateway-installatie. 
   
   1. Selecteer **onder Verbindingsgateway**uw gatewaybron.

   1. Doorgaan met het verstrekken van informatie over de verbinding. Volg voor de eigenschap **Logon type** de stap op basis van de vraag of de eigenschap is ingesteld op **Application Server** of **Groep:**
   
      * Voor **Application Server**zijn deze eigenschappen, die meestal optioneel lijken, vereist:

        ![SAP-toepassingsserververbinding maken](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Voor **Groep**zijn deze eigenschappen, die meestal optioneel lijken, vereist:

        ![Verbinding met SAP-berichtenserver maken](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Standaard wordt sterk typen gebruikt om te controleren op ongeldige waarden door XML-validatie uit te voeren op basis van het schema. Dit gedrag kan u helpen problemen eerder op te sporen. De optie **Veilig typen** is beschikbaar voor achterwaartse compatibiliteit en controleert alleen de tekenreekslengte. Meer informatie over de [optie Veilig typen](#safe-typing).

   1. Wanneer u klaar bent, selecteert u **Maken**.

      Logic Apps stelt uw verbinding in en test deze om ervoor te zorgen dat de verbinding goed werkt.

1. Geef het pad op naar het artefact waarvoor u het schema wilt genereren.

   U de SAP-actie selecteren in de bestandenkiezer:

   ![SAP-actie selecteren](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   U de actie ook handmatig invoeren:

   ![Voer handmatig SAP-actie in](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Als u schema's voor meer dan één artefact wilt genereren, geeft u de SAP-actiegegevens op voor elk artefact, bijvoorbeeld:

   ![Nieuw item toevoegen selecteren](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Twee objecten weergeven](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Zie [Berichtenschema's voor IDOC-bewerkingen voor](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)meer informatie over de SAP-actie .

1. Sla uw logische app op. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

### <a name="test-your-logic-app"></a>Uw logische app testen

1. Selecteer op de werkbalk Van de ontwerper **uitvoeren** om een run voor uw logische app te activeren.

1. Open de run en controleer de uitvoer voor de actie **Schema's genereren.**

   De uitvoer toont de gegenereerde schema's voor de opgegeven lijst met berichten.

### <a name="upload-schemas-to-an-integration-account"></a>Schema's uploaden naar een integratieaccount

Optioneel u de gegenereerde schema's downloaden of opslaan in repositories, zoals een blob, opslag of integratieaccount. Integratieaccounts bieden een eersteklas ervaring met andere XML-acties, dus in dit voorbeeld wordt uitgelegd hoe u schema's uploadt naar een integratieaccount voor dezelfde logische app met behulp van de Azure Resource Manager-connector.

1. Selecteer in de Logic App Designer onder de trigger de optie **Nieuwe stap**.

1. Voer in het `Resource Manager` zoekvak in als filter. Selecteer **Een resource maken of bijwerken**.

   ![Azure Resource Manager-actie selecteren](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Voer de details in voor de actie, waaronder uw Azure-abonnement, Azure-brongroep en integratieaccount. Als u SAP-tokens aan de velden wilt toevoegen, klikt u in de vakken voor die velden en selecteert u in de lijst met dynamische inhoud die wordt weergegeven.

   1. Open de lijst **Nieuwe parameter toevoegen** en selecteer de velden **Locatie** en **Eigenschappen.**

   1. Geef details voor deze nieuwe velden zoals in dit voorbeeld.

      ![Details invoeren voor de actie Azure Resource Manager](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   De actie SAP **Genereren schema's** genereert schema's als een verzameling, zodat de ontwerper automatisch een **Voor elke** lus aan de actie toevoegt. Hier volgt een voorbeeld dat laat zien hoe deze actie wordt weergegeven:

   ![Azure Resource Manager-actie met de lus 'voor elke'](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > De schema's maken gebruik van base64-gecodeerde indeling. Als u de schema's wilt uploaden naar een `base64ToString()` integratieaccount, moeten ze worden gedecodeerd met behulp van de functie. Hier is een voorbeeld dat de `"properties"` code voor het element weergeeft:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Sla uw logische app op. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

### <a name="test-your-logic-app"></a>Uw logische app testen

1. Selecteer op de werkbalk Van de ontwerper **uitvoeren** om uw logische app handmatig te activeren.

1. Ga na een geslaagde run naar het integratieaccount en controleer of de gegenereerde schema's bestaan.

## <a name="enable-secure-network-communications"></a>Beveiligde netwerkcommunicatie inschakelen

Controleer voordat u begint of u aan de eerder vermelde [voorwaarden hebt voldaan:](#pre-reqs)

* De on-premises datagateway is geïnstalleerd op een machine die zich in hetzelfde netwerk bevindt als uw SAP-systeem.
* Voor SSO wordt de gateway uitgevoerd als een gebruiker die is toegewezen aan een SAP-gebruiker.
* De SNC-bibliotheek die de extra beveiligingsfuncties biedt, is geïnstalleerd op dezelfde machine als de gegevensgateway. Enkele voorbeelden zijn [sapseculib,](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)Kerberos en NTLM.

   Als u SNC wilt inschakelen voor uw aanvragen van of naar het SAP-systeem, schakelt u het selectievakje **SNC gebruiken** in de SAP-verbinding in en geeft u deze eigenschappen op:

   ![SAP SNC configureren in verbinding](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Eigenschap | Beschrijving |
   |----------| ------------|
   | **SNC-bibliotheekpad** | De naam of het pad van de SNC-bibliotheek ten opzichte van de nco-installatielocatie of het absolute pad. Voorbeelden zijn `sapsnc.dll` `.\security\sapsnc.dll` of `c:\security\sapsnc.dll`. |
   | **SNC SSO** | Wanneer u verbinding maakt via SNC, wordt de SNC-identiteit meestal gebruikt voor het verifiëren van de beller. Een andere optie is om te overschrijven, zodat gebruikers- en wachtwoordgegevens kunnen worden gebruikt voor het verifiëren van de beller, maar de regel is nog steeds versleuteld. |
   | **SNC Mijn naam** | In de meeste gevallen kan deze eigenschap worden weggelaten. De geïnstalleerde SNC-oplossing kent meestal zijn eigen SNC-naam. Alleen voor oplossingen die meerdere identiteiten ondersteunen, moet u mogelijk de identiteit opgeven die voor deze specifieke bestemming of server moet worden gebruikt. |
   | **SNC-partnernaam** | De naam voor de back-end SNC. |
   | **SNC-kwaliteit van bescherming** | De kwaliteit van de service die moet worden gebruikt voor SNC-communicatie van deze specifieke bestemming of server. De standaardwaarde wordt bepaald door het back-endsysteem. De maximale waarde wordt bepaald door het beveiligingsproduct dat voor SNC wordt gebruikt. |
   |||

   > [!NOTE]
   > Stel de omgevingsvariabelen niet SNC_LIB en SNC_LIB_64 in op de machine waar u de gegevensgateway en de SNC-bibliotheek hebt. Als deze zijn ingesteld, hebben ze voorrang op de snc-bibliotheekwaarde die door de connector wordt doorgegeven.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Veilig typen

Wanneer u uw SAP-verbinding maakt, wordt sterk typen standaard gebruikt om te controleren op ongeldige waarden door XML-validatie uit te voeren op basis van het schema. Dit gedrag kan u helpen problemen eerder op te sporen. De optie **Veilig typen** is beschikbaar voor achterwaartse compatibiliteit en controleert alleen de tekenreekslengte. Als u **Veilig typen**kiest, worden het TYPE DATS en het TIMS-type `xs:date` `xs:time`in `xmlns:xs="http://www.w3.org/2001/XMLSchema"`SAP behandeld als tekenreeksen in plaats van als hun XML-equivalenten, en , waar . Veilig typen beïnvloedt het gedrag voor alle schemageneratie, het verzendbericht voor zowel de payload "verzonden" als de antwoord 'ontvangen' en de trigger. 

Wanneer sterk typen wordt gebruikt **(Veilig typen** is niet ingeschakeld), worden de TYPEN DATS en TIMS in het schema toegewezen aan eenvoudigerXML-typen:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Wanneer u berichten verzendt met behulp van sterk typen, voldoet het DATS- en TIMS-antwoord aan de overeenkomende XML-typeindeling:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Wanneer **Veilig typen** is ingeschakeld, worden de DATS- en TIMS-typen in het schema toegewezen aan XML-tekenreeksvelden met alleen lengtebeperkingen, bijvoorbeeld:

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

Wanneer berichten worden verzonden met **veilig typen** ingeschakeld, ziet de DATS- en TIMS-reactie er als volgt uit:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Geavanceerde scenario's

### <a name="confirm-transaction-explicitly"></a>Transactie expliciet bevestigen

Wanneer u transacties vanuit Logic Apps naar SAP verzendt, vindt deze uitwisseling plaats in twee stappen, zoals beschreven in het SAP-document [Transactionele RFC-serverprogramma's.](https://help.sap.com/doc/saphelp_nwpi71/7.1/en-US/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true) Standaard verwerkt de actie **Verzenden naar SAP** zowel de stappen voor de functieoverdracht als voor de transactiebevestiging in één gesprek. De SAP-connector geeft u de mogelijkheid om deze stappen los te koppelen. U een IDOC verzenden en in plaats van de transactie automatisch te bevestigen, u de expliciete **actie Transactie-ID bevestigen** gebruiken.

Deze mogelijkheid om de bevestiging van de transactie-id los te koppelen is handig wanneer u transacties in SAP niet wilt dupliceren, bijvoorbeeld in scenario's waarin fouten kunnen optreden als gevolg van oorzaken zoals netwerkproblemen. Door de transactie-id afzonderlijk te bevestigen, wordt de transactie slechts één keer voltooid in uw SAP-systeem.

Hier is een voorbeeld dat dit patroon laat zien:

1. Maak een lege logische app en voeg een HTTP-trigger toe.

1. Voeg vanuit de SAP-connector de actie **IDOC verzenden** toe. Geef de gegevens op voor het IDOC dat u naar uw SAP-systeem stuurt.

1. Als u de transactie-id expliciet wilt bevestigen in een afzonderlijke stap, selecteert u in het veld **TID bevestigen** de optie **Nee**. Voor het optionele **GUID-veld Transactie-ID** u de waarde handmatig opgeven of de connector deze GUID automatisch laten genereren en retourneren in het antwoord van de actie IDOC verzenden.

   ![IDOC-actie-eigenschappen verzenden](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Als u de transactie-id expliciet wilt bevestigen, voegt u de actie **Transactie-id bevestigen** toe. Klik in het vak **Transactie-id** zodat de lijst met dynamische inhoud wordt weergegeven. Selecteer in die lijst de **waarde transactie-id** die is geretourneerd uit de actie **IDOC verzenden.**

   ![Transactie-id-actie bevestigen](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Nadat deze stap is uitgevoerd, wordt de huidige transactie gemarkeerd voltooid aan beide uiteinden, aan de kant van de SAP-connector en aan de kant van het SAP-systeem.

## <a name="known-issues-and-limitations"></a>Bekende problemen en beperkingen

Dit zijn de momenteel bekende problemen en beperkingen voor de SAP-connector:

* De SAP-trigger biedt geen ondersteuning voor gegevensgatewayclusters. In sommige failovergevallen kan het gegevensgatewayknooppunt dat communiceert met het SAP-systeem afwijken van het actieve knooppunt, wat resulteert in onverwacht gedrag. Voor verzendscenario's worden clusters van gegevensgateways ondersteund.

* De SAP-connector ondersteunt momenteel geen SAP-routertekenreeksen. De on-premises datagateway moet bestaan op hetzelfde LAN als het SAP-systeem dat u wilt verbinden.

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentiepagina van](https://docs.microsoft.com/connectors/sap/)de connector voor meer technische details over deze connector, zoals triggers, acties en limieten zoals beschreven in het Swagger-bestand van de connector.

> [!NOTE]
> Voor logische apps in een [integratieserviceomgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)gebruikt de ISE-versie met HET LABEL ISE in plaats daarvan de [ISE-berichtlimieten.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Volgende stappen

* [Maak verbinding met on-premises systemen](../logic-apps/logic-apps-gateway-connection.md) vanuit Azure Logic Apps.
* Meer informatie over het valideren, transformeren en gebruiken van andere berichtbewerkingen met het [Enterprise Integration Pack.](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md).
