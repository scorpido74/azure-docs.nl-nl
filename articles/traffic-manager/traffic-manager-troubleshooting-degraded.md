---
title: Probleemoplossing voor de gedegradeerde status in Azure Traffic Manager
description: Hoe problemen op te lossen Traffic Manager profielen wanneer het wordt weergegeven als gedegradeerde status.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: rohink
ms.openlocfilehash: c398763405472c9018a5c30d34fbd3963ecb93b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938368"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Problemen oplossen met verminderde beschikbaarheid in Azure Traffic Manager

In dit artikel wordt beschreven hoe u een Azure Traffic Manager-profiel oplossen dat een gedegradeerde status weergeeft. Als eerste stap in het oplossen van problemen is een Azure Traffic Manager gedegradeerde status het inschakelen van diagnostische logboekregistratie.  Raadpleeg [Diagnostische logboeken inschakelen](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-diagnostic-logs) voor meer informatie. Houd er in dit scenario rekening mee dat u een Traffic Manager-profiel hebt geconfigureerd dat naar een aantal van uw cloudapp.net gehoste services wijst. Als de status van uw verkeersbeheerder een **gedegradeerde** status weergeeft, kan de status van een of meer eindpunten worden **afgebroken:**

![gedegradeerde eindpuntstatus](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Als de status van uw verkeersbeheermanager een **inactieve** status weergeeft, kunnen beide eindpunten worden **uitgeschakeld:**

![Inactieve traffic manager-status](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Inzicht in verkeersmanager-sondes

* Traffic Manager beschouwt een eindpunt alleen als online wanneer de sonde een HTTP 200-respons terugontvangt van het sondepad. Als u een andere HTTP-antwoordcode retourneert, moet u die antwoordcode toevoegen aan [de verwachte statuscodebereiken](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) van uw Traffic Manager-profiel.
* Een antwoord van 30x omleiding wordt als mislukt beschouwd, tenzij u dit hebt opgegeven als een geldige reactiecode in [verwachte statuscodebereiken](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) van uw Traffic Manager-profiel. Traffic Manager controleert het omleidingsdoel niet.
* Voor HTTP-sondes worden certificaatfouten genegeerd.
* De werkelijke inhoud van het sondepad maakt niet uit, zolang een 200 wordt geretourneerd. Het onderzoeken van een URL naar een aantal statische inhoud zoals "/favicon.ico" is een veel voorkomende techniek. Dynamische inhoud, zoals de ASP-pagina's, kan niet altijd 200 retourneren, zelfs niet als de toepassing in orde is.
* Een aanbevolen methode is om het sondepad in te stellen op iets dat voldoende logica heeft om te bepalen dat de site omhoog of omlaag is. In het vorige voorbeeld test u alleen door het pad in te stellen op "/favicon.ico". Deze sonde geeft mogelijk niet aan dat uw webtoepassing in orde is. Een betere optie zou zijn om een pad in te stellen op een iets als "/Probe.aspx" dat logica heeft om de status van de site te bepalen. U bijvoorbeeld prestatiemeteritems gebruiken voor het cpu-gebruik of het aantal mislukte aanvragen meten. U ook proberen toegang te krijgen tot databasebronnen of sessiestatus om ervoor te zorgen dat de webtoepassing werkt.
* Als alle eindpunten in een profiel zijn afgebroken, behandelt Traffic Manager alle eindpunten als gezond en leidt het verkeer naar alle eindpunten. Dit gedrag zorgt ervoor dat problemen met het indringende mechanisme niet leiden tot een volledige uitval van uw service.

## <a name="troubleshooting"></a>Problemen oplossen

Als u een fout in een sonde wilt oplossen, hebt u een hulpprogramma nodig dat de HTTP-statuscode retour van de URL van de sonde weergeeft. Er zijn veel tools beschikbaar die u de ruwe HTTP-respons laten zien.

* [Fiddler](https://www.telerik.com/fiddler)
* [Curl](https://curl.haxx.se/)
* [Wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

U ook het tabblad Netwerk van de hulpprogramma's voor foutopsporing F12 in Internet Explorer gebruiken om de HTTP-antwoorden weer te geven.

Voor dit voorbeeld willen we het antwoord van\/onze sonde URL zien: http: /watestsdp2008r2.cloudapp.net:80/Probe. Het volgende PowerShell-voorbeeld illustreert het probleem.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Voorbeelduitvoer:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Merk op dat we een omleidingsreactie hebben ontvangen. Zoals eerder vermeld, wordt een andere StatusCode dan 200 beschouwd als een fout. Traffic Manager wijzigt de eindpuntstatus in Offline. Als u het probleem wilt oplossen, controleert u de websiteconfiguratie om ervoor te zorgen dat de juiste StatusCode kan worden geretourneerd van het sondepad. Configureer de sonde Verkeersbeheer opnieuw om een pad aan te wijzen dat een 200 retourneert.

Als uw sonde het HTTPS-protocol gebruikt, moet u mogelijk certificaatcontrole uitschakelen om SSL/TLS-fouten tijdens uw test te voorkomen. Met de volgende PowerShell-instructies wordt de certificaatvalidatie voor de huidige PowerShell-sessie uitgeschakeld:

```powershell
add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
    ServicePoint srvPoint, X509Certificate certificate,
    WebRequest request, int certificateProblem) {
    return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Volgende stappen

[Over verkeersbeheermethoden](traffic-manager-routing-methods.md)

[Wat is Traffic Manager](traffic-manager-overview.md)

[Cloudservices](https://go.microsoft.com/fwlink/?LinkId=314074)

[Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/)

[Bewerkingen op Traffic Manager (REST API-referentiemateriaal)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager Cmdlets][1]

[1]: https://docs.microsoft.com/powershell/module/az.trafficmanager
