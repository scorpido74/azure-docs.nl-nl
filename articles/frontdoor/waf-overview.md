---
title: Wat is Azure Web Application Firewall voor de voor deur van Azure? (Preview)
description: Meer informatie over hoe Azure Web Application Firewall voor de Azure front-deur service uw webtoepassingen beschermt tegen kwaad aardige aanvallen.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 624a5af59a39cb0ebf647f549f3a40ed56f78b9e
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73549237"
---
# <a name="what-is-azure-web-application-firewall-for-azure-front-door"></a>Wat is Azure Web Application Firewall voor de voor deur van Azure?

Azure Web Application Firewall (WAF) biedt gecentraliseerde beveiliging voor uw web-apps die wereldwijd worden aangeboden met behulp van Azure Front Door. De firewall is ontworpen om uw webservices te beschermen tegen bekende zwakke plekken en beveiligingsproblemen. Daarnaast zorgt de firewall ervoor dat uw service maximaal beschikbaar is voor uw gebruikers en dat er aan de nalevingsvereisten wordt voldaan.


Webtoepassingen zijn in toenemende mate de doel stellingen van kwaad aardige aanvallen, zoals denial of service-flooden, SQL-injectie aanvallen en scripts voor cross-site scripting. Deze kwaad aardige aanvallen kunnen leiden tot onderbrekingen in de service en gegevens verlies. Dit vormt een aanzienlijke bedreiging voor eigen aren van webtoepassingen.

Het kan een hele uitdaging zijn om dergelijke aanvallen in toepassingscode te voorkomen en dit kan tevens veel onderhoud, patching en controle vereisen op meerdere lagen van de toepassingstopologie. Een gecentraliseerde firewall voor webtoepassingen maakt het beveiligingsbeheer veel eenvoudiger en biedt toepassingsbeheerders meer veiligheid tegen bedreigingen of aanvallen. Daarnaast kan een WAF-oplossing sneller reageren op een beveiligings risico door een bekend beveiligings probleem op een centrale locatie op te lossen in plaats van elk van de afzonderlijke webtoepassingen te beveiligen.

WAF voor de voor deur is een wereld wijde en gecentraliseerde oplossing. Het wordt geïmplementeerd op Azure-netwerk Edge-locaties rond de hele wereld en elke binnenkomende aanvraag voor een webtoepassing met WAF die wordt geleverd door de voor deur, wordt gecontroleerd aan de rand van het netwerk. Op deze manier kunnen WAF aanvallen dicht bij de aanvals bronnen voor komen, voordat ze het virtuele netwerk invoeren en wereld wijde beveiliging op schaal bieden zonder verlies van prestaties. Een WAF-beleid kan gemakkelijk worden gekoppeld aan elk front deur profiel in uw abonnement en nieuwe regels kunnen binnen enkele minuten worden geïmplementeerd, zodat u snel kunt reageren op het wijzigen van bedreigings patronen.

![Azure Web Application Firewall](./media/waf-overview/web-application-firewall-overview.png)

Azure WAF kan ook worden ingeschakeld met Application Gateway. Zie [Web Application firewall](../application-gateway/waf-overview.md)voor meer informatie.

## <a name="waf-policy-and-rules"></a>WAF-beleid en-regels

U kunt een WAF-beleid configureren en dat beleid koppelen aan een of meer front-ends vooraan voor beveiliging. Een WAF-beleid bestaat uit twee typen beveiligings regels:

- aangepaste regels die door de klant zijn gemaakt.

- beheerde regel sets die een verzameling van door Azure beheerde vooraf geconfigureerde regels zijn.

Wanneer beide aanwezig zijn, worden aangepaste regels verwerkt voordat de regels in een beheerde regelset worden verwerkt. Een regel bestaat uit een match-voor waarde, een prioriteit en een actie. Ondersteunde actie typen zijn: toestaan, blok keren, aanmelden en omleiden. U kunt een volledig aangepast beleid maken dat voldoet aan uw specifieke vereisten voor toepassings beveiliging door beheerde en aangepaste regels te combi neren.

Regels in een beleid worden verwerkt in een volg orde met prioriteit, waarbij prioriteit een uniek geheel getal is dat de volg orde definieert van de regels die worden verwerkt. Een kleinere integerwaarde duidt op een hogere prioriteit en deze worden geëvalueerd vóór regels met een hoger geheel getal. Zodra een regel is gevonden, wordt de bijbehorende actie toegepast die in de regel is gedefinieerd. Zodra een dergelijke overeenkomst is verwerkt, worden regels met lagere prioriteiten niet verder verwerkt.

Aan een webtoepassing die wordt geleverd via de voor deur kan slechts één WAF-beleid tegelijk zijn gekoppeld. U kunt echter wel een configuratie voor de voor deur hebben zonder dat er WAF-beleid aan is gekoppeld. Als er een WAF-beleid aanwezig is, wordt dit gerepliceerd naar al onze Edge-locaties om consistentie in het beveiligings beleid over de hele wereld te garanderen.

## <a name="waf-modes"></a>WAF modi

Het WAF-beleid kan worden geconfigureerd om te worden uitgevoerd in de volgende twee modi:

- **Detectie modus:** Bij uitvoering in detectie modus voert WAF geen andere acties uit dan monitors en registreert de aanvraag en de overeenkomende WAF-regel naar WAF-Logboeken. U kunt Diagnostische gegevens over logboek registratie inschakelen voor de voor deur (wanneer u Portal gebruikt, kunt u dit bereiken door naar de sectie **Diagnostische gegevens** in de Azure portal te gaan).

- **Preventie modus:** Wanneer het is geconfigureerd om te worden uitgevoerd in de preventie modus, neemt WAF de opgegeven actie als een aanvraag overeenkomt met een regel en als er een overeenkomst wordt gevonden, worden er geen verdere regels met een lagere prioriteit geëvalueerd. Alle overeenkomende aanvragen worden ook in de WAF-logboeken vastgelegd.

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

- Lijst met **toegestane IP-adressen en blok keren:** U kunt aangepaste regels configureren om de toegang tot uw webtoepassingen te beheren op basis van een lijst met IP-adressen van clients of IP-adresbereiken. IPv4-en IPv6-adres typen worden ondersteund. Deze lijst kan worden geconfigureerd om de aanvragen te blok keren of toe te staan waarbij de bron-IP overeenkomt met een IP-adres in de lijst. Een IP-adres van de client kan afwijken van het IP-adres WAF, bijvoorbeeld wanneer een client via een proxy toegang tot WAF heeft. U kunt [regels voor IP-beperking](https://docs.microsoft.com/azure/frontdoor/waf-front-door-configure-ip-restriction) maken op basis van IP-adressen van clients (RemoteAddr) of IP-adressen die worden weer gegeven door WAF (SocketAddr). De configuratie van een SocketAddr-IP-beperkings regel wordt momenteel ondersteund met Power shell en Azure CLI.

- **Geografisch gebaseerd toegangs beheer:** U kunt aangepaste regels configureren om de toegang tot uw webtoepassingen te beheren op basis van de land code die is gekoppeld aan het IP-adres van de client.

- **Http-para meters op basis van toegangs beheer:** U kunt aangepaste regels configureren op basis van een teken reeks die overeenkomt met HTTP/HTTPS-aanvraag parameters, zoals query reeksen, POST-argumenten, aanvraag-URI, aanvraag header en hoofd tekst van aanvraag.

- **Toegangs beheer op basis van aanvraag methode:** U kunt aangepaste regels configureren op basis van de HTTP-aanvraag methode van de aanvraag, zoals GET, PUT of HEAD.

- **Grootte beperking:** U kunt aangepaste regels configureren op basis van de lengte van specifieke delen van een aanvraag, zoals een query reeks, URI of aanvraag tekst.

- **Frequentie beperkende regels:** Een regel voor frequentie beheer is het beperken van abnormaal hoog verkeer van elk client-IP-adres. U kunt een drempel waarde configureren voor het aantal webaanvragen dat is toegestaan vanaf een client-IP gedurende een duur van één minuut. Houd er rekening mee dat extra aanvragen boven de drempel mogelijk kunnen worden ontvangen terwijl het aantal aanvragen wordt bijgewerkt. De frequentie beperking kan worden gecombineerd met aanvullende match voorwaarden, zoals HTTP (S)-para meters die overeenkomen met het besturings element nauw keurigheid.

### <a name="azure-managed-rule-sets"></a>Door Azure beheerde regel sets

Door Azure beheerde regel sets bieden een eenvoudige manier om beveiliging te implementeren op basis van een gemeen schappelijke reeks beveiligings Risico's. Omdat dergelijke regel definities worden beheerd door Azure, worden de regels zo nodig bijgewerkt om te beschermen tegen nieuwe aanvals handtekeningen. Bij open bare Preview bevat de door Azure beheerde standaard regelset regels voor de volgende bedreigings Categorieën:

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
Standaard regelset is standaard ingeschakeld in de detectie modus in uw WAF-beleid. U kunt afzonderlijke regels in de standaard regel instellen om te voldoen aan de vereisten van uw toepassing. U kunt ook specifieke acties (toestaan/blok keren/omleiden/logboek registratie) per regel instellen. De standaard actie is te blok keren. Daarnaast kunnen aangepaste regels worden geconfigureerd in hetzelfde WAF-beleid als u een van de vooraf geconfigureerde regels in de standaardregelset wilt omzeilen.
Aangepaste regels worden altijd toegepast voordat regels in de standaard regelset worden geëvalueerd. Als een aanvraag overeenkomt met een aangepaste regel, wordt de bijbehorende regel actie toegepast en wordt de aanvraag geblokkeerd of door gegeven aan een back-end zonder verdere aangepaste regels of de regels in de standaardregelset. Daarnaast hebt u de optie om de standaardregelset te verwijderen uit uw WAF-beleid.


### <a name="bot-protection-rule-preview"></a>Bot-beveiligings regel (preview-versie)

Een set Managed bot Protection kan worden ingeschakeld voor uw WAF om aangepaste acties uit te voeren op aanvragen van bekende bot-categorieën.
Er zijn drie bot-categorieën ondersteund: beschadigd bots, goede bots en onbekend bots. In de categorie beschadigd bots detecteert één regel schadelijke bots op basis van IP-reputatie van clieny-adressen. De IP-reputatie wordt gebrond vanuit de micro soft Threat Intelligence-feed. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) voorziet in micro soft Threat Intelligence en wordt gebruikt door meerdere services, waaronder Azure Security Center. Daarnaast worden aanvragen die Pretend worden, ook wel gedetecteerd als schadelijk.
Goede bots zijn gevalideerde seacrh-engines. Onbekende categorieën bevatten aanvragen worden aangeduid als bots, maar met onbekende doel groepen. U kunt aangepaste acties instellen voor blok keren, toestaan, Logboeken of omleiden voor verschillende categorieën botsingen.

![Set bot Protection-regel](./media/waf-front-door-configure-bot-protection/BotProtect2.png)

> [!IMPORTANT]
> De set bot Protection-regelset bevindt zich momenteel in een open bare preview en wordt weer gegeven met een preview-service level agreement. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben.  Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Als bot-beveiliging is ingeschakeld, worden inkomende aanvragen die overeenkomen met bot-regels vastgelegd in het FrontdoorWebApplicationFirewallLog-logboek. U kunt toegang krijgen tot WAF-logboeken vanuit het opslag account, Event Hub of log Analytics. 

## <a name="configuration"></a>Configuratie

Het configureren en implementeren van alle WAF-regel typen wordt volledig ondersteund met behulp van Azure Portal, REST-Api's, Azure Resource Manager sjablonen en Azure PowerShell.

## <a name="monitoring"></a>Bewaking

Bewaking voor WAF aan de voor deur is geïntegreerd met Azure Monitor om waarschuwingen bij te houden en trends in het verkeer eenvoudig te bewaken.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het configureren van een WAF-beleid voor de voor deur met behulp van de Azure Portal](waf-front-door-create-portal.md)
