---
title: Wat is Azure webapplication firewall op Azure Front Door?
description: Ontdek hoe Azure webtoepassingsfirewall op Azure Front Door-service uw webtoepassingen beschermt tegen schadelijke aanvallen.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: c8ff1849668d5effe15b6c25d00f3965a17b8e3e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77915636"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Azure Web Application Firewall op Azure Front Door

Azure Web Application Firewall (WAF) op Azure Front Door biedt gecentraliseerde bescherming voor uw webtoepassingen. WAF verdedigt uw webservices tegen veelvoorkomende exploits en kwetsbaarheden. Het houdt uw service zeer beschikbaar voor uw gebruikers en helpt u te voldoen aan de nalevingsvereisten.

WAF on Front Door is een wereldwijde en gecentraliseerde oplossing. Het wordt geïmplementeerd op Azure-netwerkrandlocaties over de hele wereld. WAF-toepassingen inspecteren elke binnenkomende aanvraag die door Front Door aan de netwerkrand wordt geleverd. 

WAF voorkomt kwaadaardige aanvallen in de buurt van de aanvalbronnen, voordat ze uw virtuele netwerk binnenkomen. U krijgt wereldwijde bescherming op schaal zonder in te leveren op prestaties. Een WAF-beleid linkt eenvoudig naar elk voordeurprofiel in uw abonnement. Nieuwe regels kunnen binnen enkele minuten worden geïmplementeerd, zodat u snel reageren op veranderende bedreigingspatronen.

![Azure-firewall voor webtoepassingen](../media/overview/wafoverview.png)

## <a name="waf-policy-and-rules"></a>WAF-beleid en -regels

U een WAF-beleid configureren en dat beleid koppelen aan een of meer front-door front-ends voor bescherming. Een WAF-beleid bestaat uit twee soorten beveiligingsregels:

- aangepaste regels die door de klant zijn geschreven.

- beheerde regelsets die een verzameling vooraf beheerde vooraf geconfigureerde regels zijn.

Wanneer beide aanwezig zijn, worden aangepaste regels verwerkt voordat de regels worden verwerkt in een beheerde regelset. Er wordt een regel gemaakt van een wedstrijdvoorwaarde, een prioriteit en een actie. Actietypen worden ondersteund: TOESTAAN, BLOKKEREN, LOGBOEKen en OMLEIDING. U een volledig aangepast beleid maken dat voldoet aan uw specifieke vereisten voor toepassingsbescherming door beheerde en aangepaste regels te combineren.

Regels binnen een beleid worden verwerkt in een prioriteitsvolgorde. Prioriteit is een uniek geheel getal dat de volgorde van de regels definieert om te verwerken. Kleinere gehele waarde duidt op een hogere prioriteit en deze regels worden geëvalueerd vóór regels met een hogere gehele waarde. Zodra een regel is gekoppeld, wordt de bijbehorende actie die in de regel is gedefinieerd, toegepast op de aanvraag. Zodra een dergelijke overeenkomst is verwerkt, worden regels met lagere prioriteiten niet verder verwerkt.

Een webapplicatie die door Front Door wordt geleverd, kan slechts één WAF-beleid tegelijk hebben. U echter een frontdeurconfiguratie hebben zonder dat er WAF-beleid aan is gekoppeld. Als er een WAF-beleid aanwezig is, wordt het gerepliceerd naar al onze randlocaties om consistent beveiligingsbeleid over de hele wereld te garanderen.

## <a name="waf-modes"></a>WAF-modi

WAF-beleid kan worden geconfigureerd om uit te voeren in de volgende twee modi:

- **Detectiemodus:** Wanneer WAF wordt uitgevoerd in de detectiemodus, voert het geen andere acties uit dan monitoren en registreert het verzoek en de overeenkomende WAF-regel aan WAF-logboeken. U logboekregistratievoor voordeur inschakelen. Wanneer u de portal gebruikt, gaat u naar de sectie **Diagnostische gegevens.**

- **Preventiemodus:** In de preventiemodus neemt WAF de opgegeven actie als een aanvraag overeenkomt met een regel. Als er een overeenkomst wordt gevonden, worden geen verdere regels met een lagere prioriteit geëvalueerd. Alle overeenkomende aanvragen worden ook geregistreerd in de WAF-logboeken.

## <a name="waf-actions"></a>WAF-acties

WAF-klanten kunnen ervoor kiezen om uit een van de acties te lopen wanneer een aanvraag overeenkomt met de voorwaarden van een regel:

- **Toestaan:**  Aanvraag gaat door de WAF en wordt doorgestuurd naar back-end. Geen verdere regels met een lagere prioriteit kunnen dit verzoek blokkeren.
- **Blok:** De aanvraag wordt geblokkeerd en WAF stuurt een antwoord naar de client zonder het verzoek door te sturen naar de back-end.
- **Log:**  Aanvraag wordt geregistreerd in de WAF-logboeken en WAF blijft regels met een lagere prioriteit evalueren.
- **Omleiding:** WAF verwijst de aanvraag door naar de opgegeven URI. De opgegeven URI is een instelling voor beleidsniveau. Zodra alle aanvragen die overeenkomen met de actie Redirect, worden alle aanvragen die overeenkomen met de actie **Redirect,** naar die URI verzonden.

## <a name="waf-rules"></a>WAF-regels

Een WAF-beleid kan bestaan uit twee soorten beveiligingsregels: aangepaste regels, geschreven door de klant en beheerde regelsets, vooraf beheerde vooraf geconfigureerde set regels met Azure.

### <a name="custom-authored-rules"></a>Aangepaste geschreven regels

U aangepaste regels WAF als volgt configureren:

- **LIJST- en bloklijst voor IP-toestaan:** U de toegang tot uw webtoepassingen beheren op basis van een lijst met IP-adressen van klanten of IP-adresbereiken. Zowel IPv4- als IPv6-adrestypen worden ondersteund. Deze lijst kan worden geconfigureerd om aanvragen te blokkeren of toe te staan wanneer het bron-IP overeenkomt met een IP in de lijst.

- **Geografisch gebaseerd toegangsbeheer:** U de toegang tot uw webtoepassingen beheren op basis van de landcode die is gekoppeld aan het IP-adres van een client.

- **Op HTTP-parameters gebaseerd toegangsbeheer:** U regels baseren op tekenreeksovereenkomsten in HTTP/HTTPS-aanvraagparameters.  Querytekenreeksen, POST-args, Request URI, Request Header en Request Body.

- **Toegangsbeheer op basis van methoden aanvragen:** U baseert regels op de HTTP-aanvraagmethode van de aanvraag. Bijvoorbeeld, GET, PUT, of HEAD.

- **Beperking van de grootte:** U regels baseren op de lengtes van specifieke delen van een aanvraag, zoals querytekenreeks, Uri of aanvraaginstantie.

- **Regels voor tariefbeperking:** Een regel voor tariefcontrole is om abnormaal hoog verkeer van een client-IP te beperken. U een drempelwaarde configureren voor het aantal webaanvragen dat is toegestaan vanaf een client-IP gedurende een duur van één minuut. Deze regel onderscheidt zich van een aangepaste aangepaste regel voor ip-lijsten toestaan/blokkeren waarmee alle aanvragen van een client-IP worden toegestaan of geblokkeerd. Tarieflimieten kunnen worden gecombineerd met aanvullende wedstrijdvoorwaarden, zoals http(S)-parameterovereenkomsten voor gedetailleerd tariefbeheer.

### <a name="azure-managed-rule-sets"></a>Azure-beheerde regelsets

Azure-beheerde regelsets bieden een eenvoudige manier om bescherming te implementeren tegen een veelvoorkomende set beveiligingsbedreigingen. Aangezien dergelijke regelsets worden beheerd door Azure, worden de regels zo nodig bijgewerkt om te beschermen tegen nieuwe aanvalshandtekeningen. De standaardregelset voor Azure bevat regels voor de volgende bedreigingscategorieën:

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
Standaardregelset is standaard ingeschakeld in de detectiemodus in uw WAF-beleid. U afzonderlijke regels in de standaardregelset uitschakelen of inschakelen om aan uw toepassingsvereisten te voldoen. U ook specifieke acties (ALLOW/BLOCK/REDIRECT/LOG) per regel instellen.

Soms moet u bepaalde aanvraagkenmerken weglaten uit een WAF-evaluatie. Een veelvoorkomend voorbeeld is Active Directory-ingevoegde tokens die worden gebruikt voor verificatie. U een uitsluitingslijst configureren voor een beheerde regel, regelgroep of voor de hele regelset.  

De actie Standaard is blokkeren. Bovendien kunnen aangepaste regels worden geconfigureerd in hetzelfde WAF-beleid als u een van de vooraf geconfigureerde regels in de standaardregelset wilt omzeilen.

Aangepaste regels worden altijd toegepast voordat regels in de standaardregelset worden geëvalueerd. Als een aanvraag overeenkomt met een aangepaste regel, wordt de bijbehorende regelactie toegepast. Het verzoek wordt geblokkeerd of doorgegeven aan de back-end. Er worden geen andere aangepaste regels of regels in de standaardregelset verwerkt. U de standaardregelset ook verwijderen uit uw WAF-beleid.

### <a name="bot-protection-rule-set-preview"></a>Beveiligingsregelset bot (voorbeeld)

U een beheerde botbeveiligingsregel instellen om aangepaste acties uit te voeren op aanvragen van bekende botcategorieën. 

Er zijn drie bot categorieën ondersteund: Slecht, Goed en Onbekend. Bot-handtekeningen worden beheerd en dynamisch bijgewerkt door het WAF-platform.

Slechte bots bevatten bots van kwaadaardige IP-adressen en bots die hun identiteit hebben vervalst. Kwaadaardige IP-adressen zijn afkomstig uit de Microsoft Threat Intelligence-feed en worden elk uur bijgewerkt. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) is de drijvende kracht achter Microsoft Threat Intelligence en wordt gebruikt door meerdere services, waaronder Azure Security Center.

Goede Bots omvatten gevalideerde zoekmachines. Onbekende categorieën omvatten extra botgroepen die zichzelf hebben geïdentificeerd als bots. Bijvoorbeeld market analyzer, feed fetchers en data collection agents. 

Onbekende bots worden geclassificeerd via gepubliceerde gebruikersagents zonder extra validatie. U aangepaste acties instellen om verschillende typen bots te blokkeren, toestaan, aanmelden of omte leiden.

![Beveiligingsregelset bot](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> De beveiligingsregelset bot bevindt zich momenteel in een openbare preview en wordt voorzien van een preview-serviceniveauovereenkomst. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben.  Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Als botbeveiliging is ingeschakeld, worden binnenkomende aanvragen die overeenkomen met botregels geregistreerd in het frontdoorWebApplicationFirewallLog-logboek. U WAF-logboeken openen via een opslagaccount, gebeurtenishub of logboekanalyse.

## <a name="configuration"></a>Configuratie

U alle WAF-regeltypen configureren en implementeren met behulp van de Azure-portal, REST API's, Azure Resource Manager-sjablonen en Azure PowerShell.

## <a name="monitoring"></a>Bewaking

Monitoring voor WAF bij Front Door is geïntegreerd met Azure Monitor om waarschuwingen te volgen en verkeerstrends eenvoudig te volgen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Web Application Firewall op Azure Application Gateway](../ag/ag-overview.md)