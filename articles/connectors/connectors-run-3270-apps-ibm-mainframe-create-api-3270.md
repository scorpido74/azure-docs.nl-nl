---
title: Verbinding maken met 3270 apps op IBM mainframes
description: 3270 schermgestuurde apps integreren en automatiseren met Azure met Azure Logic Apps en IBM 3270-connector
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
ms.openlocfilehash: 808eef5424d678559ae94ffd04e41eacd0f16aee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371106"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Integreer 3270 schermgestuurde apps op IBM-mainframes met Azure met Azure met Azure

> [!NOTE]
> Deze connector bevindt zich in [*een openbare preview*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Met Azure Logic Apps en de IBM 3270-connector u ibm mainframe-apps openen en uitvoeren die u meestal gebruikt door door 3270 emulatorschermen te navigeren. Op die manier u uw IBM-mainframe-apps integreren met Azure, Microsoft en andere apps, services en systemen door geautomatiseerde werkstromen te maken met Azure Logic Apps. De connector communiceert met IBM mainframes met behulp van het TN3270-protocol en is beschikbaar in alle Azure Logic Apps-regio's, behalve Azure Government en Azure China 21Vianet. Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

In dit artikel worden deze aspecten beschreven voor het gebruik van de 3270-connector: 

* Waarom de IBM 3270-connector gebruiken in Azure Logic Apps en hoe de connector 3270-schermgestuurde apps uitvoert

* De vereisten en installatie voor het gebruik van de 3270-connector

* De stappen voor het toevoegen van 3270 connectoracties aan uw logische app

## <a name="why-use-this-connector"></a>Waarom deze connector gebruiken?

Om toegang te krijgen tot apps op IBM mainframes, gebruikt u meestal een 3270 terminal emulator, vaak een "groen scherm" genoemd. Deze methode is een beproefde manier, maar heeft beperkingen. Hoewel Host Integration Server (HIS) u helpt om rechtstreeks met deze apps te werken, is het soms mogelijk dat het scheiden van het scherm en de bedrijfslogica niet mogelijk is. Of misschien hebt u geen informatie meer over hoe de hosttoepassingen werken.

Om deze scenario's uit te breiden, werkt de IBM 3270-connector in Azure Logic Apps met de 3270-ontwerptool, die u gebruikt om de hostschermen die voor een specifieke taak worden gebruikt, de navigatiestroom voor die taak op te nemen en de methoden met invoer- en uitvoerparameters voor die taak. Het ontwerphulpprogramma converteert die informatie in metagegevens die de 3270-connector gebruikt bij het aanroepen van een actie die die taak vertegenwoordigt vanuit uw logische app.

Nadat u het metagegevensbestand hebt gegenereerd vanuit het ontwerphulpprogramma, voegt u dat bestand toe aan een integratieaccount in Azure. Op die manier heeft uw logica-app toegang tot de metagegevens van uw app wanneer u een actie voor 3270-connectoren toevoegt. De connector leest het metagegevensbestand van uw integratieaccount, verwerkt navigatie via de 3270-schermen en presenteert dynamisch de parameters voor de actie van de 3270-connector. U vervolgens gegevens aan de hosttoepassing verstrekken en de connector retourneert de resultaten naar uw logische app. Op die manier u uw oudere apps integreren met Azure, Microsoft en andere apps, services en systemen die Azure Logic Apps ondersteunt.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aanbevolen: een [integratieserviceomgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  U deze omgeving selecteren als locatie voor het maken en uitvoeren van uw logische app. Een ISE biedt toegang vanuit uw logische app tot bronnen die zijn beveiligd in virtuele Azure-netwerken.

* De logische app die u wilt gebruiken voor het automatiseren en uitvoeren van uw 3270-app op het scherm

  De IBM 3270-connector heeft geen triggers, dus gebruik een andere trigger om uw logische app te starten, zoals de **Recidief-trigger.** U vervolgens 3270-connectoracties toevoegen. Maak een [lege logische app om](../logic-apps/quickstart-create-first-logic-app-workflow.md)aan de slag te gaan. 
  Als u een ISE gebruikt, selecteert u die ISE als locatie van uw logische app.

* [Download en installeer de 3270 Design Tool.](https://aka.ms/3270-design-tool-download)
De enige voorwaarde is [Microsoft .NET Framework 4.6.1](https://aka.ms/net-framework-download).

  Met deze tool u de schermen, navigatiepaden, methoden en parameters voor de taken in uw app vastleggen die u toevoegt en uitvoert als 3270-connectoracties. De tool genereert een HIDX-bestand (Host Integration Designer XML) dat de benodigde metagegevens biedt voor de connector die kan worden gebruikt voor het besturen van uw mainframe-app.
  
  Volg na het downloaden en installeren van deze tool de volgende stappen om verbinding te maken met uw host:

  1. Open de 3270 Design Tool. Selecteer **Hostsessies**in het **menu Sessie** .
  
  1. Geef uw TN3270-hostservergegevens op.

* Een [integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), dat is de plaats waar u uw HIDX-bestand opslaat als een kaart, zodat uw logische app toegang heeft tot de metagegevens en methodedefinities in dat bestand. 

  Zorg ervoor dat je integratieaccount is gekoppeld aan de logische app die u gebruikt. Als u ook een ISE gebruikt, controleert u of de locatie van uw integratieaccount dezelfde ISE is die uw logische app gebruikt.

* Toegang tot de TN3270-server die uw mainframe-app host

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Overzicht van metagegevens maken

In een 3270-schermgestuurde app zijn de schermen en gegevensvelden uniek voor uw scenario's, dus de 3270-connector heeft deze informatie over uw app nodig, die u als metagegevens leveren. Met deze metagegevens worden gegevens beschreven waarmee uw logische app schermen kan identificeren en herkennen, wordt beschreven hoe u tussen schermen navigeren, waar gegevens moeten worden ingevoerd en waar u resultaten verwachten. Om deze metagegevens op te geven en te genereren, gebruikt u de 3270 Design Tool, die u door deze specifieke *modi*of fasen leidt, zoals later in meer details wordt beschreven:

* **Vastleggen:** In deze modus registreert u de schermen die nodig zijn voor het voltooien van een specifieke taak met uw mainframe-app, bijvoorbeeld het verkrijgen van een banksaldo.

* **Navigatie:** In deze modus geeft u het plan of pad op voor het navigeren door de schermen van uw mainframe-app voor de specifieke taak.

* **Methoden:** In deze modus definieert u bijvoorbeeld `GetBalance`de methode die het schermnavigatiepad beschrijft. U kiest ook de velden op elk scherm die de invoer- en uitvoerparameters van de methode worden.

### <a name="unsupported-elements"></a>Niet-ondersteunde elementen

Het ontwerpgereedschap ondersteunt deze elementen niet:

* Bms-kaarten (Partial IBM Basic Mapping Support) : Als u een BMS-kaart importeert, negeert het ontwerpgereedschap gedeeltelijke schermdefinities.
* In/out-parameters: u in/uit-parameters niet definiëren.
* Menuverwerking: niet ondersteund tijdens preview
* Arrayverwerking: niet ondersteund tijdens preview

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Beeldschermen vastleggen

In deze modus markeert u een item op elk 3270-scherm dat dat scherm uniek identificeert. U bijvoorbeeld een tekstregel of een complexere set voorwaarden opgeven, zoals specifieke tekst en een niet-leeg veld. U deze schermen opnemen via een liveverbinding met de hostserver of deze informatie importeren uit een BMS-kaart (IBM Basic Mapping Support). De live-verbinding maakt gebruik van een TN3270 emulator voor het verbinden met de host. Elke connectoractie moet worden toegewezen aan één taak die begint met verbinding maken met uw sessie en eindigt met het loskoppelen van uw sessie.

1. Als u dat nog niet hebt gedaan, opent u de 3270 Design Tool. Kies **vastleggen op** de werkbalk, zodat u de opnamemodus invoert.

1. Als u wilt beginnen met opnemen, drukt u op de F5-toets of selecteert u **Opname starten** in het menu **Opnemen**. 

1. Selecteer **Verbinding maken**in het menu **Sessie** .

1. Stap in het deelvenster **Vastleggen** vanaf het eerste scherm in uw app door uw app voor de specifieke taak die u opneemt.

1. Nadat u de taak hebt voltooid, meldt u zich af bij uw app zoals u dat gewoonlijk doet.

1. Selecteer **Verbinding verbreken**in het menu **Sessie** .

1. Als u de opname wilt stoppen, drukt u op de toetsen Shift + F5 of selecteert u In het menu **Opname** stoppen de optie **Opname stoppen**.

   Nadat u de schermen voor een taak hebt vastgelegd, toont het ontwerpgereedschap miniaturen die deze schermen vertegenwoordigen. Enkele opmerkingen over deze miniaturen:

   * Inbegrepen bij uw vastgelegde schermen, hebt u een scherm met de naam "Leeg".

     Wanneer u voor het eerst verbinding maakt met [CICS,](https://www.ibm.com/it-infrastructure/z/cics)moet u de sleutel 'Wissen' verzenden voordat u de naam invoeren voor de transactie die u wilt uitvoeren. Het scherm waarin u de toets 'Wissen' verzendt, heeft geen *herkenningskenmerken,* zoals een schermtitel, die u toevoegen met de editor Schermherkenning. Om dit scherm weer te geven, bevatten de miniaturen een scherm met de naam 'Leeg'. U dit scherm later gebruiken om het scherm weer te geven waarin u de transactienaam invoert.

   * Standaard gebruikt de naam voor een vastgelegd scherm het eerste woord op het scherm. Als die naam al bestaat, voegt het ontwerpgereedschap de naam toe aan een underscore en een getal, bijvoorbeeld 'WBGB' en 'WBGB_1'.

1. Voer de volgende stappen uit om een zinvollere naam aan een vastgelegd scherm te geven:

   1. Selecteer in het deelvenster **Hostscreens** het scherm dat u de naam wilt wijzigen.

   1. Zoek in hetzelfde deelvenster, onderaan in hetzelfde deelvenster, de eigenschap **Schermnaam.**

   1. Wijzig de huidige schermnaam in een meer beschrijvende naam.

1. Geef nu de velden op voor het identificeren van elk scherm.

   Met de 3270-gegevensstroom hebben schermen geen standaard-id's, dus u moet op elk scherm unieke tekst selecteren. Voor complexe scenario's u meerdere voorwaarden opgeven, bijvoorbeeld unieke tekst en een veld met een specifieke voorwaarde.

Nadat u klaar bent met het selecteren van de herkenningsvelden, gaat u naar de volgende modus.

### <a name="conditions-for-identifying-repeated-screens"></a>Voorwaarden voor het identificeren van herhaalde schermen

Als u de connector wilt navigeren en onderscheid wilt maken tussen schermen, vindt u meestal unieke tekst op een scherm dat u als id gebruiken tussen de vastgelegde schermen. Voor herhaalde schermen hebt u mogelijk meer identificatiemethoden nodig. Stel dat u twee schermen hebt die er hetzelfde uitzien, behalve het ene scherm, een geldige waarde retourneert, terwijl het andere scherm een foutbericht retourneert.

In het ontwerpgereedschap u *herkenningskenmerken*toevoegen, bijvoorbeeld een schermtitel zoals 'Accountsaldo opvragen', met behulp van de editor Schermherkenning. Als u een gevorkt pad hebt en beide branches hetzelfde scherm retourneren, maar met verschillende resultaten, hebt u andere herkenningskenmerken nodig. Bij uitvoering gebruikt de connector deze kenmerken voor het bepalen van de huidige vertakking en vork. Dit zijn de voorwaarden die u gebruiken:

* Specifieke waarde: deze waarde komt overeen met de opgegeven tekenreeks op de opgegeven locatie.
* GEEN specifieke waarde: deze waarde komt niet overeen met de opgegeven tekenreeks op de opgegeven locatie.
* Leeg: dit veld is leeg.
* NIET leeg: dit veld is niet leeg.

Zie het [navigatieplan Voorbeeld](#example-plan) later in dit onderwerp voor meer informatie.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Navigatieplannen definiëren

In deze modus definieert u de stroom of stappen voor het navigeren door de schermen van uw mainframe-app voor uw specifieke taak. Soms hebt u bijvoorbeeld meer dan één pad dat uw app kan volgen waarbij het ene pad het juiste resultaat oplevert, terwijl het andere pad een fout oplevert. Geef voor elk scherm de toetsaanslagen op die nodig `CICSPROD <enter>`zijn om naar het volgende scherm te gaan, zoals .

> [!TIP]
> Als u meerdere taken automatiseert die dezelfde verbindings- en koppelschermen gebruiken, biedt het ontwerpprogramma speciale typen Connect- en Disconnect-plannen. Wanneer u deze plannen definieert, u ze toevoegen aan het begin en het einde van uw navigatieplan.

### <a name="guidelines-for-plan-definitions"></a>Richtlijnen voor plandefinities

* Neem alle schermen op, te beginnen met verbinding maken en eindigen met het loskoppelen.

* U een zelfstandig abonnement maken of de abonnementen Verbinden en loskoppelen gebruiken, waarmee u een reeks schermen hergebruiken die vaak voor al uw transacties zijn gebruikt.

  * Het laatste scherm in uw Connect-abonnement moet hetzelfde scherm hebben als het eerste scherm in uw navigatieplan.

  * Het eerste scherm in uw Disconnect-abonnement moet hetzelfde scherm hebben als het laatste scherm in uw navigatieplan.

* Uw vastgelegde schermen kunnen veel herhaalde schermen bevatten, dus selecteer en gebruik slechts één exemplaar van herhaalde schermen in uw abonnement. Hier zijn enkele voorbeelden van herhaalde schermen:

  * Het aanmeldingsscherm, bijvoorbeeld het **MSG-10-scherm**
  * Het welkomstscherm voor CICS
  * Het scherm 'Wissen' of **Leeg**

<a name="create-plans"></a>

### <a name="create-plans"></a>Plannen maken

1. Kies **navigatie** op de werkbalk van het ontwerpgereedschap 3270 zodat u de navigatiemodus invoert.

1. Als u uw abonnement wilt starten, kiest u in het **navigatiedeelvenster** **Nieuw plan**.

1. Voer **onder Nieuwe abonnementsnaam**kiezen een naam in voor uw abonnement. Selecteer **in** de lijst Type het plantype:

   | Type plannen | Beschrijving |
   |-----------|-------------|
   | **Proces** | Voor zelfstandige of gecombineerde abonnementen |
   | **Verbinden** | Voor Connect-abonnementen |
   | **Verbinding verbreken** | Voor Abonnementen loskoppelen |
   |||

1. Sleep in het deelvenster **Hostscreens** de vastgelegde miniaturen naar het oppervlak van het navigatieplan in het **navigatiedeelvenster.**

   Als u het lege scherm wilt weergeven waar u de transactienaam invoert, gebruikt u het scherm Leeg.

1. Schik de schermen in de volgorde waarin de taak wordt beschreven die u definieert.

1. Als u het stroompad tussen schermen wilt definiëren, inclusief vorken en joins, kiest u op de werkbalk van het ontwerpgereedschap **Flow**.

1. Kies het eerste scherm in de flow. Sleep en teken een verbinding naar het volgende scherm in de stroom.

1. Geef voor elk scherm de waarden op voor de eigenschap **AID Key** (Attention Identifier) en voor de eigenschap **Vaste tekst,** waarmee de stroom naar het volgende scherm wordt verplaatst.

   Je zou kunnen hebben alleen de AID-sleutel, of zowel de AID-sleutel en vaste tekst.

Nadat u uw navigatieplan hebt voltooid, u [methoden definiëren in de volgende modus.](#define-method)

<a name="example-plan"></a>

### <a name="example"></a>Voorbeeld

Stel dat u in dit voorbeeld een CICS-transactie met de naam "WBGB" uitvoert met de volgende stappen: 

* Op het eerste scherm voert u een naam en een account in.
* Op het tweede scherm krijg je het rekeningsaldo.
* U verlaat het scherm 'Leeg'.
* U meldt zich af van CICS op het "MSG-10" scherm.

Stel ook dat u deze stappen herhaalt, maar u voert onjuiste gegevens in, zodat u het scherm vastleggen dat de fout weergeeft. Hier zijn de schermen die u vast te leggen:

* MSG-10
* CICS Welkom
* Leeg
* WBGB_1 (input)
* WBGB_2 (fout)
* Empty_1
* MSG-10_1

Hoewel veel schermen hier unieke namen krijgen, zijn sommige schermen hetzelfde scherm, bijvoorbeeld "MSG-10" en "Empty". Voor een herhaald scherm gebruikt u slechts één exemplaar voor dat scherm in uw abonnement. Hier volgen voorbeelden die laten zien hoe een zelfstandig abonnement, Connect-abonnement, Disconnect-abonnement en een gecombineerd abonnement er mogelijk uitzien:

* Zelfstandig abonnement

  ![Zelfstandig navigatieplan](./media/connectors-create-api-3270/standalone-plan.png)

* Abonnement verbinden

  ![Abonnement verbinden](./media/connectors-create-api-3270/connect-plan.png)

* Abonnement verbreken

  ![Abonnement verbreken](./media/connectors-create-api-3270/disconnect-plan.png)

* Gecombineerd plan

  ![Gecombineerd plan](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Voorbeeld: Herhaalde schermen identificeren

Als u de connector wilt navigeren en schermen wilt differentiëren, vindt u meestal unieke tekst op een scherm dat u als id op de vastgelegde schermen gebruiken. Voor herhaalde schermen hebt u mogelijk meer identificatiemethoden nodig. Het voorbeeldplan heeft een vork waar u schermen krijgen die op elkaar lijken. Het ene scherm retourneert een rekeningsaldo, terwijl het andere scherm een foutbericht retourneert.

Met het ontwerpgereedschap u herkenningskenmerken toevoegen, bijvoorbeeld een schermtitel met de naam 'Accountsaldo opvragen', met behulp van de editor Schermherkenning. In het geval met vergelijkbare schermen hebt u andere kenmerken nodig. Bij uitvoering gebruikt de connector deze kenmerken voor het bepalen van de vertakking en vork.

* In de vertakking die geldige invoer retourneert, het scherm met het accountsaldo, u een veld toevoegen met een 'niet lege' voorwaarde.

* In de vertakking die met een fout retourneert, u een veld toevoegen met een 'lege' voorwaarde.

<a name="define-method"></a>

## <a name="define-methods"></a>Methoden definiëren

In deze modus definieert u een methode die is gekoppeld aan uw navigatieplan. Voor elke parameter van de methode geeft u het gegevenstype op, zoals een tekenreeks, geheel getal, datum of tijd, enzovoort. Wanneer u klaar bent, u uw methode testen op de live host en bevestigen dat de methode werkt zoals verwacht. Vervolgens genereert u het bestand met metagegevens of hidx-bestand (Host Integration Designer XML), dat nu de methodedefinities heeft die moeten worden gebruikt voor het maken en uitvoeren van een actie voor de IBM 3270-connector.

1. Kies op de werkbalk van het ontwerpgereedschap 3270 **methoden,** zodat u de modus Methoden invoert. 

1. Selecteer in het **navigatiedeelvenster** het gewenste scherm met de gewenste invoervelden.

1. Voer de volgende stappen uit om de eerste invoerparameter voor uw methode toe te voegen:

   1. Kies in het **deelvenster Vastleggen** op het 3270-emulatorscherm het hele veld, niet alleen tekst in het veld, dat u als eerste ingang wilt gebruiken.

      > [!TIP]
      > Als u alle velden wilt weergeven en ervoor wilt zorgen dat u het volledige veld selecteert, selecteert u in het menu **Weergave** **alle velden**.

   1. Kies **invoerveld**op de werkbalk van het ontwerpgereedschap . 

   Als u meer invoerparameters wilt toevoegen, herhaalt u de vorige stappen voor elke parameter.

1. Voer de volgende stappen uit om de eerste uitvoerparameter voor uw methode toe te voegen:

   1. Kies in het **deelvenster Vastleggen** op het 3270-emulatorscherm het hele veld, niet alleen tekst in het veld, dat u als eerste uitvoer wilt gebruiken.

      > [!TIP]
      > Als u alle velden wilt weergeven en ervoor wilt zorgen dat u het volledige veld selecteert, selecteert u in het menu **Weergave** **alle velden**.

   1. Kies **Uitvoerveld**op de werkbalk van het ontwerpgereedschap .

   Als u meer uitvoerparameters wilt toevoegen, herhaalt u de vorige stappen voor elke parameter.

1. Nadat u alle parameters van uw methode hebt toegevoegd, definieert u deze eigenschappen voor elke parameter:

   | Naam van eigenschap | Mogelijke waarden | 
   |---------------|-----------------|
   | **Gegevenstype** | Byte, Datumtijd, Decimaal, Int, Lang, Kort, Tekenreeks |
   | **Veldvultechniek** | Parameters ondersteunen deze vultypen en vullen zich indien nodig met spaties: <p><p>- **Tekst:** Voer tekens achtereenvolgens in het veld in. <p>- **Vulling:** Vervang de inhoud van het veld door tekens, vul indien nodig met spaties. <p>- **EraseEofType**: Schakel het veld uit en voer vervolgens tekens achtereenvolgens in het veld in. |
   | **Tekenreeks opmaken** | Sommige parametergegevenstypen gebruiken een opmaaktekenreeks, waarmee de 3270-connector wordt geïnformeerd over het converteren van tekst van het scherm naar een .NET-gegevenstype: <p><p>- **DateTime:** de tekenreeks datetime-notatie volgt de [tekenreeksen voor aangepaste datum en tijdnotatie .NET](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). De datum `06/30/2019` gebruikt bijvoorbeeld de `MM/dd/yyyy`opmaaktekenreeks . <p>- **Decimal :** de tekenreeks decimale opmaak gebruikt de [cobol-afbeeldingscomponent](https://www.ibm.com/support/knowledgecenter/SS6SG3_5.2.0/com.ibm.cobol52.ent.doc/PGandLR/ref/rlddepic.html). Het getal `100.35` gebruikt bijvoorbeeld de `999V99`opmaaktekenreeks . |
   |||

## <a name="save-and-view-metadata"></a>Metagegevens opslaan en weergeven

Nadat u uw methode hebt gedefinieerd, maar voordat u uw methode test, slaat u alle informatie die u tot nu toe hebt gedefinieerd op in een RAP-bestand (.rap).
U dit RAP-bestand op elk gewenst moment opslaan tijdens elke modus. Het ontwerptool bevat ook een voorbeeld-RAP-bestand dat u openen en bekijken door te bladeren naar de installatiemap van het ontwerpgereedschap op deze locatie en het bestand WoodgroveBank.rap te openen:

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

Als u echter wijzigingen in het rap-bestand van het voorbeeld probeert op te slaan of een HIDX-bestand uit het voorbeeld-RAP-bestand genereert terwijl het bestand in de installatiemap van het ontwerpprogramma blijft, u een foutmelding 'toegang geweigerd' krijgen. Standaard wordt het ontwerpgereedschap geïnstalleerd in de map Programmabestanden zonder verhoogde machtigingen. Als u een foutmelding krijgt, probeert u een van de volgende oplossingen:

* Kopieer het voorbeeldbestand naar een andere locatie.
* Voer het ontwerpgereedschap uit als beheerder.
* Maak jezelf de eigenaar voor de SDK-map.

## <a name="test-your-method"></a>Test uw methode

1. Als u uw methode wilt uitvoeren tegen de live host, terwijl u nog steeds in de modus Methoden staat, drukt u op de F5-toets of op de werkbalk van het ontwerpgereedschap de optie **Uitvoeren**.

   > [!TIP]
   > U op elk gewenst moment van modus veranderen. Selecteer **in het** menu Bestand de optie **Modus**en selecteer de gewenste modus.

1. Voer de waarden van uw parameters in en kies **OK.**

1. Als u door wilt gaan naar het volgende scherm, kiest u **Volgende**.

1. Wanneer u klaar bent, kiest u **Gereed,** waarin de waarden van de uitvoerparameter worden weergegeven.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>HIDX-bestand genereren en uploaden

Wanneer u er klaar voor bent, genereert u het HIDX-bestand, zodat u uploaden naar uw integratieaccount. Met de 3270-ontwerptool wordt het HIDX-bestand gemaakt in een nieuwe submap waarin u uw RAP-bestand hebt opgeslagen.

1. Kies op de werkbalk van het ontwerpprogramma 3270 de optie **Code genereren**.

1. Ga naar de map met het RAP-bestand en open de submap die het hulpprogramma heeft gemaakt na het genereren van uw HIDX-bestand. Controleer of het hulpprogramma het HIDX-bestand heeft gemaakt.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en vind uw integratieaccount.

1. Voeg uw HIDX-bestand toe als een kaart aan uw integratieaccount door deze vergelijkbare stappen voor het toevoegen van kaarten te [volgen,](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md)maar wanneer u het kaarttype selecteert, selecteert u **HIDX**.

Wanneer u later in dit onderwerp voor het eerst een IBM 3270-actie toevoegt aan uw logische app, wordt u gevraagd een verbinding te maken tussen uw logische app en de hostserver door verbindingsgegevens te verstrekken, zoals de namen voor uw integratieaccount en hostserver . Nadat u de verbinding hebt gemaakt, u het eerder toegevoegde HIDX-bestand, de uit te voeren methode en de te gebruiken parameters selecteren.

Wanneer u al deze stappen hebt voltooid, u de actie gebruiken die u in uw logische app maakt om verbinding te maken met uw IBM-mainframe, schermen voor uw app te stimuleren, gegevens in te voeren, resultaten te retourneren, enzovoort. U ook andere acties blijven toevoegen aan uw logische app voor integratie met andere apps, services en systemen.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>IBM 3270-acties uitvoeren

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw logische app in Logic App Designer, als deze nog niet is geopend.

1. Kies onder de laatste stap waarin u een actie wilt toevoegen de optie **Nieuwe stap**en selecteer Een **actie toevoegen**. 

1. Kies onder het zoekvak de optie **Onderneming**. Voer in het zoekvak '3270' in als filter. Selecteer deze actie in de lijst met acties: **Een mainframeprogramma uitvoeren via een TN3270-verbinding**

   ![3270-actie selecteren](./media/connectors-create-api-3270/select-3270-action.png)

   Als u een actie tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl tussen de stappen. 
   Kies het plusteken (**+**) dat wordt weergegeven en selecteer Vervolgens Een actie **toevoegen**.

1. Als er nog geen verbinding bestaat, geeft u de benodigde informatie voor uw verbinding en kiest **u Maken**.

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Verbindingsnaam** | Ja | <*verbindingsnaam*> | De naam voor uw verbinding |
   | **Integratieaccount-id** | Ja | <*integratie-accountnaam*> | De naam van uw integratieaccount |
   | **URL van integratieaccount SAS** | Ja | <*integratie-account-SAS-URL*> | De SAS-URL (Shared Access Signature) van uw integratieaccount, die u genereren vanuit de instellingen van uw integratieaccount in de Azure-portal. <p>1. Selecteer in het menu van uw integratieaccount onder **Instellingen**de optie **Callback-URL**. <br>2. Kopieer in het rechterdeelvenster de **URL-waarde gegenereerde callback.** |
   | **Server** | Ja | <*TN3270-servernaam*> | De servernaam voor uw TN3270-service |
   | **Poort** | Nee | <*TN3270-serverpoort*> | De poort die wordt gebruikt door uw TN3270-server. Als deze optie leeg `23` blijft, wordt de standaardwaarde gebruikt als standaardwaarde. |
   | **Apparaattype** | Nee | <*IBM-terminal-model*> | De modelnaam of het nummer voor de IBM-terminal te emuleren. Als de connector leeg blijft, gebruikt deze standaardwaarden. |
   | **Codepagina** | Nee | <*code-paginanummer*> | Het codepaginanummer voor de host. Als deze optie leeg `37` blijft, wordt de standaardwaarde gebruikt als standaardwaarde. |
   | **Logische eenheidsnaam** | Nee | <*logische eenheidsnaam*> | De specifieke logische eenheidsnaam die u van de host moet aanvragen |
   | **SSL inschakelen?** | Nee | Aan of uit | Tls-versleuteling in- of uitschakelen. |
   | **Host ssl-certificaat valideren?** | Nee | Aan of uit | Validatie voor het certificaat van de server in- of uitschakelen. |
   ||||

   Bijvoorbeeld:

   ![Verbindingseigenschappen](./media/connectors-create-api-3270/connection-properties.png)

1. Geef de nodige informatie voor de actie:

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Hidx-naam** | Ja | <*HIDX-bestandsnaam*> | Selecteer het 3270 HIDX-bestand dat u wilt gebruiken. |
   | **Methodenaam** | Ja | <*methodenaam*> | Selecteer de methode in het HIDX-bestand dat u wilt gebruiken. Nadat u een methode hebt geselecteerd, wordt de lijst **nieuwe parameter toevoegen** weergegeven, zodat u parameters selecteren die u met die methode wilt gebruiken. |
   ||||

   Bijvoorbeeld:

   **Het HIDX-bestand selecteren**

   ![HIDX-bestand selecteren](./media/connectors-create-api-3270/select-hidx-file.png)

   **De methode selecteren**

   ![Methode selecteren](./media/connectors-create-api-3270/select-method.png)

   **De parameters selecteren**

   ![Parameters selecteren](./media/connectors-create-api-3270/add-parameters.png)

1. Wanneer u klaar bent, slaat u uw logica-app op en voert u deze uit.

   Nadat de logische app is uitgevoerd, worden de stappen van de uitvoering weergegeven. 
   In succesvolle stappen worden vinkjes weergegeven, terwijl in mislukte stappen de letter 'X' wordt weergegeven.

1. Als u de ingangen en uitvoer voor elke stap wilt bekijken, vouwt u die stap uit.

1. Als u de uitvoer wilt bekijken, kiest **u Ruwe uitvoer zien**.

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentiepagina van](https://docs.microsoft.com/connectors/si3270/)de connector voor meer technische details over deze connector, zoals triggers, acties en limieten zoals beschreven in het Swagger-bestand van de connector.

> [!NOTE]
> Voor logische apps in een [integratieserviceomgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)gebruikt de ISE-versie met HET LABEL ISE in plaats daarvan de [ISE-berichtlimieten.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
