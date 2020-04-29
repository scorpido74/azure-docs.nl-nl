---
title: Azure Resource Manager test station | Azure Marketplace
description: Een Marketplace-test station bouwen met behulp van Azure Resource Manager
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6125aa010d8676518b84f866343b01f95246160f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275931"
---
# <a name="azure-resource-manager-test-drive"></a>Test Drive voor Azure Resource Manager

Dit artikel is voor uitgevers die hun aanbieding op de Azure Marketplace hebben of die zich op AppSource bevinden, maar willen hun test station alleen bouwen met Azure-resources.

Een Azure Resource Manager-sjabloon (Resource Manager) is een gecodeerde container van Azure-resources die u ontwerpt om uw oplossing het beste weer te geven. Als u niet bekend bent met wat een resource manager-sjabloon is, leest u meer over [Resource Manager-sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) en [ontwerp Resource Manager-sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) om er zeker van te zijn dat u weet hoe u uw eigen sjablonen kunt bouwen en testen.

Welk test station gebruikt de opgegeven resource manager-sjabloon en maakt een implementatie van alle resources die nodig zijn van die resource manager-sjabloon in een resource groep.

Als u ervoor kiest om een Azure Resource Manager test station te bouwen, zijn de vereisten voor u:

- Bouw, test en upload uw Resource Manager-sjabloon voor testen.
- Configureer alle vereiste meta gegevens en instellingen om uw test station in te scha kelen.
- Publiceer uw aanbieding opnieuw met een test station ingeschakeld.

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>Een Azure Resource Manager-test station bouwen

Dit is het proces voor het bouwen van een Azure Resource Manager test station:

1. Ontwerp wat u met uw klanten wilt doen in een stroom diagram.
1. Definieer welke ervaringen u uw klanten wilt laten bouwen.
1. Bepaal op basis van de bovenstaande definities welke onderdelen en resources nodig zijn voor klanten om een dergelijke ervaring te bereiken: bijvoorbeeld D365 instance of een website met een Data Base.
1. Bouw het ontwerp lokaal en test de ervaring.
1. Verdeel de ervaring in een ARM-sjabloon implementatie en van daaruit:
    1. Definiëren welke onderdelen van de resources invoer parameters zijn;
    1. Wat variabelen zijn;
    1. De resultaten van de klant.
1. Publiceren, testen en live gaan.

Het belangrijkste deel van het bouwen van een Azure Resource Manager test station is het definiëren van de scenario ('s) die uw klanten moeten ervaren. Bent u een firewall product en wilt u zelf bepalen hoe goed u de aanvallen voor script injecties kunt afhandelen? Bent u een opslag product en wilt u een demonstratie doen over hoe snel en eenvoudig uw oplossing bestanden comprimeert?

Zorg ervoor dat u voldoende tijd besteedt aan het evalueren van de beste manieren om uw product uit te geven. In het bijzonder rond alle vereiste bronnen die u nodig hebt, wordt de Resource Manager-sjabloon nu voldoende gebundeld.

Om door te gaan met ons Firewall-voor beeld is het mogelijk dat u een open bare IP-URL nodig hebt voor uw service en een andere open bare IP-URL voor de website die uw firewall beveiligt. Elk IP-adres wordt geïmplementeerd op een virtuele machine en verbonden met een netwerk beveiligings groep + netwerk interface.

Zodra u het gewenste pakket met resources hebt ontworpen, wordt nu het schrijven en maken van de Resource Manager-sjabloon voor het test station geleverd.

## <a name="writing-test-drive-resource-manager-templates"></a>Resource Manager-sjablonen voor test stations schrijven

Test Drive voert implementaties uit in een volledig geautomatiseerde modus. als gevolg hiervan worden er door het testen van drive-sjablonen een aantal beperkingen beschreven.

### <a name="parameters"></a>Parameters

De meeste sjablonen hebben een aantal para meters. Para meters definiëren resource namen, bronnen grootten (bijvoorbeeld typen opslag accounts of grootte van virtuele machines), gebruikers namen en wacht woorden, DNS-namen, enzovoort. Wanneer u oplossingen implementeert met behulp van Azure Portal, kunt u alle para meters hand matig invullen, beschik bare DNS-namen of namen van opslag accounts kiezen, enzovoort.

![Lijst met para meters in een Azure Resource Manager](./media/azure-resource-manager-test-drive/param1.png)

Test drive werkt echter in een volledig automatische modus, zonder menselijke tussen komst, zodat alleen een beperkt aantal parameter categorieën wordt ondersteund. Als een para meter in de Resource Manager-sjabloon van het test station niet in een van de ondersteunde categorieën valt, moet u **deze para meter vervangen door een variabele of constante waarde.**

U kunt een geldige naam voor uw para meters gebruiken. test station herkent parameter categorie door gebruik te maken van de waarde van het meta gegevens type. U **moet het meta gegevens type voor elke sjabloon parameter opgeven**, anders wordt de validatie door de sjabloon niet door gegeven:

```json
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

Het is ook belang rijk om te weten dat **alle para meters optioneel zijn**,\'dus als u geen gebruik wilt maken van\', hoeft u dit niet te doen.

### <a name="accepted-parameter-metadata-types"></a>Geaccepteerde meta gegevens typen

| Meta gegevens type   | Parameter type  | Beschrijving     | Voorbeeld waarde    |
|---|---|---|---|
| **baseuri**     | tekenreeks          | Basis-URI van uw implementatie pakket| https:\//\<.\. \>. blob.core.Windows.net/\<\..\> |
| **gebruikers**    | tekenreeks          | Nieuwe wille keurige gebruikers naam.| admin68876      |
| **wachtwoord**    | beveiligde teken reeks    | Nieuw wille keurig wacht woord | LP! ACS\^2kh     |
| **sessie-id**   | tekenreeks          | Unieke sessie-ID van het test station (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>baseuri

Het test station initialiseert deze para meter met een **basis-URI** van uw implementatie pakket. u kunt deze para meter gebruiken om URI te maken van elk bestand dat in uw pakket is opgenomen.

```json
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

In uw sjabloon kunt u deze para meter gebruiken om een URI te maken van elk bestand uit het implementatie pakket voor test stations. In het volgende voor beeld ziet u hoe u een URI van de gekoppelde sjabloon bouwt:

```json
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>gebruikersnaam

Het test station initialiseert deze para meter met een nieuwe wille keurige gebruikers naam:

```json
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

Voorbeeld waarde:

    admin68876

U kunt wille keurige of constante gebruikers namen gebruiken voor uw oplossing.

#### <a name="password"></a>wachtwoord

Het test station initialiseert deze para meter met een nieuw wille keurig wacht woord:

```json
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

Voorbeeld waarde:

    Lp!ACS^2kh

U kunt wille keurige of constante wacht woorden gebruiken voor uw oplossing.

#### <a name="session-id"></a>sessie-ID

Test station Initialiseer deze para meter met een unieke GUID die de sessie-ID van het test station vertegenwoordigt:

```json
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique Test Drive session id."
    }
  },
  ...
}
```

Voorbeeld waarde:

    b8c8693e-5673-449c-badd-257a405a6dee

U kunt deze para meter gebruiken om de sessie van het test station uniek te identificeren, als dat nodig is.

### <a name="unique-names"></a>Unieke namen

Voor sommige Azure-resources, zoals opslag accounts of DNS-namen, zijn wereld wijd unieke namen vereist.

Dit betekent dat elke keer dat test station de Resource Manager-sjabloon implementeert, een **nieuwe resource groep wordt gemaakt met een unieke naam** voor\' alle bijbehorende resources. Daarom is het nodig om de functie [Unique string](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) te gebruiken die is gekoppeld aan de namen van de variabelen van de resource groep-id's voor het genereren van wille keurige unieke waarden:

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Zorg ervoor dat u de para meters en variabelen teken\'reeksen\'(contosovm) samen voegt met\'een unieke teken reeks uitvoer\'(resourceGroup (). id), omdat dit de unieke waarde en betrouw baarheid van elke variabele waarborgt.

De meeste resource namen kunnen bijvoorbeeld niet beginnen met een cijfer, maar een unieke teken reeks functie kan een teken reeks retour neren die begint met een cijfer. Als u onbewerkte unieke teken reeks uitvoer gebruikt, mislukken uw implementaties. 

In [dit artikel](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)vindt u meer informatie over regels en beperkingen voor de naamgeving van resources.

### <a name="deployment-location"></a>Implementatie locatie

U kunt het testen van het station beschikbaar maken in verschillende Azure-regio's. Het is een goed idee om een gebruiker te laten kiezen uit de dichtstbijzijnde regio om te voorzien in de Beast-gebruikers ervaring.

Wanneer een test station een exemplaar van het Lab maakt, wordt er altijd een resource groep gemaakt in de regio die is gekozen door een gebruiker en wordt uw implementatie sjabloon uitgevoerd in deze groeps context. Uw sjabloon moet dus de implementatie locatie van de resource groep kiezen:

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

En gebruik deze locatie voor elke resource voor een specifiek Lab-exemplaar:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

U moet ervoor zorgen dat uw abonnement alle resources mag implementeren die u wilt implementeren in elk van de regio's die u selecteert. U moet er ook voor zorgen dat de installatie kopieën van uw virtuele machines beschikbaar zijn in alle regio's die u wilt inschakelen. anders werkt uw implementatie sjabloon niet voor bepaalde regio's.

### <a name="outputs"></a>Uitvoer

Normaal gesp roken met Resource Manager-sjablonen kunt u implementeren zonder dat er uitvoer wordt geproduceerd. Dit komt doordat u alle waarden kent die u gebruikt om sjabloon parameters te vullen en u kunt altijd hand matig eigenschappen van een resource controleren.

Voor testen van Resource Manager-sjablonen is het\'echter belang rijk om terug te gaan naar alle informatie die is vereist om toegang te krijgen tot de test omgeving (website-uri's, hostnamen van virtuele machines, gebruikers namen en wacht woorden). Zorg ervoor dat alle namen van uw uitvoer leesbaar zijn omdat deze variabelen aan de klant worden gepresenteerd.

Er zijn geen beperkingen met betrekking tot sjabloon uitvoer. Houd er rekening mee dat met test drive alle uitvoer waarden worden geconverteerd naar **teken reeksen**, dus als u een object naar de uitvoer verzendt, ziet de gebruiker een JSON-teken reeks.

Voorbeeld:

```json
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>Abonnements limieten

U moet rekening houden met de limieten voor abonnementen en services. Als u bijvoorbeeld Maxi maal tien virtuele machines met 4 kernen wilt implementeren, moet u ervoor zorgen dat het abonnement dat u gebruikt voor uw Lab, gebruikmaakt van 40-kernen.

In [dit artikel](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)vindt u meer informatie over Azure-abonnement en service limieten. Als er meerdere test stations tegelijk kunnen worden gemaakt, controleert u of uw abonnement de \# kern geheugens kan verwerken vermenigvuldigd met het totale aantal gelijktijdige test schijven dat kan worden gemaakt.

### <a name="what-to-upload"></a>Wat u kunt uploaden

Test Drive-Resource Manager-sjabloon wordt geüpload als een zip-bestand, dat verschillende implementatie artefacten kan bevatten, maar moet één bestand hebben met de naam **Main-Temp late. json**. Dit bestand is Azure Resource Manager implementatie sjabloon en het test station gebruikt het om een lab te instantiëren.

Als u meer resources voorbij dit bestand hebt, kunt u ernaar verwijzen als een externe resource in de sjabloon, of u kunt de resource in het zip-bestand toevoegen.

Tijdens de publicatie certificering pakt test station uw implementatie pakket uit en wordt de inhoud ervan in een interne BLOB-container van het test station geplaatst. De container structuur weerspiegelt de structuur van uw implementatie pakket:

| package. zip                       | BLOB-container van test station         |
|---|---|
| Main-Temp late. json                | \//\<https:\... \>. blob.core.Windows.net/\<\... \>/Main-template.json  |
| sjablonen/oplossing. json           | \//\<https:\... \>. blob.core.Windows.net/\<\... \>/templates/Solution.json |
| scripts/opwarm. ps1                | \//\<https:\... \>. blob.core.Windows.net/\<\... \>/scripts/Warmup.ps1  |


We noemen een URI van deze basis-URI voor de BLOB-container. Elke revisie van uw Lab heeft een eigen BLOB-container en daarom heeft elke revisie van uw Lab een eigen basis-URI. Het test station kan een basis-URI van uw uitgepakt implementatie pakket door geven aan uw sjabloon via sjabloon parameters.

## <a name="transforming-template-examples-for-test-drive"></a>Sjabloon voorbeelden voor een test station transformeren

Het proces van het omzetten van een architectuur van resources in een resource manager-sjabloon voor test schijven kan bevallen. Om dit proces gemakkelijker te maken, hebben we\'voor beelden gemaakt van de beste [trans formatie van huidige implementatie sjablonen](./transforming-examples-for-test-drive.md).

## <a name="how-to-publish-a-test-drive"></a>Een test station publiceren

Nu u uw test station hebt gemaakt, gaat u in deze sectie naar elk van de velden die nodig zijn voor het publiceren van uw test station.

![Test station inschakelen in de gebruikers interface](./media/azure-resource-manager-test-drive/howtopub1.png)

Het eerste en belangrijkste veld is om te scha kelen of u een test station wilt inschakelen voor uw aanbieding of niet. Als u **Ja selecteert,** wordt de rest van het formulier met alle vereiste velden weer gegeven om in te vullen. Wanneer u **Nee selecteert,** wordt het formulier uitgeschakeld en als u opnieuw publiceert met het test station is uitgeschakeld, wordt het test station uit productie verwijderd.

Opmerking: als er tests zijn die actief worden gebruikt door gebruikers, blijven die test stations actief totdat de sessie is verlopen.

### <a name="details"></a>Details

De volgende sectie die u moet invullen, is de details van de aanbieding voor uw test drive.

![Gedetailleerde gegevens van het test station](./media/azure-resource-manager-test-drive/howtopub2.png)

**Beschrijving:** *vereist* dit is de plaats waar u de hoofd beschrijving schrijft over wat er op uw test station staat. De klant komt hier om te lezen welke scenario's uw test station zal bedekken over uw product. 

**Gebruikers handleiding:** *vereist* dit is het gedetailleerde overzicht van uw test drive-ervaring. De klant opent dit en kan leiden tot precies wat u nodig hebt voor hun test station. Het is belang rijk dat deze inhoud eenvoudig te begrijpen is en volgt. (Moet een PDF-bestand zijn)

**Demo video testen:** *Aanbevolen* , vergelijkbaar met de gebruikers handleiding, het is het beste om een video-zelf studie voor uw test beleving op te vragen. De klant zal dit vóór of tijdens hun test station bekijken en kunnen door lopen wat u nodig hebt voor hun test station. Het is belang rijk dat deze inhoud eenvoudig te begrijpen is en volgt.

- **Naam** : titel van uw video
- **Link** -moet een Inge sloten URL van uw buis of video zijn. Hieronder vindt u een voor beeld van het ophalen van de Inge sloten URL:
- **Miniatuur** : moet een afbeelding van hoge kwaliteit (533x324) zijn. Het is raadzaam om hier een scherm opname van een deel van de test drive-ervaring te maken.

Hieronder ziet u hoe deze velden voor uw klant worden weer gegeven tijdens hun test beleving.

![Locatie van de velden van het test station in de Marketplace-aanbieding](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Technische configuratie

In het volgende gedeelte om in te vullen kunt u uw Resource Manager-sjabloon voor testen uploaden en definiëren hoe specifiek uw exemplaren van het test station werken.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**Instanties-** *vereist* dit is de locatie van het aantal instanties dat u wilt, in welke regio (en) en hoe snel uw klanten het test station kunnen verkrijgen.

- **Instanties** : in de geselecteerde regio's kunt u kiezen waar u de Resource Manager-sjabloon voor testen hebt geïmplementeerd. Het is raadzaam om slechts één regio te kiezen waarin u uw klanten het beste kunt vinden.
- **Het aantal** exemplaren van een test station dat al is geïmplementeerd en nog niet toegankelijk is op basis van de geselecteerde regio. Klanten hebben direct toegang tot deze test stations in plaats van te wachten op een implementatie. De balans is dat deze instanties altijd worden uitgevoerd op uw Azure-abonnement, zodat de kosten voor de uptime groter worden. Het wordt ten zeerste aanbevolen om **ten minste één Hot instance**te hebben, omdat de meeste klanten niet willen wachten op volledige implementaties om te volt ooien, zodat er sprake is van een afname van het gebruik door de klant.
- **Warm** : aantal exemplaren van het test station per regio die zijn geïmplementeerd en vervolgens de virtuele machine is gestopt en opgeslagen in azure Storage. De wacht tijd voor warme instanties is trager dan hot instances, maar de kosten voor de uptime van opslag zijn ook minder duur.
- **Koude** -aantal exemplaren van het test station per regio die mogelijk kunnen worden geïmplementeerd. Voor koude instanties is het hele test station Resource Manager-sjabloon vereist om een implementatie te door lopen op het moment van een klant die het test station aanvraagt, zodat het langzamer is dan warme of warme exemplaren. De balans is echter dat u alleen betaalt voor de duur van de test schijf.

Op dit moment wordt het totale aantal mogelijke gelijktijdige test stations berekend dat u beschikbaar wilt maken en controleren of uw quotum limiet voor uw abonnement de gelijktijdige hoeveelheid kan verwerken:

**(Aantal geselecteerde regio's x Hot instances) + (aantal geselecteerde regio's x warme instanties) + (aantal geselecteerde regio's x koude instanties)**

**Test drive duur (uren):** *vereiste* duur voor hoe lang het test station actief blijft, in \# uren. Het test station wordt automatisch beëindigd nadat deze periode is verstreken.

De resource **Manager-sjabloon testen:** u *moet* uw Resource Manager-sjabloon hier uploaden. Dit is het bestand dat u in de vorige sectie hebt gemaakt. Noem het hoofd sjabloon bestand: ' Main-Temp late. json ' en zorg ervoor dat uw Resource Manager-sjabloon uitvoer parameters bevat voor sleutel variabelen die nodig zijn. (Moet een zip-bestand zijn)

**Toegangs gegevens:** *vereist* nadat een klant hun test station heeft opgehaald, de toegangs gegevens worden weer gegeven. Deze instructies zijn bedoeld voor het delen van de bruikbare uitvoer parameters van uw test station Resource Manager-sjabloon. Als u uitvoer parameters wilt toevoegen, gebruikt u dubbele accolades (bijvoorbeeld **{{uitvoermap}}**). deze worden op de juiste wijze ingevoegd op de locatie. (De opmaak van HTML-teken reeksen wordt hier aanbevolen om weer te geven in de front-end).

### <a name="test-drive-deployment-subscription-details"></a>Details van implementatie abonnement testen

De laatste sectie die moet worden ingevuld, is om de test stations automatisch te kunnen implementeren door uw Azure-abonnement en Azure Active Directory (AD) te verbinden.

![Details van implementatie abonnement testen](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure-abonnements-id:** dit is *vereist* voor toegang tot Azure-Services en de Azure Portal. In het abonnement wordt het resource gebruik gerapporteerd en de services worden gefactureerd. Als u nog geen **apart** Azure-abonnement voor test stations hebt, kunt u er een maken. U kunt Azure-abonnement-Id's vinden door u aan te melden bij Azure Portal en te navigeren naar de abonnementen in het menu aan de linkerkant. (Bijvoorbeeld: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Azure-abonnementen](./media/azure-resource-manager-test-drive/subdetails2.png)

**Azure AD-Tenant-id:** *vereist* als u een Tenant-id al beschikbaar hebt, kunt u deze hieronder in\> de eigenschappen-map-id vinden.

![Azure Active Directory eigenschappen](./media/azure-resource-manager-test-drive/subdetails3.png)

Als dat niet het geval is, maakt u een nieuwe Tenant in Azure Active Directory.

![Lijst met Azure Active Directory tenants](./media/azure-resource-manager-test-drive/subdetails4.png)

![De organisatie, het domein en het land/de regio van de Azure AD-Tenant definiëren](./media/azure-resource-manager-test-drive/subdetails5.png)

![De selectie bevestigen](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure AD-App-ID:** de volgende stap is *vereist* om een nieuwe toepassing te maken en te registreren. Deze toepassing wordt gebruikt voor het uitvoeren van bewerkingen op uw exemplaar van het test station.

1. Ga naar de zojuist gemaakte map of al een bestaande map en selecteer Azure Active Directory in het deel venster filter.
2. Zoek "App-registraties" en klik op toevoegen.
3. Geef een toepassings naam op.
4. Het type van als web-app/API selecteren
5. Geef een waarde op voor de aanmeldings-URL, die\'we hebben gewonnen door dit veld te gebruiken.
6. Klik op maken.
7. Nadat de toepassing is gemaakt, gaat u naar eigenschappen:\> Stel de toepassing in als multi tenant en druk op opslaan.

Klik op Opslaan. De laatste stap bestaat uit het oppakken van de toepassings-ID voor deze geregistreerde app en deze in het veld test station te plakken.

![Details van Azure AD-toepassings-ID](./media/azure-resource-manager-test-drive/subdetails7.png)

Omdat we de toepassing gebruiken om te implementeren in het abonnement, moeten we de toepassing toevoegen als een bijdrager aan het abonnement. Hieronder vindt u instructies:

1. Navigeer naar de Blade abonnementen en selecteer het juiste abonnement dat u alleen voor het test station gebruikt.
1. Klik op **Toegangsbeheer (IAM)**.
1. Klik op **het tabblad roltoewijzingen.**  ![Een nieuwe Access Control Principal toevoegen](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Klik op **roltoewijzing toevoegen**.
1. Stel de rol in als **Inzender**.
1. Typ de naam van de Azure AD-toepassing en selecteer de toepassing om de rol toe te wijzen.
    ![De machtigingen toevoegen](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Klik op **Opslaan**.

**Azure AD-App sleutel:** *het laatste veld moet een* verificatie sleutel genereren. Voeg onder sleutels een sleutel beschrijving toe, stel de duur in op nooit verlopen en selecteer vervolgens opslaan. Het is **belang rijk** om te voor komen dat u een verlopen sleutel hebt, waardoor uw test drive in productie worden verbroken. Kopieer deze waarde en plak deze in het verplichte veld test station.

![Hiermee worden de sleutels voor de Azure AD-toepassing weer gegeven](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>Volgende stappen

Nu u alle velden van het test station hebt ingevuld, gaat u naar uw aanbieding en publiceert u deze **opnieuw** . Zodra uw test station is door gegeven, kunt u de gebruikers ervaring uitgebreid testen in de **Preview** van uw aanbieding. Start een test station in de gebruikers interface en open vervolgens uw Azure-abonnement in de Azure Portal en controleer of uw test stations volledig correct zijn geïmplementeerd.

![Azure Portal](./media/azure-resource-manager-test-drive/subdetails9.png)

Het is belang rijk te weten dat u geen exemplaren van een test station verwijdert wanneer deze zijn ingericht voor uw klanten, zodat de service van de test drive deze bron groepen automatisch opschoont nadat een klant is voltooid.

Zodra u vertrouwd bent met uw preview-aanbieding, is het tijd om **Live te gaan**! Er is een laatste beoordelings proces van micro soft wanneer de aanbieding is gepubliceerd om de volledige end-to-end-ervaring dubbel te controleren. Als het aanbod om de een of andere reden wordt afgewezen, wordt er een melding verzonden naar de technische contact persoon van uw aanbieding waarin wordt uitgelegd wat er moet worden opgelost.

Als u meer vragen hebt, zoekt u naar advies over het oplossen van problemen of wilt u uw test station sneller laten verlopen, ga dan naar [Veelgestelde vragen, probleem oplossing & aanbevolen procedures](./marketing-and-best-practices.md).
