---
title: Wat is Azure webapplication firewall op Azure CDN?
description: Lees hoe Azure webtoepassingsfirewall op Azure CDN-service uw webtoepassingen beschermt tegen schadelijke aanvallen.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 03/18/2020
ms.author: victorh
ms.openlocfilehash: 28cf8d9fd60cc6fc158812aa0a1dff3a4b0dced1
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754295"
---
# <a name="azure-web-application-firewall-on-azure-content-delivery-network"></a>Azure Web Application Firewall op Azure Content Delivery Network

Azure Web Application Firewall (WAF) op Azure Content Delivery Network (CDN) van Microsoft biedt gecentraliseerde bescherming voor uw webinhoud. WAF verdedigt uw webservices tegen veelvoorkomende exploits en kwetsbaarheden. Het houdt uw service zeer beschikbaar voor uw gebruikers en helpt u te voldoen aan de nalevingsvereisten.

> [!IMPORTANT]
> WAF op Azure CDN van Microsoft is momenteel in openbare preview en wordt voorzien van een preview service level overeenkomst. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben.  Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

WAF op Azure CDN is een wereldwijde en gecentraliseerde oplossing. Het wordt geïmplementeerd op Azure-netwerkrandlocaties over de hele wereld. WAF stopt kwaadaardige aanvallen in de buurt van de aanvalbronnen, voordat ze uw oorsprong bereiken. U krijgt wereldwijde bescherming op schaal zonder in te leveren op prestaties. 

Een WAF-beleid wordt eenvoudig gekoppeld aan een CDN-eindpunt in uw abonnement. Nieuwe regels kunnen binnen enkele minuten worden geïmplementeerd, zodat u snel reageren op veranderende bedreigingspatronen.

![Azure-firewall voor webtoepassingen](../media/cdn-overview/waf-cdn-overview.png)

## <a name="waf-policy-and-rules"></a>WAF-beleid en -regels

U een WAF-beleid configureren en dat beleid koppelen aan een of meer CDN-eindpunten voor bescherming. Een WAF-beleid bestaat uit twee soorten beveiligingsregels:

- aangepaste regels die u maken.

- beheerde regelsets die een verzameling van vooraf geconfigureerde regels met Azure zijn beheerd.

Wanneer beide aanwezig zijn, worden aangepaste regels verwerkt voordat de regels worden verwerkt in een beheerde regelset. Er wordt een regel gemaakt van een wedstrijdvoorwaarde, een prioriteit en een actie. Ondersteunde actietypen zijn: *TOESTAAN,* *BLOKKEREN,* *LOGBOEK*en *OMLEIDEN*. U een volledig aangepast beleid maken dat voldoet aan uw specifieke vereisten voor toepassingsbescherming door beheerde en aangepaste regels te combineren.

Regels binnen een beleid worden verwerkt in een prioriteitsvolgorde. Prioriteit is een uniek getal dat de volgorde van de regels definieert om te verwerken. Kleinere getallen hebben een hogere prioriteit en deze regels worden geëvalueerd vóór regels met een grotere waarde. Zodra een regel is gekoppeld, wordt de bijbehorende actie die in de regel is gedefinieerd, toegepast op de aanvraag. Zodra een dergelijke overeenkomst is verwerkt, worden regels met lagere prioriteiten niet verder verwerkt.

Aan een webtoepassing die wordt gehost op Azure CDN, is slechts één WAF-beleid tegelijk gekoppeld. U echter een CDN-eindpunt hebben zonder dat er WAF-beleid aan is gekoppeld. Als er een WAF-beleid aanwezig is, wordt het gerepliceerd naar al onze randlocaties om consistent beveiligingsbeleid over de hele wereld te garanderen.

## <a name="waf-modes"></a>WAF-modi

WAF-beleid kan worden geconfigureerd om uit te voeren in de volgende twee modi:

- *Detectiemodus*: Wanneer waf wordt uitgevoerd in de detectiemodus, neemt het geen andere acties dan monitoren en registreert het verzoek en de overeenkomende WAF-regel aan WAF-logboeken. U logboekregistratievoor voordeur inschakelen. Wanneer u de portal gebruikt, gaat u naar de sectie **Diagnostische gegevens.**

- *Preventiemodus*: In de preventiemodus neemt WAF de opgegeven actie als een aanvraag overeenkomt met een regel. Als er een overeenkomst wordt gevonden, worden geen verdere regels met een lagere prioriteit geëvalueerd. Alle overeenkomende aanvragen worden ook geregistreerd in de WAF-logboeken.

## <a name="waf-actions"></a>WAF-acties

U een van de volgende acties kiezen wanneer een aanvraag overeenkomt met de voorwaarden van een regel:

- *Toestaan*: De aanvraag gaat door de WAF en wordt doorgestuurd naar back-end. Geen verdere regels met een lagere prioriteit kunnen dit verzoek blokkeren.
- *Blok*: De aanvraag wordt geblokkeerd en WAF stuurt een antwoord naar de client zonder het verzoek door te sturen naar de back-end.
- *Logboek*: De aanvraag wordt geregistreerd in de WAF-logboeken en WAF blijft regels met een lagere prioriteit evalueren.
- *Omleiding*: WAF leidt de aanvraag om naar de opgegeven URI. De opgegeven URI is een instelling voor beleidsniveau. Eenmaal geconfigureerd, worden alle aanvragen die overeenkomen met de *actie Redirect* naar die URI verzonden.

## <a name="waf-rules"></a>WAF-regels

Een WAF-beleid kan bestaan uit twee soorten beveiligingsregels:

- *aangepaste regels*: regels die u zelf maakt 
- *beheerde regelsets:* door Azure beheerde vooraf geconfigureerde set regels

### <a name="custom-rules"></a>Aangepaste regels

Aangepaste regels kunnen overeenkomen met regels en regels voor tariefcontrole.

U de volgende aangepaste overeenkomstregels configureren:

- *LIJST- en bloklijst voor IP-toepassingen:* u de toegang tot uw webtoepassingen beheren op basis van een lijst met IP-adressen of IP-adresbereiken van klanten. Zowel IPv4- als IPv6-adrestypen worden ondersteund. Deze lijst kan worden geconfigureerd om aanvragen te blokkeren of toe te staan wanneer het bron-IP overeenkomt met een IP in de lijst.

- *Geografisch gebaseerd toegangsbeheer:* u de toegang tot uw webtoepassingen beheren op basis van de landcode die is gekoppeld aan het IP-adres van een client.

- *HTTP-parameters gebaseerd toegangsbeheer*: U regels baseren op tekenreeksovereenkomsten in HTTP/HTTPS-aanvraagparameters.  Querytekenreeksen, POST-args, Request URI, Request Header en Request Body.

- *Op methode gebaseerd toegangsbeheer aanvragen:* u baseert regels op de HTTP-aanvraagmethode van de aanvraag. Bijvoorbeeld, GET, PUT, of HEAD.

- *Beperking van de grootte*: u regels baseren op de lengtes van specifieke delen van een aanvraag, zoals querytekenreeks, Uri of aanvraaginstantie.

Een regel voor tariefcontrole beperkt abnormaal veel verkeer vanaf een IP-adres van de client.

- *Regels voor beperking van de snelheid*: U een drempelwaarde configureren op het aantal webaanvragen dat is toegestaan vanaf een IP-adres van een client gedurende een duur van één minuut. Deze regel onderscheidt zich van een aangepaste aangepaste regel voor ip-lijsten toestaan/blokkeren waarmee alle aanvragen van een IP-adres van een client worden toegestaan of geblokkeerd. Tarieflimieten kunnen worden gecombineerd met aanvullende wedstrijdvoorwaarden, zoals http(S)-parameterovereenkomsten voor gedetailleerd tariefbeheer.

### <a name="azure-managed-rule-sets"></a>Azure-beheerde regelsets

Azure-beheerde regelsets bieden een eenvoudige manier om bescherming te implementeren tegen een veelvoorkomende set beveiligingsbedreigingen. Aangezien deze regelsets worden beheerd door Azure, worden de regels zo nodig bijgewerkt om te beschermen tegen nieuwe aanvalshandtekeningen. De azure managed Default Rule Set bevat regels voor de volgende bedreigingscategorieën:

- Scripting op meerdere sites
- Java-aanvallen
- Lokale bestandsopname
- PHP-injectieaanvallen
- Uitvoering van opdrachten op afstand
- Externe bestandsopname
- Sessiefixatie
- Beveiliging tegen SQL-injecties
- Protocolaanvallers

Het versienummer van de stappen van de standaardregelset wanneer nieuwe aanvalshandtekeningen worden toegevoegd aan de regelset.
De standaardregelset is standaard ingeschakeld in *de detectiemodus* in uw WAF-beleid. U afzonderlijke regels in de standaardregelset uitschakelen of inschakelen om aan uw toepassingsvereisten te voldoen. U ook specifieke acties (ALLOW/BLOCK/REDIRECT/LOG) per regel instellen. De standaardactie voor de beheerde standaardregelset is *Blokkeren*.

Aangepaste regels worden altijd toegepast voordat regels in de standaardregelset worden geëvalueerd. Als een aanvraag overeenkomt met een aangepaste regel, wordt de bijbehorende regelactie toegepast. Het verzoek wordt geblokkeerd of doorgegeven aan de back-end. Er worden geen andere aangepaste regels of regels in de standaardregelset verwerkt. U de standaardregelset ook verwijderen uit uw WAF-beleid.

## <a name="configuration"></a>Configuratie

U alle WAF-regeltypen configureren en implementeren met behulp van de Azure-portal, REST API's, Azure Resource Manager-sjablonen en Azure PowerShell.

## <a name="monitoring"></a>Bewaking

Monitoring voor WAF met CDN is geïntegreerd met Azure Monitor om waarschuwingen bij te houden en verkeerstrends eenvoudig te volgen.

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Een WAF-beleid maken met Azure CDN met de Azure-portal](waf-cdn-create-portal.md)
