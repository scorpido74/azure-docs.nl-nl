---
title: Azure Resource Manager-teststation | Azure Marketplace
description: Een Marketplace-teststation maken met Azure Resource Manager
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6125aa010d8676518b84f866343b01f95246160f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275931"
---
# <a name="azure-resource-manager-test-drive"></a>Test Drive voor Azure Resource Manager

Dit artikel is voor uitgevers die hun aanbieding op de Azure Marketplace hebben of die zich op AppSource bevinden, maar hun teststation willen bouwen met alleen Azure-bronnen.

Een azure resource manager-sjabloon (Resource Manager) is een gecodeerde container met Azure-resources die u ontwerpt om uw oplossing het beste weer te geven. Als u niet bekend bent met wat een Resource Manager-sjabloon is, leest u het [begrijpen van Resource Manager-sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) en [het ontwerpen van Resource Manager-sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) om ervoor te zorgen dat u weet hoe u uw eigen sjablonen maken en testen.

Wat Test Drive doet, is dat de meegeleverde resourcemanagersjabloon wordt opgenomen en dat alle resources die nodig zijn van die resourcemanagersjabloon, worden geïmplementeerd in een resourcegroep.

Als u ervoor kiest een Azure Resource Manager-teststation te bouwen, zijn de vereisten voor u om:

- Bouw, test en upload vervolgens de sjabloon Test Drive Resource Manager.
- Configureer alle vereiste metagegevens en instellingen om uw teststation in te schakelen.
- Publiceer uw aanbieding opnieuw met Test Drive ingeschakeld.

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>Een Azure Resource Manager-teststation maken

Hier is het proces voor het bouwen van een Azure Resource Manager Test Drive:

1. Ontwerp wat u wilt dat uw klanten doen in een stroomdiagram.
1. Definieer welke ervaringen u wilt dat uw klanten bouwen.
1. Op basis van de bovenstaande definities, beslissen welke stukken en middelen nodig zijn voor klanten om een dergelijke ervaring te bereiken: bijvoorbeeld, D365 instantie, of een website met een database.
1. Bouw het ontwerp lokaal en test de ervaring.
1. Verpak de ervaring in een ARM-sjabloonimplementatie en van daaruit:
    1. Definieer welke onderdelen van de resources invoerparameters zijn;
    1. Welke variabelen zijn;
    1. Welke outputs worden gegeven aan de klantervaring.
1. Publiceer, test en ga live.

Het belangrijkste onderdeel van het bouwen van een Azure Resource Manager Test Drive is om te definiëren welk scenario(en) u wilt dat uw klanten ervaren. Bent u een firewall product en u wilt demo hoe goed je omgaan met script injectie aanvallen? Bent u een opslagproduct en wilt u demo hoe snel en eenvoudig uw oplossing bestanden comprimeert?

Zorg ervoor dat u voldoende tijd besteedt aan het evalueren van wat de beste manieren zijn om met uw product te pronken. Met name rond alle benodigde resources die u nodig zou hebben, omdat het verpakken van de Resource Manager-sjabloon voldoende eenvoudiger maakt.

Om verder te gaan met ons firewallvoorbeeld, kan de architectuur zijn dat u een openbare IP-URL voor uw service en een andere openbare IP-URL voor de website die uw firewall beschermt, nodig hebt. Elk IP-adres wordt geïmplementeerd op een virtuele machine en verbonden met een netwerkbeveiligingsgroep + netwerkinterface.

Zodra u het gewenste pakket van resources hebt ontworpen, komt nu het schrijven en bouwen van de Test Drive Resource Manager sjabloon.

## <a name="writing-test-drive-resource-manager-templates"></a>Sjablonen voor Test Drive Resource Manager schrijven

Test drive voert implementaties uit in een volledig geautomatiseerde modus en daarom hebben Test Drive-sjablonen een aantal beperkingen die hieronder worden beschreven.

### <a name="parameters"></a>Parameters

De meeste sjablonen hebben een reeks parameters. Parameters definiëren resourcenamen, resources-grootte (bijvoorbeeld typen opslagaccounts of virtuele machineformaten), gebruikersnamen en wachtwoorden, DNS-namen enzovoort. Wanneer u oplossingen implementeert met Azure-portal, u al deze parameters handmatig invullen, beschikbare DNS-namen of namen van opslagaccountkiezen kiezen, enzovoort.

![Lijst met parameters in een Azure Resource Manager](./media/azure-resource-manager-test-drive/param1.png)

Testdrive werkt echter in een volledig automatische modus, zonder menselijke interactie, dus het ondersteunt slechts een beperkte set parametercategorieën. Als een parameter in de sjabloon Test Drive Resource Manager niet in een van de ondersteunde categorieën valt, moet u **deze parameter vervangen door een variabele of constante waarde.**

U elke geldige naam voor uw parameters gebruiken, Test Drive herkent parametercategorie met behulp van metagegevens-type waarde. U **moet metagegevens type opgeven voor elke sjabloonparameter,** anders wordt de validatie niet doorgegeven:

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

Het is ook belangrijk op te merken dat **alle parameters zijn optioneel,** dus als je niet\'wilt gebruiken, hoeft\'u niet te doen.

### <a name="accepted-parameter-metadata-types"></a>Geaccepteerde parametermetagegevenstypen

| Metagegevenstype   | Parametertype  | Beschrijving     | Voorbeeldwaarde    |
|---|---|---|---|
| **baseuri baseuri**     | tekenreeks          | Uri baseren op uw implementatiepakket| \//\<https:\.. \>.blob.core.windows.net/\<\..\> |
| **Gebruikersnaam**    | tekenreeks          | Nieuwe willekeurige gebruikersnaam.| beheerder68876      |
| **wachtwoord**    | beveiligde tekenreeks    | Nieuw willekeurig wachtwoord | Lp!ACS\^2kh     |
| **sessie-id**   | tekenreeks          | Unieke testdrive-sessie-id (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>baseuri baseuri

Test Drive initialiseert deze parameter met een **Base Uri** van uw implementatiepakket, zodat u deze parameter gebruiken om Uri te construeren van elk bestand dat in uw pakket is opgenomen.

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

In uw sjabloon u deze parameter gebruiken om een Uri van een bestand te maken vanuit uw implementatiepakket teststation. In het onderstaande voorbeeld ziet u hoe u een Uri van de gekoppelde sjabloon maakt:

```json
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>gebruikersnaam

Test Drive initialiseert deze parameter met een nieuwe willekeurige gebruikersnaam:

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

Voorbeeldwaarde:

    admin68876

U willekeurige of constante gebruikersnamen gebruiken voor uw oplossing.

#### <a name="password"></a>wachtwoord

Test Drive initialiseert deze parameter met een nieuw willekeurig wachtwoord:

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

Voorbeeldwaarde:

    Lp!ACS^2kh

U willekeurige of constante wachtwoorden gebruiken voor uw oplossing.

#### <a name="session-id"></a>sessie-id

Test Drive initialiseer deze parameter met een unieke GUID die testdrive-sessie-id vertegenwoordigt:

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

Voorbeeldwaarde:

    b8c8693e-5673-449c-badd-257a405a6dee

U deze parameter gebruiken om de Test Drive-sessie op unieke wijze te identificeren, als dat nodig is.

### <a name="unique-names"></a>Unieke namen

Voor sommige Azure-bronnen, zoals opslagaccounts of DNS-namen, zijn wereldwijd unieke namen vereist.

Dit betekent dat elke keer dat Test Drive de resourcemanagersjabloon implementeert, een\' **nieuwe resourcegroep** wordt gemaakt met een unieke naam voor al zijn resources. Daarom is het vereist om de [unieke string-functie](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) te gebruiken die is gekoppeld aan uw variabele namen op namen van resourcegroepen om willekeurige unieke waarden te genereren:

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Zorg ervoor dat u uw parameter/variabele\'tekenreeksen (contosovm)\'samenvoegt met een\'unieke tekenreeksuitvoer (resourceGroup().id),\'omdat dit de uniciteit en betrouwbaarheid van elke variabele garandeert.

De meeste resourcenamen kunnen bijvoorbeeld niet beginnen met een cijfer, maar de unieke tekenreeksfunctie kan een tekenreeks retourneren, die begint met een cijfer. Dus als u onbewerkte unieke tekenreeksuitvoer gebruikt, mislukken uw implementaties. 

In [dit artikel](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)vindt u aanvullende informatie over regels en beperkingen voor het benoemen van resources.

### <a name="deployment-location"></a>Implementatielocatie

U u Test Drive beschikbaar maken in verschillende Azure-regio's. Het idee is om een gebruiker in staat te stellen de dichtstbijzijnde regio te kiezen, om de gebruikerservaring van het beest te bieden.

Wanneer Test Drive een instantie van het lab maakt, wordt er altijd een resourcegroep in de regio gemaakt die door een gebruiker is gekozen en wordt de implementatiesjabloon in deze groepscontext uitgevoerd. Uw sjabloon moet dus de implementatielocatie kiezen uit de resourcegroep:

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

En gebruik deze locatie vervolgens voor elke resource voor een specifiek Lab-exemplaar:

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

U moet ervoor zorgen dat uw abonnement alle resources kan implementeren die u wilt implementeren in elk van de regio's die u selecteert. U moet er ook voor zorgen dat uw virtuele machineafbeeldingen beschikbaar zijn in alle regio's die u gaat inschakelen, anders werkt uw implementatiesjabloon niet voor sommige regio's.

### <a name="outputs"></a>Uitvoer

Normaal gesproken u met Resource Manager-sjablonen implementeren zonder dat u uitvoer produceert. Dit komt omdat u alle waarden kent die u gebruikt om sjabloonparameters in te vullen en u altijd handmatig eigenschappen van een resource inspecteren.

Voor Test Drive Resource Manager-sjablonen is het\'echter belangrijk om alle informatie terug te sturen naar Test Drive, die nodig is om toegang te krijgen tot het lab (website-URI's, hostnamen van virtuele machines, gebruikersnamen en wachtwoorden). Zorg ervoor dat al uw uitvoernamen leesbaar zijn omdat deze variabelen aan de klant worden gepresenteerd.

Er zijn geen beperkingen met betrekking tot sjabloonuitvoer. Vergeet niet dat Test Drive alle uitvoerwaarden omzet in **tekenreeksen,** dus als u een object naar de uitvoer verzendt, ziet een gebruiker JSON-tekenreeks.

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

### <a name="subscription-limits"></a>Abonnementslimieten

Nog een ding dat je moet rekening houden is abonnement en service grenzen. Als u bijvoorbeeld maximaal tien virtuele 4-core virtuele machines wilt implementeren, moet u ervoor zorgen dat het abonnement dat u voor uw Lab gebruikt, u 40 cores gebruiken.

Meer informatie over Azure-abonnements- en servicelimieten vindt u in [dit artikel.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) Aangezien meerdere teststations tegelijkertijd kunnen worden gemaakt, controleert u \# of uw abonnement het aantal cores kan verwerken dat wordt vermenigvuldigd met het totale aantal gelijktijdige teststations dat kan worden gemaakt.

### <a name="what-to-upload"></a>Wat te uploaden

De sjabloon Test Drive Resource Manager wordt geüpload als een zip-bestand, dat verschillende implementatieartefacten kan bevatten, maar moet één bestand hebben met de naam **main-template.json**. Dit bestand is azure resource manager-implementatiesjabloon en Test Drive gebruikt het om een lab te instantiëren.

Als u extra bronnen buiten dit bestand hebt, u ernaar verwijzen als een externe bron in de sjabloon of u de bron opnemen in het zip-bestand.

Tijdens de publicatiecertificering ritst Test Drive uw implementatiepakket uit en stopt de inhoud ervan in een interne Blob-container voor teststations. De containerstructuur weerspiegelt de structuur van uw implementatiepakket:

| package.zip                       | Blobcontainer teststation         |
|---|---|
| main-template.json                | \//\<https:\... \>.blob.core.windows.net/\<\.. \>/main-template.json  |
| sjablonen/solution.json           | \//\<https:\... \>.blob.core.windows.net/\<\.. \>/templates/solution.json |
| scripts/warming-up.ps1                | \//\<https:\... \>.blob.core.windows.net/\<\.. \>/scripts/warming-up.ps1  |


We noemen een Uri van deze blob container Base Uri. Elke revisie van uw lab heeft zijn eigen blobcontainer, en daarom heeft elke revisie van uw lab zijn eigen Base Uri. Testdrive kan een Base Uri van uw uitgepakte implementatiepakket in uw sjabloon passeren via sjabloonparameters.

## <a name="transforming-template-examples-for-test-drive"></a>Sjabloonvoorbeelden transformeren voor teststation

Het proces van het omzetten van een architectuur van resources in een Test Drive Resource Manager-sjabloon kan ontmoedigend zijn. Om dit proces gemakkelijker te\'maken, hebben we voorbeelden gemaakt over hoe [u de huidige implementatiesjablonen hier](./transforming-examples-for-test-drive.md)het beste transformeren.

## <a name="how-to-publish-a-test-drive"></a>Een teststation publiceren

Nu u uw testrit hebt laten bouwen, loopt deze sectie door elk van de velden die nodig zijn om uw testrit met succes te publiceren.

![Teststation inschakelen in de gebruikersinterface](./media/azure-resource-manager-test-drive/howtopub1.png)

Het eerste en belangrijkste veld is om te schakelen of u testdrive wilt inschakelen voor uw aanbieding of niet. Wanneer u **Ja selecteert,** wordt de rest van het formulier met alle vereiste velden weergegeven die u invullen. Wanneer u **Nee selecteert,** wordt het formulier uitgeschakeld en als u opnieuw publiceert met de testdrive uitgeschakeld, wordt uw teststation uit de productie verwijderd.

Opmerking: Als er testsstations zijn die actief door gebruikers worden gebruikt, blijven deze teststations worden uitgevoerd totdat hun sessie is verlopen.

### <a name="details"></a>Details

Het volgende gedeelte dat u moet invullen, zijn de details over uw testdrive-aanbieding.

![Gedetailleerde informatie over teststation](./media/azure-resource-manager-test-drive/howtopub2.png)

**Beschrijving -** *Vereist* Dit is waar u de hoofdbeschrijving schrijft over wat er op uw proefrit staat. De klant komt hier om te lezen welke scenario's uw testdrive zal behandelen over uw product. 

**Handleiding -** *Vereist* Dit is de diepgaande doorloop van uw Test Drive-ervaring. De klant opent dit en kan precies doorlopen wat u wilt dat ze doen tijdens hun proefrit. Het is belangrijk dat deze inhoud gemakkelijk te begrijpen en te volgen is! (Moet een .pdf-bestand zijn)

**Demovideo teststation -** *Aanbevolen* Net als in de gebruikershandleiding u het beste een video-zelfstudie van uw Test Drive-ervaring opnemen. De klant zal dit voorafgaand of tijdens zijn proefrit bekijken en kan precies doorlopen wat u wilt dat ze doen tijdens hun proefrit. Het is belangrijk dat deze inhoud gemakkelijk te begrijpen en te volgen is!

- **Naam** - Titel van je video
- **Link** - Moet een ingesloten URL van uw tube of video zijn. Voorbeeld over hoe u de ingesloten url krijgt, vindt u:
- **Miniatuur** - Moet een afbeelding van hoge kwaliteit (533x324) pixels zijn. Het wordt aanbevolen om hier een screenshot te maken van een deel van je Test Drive-ervaring.

Hieronder vindt u hoe deze velden voor uw klant worden weergegeven tijdens hun Test Drive-ervaring.

![Locatie van testdrive-velden in de Marketplace-aanbieding](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Technische configuratie

De volgende sectie die u moet invullen, is waar u de sjabloon Test Drive Resource Manager uploadt en bepaalt hoe specifiek uw Test Drive-exemplaren werken.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**Instanties -** *Vereist* Dit is waar u configureert hoeveel exemplaren u wilt, in welke regio(s) en hoe snel uw klanten de testdrive kunnen krijgen.

- **Instanties** : de regio's selecteren is de plaats waar u kiest waar de sjabloon Test Drive Resource Manager is geïmplementeerd. Het wordt aanbevolen om gewoon één regio te kiezen waar u het meest verwacht dat uw klanten zich bevinden.
- **Hot** - Aantal teststation-exemplaren dat al is geïmplementeerd en in afwachting van toegang per geselecteerde regio. Klanten hebben direct toegang tot deze testritten in plaats van te hoeven wachten op een implementatie. De afweging is dat deze exemplaren altijd worden uitgevoerd op uw Azure-abonnement, zodat ze een grotere uptime kosten. Het wordt ten zeerste aanbevolen om **ten minste één Hot-exemplaar**te hebben, omdat de meeste van uw klanten niet willen wachten tot volledige implementaties zijn voltooid en er dus een drop-off is in het gebruik van de klant.
- **Warm** - Aantal teststation-exemplaren per regio die zijn geïmplementeerd en vervolgens is de VM gestopt en opgeslagen in Azure-opslag. De wachttijd voor Warme exemplaren is langzamer dan Hot-exemplaren, maar de uptime-kosten van opslag zijn ook goedkoper.
- **Koud** - Aantal testdrive-exemplaren per regio die mogelijk kunnen worden geïmplementeerd. Koude instanties vereisen dat de volledige sjabloon Test Drive Resource Manager een implementatie doorloopt op het moment dat een klant de testdrive aanvraagt, dus het is langzamer dan warme of warme exemplaren. De afweging is echter dat u alleen hoeft te betalen voor de duur van de proefrit.

Berekent op dit moment het totale aantal potentiële gelijktijdige teststations dat u beschikbaar gaat maken en controleer of uw quotumlimiet voor uw abonnement dat gelijktijdige bedrag aankan:

**(Aantal geselecteerde regio's x Hotspots) + (Aantal geselecteerde regio's x Warme instanties) + (Aantal geselecteerde regio's x Koude instanties)**

**Testritduur (uren) -** *Vereiste* duur voor hoe lang \# de testrit binnen enkele uren actief blijft. De testdrive eindigt automatisch nadat deze periode is afgelopen.

**Sjabloon Test Drive Resource Manager -** *Vereiste* Upload hier uw resourcemanagersjabloon. Dit is het bestand dat u in de vorige sectie hierboven hebt gebouwd. Geef het hoofdsjabloonbestand een naam: "main-template.json" en zorg ervoor dat uw resourcemanagersjabloon uitvoerparameters bevat voor belangrijke variabelen die nodig zijn. (Moet een .zip-bestand zijn)

**Toegangsgegevens -** *Vereist* Nadat een klant zijn teststation heeft opgevraagd, wordt de toegangsinformatie aan hem of haar gepresenteerd. Deze instructies zijn bedoeld om de nuttige uitvoerparameters te delen van de sjabloon Test Drive Resource Manager. Als u uitvoerparameters wilt opnemen, gebruikt u dubbele krullende haakjes (bijvoorbeeld **{{outputname}}** en worden ze correct ingevoegd op de locatie. (HTML-tekenreeksopmaak wordt hier aanbevolen om aan de voorkant weer te geven).

### <a name="test-drive-deployment-subscription-details"></a>Abonnementsgegevens voor implementatie van teststation

Het laatste gedeelte dat moet worden ingevuld, is om de teststations automatisch te kunnen implementeren door uw Azure Subscription en Azure Active Directory (AD) met elkaar te verbinden.

![Abonnementsgegevens voor implementatie van Test Drive](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure Subscription ID -** *Vereist* Dit verleent toegang tot Azure-services en de Azure-portal. Het abonnement is waar resourcegebruik wordt gerapporteerd en services worden gefactureerd. Als u nog niet alleen een **apart** Azure-abonnement voor teststations hebt, gaat u verder en maakt u er een. U Azure Subscription Id's vinden door u aan te melden bij azure portal en te navigeren naar de abonnementen in het menu aan de linkerkant. (Voorbeeld: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Azure-abonnementen](./media/azure-resource-manager-test-drive/subdetails2.png)

**Azure AD-tenant-id -** *vereist* Als u al een tenant-id\> beschikbaar hebt, u deze hieronder vinden in de eigenschappen - directory-id.

![Azure Active Directory-eigenschappen](./media/azure-resource-manager-test-drive/subdetails3.png)

Maak anders een nieuwe tenant in Azure Active Directory.

![Lijst met Azure Active Directory-tenants](./media/azure-resource-manager-test-drive/subdetails4.png)

![De organisatie, het domein en land/regio definiëren voor de Azure AD-tenant](./media/azure-resource-manager-test-drive/subdetails5.png)

![De selectie bevestigen](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure AD App ID -** *Vereist* Volgende stap is het maken en registreren van een nieuwe toepassing. We gebruiken deze toepassing om bewerkingen uit te voeren op uw Test Drive-exemplaar.

1. Navigeer naar de nieuw gemaakte map of reeds bestaande map en selecteer Azure Active-map in het filtervenster.
2. Zoek op 'App-registraties' en klik op 'Toevoegen'
3. Geef een toepassingsnaam op.
4. Selecteer het type als "Web-app / API"
5. Geef enige waarde op in de\'URL van aanmelding, we gebruiken dat veld niet.
6. Klik op Maken.
7. Nadat de toepassing is gemaakt,\> gaat u naar Eigenschappen - Stel de toepassing in als meervoudige tenant en gaat u op Opslaan.

Klik op Opslaan. De laatste stap is om de applicatie-id voor deze geregistreerde app te pakken en deze hier in het veld Testdrive te plakken.

![Azure AD-toepassingsid-detail](./media/azure-resource-manager-test-drive/subdetails7.png)

Aangezien we de toepassing gebruiken om te implementeren op het abonnement, moeten we de toepassing toevoegen als bijdrager aan het abonnement. De instructies hiervoor zijn hieronder:

1. Navigeer naar het blad Abonnementen en selecteer het juiste abonnement dat u alleen voor het teststation gebruikt.
1. Klik op **Toegangsbeheer (IAM)**.
1. Klik op het tabblad **Roltoewijzingen.**  ![Een nieuwe hoofdvoor toegangsbeheer toevoegen](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Klik **op Roltoewijzing toevoegen**.
1. Stel de rol in als **bijdrager**.
1. Typ de naam van de Azure AD-toepassing en selecteer de toepassing om de rol toe te wijzen.
    ![De machtigingen toevoegen](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Klik op **Opslaan**.

**Azure AD-appsleutel -** *Vereist* Het laatste veld is het genereren van een verificatiesleutel. Voeg onder toetsen een sleutelbeschrijving toe, stel de duur in om nooit te verlopen en selecteer opslaan. Het is **belangrijk** om te voorkomen dat u een verlopen sleutel, die uw proefrit in productie zal breken. Kopieer deze waarde en plak deze in het veld Vereist Testdrive.

![Toont de sleutels voor de Azure AD-toepassing](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>Volgende stappen

Nu u al uw testdrive-velden hebt ingevuld, gaat u door en publiceert u uw aanbieding **opnieuw.** Zodra uw testdrive is geslaagd voor certificering, moet u de klantervaring uitgebreid testen in de **preview** van uw aanbieding. Start een teststation in de gebruikersinterface en open uw Azure-abonnement in de Azure-portal en controleer of uw teststations volledig correct worden geïmplementeerd.

![Azure Portal](./media/azure-resource-manager-test-drive/subdetails9.png)

Het is belangrijk op te merken dat u geen Test Drive-exemplaren verwijdert omdat deze zijn ingericht voor uw klanten, dus de Test Drive-service zal deze resourcegroepen automatisch opschonen nadat een klant ermee is voltooid.

Zodra u zich comfortabel voelt met uw Preview-aanbod, is het nu tijd om live te **gaan!** Er is een laatste beoordelingsproces van Microsoft zodra de aanbieding is gepubliceerd om de volledige ervaring van eind tot eind te controleren. Als om de een of andere reden het aanbod wordt afgewezen, sturen we een melding naar de contactpersoon voor uw aanbieding waarin wordt uitgelegd wat er moet worden opgelost.

Als u meer vragen hebt, op zoek bent naar advies over het oplossen van problemen of uw testrit succesvoller wilt maken, gaat u naar [veelgestelde vragen, probleemoplossing, & Best Practices.](./marketing-and-best-practices.md)
