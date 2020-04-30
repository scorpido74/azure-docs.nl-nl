---
title: Wat is Azure Web Application Firewall op Azure CDN?
description: Meer informatie over hoe Azure Web Application Firewall op Azure CDN-service uw webtoepassingen beschermt tegen kwaad aardige aanvallen.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 03/18/2020
ms.author: victorh
ms.openlocfilehash: 28cf8d9fd60cc6fc158812aa0a1dff3a4b0dced1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80754295"
---
# <a name="azure-web-application-firewall-on-azure-content-delivery-network"></a>Azure Web Application firewall op Azure Content Delivery Network

Azure Web Application firewall (WAF) op Azure Content Delivery Network (CDN) van micro soft biedt gecentraliseerde beveiliging voor uw webinhoud. WAF beschermt uw webservices tegen veelvoorkomende aanvallen en beveiligings problemen. Het houdt uw service Maxi maal beschikbaar voor uw gebruikers en helpt u te voldoen aan de nalevings vereisten.

> [!IMPORTANT]
> WAF op Azure CDN van micro soft is momenteel beschikbaar als open bare preview en wordt meegeleverd met een preview-service level agreement. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben.  Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

WAF op Azure CDN is een wereld wijde en gecentraliseerde oplossing. Het is geïmplementeerd op Azure Network Edge-locaties over de hele wereld. WAF stopt schadelijke aanvallen dicht bij de aanvals bronnen voordat ze uw oorsprong bereiken. U krijgt wereld wijde beveiliging op schaal zonder verlies van prestaties. 

Een WAF-beleid kan eenvoudig worden gekoppeld aan een CDN-eind punt in uw abonnement. Nieuwe regels kunnen binnen enkele minuten worden geïmplementeerd, zodat u snel kunt reageren op het wijzigen van bedreigings patronen.

![Azure Web Application Firewall](../media/cdn-overview/waf-cdn-overview.png)

## <a name="waf-policy-and-rules"></a>WAF-beleid en-regels

U kunt een WAF-beleid configureren en dat beleid koppelen aan een of meer CDN-eind punten voor beveiliging. Een WAF-beleid bestaat uit twee typen beveiligings regels:

- aangepaste regels die u kunt maken.

- beheerde regel sets die een verzameling van door Azure beheerde vooraf geconfigureerde regels zijn.

Wanneer beide aanwezig zijn, worden aangepaste regels verwerkt voordat de regels in een beheerde regelset worden verwerkt. Een regel bestaat uit een match-voor waarde, een prioriteit en een actie. Ondersteunde actie typen zijn: *toestaan*, *blok keren*, *Aanmelden*en *omleiden*. U kunt een volledig aangepast beleid maken dat voldoet aan uw specifieke vereisten voor toepassings beveiliging door beheerde en aangepaste regels te combi neren.

Regels in een beleid worden verwerkt in een prioriteits volgorde. Priority is een uniek nummer waarmee de volg orde van de te verwerken regels wordt gedefinieerd. Kleinere getallen hebben een hogere prioriteit en deze regels worden geëvalueerd vóór regels met een grotere waarde. Zodra een regel is gevonden, wordt de bijbehorende actie toegepast die in de regel is gedefinieerd. Zodra een dergelijke overeenkomst is verwerkt, worden regels met lagere prioriteiten niet verder verwerkt.

Aan een webtoepassing die wordt gehost op Azure CDN, kan slechts één WAF-beleid tegelijk zijn gekoppeld. U kunt echter een CDN-eind punt hebben zonder dat er WAF-beleid aan is gekoppeld. Als er een WAF-beleid aanwezig is, wordt dit gerepliceerd naar al onze Edge-locaties om consistent beveiligings beleid over de hele wereld te garanderen.

## <a name="waf-modes"></a>WAF modi

Het WAF-beleid kan worden geconfigureerd om te worden uitgevoerd in de volgende twee modi:

- *Detectie modus*: wanneer wordt uitgevoerd in de detectie modus, nemen WAF geen andere andere acties dan monitors en registreert de aanvraag en de overeenkomende WAF-regel naar WAF-Logboeken. U kunt Diagnostische gegevens over logboek registratie inschakelen voor de voor deur. Wanneer u de portal gebruikt, gaat u naar de sectie **Diagnostische gegevens** .

- *Preventie modus*: in de modus preventie, WAF neemt de opgegeven actie op als een aanvraag overeenkomt met een regel. Als er een overeenkomst wordt gevonden, worden er geen verdere regels met een lagere prioriteit geëvalueerd. Alle overeenkomende aanvragen worden ook in de WAF-logboeken vastgelegd.

## <a name="waf-actions"></a>WAF-acties

U kunt een van de volgende acties kiezen wanneer een aanvraag overeenkomt met de voor waarden van een regel:

- *Toestaan*: de aanvraag wordt door gegeven via de WAF en wordt doorgestuurd naar een back-end. Voor regels met een lagere prioriteit kan deze aanvraag niet worden geblokkeerd.
- *Blok keren*: de aanvraag is geblokkeerd en WAF stuurt een reactie naar de client zonder de aanvraag door te sturen naar de back-end.
- *Log*: de aanvraag wordt geregistreerd in de WAF-logboeken en WAF gaat verder met het evalueren van regels met een lagere prioriteit.
- *Omleiden*: WAF leidt de aanvraag om naar de opgegeven URI. De opgegeven URI is een instelling voor het beleids niveau. Eenmaal geconfigureerd, worden alle aanvragen die overeenkomen met de *omleidings* actie, naar die URI verzonden.

## <a name="waf-rules"></a>WAF-regels

Een WAF-beleid kan bestaan uit twee typen beveiligings regels:

- *aangepaste regels*: regels die u zelf maakt 
- *beheerde regel sets*: door Azure beheerde vooraf geconfigureerde regels instellen

### <a name="custom-rules"></a>Aangepaste regels

Aangepaste regels kunnen overeenkomen regels en regels voor frequentie beheer hebben.

U kunt de volgende regels voor aangepaste overeenkomsten configureren:

- Lijst met *toegestane IP-adressen en*lijst met geblokkeerde websites: u kunt de toegang tot uw webtoepassingen beheren op basis van een lijst met IP-adres-of IP-adresbereiken van de client. IPv4-en IPv6-adres typen worden ondersteund. Deze lijst kan worden geconfigureerd om de aanvragen te blok keren of toe te staan waarbij de bron-IP overeenkomt met een IP-adres in de lijst.

- *Geografisch gebaseerd toegangs beheer*: u kunt de toegang tot uw webtoepassingen beheren op basis van de land code die is gekoppeld aan het IP-adres van de client.

- *Http-para meters op basis van toegangs beheer*: u kunt regels baseren op teken reeks overeenkomsten in HTTP/HTTPS-aanvraag parameters.  Bijvoorbeeld query reeksen, argumenten POST, aanvraag-URI, aanvraag header en hoofd tekst van aanvraag.

- *Toegangs beheer op basis van aanvraag methode*: u basis regels voor de HTTP-aanvraag methode van de aanvraag. Bijvoorbeeld GET, PUT of HEAD.

- *Grootte beperking*: u kunt regels baseren op de lengte van specifieke delen van een aanvraag, zoals een query reeks, een URI of een aanvraag tekst.

Een regel voor frequentie beheer beperkt een abnormaal hoog verkeer van elk IP-adres van de client.

- *Regels voor frequentie limieten*: u kunt een drempel waarde configureren voor het aantal webaanvragen dat is toegestaan vanaf een client-IP-adres gedurende een duur van één minuut. Deze regel is verschillend van een op een IP-lijst gebaseerde aangepaste regel toestaan/blok keren, waarmee alle aanvragen van een client-IP-adres kunnen worden toegestaan of geblokkeerd. Frequentie limieten kunnen worden gecombineerd met aanvullende match voorwaarden, zoals HTTP (S)-para meters die overeenkomen met het besturings element nauw keurigheid.

### <a name="azure-managed-rule-sets"></a>Door Azure beheerde regel sets

Door Azure beheerde regel sets bieden een eenvoudige manier om beveiliging te implementeren op basis van een gemeen schappelijke reeks beveiligings Risico's. Omdat deze Rules worden beheerd door Azure, worden de regels zo nodig bijgewerkt om te beschermen tegen nieuwe aanvals handtekeningen. De set met Azure Managed default Rules bevat regels voor de volgende bedreigings Categorieën:

- Scripting op meerdere sites
- Java-aanvallen
- Lokaal bestand opnemen
- Inspuit-aanvallen voor PHP
- Externe opdrachten uitvoeren
- Externe bestandsopname
- Sessie-vastleg ging
- Beveiliging tegen SQL-injecties
- Protocol aanvallen

Het versie nummer van de standaardregelset wordt verhoogd wanneer nieuwe aanvals handtekeningen worden toegevoegd aan de regelset.
De standaardregelset is standaard ingeschakeld in de *detectie* modus in uw WAF-beleid. U kunt afzonderlijke regels in de standaard regel instellen om te voldoen aan de vereisten van uw toepassing. U kunt ook specifieke acties (toestaan/blok keren/omleiden/logboek registratie) per regel instellen. De standaard actie voor de beheerde set met standaard regels is *blok keren*.

Aangepaste regels worden altijd toegepast voordat regels in de standaard regelset worden geëvalueerd. Als een aanvraag overeenkomt met een aangepaste regel, wordt de bijbehorende regel actie toegepast. De aanvraag wordt geblokkeerd of door gegeven aan de back-end. Er worden geen andere aangepaste regels of regels in de standaardregelset verwerkt. U kunt ook de standaardregelset uit uw WAF-beleid verwijderen.

## <a name="configuration"></a>Configuratie

U kunt alle WAF-regel typen configureren en implementeren met behulp van de Azure Portal, REST-Api's, Azure Resource Manager sjablonen en Azure PowerShell.

## <a name="monitoring"></a>Bewaking

Bewaking voor WAF met CDN is geïntegreerd met Azure Monitor om waarschuwingen bij te houden en trends in het verkeer eenvoudig te bewaken.

## <a name="next-steps"></a>Volgende stappen

- [Zelf studie: een WAF-beleid maken met Azure CDN met behulp van de Azure Portal](waf-cdn-create-portal.md)
