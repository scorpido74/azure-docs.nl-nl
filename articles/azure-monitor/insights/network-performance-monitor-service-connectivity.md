---
title: Network Performance Monitor solution Service Connectivity - Azure Log Analytics
description: Gebruik de serviceconnectivity monitor-mogelijkheid in netwerkprestatiemeter om de netwerkconnectiviteit te controleren op elk eindpunt met een open TCP-poort.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 93f3820b7cf1db85b9ff4cd514fe22efc75a90d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249241"
---
# <a name="service-connectivity-monitor"></a>Bewaking van serviceverbinding

U de serviceconnectivity monitor-mogelijkheid in [netwerkprestatiemonitor](network-performance-monitor.md) gebruiken om de netwerkconnectiviteit te controleren op elk eindpunt met een open TCP-poort. Dergelijke eindpunten omvatten websites, SaaS-toepassingen, PaaS-toepassingen en SQL-databases. 

U de volgende functies uitvoeren met Service Connectivity Monitor: 

- Monitor de netwerkconnectiviteit met uw applicaties en netwerkservices vanuit meerdere filialen of locaties. Toepassingen en netwerkservices omvatten Office 365, Dynamics CRM, interne line-of-business-toepassingen en SQL-databases.
- Gebruik ingebouwde tests om de netwerkconnectiviteit met Office 365- en Dynamics 365-eindpunten te bewaken. 
- Bepaal de reactietijd, netwerklatentie en pakketverlies die worden ervaren bij het maken van verbinding met het eindpunt.
- Bepaal of slechte toepassingsprestaties te wijten zijn aan het netwerk of vanwege een probleem aan het einde van de toepassingsprovider.
- Identificeer hotspots in het netwerk die slechte toepassingsprestaties kunnen veroorzaken door de latentie te bekijken die door elke hop wordt bijgedragen op een topologiekaart.


![Bewaking van serviceverbinding](media/network-performance-monitor-service-endpoint/service-endpoint-intro.png)


## <a name="configuration"></a>Configuratie 
Als u de configuratie voor netwerkprestatiemonitor wilt openen, opent u de [oplossing netwerkprestatiemeter](network-performance-monitor.md) en selecteert **u Configureren**.

![Netwerkprestatiemeter configureren](media/network-performance-monitor-service-endpoint/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>Log Analytics-agents configureren voor bewaking
Schakel de volgende firewallregels in op de knooppunten die worden gebruikt voor bewaking, zodat de oplossing de topologie kan ontdekken van uw knooppunten tot het serviceeindpunt: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Service Connectivity Monitor-tests maken 

Begin met het maken van uw tests om de netwerkverbinding met de serviceeindpunten te bewaken.

1. Selecteer het tabblad **Serviceconnectivity Monitor.**
2. Selecteer **Test toevoegen**en voer de testnaam en -beschrijving in. U maximaal 450 tests per werkruimte maken. 
3. Selecteer het type test:<br>

    * Selecteer **Web** om de verbinding met een service te controleren die reageert op HTTP/S-verzoeken, zoals outlook.office365.com of bing.com.<br>
    * Selecteer **Netwerk** om de verbinding met een service te controleren die reageert op TCP-aanvragen, maar niet reageert op HTTP/S-verzoeken, zoals een SQL-server, FTP-server of SSH-poort. 
    * Bijvoorbeeld: Als u een webtest wilt maken voor een blob-opslagaccount, selecteert u **Web** en voert u doel in als *uw opslagaccount*.blob.core.windows.net. Op dezelfde manier u tests maken voor andere tabelopslag, wachtrijopslag en Azure-bestanden met behulp van [deze koppeling.](https://docs.microsoft.com/azure/storage/common/storage-account-overview#storage-account-endpoints)
4. Als u geen netwerkmetingen wilt uitvoeren, zoals netwerklatentie, pakketverlies en topologiedetectie, schakelt u het selectievakje **Netwerkmetingen uitvoeren** uit. Houd het geselecteerd om maximaal voordeel te halen uit de mogelijkheid. 
5. Voer **in Target**het URL/FQDN/IP-adres in waarop u de netwerkconnectiviteit wilt bewaken.
6. Voer **in Poortnummer**het poortnummer van de doelservice in. 
7. Voer in **Testfrequentie**een waarde in voor hoe vaak u de test wilt uitvoeren. 
8. Selecteer de knooppunten waaruit u de netwerkverbinding met de service wilt controleren. Zorg ervoor dat het aantal agentia per test minder dan 150 bedraagt. Elke agent kan maximaal 150 eindpunten/agenten testen.

    >[!NOTE]
    > Voor op Windows-servers gebaseerde knooppunten maakt de mogelijkheid gebruik van op TCP gebaseerde aanvragen om de netwerkmetingen uit te voeren. Voor windows-clientknooppunten gebruikt de mogelijkheid ICMP-gebaseerde aanvragen om de netwerkmetingen uit te voeren. In sommige gevallen blokkeert de doeltoepassing binnenkomende ICMP-aanvragen wanneer de knooppunten op Windows-client zijn gebaseerd. De oplossing kan geen netwerkmetingen uitvoeren. We raden u aan in dergelijke gevallen op Windows-server gebaseerde knooppunten te gebruiken. 

9. Als u geen statusgebeurtenissen wilt maken voor de items die u selecteert, schakelt u **Statuscontrole inschakelen uit in de doelen die onder deze test vallen.** 
10. Kies controlevoorwaarden. U aangepaste drempelwaarden instellen voor het genereren van statusgebeurtenissen door drempelwaarden in te voeren. Wanneer de waarde van de voorwaarde boven de geselecteerde drempelwaarde voor het geselecteerde netwerk- of subnetwerkpaar komt, wordt een statusgebeurtenis gegenereerd. 
11. Selecteer **Opslaan** om de configuratie op te slaan. 

    ![Testconfiguraties van serviceconnectiviteitsmonitor](media/network-performance-monitor-service-endpoint/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Kennismaking 

Ga naar de dashboardweergave netwerkprestatiemeter. Als u een overzicht wilt krijgen van de status van de verschillende tests die u hebt gemaakt, kijkt u op de pagina **Service Connectivity Monitor.** 

![Pagina Service Connectivity Monitor](media/network-performance-monitor-service-endpoint/service-endpoint-blade.png)

Selecteer de tegel om de details van de tests weer te geven op de pagina **Tests.** In de tabel aan de linkerkant u de point-in-time status en waarde van de serviceresponstijd, netwerklatentie en pakketverlies voor alle tests weergeven. Gebruik het besturingselement Network State Recorder om de netwerkmomentopname op een ander tijdstip in het verleden weer te geven. Selecteer de test in de tabel die u wilt onderzoeken. In de grafieken in het deelvenster aan de rechterkant u de historische trend van de waarden voor verlies, latentie en responstijd bekijken. Selecteer de koppeling **Testdetails** om de prestaties van elk knooppunt weer te geven.

![Tests voor serviceconnectiviteitsmonitor](media/network-performance-monitor-service-endpoint/service-endpoint-tests.png)

In de weergave **Testknooppunten** u de netwerkconnectiviteit vanaf elk knooppunt observeren. Selecteer het knooppunt met prestatiedegradatie. Dit is het knooppunt waar de toepassing wordt waargenomen traag te worden uitgevoerd.

Bepaal of slechte toepassingsprestaties te wijten zijn aan het netwerk of een probleem aan het einde van de toepassingsprovider door de correlatie tussen de responstijd van de toepassing en de netwerklatentie te observeren. 

* **Aanvraagprobleem:** Een piek in de responstijd, maar consistentie in de netwerklatentie suggereert dat het netwerk werkt prima en het probleem kan te wijten zijn aan een probleem op de toepassing einde. 

    ![Probleem met de toepassing serviceconnectiviteitsmonitor](media/network-performance-monitor-service-endpoint/service-endpoint-application-issue.png)

* **Netwerkprobleem:** Een piek in de responstijd gepaard met een overeenkomstige piek in netwerklatentie suggereert dat de toename van de responstijd mogelijk te wijten is aan een toename van de netwerklatentie. 

    ![Netwerkprobleem serviceconnectiviteitsmonitor](media/network-performance-monitor-service-endpoint/service-endpoint-network-issue.png)

Nadat u hebt vastgesteld dat het probleem vanwege het netwerk komt, selecteert u de koppeling **Topologieweergave** om de lastige hop op de topologiekaart te identificeren. Een voorbeeld wordt weergegeven in de volgende afbeelding. Van de totale latentie van 105 ms tussen het knooppunt en het toepassingseindpunt is 96 ms vanwege de in het rood gemarkeerde hop. Nadat u de lastige hop hebt geïdentificeerd, u corrigerende maatregelen nemen. 

![Tests voor serviceconnectiviteitsmonitor](media/network-performance-monitor-service-endpoint/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnostiek 

Als u een afwijking waarneemt, volgt u de volgende stappen:

* Als de reactietijd van de service, netwerkverlies en latentie worden weergegeven als NA, kunnen een of meer van de volgende redenen de oorzaak zijn:

    - De aanvraag is uitgevallen.
    - Het knooppunt dat wordt gebruikt voor het controleren van de netwerkverbinding met de service is uitgeschakeld.
    - Het doel dat in de testconfiguratie is ingevoerd, is onjuist.
    - Het knooppunt heeft geen netwerkconnectiviteit.

* Als een geldige serviceresponstijd wordt weergegeven, maar netwerkverlies en latentie worden weergegeven als NA, kunnen een of meer van de volgende redenen de oorzaak zijn:

    - Als het knooppunt dat wordt gebruikt voor het controleren van de netwerkverbinding met de service een Windows-clientmachine is, blokkeert de doelservice ICMP-aanvragen of blokkeert een netwerkfirewall ICMP-verzoeken die afkomstig zijn van het knooppunt.
    - Het selectievakje **Netwerkmetingen uitvoeren** is leeg in de testconfiguratie. 

* Als de reactietijd van de service NA is, maar netwerkverlies en latentie geldig zijn, is de doelservice mogelijk geen webtoepassing. Bewerk de testconfiguratie en kies het testtype als **netwerk** in plaats van **web.** 

* Als de toepassing traag werkt, bepaalt u of de prestaties van de toepassing zijn vanwege het netwerk of een probleem aan het einde van de toepassingsprovider.

## <a name="gcc-office-urls-for-us-government-customers"></a>GCC Office-URL's voor klanten van de Amerikaanse overheid
Voor de regio Virginia van de Amerikaanse regering zijn alleen DOD-URL's ingebouwd in NPM. Klanten die GCC-URL's gebruiken, moeten aangepaste tests maken en elke URL afzonderlijk toevoegen.

| Veld | GCC |
|:---   |:--- |
| Office 365-portal en gedeeld | portal.apps.mil |
| Office 365 auth en identiteit | * login.microsoftonline.us <br> * api.login.microsoftonline.com <br> * clientconfig.microsoftonline-p.net <br> * login.microsoftonline.com <br> * login.microsoftonline-p.com <br> * login.windows.net <br> * loginex.microsoftonline.com <br> * login-us.microsoftonline.com <br> * nexus.microsoftonline-p.com <br> * mscrl.microsoft.com <br> * secure.aadcdn.microsoftonline-p.com |
| Office Online | * adminwebservice.gov.us.microsoftonline.com <br>  * adminwebservice-s1-bn1a.microsoftonline.com <br> * adminwebservice-s1-dm2a.microsoftonline.com <br> * becws.gov.us.microsoftonline.com <br> * provisioningapi.gov.us.microsoftonline.com <br> * officehome.msocdn.us <br> * prod.msocdn.us <br> * portal.office365.us <br> * webshell.suite.office365.us <br> * www .office365.us <br> * activation.sls.microsoft.com <br> * crl.microsoft.com <br> * go.microsoft.com <br> * insertmedia.bing.office.net <br> * ocsa.officeapps.live.com <br> * ocsredir.officeapps.live.com <br> * ocws.officeapps.live.com <br> * office15client.microsoft.com <br>* officecdn.microsoft.com <br> * officecdn.microsoft.com.edgesuite.net <br> * officepreviewredir.microsoft.com <br> * officeredir.microsoft.com <br> * ols.officeapps.live.com  <br> * r.office.microsoft.com <br> * cdn.odc.officeapps.live.com <br> * odc.officeapps.live.com <br> * officeclient.microsoft.com |
| Exchange Online | * outlook.office365.us <br> * attachments.office365-net.us <br> * autodiscover-s.office365.us <br> * manage.office365.us <br> * scc.office365.us |
| MS-teams | gov.teams.microsoft.us | 

## <a name="next-steps"></a>Volgende stappen
[Zoeklogboeken](../../azure-monitor/log-query/log-query-overview.md) om gedetailleerde netwerkprestatiegegevensrecords weer te geven.
