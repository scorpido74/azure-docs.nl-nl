---
title: Problemen met de beschikbaarheidstests voor Azure Application Insights oplossen | Microsoft Documenten
description: Problemen met webtests oplossen in Azure Application Insights. Ontvang een waarschuwing wanneer een website niet meer beschikbaar is of traag reageert.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/19/2019
ms.reviewer: sdash
ms.openlocfilehash: f135aa6c0a4a55f8a42fd858572cc811e25b27c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671100"
---
# <a name="troubleshooting"></a>Problemen oplossen

In dit artikel u veelvoorkomende problemen oplossen die zich kunnen voordoen bij het gebruik van beschikbaarheidsbewaking.

## <a name="ssltls-errors"></a>SSL/TLS-fouten

|Symptoom/foutmelding| Mogelijke oorzaken|
|--------|------|
|Kan ssl/tls secure channel niet maken  | SSL-versie. Alleen TLS 1.0, 1.1 en 1.2 worden ondersteund. **SSLv3 wordt niet ondersteund.**
|TLSv1.2 Record Layer: Alert (Niveau: Fatal, Beschrijving: Bad Record MAC)| Zie StackExchange-thread voor [meer informatie](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake).
|URL die mislukt, is naar een CDN (Content Delivery Network) | Dit kan worden veroorzaakt door een verkeerde configuratie op uw CDN |  

### <a name="possible-workaround"></a>Mogelijke tijdelijke oplossing

* Als de URL's die het probleem ervaren altijd afhankelijk zijn van resources, wordt aanbevolen om **afhankelijke aanvragen** voor de webtest uit te schakelen.

## <a name="test-fails-only-from-certain-locations"></a>Test mislukt alleen vanaf bepaalde locaties

|Symptoom/foutmelding| Mogelijke oorzaken|
|----|---------|
|Een verbindingspoging is mislukt omdat de verbonden partij na een bepaalde periode niet correct heeft gereageerd  | Testagenten op bepaalde locaties worden geblokkeerd door een firewall.|
|    |Het omleiden van bepaalde IP-adressen vindt plaats via (Load Balancers, Geo traffic managers, Azure Express Route.) 
|    |Als u Azure ExpressRoute gebruikt, zijn er scenario's waarin pakketten kunnen worden verwijderd in gevallen waarin [Asymmetrische routering plaatsvindt.](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing)|

## <a name="test-failure-with-a-protocol-violation-error"></a>Fout testen met een fout in protocolschending

|Symptoom/foutmelding| Mogelijke oorzaken| Mogelijke resoluties |
|----|---------|-----|
|De server heeft een protocolschending begaan. Section=ResponseHeader Detail=CR moet worden gevolgd door LF | Dit gebeurt wanneer misvormde kopteksten worden gedetecteerd. In het bijzonder gebruiken sommige headers crlf niet om het einde van de regel aan te geven, wat in strijd is met de HTTP-specificatie. Application Insights dwingt deze HTTP-specificatie af en faalt in reacties met misvormde headers.| a. Neem contact op met de websitehostprovider / CDN-provider om de defecte servers op te lossen. <br> b. In het geval dat de mislukte aanvragen resources zijn (bijvoorbeeld stijlbestanden, afbeeldingen, scripts), u overwegen om het ontleden van afhankelijke aanvragen uit te schakelen. Houd er rekening mee dat als u dit doet, u de mogelijkheid verliest om de beschikbaarheid van die bestanden te controleren).

> [!NOTE]
> De URL mag niet mislukken in browsers met een ontspannen validatie van HTTP-headers. Zie dit blogbericht voor een gedetailleerde uitleg van dit probleem: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Veelvoorkomende probleemoplossingsvragen

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>De site ziet er goed uit, maar ik constateer testfouten Waarom waarschuwt Application Insights mij?

   * Is er in uw test **Parse afhankelijke aanvragen** ingeschakeld? Dat resulteert in een strikte controle op middelen zoals scripts, afbeeldingen etc. Dit soort fouten is mogelijk niet merkbaar in een browser. Controleer alle afbeeldingen, scripts, stijlmodellen en andere bestanden geladen door de pagina. Als een van hen mislukt, wordt de test gerapporteerd als mislukt, zelfs als de hoofd-HTML-pagina zonder problemen wordt geladen. Als u de test wilt desensibiliseren voor dergelijke resourcefouten, schakelt u de parse-afhankelijke aanvragen uit de testconfiguratie uit.

   * Om de kans op ruis door tijdelijke netwerkblips enz. U ook vanaf meer locaties testen en de drempelwaarde voor waarschuwingsregels dienovereenkomstig beheren om locatiespecifieke problemen te voorkomen die onnodige waarschuwingen veroorzaken.

   * Klik op een van de rode stippen uit de beschikbaarheidservaring of een beschikbaarheidsfout van de zoekverkenner om de details te zien van waarom we de fout hebben gemeld. Het testresultaat, samen met de gecorreleerde telemetrie aan de serverzijde (indien ingeschakeld) moet helpen begrijpen waarom de test is mislukt. Veelvoorkomende oorzaken van tijdelijke problemen zijn netwerk- of verbindingsproblemen.

   * Heeft de test time-out? We aborteren tests na 2 minuten. Als uw ping- of multi-step test langer dan 2 minuten duurt, zullen we dit als een fout melden. Overweeg om de test op te splitsen in meerdere exemplaren die in kortere duur kunnen worden voltooid.

   * Hebben alle locaties melding gemaakt van falen, of slechts enkele van hen? Als slechts enkele gemelde storingen, kan dit te wijten zijn aan netwerk / CDN problemen. Nogmaals, te klikken op de rode stippen moet helpen begrijpen waarom de locatie gemeld mislukkingen.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Ik heb geen e-mail ontvangen wanneer de waarschuwing geactiveerd, of opgelost of beide?

Controleer de configuratie van klassieke waarschuwingen om te controleren of uw e-mail rechtstreeks wordt vermeld, of een distributielijst waarin u zich bevindt, is geconfigureerd om meldingen te ontvangen. Als dit het is, controleer dan de configuratie van de distributielijst om te bevestigen dat het externe e-mails kan ontvangen. Controleer ook of uw e-mailbeheerder beleid heeft geconfigureerd dat dit probleem kan veroorzaken.

### <a name="i-did-not-receive-the-webhook-notification"></a>Heb ik de webhook-melding niet ontvangen?

Controleer of de toepassing die de webhook-melding ontvangt beschikbaar is en verwerkt met succes de webhook-verzoeken. Zie [dit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) voor meer informatie.

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Onregelmatige testfout met een protocolfout?

De fout 'Schending van protocol... CR moet worden gevolgd door LF' geeft een probleem met de server (of afhankelijkheden) aan. Dit gebeurt wanneer onjuist gevormde headers zijn ingesteld in het antwoord. Dit kan worden veroorzaakt door load balancers of CDN's. In het bijzonder gebruiken sommige headers crlf niet om het einde van de regel aan te geven, wat in strijd is met de HTTP-specificatie en daarom de validatie op .NET WebRequest-niveau mislukken. Controleer het antwoord op kopteksten ter plaatse, die mogelijk in overtreding zijn.

> [!NOTE]
> De URL mag niet mislukken in browsers met een ontspannen validatie van HTTP-headers. Zie dit blogbericht voor een gedetailleerde uitleg van dit probleem: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Ik zie geen gerelateerde telemetrie aan de serverzijde om testfouten te diagnosticeren?*

Als u Application Insights hebt ingesteld voor uw app aan serverzijde, kan dit komen doordat er [steekproeven](../../azure-monitor/app/sampling.md) worden uitgevoerd. Selecteer een ander beschikbaarheidsresultaat.

### <a name="can-i-call-code-from-my-web-test"></a>Kan ik code aanroepen via mijn webtest?

Nee. De stappen van de test moeten zich in het bestand .webtest bevinden. U kunt geen andere webtests aanroepen of lussen gebruiken. Maar er zijn verschillende invoegtoepassingen die nuttig kunnen zijn.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Is er een verschil tussen webtests en beschikbaarheidstests?

De twee voorwaarden kunnen door elkaar worden gebruikt. 'Beschikbaarheidstest' is een algemenere term waar niet alleen webtests met meerdere stappen, maar ook tests met enkele URL-ping onder vallen.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Ik wil graag beschikbaarheidstests gebruiken voor onze interne server die achter een firewall wordt uitgevoerd.

   Er zijn twee mogelijke oplossingen:

   * Configureer uw firewall om binnenkomende aanvragen van de [IP-adressen van onze webtestagents](../../azure-monitor/app/ip-addresses.md) toe te staan.
   * Schrijf uw eigen code om uw interne server periodiek te testen. Voer de code uit als achtergrondproces op een testserver achter de firewall. De resultaten van het testproces kunnen worden verzonden naar Application Insights door de API [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) te gebruiken in het SDK-kernpakket. Hiervoor moet uw testserver uitgaande toegang hebben tot het opname-eindpunt van Application Insights, maar dit is een veel kleiner beveiligingsrisico dan wanneer u binnenkomende aanvragen toestaat. De resultaten zullen verschijnen in de beschikbaarheid web tests bladen hoewel de ervaring zal iets worden vereenvoudigd van wat beschikbaar is voor tests gemaakt via het portaal. Aangepaste beschikbaarheidstests worden ook weergegeven als beschikbaarheidsresultaten in Analytics, Search en Metrics.

### <a name="uploading-a-multi-step-web-test-fails"></a>Het uploaden van een webtest met meerdere stappen mislukt

Enkele redenen waarom dit zou kunnen gebeuren:
   * Er is een limiet van 300 K.
   * Lussen worden niet ondersteund.
   * Verwijzingen naar andere webtests worden niet ondersteund.
   * Gegevensbronnen worden niet ondersteund.

### <a name="my-multi-step-test-doesnt-complete"></a>Mijn test met meerdere stappen wordt niet voltooid

Er is een limiet van 100 aanvragen per test. Ook wordt de test gestopt als deze langer dan twee minuten duurt.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Hoe voer ik een test uit met clientcertificaten?

Dit wordt momenteel niet ondersteund.

## <a name="who-receives-the-classic-alert-notifications"></a>Wie ontvangt de (klassieke) waarschuwingsmeldingen?

Deze sectie is alleen van toepassing op klassieke waarschuwingen en helpt u uw waarschuwingsmeldingen te optimaliseren om ervoor te zorgen dat alleen uw gewenste ontvangers meldingen ontvangen. Voor meer informatie over het verschil tussen [klassieke waarschuwingen](../platform/alerts-classic.overview.md)en de nieuwe waarschuwingen ervaring verwijzen naar de [waarschuwingen overzicht artikel](../platform/alerts-overview.md). Als u de meldingsmelding in de nieuwe waarschuwingen wilt beheren, gebruikt u [actiegroepen](../platform/action-groups.md).

* We raden het gebruik van specifieke ontvangers aan voor klassieke waarschuwingsmeldingen.

* Voor waarschuwingen over storingen van X uit Y-locaties wordt de optie **bulk/groep,** indien ingeschakeld, naar gebruikers met beheerders-/co-beheerdersrollen verzendt.  In wezen ontvangen _alle_ beheerders van het _abonnement_ meldingen.

* Voor waarschuwingen over beschikbaarheidsstatistieken wordt de optie **bulk/groep** ingeschakeld als deze is ingeschakeld, wordt u naar gebruikers met eigenaar, bijdrager of lezer in het abonnement verzendt. In feite zijn _alle_ gebruikers met toegang tot het abonnement de Application Insights-bron in het bereik en ontvangen ze meldingen. 

> [!NOTE]
> Als u momenteel de optie **bulk/groep** gebruikt en uitschakelt, u de wijziging niet terugdraaien.

Gebruik de nieuwe waarschuwingservaring/bijna realtime waarschuwingen als u gebruikers op de hoogte moet stellen op basis van hun rollen. Met [actiegroepen](../platform/action-groups.md)u e-mailmeldingen configureren aan gebruikers met een van de rollen tussen de inzender/eigenaar/lezer (niet gecombineerd als één optie).

## <a name="next-steps"></a>Volgende stappen

* [Webtesten in meerdere stappen](availability-multistep.md)
* [URL-pingtests](monitor-web-app-availability.md)
