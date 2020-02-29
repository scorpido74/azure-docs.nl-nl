---
title: Wat is Azure Web Application Firewall op de front-deur van Azure?
description: Meer informatie over hoe Azure Web Application Firewall op de Azure front-deur-service uw webtoepassingen beschermt tegen kwaad aardige aanvallen.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: c8ff1849668d5effe15b6c25d00f3965a17b8e3e
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77915636"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Azure Web Application firewall op de front-deur van Azure

Azure Web Application firewall (WAF) in azure front deur biedt gecentraliseerde beveiliging voor uw webtoepassingen. WAF beschermt uw webservices tegen veelvoorkomende aanvallen en beveiligings problemen. Het houdt uw service Maxi maal beschikbaar voor uw gebruikers en helpt u te voldoen aan de nalevings vereisten.

WAF aan de voor deur is een wereld wijde en gecentraliseerde oplossing. Het is geïmplementeerd op Azure Network Edge-locaties over de hele wereld. WAF ingeschakelde webtoepassingen controleren elke inkomende aanvraag die wordt afgeleverd door de voor deur aan de rand van het netwerk. 

WAF voor komt dat kwaad aardige aanvallen zich dicht bij de aanvals bronnen voordoen voordat ze uw virtuele netwerk binnenkomen. U krijgt wereld wijde beveiliging op schaal zonder verlies van prestaties. Een WAF-beleid kan eenvoudig worden gekoppeld aan een front deur profiel in uw abonnement. Nieuwe regels kunnen binnen enkele minuten worden geïmplementeerd, zodat u snel kunt reageren op het wijzigen van bedreigings patronen.

![Azure Web Application Firewall](../media/overview/wafoverview.png)

## <a name="waf-policy-and-rules"></a>WAF-beleid en-regels

U kunt een WAF-beleid configureren en dat beleid koppelen aan een of meer front-ends vooraan voor beveiliging. Een WAF-beleid bestaat uit twee typen beveiligings regels:

- aangepaste regels die door de klant zijn gemaakt.

- beheerde regel sets die een verzameling van door Azure beheerde vooraf geconfigureerde regels zijn.

Wanneer beide aanwezig zijn, worden aangepaste regels verwerkt voordat de regels in een beheerde regelset worden verwerkt. Een regel bestaat uit een match-voor waarde, een prioriteit en een actie. Ondersteunde actie typen zijn: toestaan, blok keren, aanmelden en omleiden. U kunt een volledig aangepast beleid maken dat voldoet aan uw specifieke vereisten voor toepassings beveiliging door beheerde en aangepaste regels te combi neren.

Regels in een beleid worden verwerkt in een prioriteits volgorde. Priority is een uniek geheel getal dat de volg orde van de te verwerken regels definieert. Een kleinere integer-waarde geeft een hogere prioriteit aan en deze regels worden geëvalueerd vóór regels met een hoger geheel getal. Zodra een regel is gevonden, wordt de bijbehorende actie toegepast die in de regel is gedefinieerd. Zodra een dergelijke overeenkomst is verwerkt, worden regels met lagere prioriteiten niet verder verwerkt.

Aan een webtoepassing die wordt geleverd via de voor deur kan slechts één WAF-beleid tegelijk zijn gekoppeld. U kunt echter wel een configuratie voor de voor deur hebben zonder dat er WAF-beleid aan is gekoppeld. Als er een WAF-beleid aanwezig is, wordt dit gerepliceerd naar al onze Edge-locaties om consistent beveiligings beleid over de hele wereld te garanderen.

## <a name="waf-modes"></a>WAF modi

Het WAF-beleid kan worden geconfigureerd om te worden uitgevoerd in de volgende twee modi:

- **Detectie modus:** Wanneer u de detectie modus uitvoert, nemen WAF geen andere andere acties dan monitors en registreert de aanvraag en de overeenkomende WAF-regel naar WAF-Logboeken. U kunt Diagnostische gegevens over logboek registratie inschakelen voor de voor deur. Wanneer u de portal gebruikt, gaat u naar de sectie **Diagnostische gegevens** .

- **Preventie modus:** In de preventie modus neemt WAF de opgegeven actie op als een aanvraag overeenkomt met een regel. Als er een overeenkomst wordt gevonden, worden er geen verdere regels met een lagere prioriteit geëvalueerd. Alle overeenkomende aanvragen worden ook in de WAF-logboeken vastgelegd.

## <a name="waf-actions"></a>WAF-acties

WAF-klanten kunnen ervoor kiezen om uit te voeren vanaf een van de acties wanneer een aanvraag overeenkomt met de voor waarden van een regel:

- **Toestaan:**  De aanvraag wordt door gegeven via de WAF en wordt doorgestuurd naar de back-end. Voor regels met een lagere prioriteit kan deze aanvraag niet worden geblokkeerd.
- **Blok keren:** De aanvraag is geblokkeerd en WAF stuurt een reactie naar de client zonder de aanvraag door te sturen naar de back-end.
- **Logboek:**  De aanvraag wordt geregistreerd in de WAF-logboeken en WAF gaat door met het evalueren van regels met een lagere prioriteit.
- **Omleiden:** WAF leidt de aanvraag om naar de opgegeven URI. De opgegeven URI is een instelling voor het beleids niveau. Zodra de configuratie is geconfigureerd, worden alle aanvragen die overeenkomen met de **omleidings** actie naar die URI verzonden.

## <a name="waf-rules"></a>WAF-regels

Een WAF-beleid kan bestaan uit twee typen beveiligings regels: aangepaste regels, gemaakt door de klant en beheerde regel sets, door Azure beheerde vooraf geconfigureerde set met regels.

### <a name="custom-authored-rules"></a>Aangepaste regels voor ontwerpen

U kunt aangepaste regels WAF als volgt configureren:

- Lijst met **toegestane IP-adressen en blok keren:** U kunt de toegang tot uw webtoepassingen beheren op basis van een lijst met IP-adressen van clients of IP-adresbereiken. IPv4-en IPv6-adres typen worden ondersteund. Deze lijst kan worden geconfigureerd om de aanvragen te blok keren of toe te staan waarbij de bron-IP overeenkomt met een IP-adres in de lijst.

- **Geografisch gebaseerd toegangs beheer:** U kunt de toegang tot uw webtoepassingen beheren op basis van de land code die is gekoppeld aan het IP-adres van de client.

- **Http-para meters op basis van toegangs beheer:** U kunt regels baseren op teken reeks overeenkomsten in para meters voor HTTP/HTTPS-aanvragen.  Bijvoorbeeld query reeksen, argumenten POST, aanvraag-URI, aanvraag header en hoofd tekst van aanvraag.

- **Toegangs beheer op basis van aanvraag methode:** U hebt regels op basis van de HTTP-aanvraag methode van de aanvraag. Bijvoorbeeld GET, PUT of HEAD.

- **Grootte beperking:** U kunt regels baseren op de lengte van specifieke delen van een aanvraag, zoals een query reeks, URI of aanvraag tekst.

- **Frequentie beperkende regels:** Een regel voor frequentie beheer is het beperken van abnormaal hoog verkeer van elk client-IP-adres. U kunt een drempel waarde configureren voor het aantal webaanvragen dat is toegestaan vanaf een client-IP gedurende een duur van één minuut. Deze regel is verschillend van een op een IP-lijst gebaseerde aangepaste regel toestaan/blok keren, waarmee alle aanvragen van een client-IP worden toegestaan of geblokkeerd. Frequentie limieten kunnen worden gecombineerd met aanvullende match voorwaarden, zoals HTTP (S)-para meters die overeenkomen met het besturings element nauw keurigheid.

### <a name="azure-managed-rule-sets"></a>Door Azure beheerde regel sets

Door Azure beheerde regel sets bieden een eenvoudige manier om beveiliging te implementeren op basis van een gemeen schappelijke reeks beveiligings Risico's. Omdat dergelijke regel definities worden beheerd door Azure, worden de regels zo nodig bijgewerkt om te beschermen tegen nieuwe aanvals handtekeningen. Door Azure beheerde standaard regelset bevat regels voor de volgende bedreigings Categorieën:

- Cross-site scripting
- Java-aanvallen
- Lokaal bestand opnemen
- Inspuit-aanvallen voor PHP
- Externe opdrachten uitvoeren
- Externe bestanden opnemen
- Sessie-vastleg ging
- Beveiliging tegen SQL-injecties
- Protocol aanvallen

Het versie nummer van de standaardregelset wordt verhoogd wanneer nieuwe aanvals handtekeningen worden toegevoegd aan de regelset.
Standaard regelset is standaard ingeschakeld in de detectie modus in uw WAF-beleid. U kunt afzonderlijke regels in de standaard regel instellen om te voldoen aan de vereisten van uw toepassing. U kunt ook specifieke acties (toestaan/blok keren/omleiden/logboek registratie) per regel instellen.

Soms moet u bepaalde aanvraag kenmerken uit een WAF-evaluatie weglaten. Een gemeen schappelijk voor beeld is Active Directory-invoeg tokens die worden gebruikt voor verificatie. U kunt een uitsluitings lijst voor een beheerde regel, regel groep of voor de volledige regelset configureren.  

De standaard actie moet worden geblokkeerd. Daarnaast kunnen aangepaste regels worden geconfigureerd in hetzelfde WAF-beleid als u een van de vooraf geconfigureerde regels in de standaardregelset wilt omzeilen.

Aangepaste regels worden altijd toegepast voordat regels in de standaard regelset worden geëvalueerd. Als een aanvraag overeenkomt met een aangepaste regel, wordt de bijbehorende regel actie toegepast. De aanvraag wordt geblokkeerd of door gegeven aan de back-end. Er worden geen andere aangepaste regels of regels in de standaardregelset verwerkt. U kunt ook de standaardregelset uit uw WAF-beleid verwijderen.

### <a name="bot-protection-rule-set-preview"></a>Set bot Protection-regel (preview-versie)

U kunt een set beheerde bot-beveiliging inschakelen om aangepaste acties uit te voeren op aanvragen van bekende bot-categorieën. 

Er worden drie bot-categorieën ondersteund: slecht, goed en onbekend. Bot-hand tekeningen worden beheerd en worden dynamisch bijgewerkt door het WAF-platform.

Ongeldige bots bevatten botsing van schadelijke IP-adressen en bots die hun identiteit hebben vervalst. Schadelijke IP-adressen worden vanaf de micro soft Threat Intelligence-feed gebrond en elk uur bijgewerkt. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) voorziet in micro soft Threat Intelligence en wordt gebruikt door meerdere services, waaronder Azure Security Center.

Goede bots bevatten gevalideerde zoek machines. Onbekende categorieën bevatten extra bot-groepen die zichzelf als bots hebben geïdentificeerd. Bijvoorbeeld: Market Analyzer, feed ophaleners en agents voor het verzamelen van gegevens. 

Onbekende bots worden geclassificeerd via gepubliceerde gebruikers agenten zonder verdere validatie. U kunt aangepaste acties instellen voor blok keren, toestaan, Logboeken of omleiden voor verschillende typen botsingen.

![Set bot Protection-regel](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> De set bot Protection-regelset bevindt zich momenteel in een open bare preview en wordt weer gegeven met een preview-service level agreement. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben.  Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Als bot-beveiliging is ingeschakeld, worden inkomende aanvragen die overeenkomen met bot-regels vastgelegd in het FrontdoorWebApplicationFirewallLog-logboek. U kunt WAF-Logboeken openen vanuit een opslag account, Event Hub of log Analytics.

## <a name="configuration"></a>Configuratie

U kunt alle WAF-regel typen configureren en implementeren met behulp van de Azure Portal, REST-Api's, Azure Resource Manager sjablonen en Azure PowerShell.

## <a name="monitoring"></a>Bewaking

Bewaking voor WAF aan de voor deur is geïntegreerd met Azure Monitor om waarschuwingen bij te houden en trends in het verkeer eenvoudig te bewaken.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Web Application firewall op Azure-toepassing gateway](../ag/ag-overview.md)