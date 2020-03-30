---
title: Regels aanpassen met portal - Azure Web Application Firewall
description: In dit artikel vindt u informatie over het aanpassen van firewallregels voor webtoepassingen in Application Gateway met de Azure-portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: c4635333614ee1c0fd0322c29a659380fb4315c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048379"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Firewallregels voor webtoepassingen aanpassen met de Azure-portal

De Azure Application Gateway Web Application Firewall (WAF) biedt bescherming voor webtoepassingen. Deze beveiligingen worden geboden door de Open Web Application Security Project (OWASP) Core Rule Set (CRS). Sommige regels kunnen valse positieven veroorzaken en echt verkeer blokkeren. Daarom biedt Application Gateway de mogelijkheid om regelgroepen en regels aan te passen. Zie [Lijst met CRS-regelgroepen en -regels voor webtoepassingsfirewall.](application-gateway-crs-rulegroups-rules.md)

>[!NOTE]
> Als de toepassingsgateway de WAF-laag niet gebruikt, wordt de optie om de toepassingsgateway te upgraden naar de WAF-laag in het rechterdeelvenster weergegeven. 

![WAF inschakelen][fig1]

## <a name="view-rule-groups-and-rules"></a>Regelgroepen en regels weergeven

**Regelgroepen en regels weergeven**
1. Blader naar de toepassingsgateway en selecteer vervolgens **firewall voor webtoepassingen**.  
2. Selecteer uw **WAF-beleid**.
2. Selecteer **Beheerregels**.

   In deze weergave wordt een tabel weergegeven op de pagina van alle regelgroepen die zijn voorzien van de gekozen regelset. Alle selectievakjes van de regel zijn geselecteerd.

## <a name="disable-rule-groups-and-rules"></a>Regelgroepen en regels uitschakelen

> [!IMPORTANT]
> Wees voorzichtig bij het uitschakelen van regelgroepen of regels. Dit kan u blootstellen aan verhoogde veiligheidsrisico's.

Wanneer u regels uitschakelt, u een hele regelgroep of specifieke regels uitschakelen onder een of meer regelgroepen. 

**Regelgroepen of specifieke regels uitschakelen**

   1. Zoek naar de regels of regelgroepen die u wilt uitschakelen.
   2. Schakel de selectievakjes in voor de regels die u wilt uitschakelen. 
   3. Selecteer de actie boven aan de pagina (inschakelen/uitschakelen) voor de geselecteerde regels.
   2. Selecteer **Opslaan**. 

![Wijzigingen opslaan][3]

## <a name="mandatory-rules"></a>Verplichte regels

De volgende lijst bevat voorwaarden die ervoor zorgen dat de WAF het verzoek blokkeert in de preventiemodus. In de detectiemodus worden ze geregistreerd als uitzonderingen.

Deze kunnen niet worden geconfigureerd of uitgeschakeld:

* Als u de aanvraaginstantie niet ontleedt, wordt de aanvraag geblokkeerd, tenzij de lichaamsinspectie is uitgeschakeld (XML, JSON, formuliergegevens)
* De gegevenslengte van de aanvraagtekst (zonder bestanden) is groter dan de geconfigureerde limiet
* Aanvraagbody (inclusief bestanden) is groter dan de limiet
* Er is een interne fout opgetreden in de WAF-engine

CRS 3.x specifiek:

* Inkomende anomaliescore overschreden drempelwaarde

## <a name="next-steps"></a>Volgende stappen

Nadat u uw uitgeschakelde regels hebt geconfigureerd, u leren hoe u uw WAF-logboeken bekijken. Zie [Application Gateway diagnostics](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)voor meer informatie.

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png
