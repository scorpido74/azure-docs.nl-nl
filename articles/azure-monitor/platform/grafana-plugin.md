---
title: Azure-services en -toepassingen bewaken met Grafana
description: Route Azure Monitor en Application Insights gegevens, zodat u ze bekijken in Grafana.
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: 142e3e19c13710963d239a75bc237b63713c29cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672205"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Uw Azure-services controleren in Grafana
U azure-services en -toepassingen nu vanuit [Grafana](https://grafana.com/) controleren met behulp van de [plug-in azure monitor-gegevensbron.](https://grafana.com/plugins/grafana-azure-monitor-datasource) De plug-in verzamelt prestatiegegevens van toepassingen die zijn verzameld door Azure Monitor, inclusief verschillende logboeken en statistieken. U deze gegevens vervolgens weergeven op uw Grafana-dashboard.

Gebruik de volgende stappen om een Grafana-server in te stellen en dashboards te bouwen voor statistieken en logboeken van Azure Monitor.

## <a name="set-up-a-grafana-server"></a>Een Grafana-server instellen

### <a name="set-up-grafana-locally"></a>Grafana lokaal instellen
Als u een lokale Grafana-server wilt instellen, [downloadt en installeert u Grafana in uw lokale omgeving.](https://grafana.com/grafana/download) Als u de Azure Monitor-integratie van de plug-in wilt gebruiken, installeert u Grafana-versie 5.3 of hoger.

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Grafana instellen op Azure via de Azure Marketplace
1. Ga naar Azure Marketplace en kies Grafana by Grafana Labs.

2. Vul de namen en details in. Maak een nieuwe resourcegroep. Houd bij welke waarden u kiest voor het wachtwoord voor de VM-gebruikersnaam, het VM-wachtwoord en het wachtwoord van de Grafana-serverbeheerder.  

3. Kies VM-grootte en een opslagaccount.

4. Configureer de netwerkconfiguratie-instellingen.

5. Bekijk het overzicht en selecteer **Maken** nadat u de gebruiksvoorwaarden hebt geaccepteerd.

6. Nadat de implementatie is voltooid, selecteert **u Ga naar Resourcegroep**. U ziet een lijst met nieuw gemaakte bronnen.

    ![Objecten van grafana-resourcegroep](media/grafana-plugin/grafana1.png)

    Als u de netwerkbeveiligingsgroep *(grafana-nsg* in dit geval) selecteert, u zien dat poort 3000 wordt gebruikt om toegang te krijgen tot de Grafana-server.

7. Download het openbare IP-adres van uw Grafana-server - ga terug naar de lijst met bronnen en selecteer **Openbaar IP-adres**.

## <a name="sign-in-to-grafana"></a>Log hier in

1. Open de pagina Login op *http://\<IP-adres\>:3000* of * \<dnsname \:>3000* in uw browser met het IP-adres van uw server. Hoewel 3000 de standaardpoort is, moet u er rekening mee houden dat u tijdens de installatie een andere poort hebt geselecteerd. U ziet een aanmeldingspagina voor de Grafana-server die u hebt gebouwd.

    ![Grafana-inlogscherm](./media/grafana-plugin/grafana-login-screen.png)

2. Meld u aan bij de beheerder *van* de gebruikersnaam en het wachtwoord van de Grafana-serverbeheerder die u eerder hebt gemaakt. Als u een lokale instelling gebruikt, is het standaardwachtwoord *beheerder*en wordt u gevraagd om het te wijzigen bij uw eerste aanmelding.

## <a name="configure-data-source-plugin"></a>Plug-in gegevensbron configureren

Nadat u met succes bent ingelogd, moet u zien dat de plug-in Azure Monitor-gegevensbron al is opgenomen.

![Grafana bevat Azure Monitor-plug-in](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. Selecteer **Gegevensbron toevoegen** om de Azure Monitor-gegevensbron toe te voegen en te configureren.

2. Kies een naam voor de gegevensbron en selecteer **Azure Monitor** als type in de vervolgkeuzelijst.

3. Een serviceprincipal maken - Grafana gebruikt een Azure Active Directory-serviceprincipal om verbinding te maken met Azure Monitor API's en gegevens te verzamelen. U moet een bestaande serviceprincipal maken of gebruiken om toegang tot uw Azure-bronnen te beheren.
    * Bekijk [deze instructies](../../azure-resource-manager/resource-group-create-service-principal-portal.md) om een serviceprincipal te maken. Kopieer en sla uw tenant-id (Directory-id), client-id (toepassings-id) en clientgeheim (waarde van de toepassingssleutel) op.
    * Zie [Toepassing toewijzen aan rol](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) om de leesrol toe te wijzen aan de Azure Active Directory-toepassing op het abonnement, de resourcegroep of de resource die u wilt controleren. 
    De Log Analytics API vereist de [rol Log Analytics Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader), inclusief de machtigingen van de Reader-rol en wordt eraan toegevoegd.

4. Geef de verbindingsgegevens op aan de API's die u wilt gebruiken. U verbinding maken met alle of met een aantal van hen. 
    * Als u verbinding maakt met zowel metrische gegevens als logboeken in Azure Monitor, u dezelfde referenties opnieuw gebruiken door **dezelfde details als Azure Monitor API te**selecteren.
    * Wanneer u de plug-in configureert, u aangeven welke Azure Cloud u wilt dat de plug-in controleert (Openbare, Azure US Government, Azure Germany of Azure China).
    * Als u Application Insights gebruikt, u ook uw Application Insights API en application ID opnemen om op Application Insights gebaseerde statistieken te verzamelen. Zie [Uw API-sleutel en toepassings-id opvragen](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)voor meer informatie.

        > [!NOTE]
        > Sommige gegevensbronvelden krijgen een andere naam dan de gecorreleerde Azure-instellingen:
        > * Tenant-id is de Azure Directory-id
        > * Client-id is de Azure Active Directory Application ID
        > * Clientgeheim is de waarde van de Azure Active Directory Application-sleutel

5. Als u Application Insights gebruikt, u ook uw Application Insights API en application ID opnemen om op Application Insights gebaseerde statistieken te verzamelen. Zie [Uw API-sleutel en toepassings-id opvragen](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)voor meer informatie.

6. Selecteer **Opslaan**en Grafana test de referenties voor elke API. U ziet een bericht dat vergelijkbaar is met het volgende bericht.  
    ![Grafana-gegevensbronconfig goedgekeurd](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Een Grafana-dashboard bouwen

1. Ga naar de startpagina van Grafana en selecteer **Nieuw dashboard**.

2. Selecteer in het nieuwe dashboard de **grafiek**. U andere grafiekopties proberen, maar in dit artikel wordt *Grafiek* als voorbeeld gebruikt.

3. Er verschijnt een lege grafiek op uw dashboard. Klik op de deelvenstertitel en selecteer **Bewerken** om de details in te voeren van de gegevens die u wilt plotten in deze grafiek.
    ![Grafana nieuwe grafiek](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Selecteer de Azure Monitor-gegevensbron die u hebt geconfigureerd.
   * Azure Monitor-statistieken verzamelen : selecteer **Azure Monitor** in de vervolgkeuzelijst van de service. Er verschijnt een lijst met selectors, waarin u de resources en metrische gegevens selecteren die u in deze grafiek wilt controleren. Als u statistieken van een virtuele machine wilt verzamelen, gebruikt u de naamruimte **Microsoft.Compute/VirtualMachines**. Zodra u VM's en statistieken hebt geselecteerd, u beginnen met het weergeven van hun gegevens in het dashboard.
     ![Grafana-grafiekconfig voor Azure-monitor](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * Azure Monitor-logboekgegevens verzamelen - selecteer **Azure Log Analytics** in de vervolgkeuzelijst van de service. Selecteer de werkruimte die u wilt opvragen en stel de querytekst in. U hier elke logquery kopiëren die u al hebt of een nieuwe maken. Terwijl u uw query intypt, wordt IntelliSense weergegeven en worden opties voor automatisch aanvullen voorgesteld. Selecteer het visualisatietype, **De tabel met de reeksen tijd** **Table**en voer de query uit.
    
     > [!NOTE]
     >
     > De standaardquery bij de plug-in maakt gebruik van twee macro's: "$__timeFilter() en $__interval. 
     > Met deze macro's kan Grafana dynamisch het tijdsbereik en de tijdskorrel berekenen wanneer u inzoomt op een deel van een grafiek. U deze macro's verwijderen en een standaardtijdfilter gebruiken, zoals *TimeGenerated > geleden(1h),* maar dat betekent dat de grafiek de zoomfunctie niet ondersteunt.
    
     ![Grafana-grafiekconfig voor Azure Log Analytics](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. Hieronder volgt een eenvoudig dashboard met twee grafieken. De ene aan de linkerkant toont het CPU-percentage van twee VM's. De grafiek aan de rechterkant toont de transacties in een Azure Storage-account, onderverdeeld naar het type Transaction API.
    ![Voorbeeld van grafana twee grafieken](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Optioneel: uw aangepaste statistieken controleren op dezelfde Grafana-server

U Telegraf en InfluxDB ook installeren om zowel aangepaste als op agenten gebaseerde statistieken te verzamelen en uit te zetten, dezelfde Grafana-instantie. Er zijn veel plug-ins voor gegevensbronnen die u gebruiken om deze statistieken samen te brengen in een dashboard.

U deze set ook opnieuw gebruiken om statistieken van uw Prometheus-server op te nemen. Gebruik de Prometheus data source plugin in Grafana's plugin galerij.

Hier zijn goede referentie artikelen over hoe telegraf, InfluxDB, Prometheus en Docker te gebruiken
 - [System Metrics monitoren met de TICK Stack op Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Een bewakingsoplossing voor Docker-hosts, containers en containerservices](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Hier volgt een afbeelding van een volledig Grafana-dashboard met statistieken van Azure Monitor en Application Insights.
![Grafana-voorbeeldstatistieken](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Geavanceerde Grafana-functies

### <a name="variables"></a>Variabelen
Sommige querywaarden kunnen worden geselecteerd via ui-vervolgkeuzewaarden en worden bijgewerkt in de query. Beschouw de volgende query als een voorbeeld:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

U een variabele configureren die alle beschikbare **oplossingswaarden** weergeeft en vervolgens uw query bijwerken om deze te gebruiken.
Als u een nieuwe variabele wilt maken, klikt u op de knop Instellingen van het dashboard in het rechterbovengebied, selecteert u **Variabelen**en vervolgens **Nieuw**.
Definieer op de variabele pagina de gegevensbron en query die moet worden uitgevoerd om de lijst met waarden te krijgen.
![Grafana configureren variabele](./media/grafana-plugin/grafana-configure-variable-dark.png)

Pas na het maken de query aan om de geselecteerde waarde(en) te gebruiken en in uw grafieken wordt dienovereenkomstig gereageerd:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Grafana gebruik variabelen](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Dashboardafspeellijsten maken

Een van de vele handige functies van Grafana is de dashboard afspeellijst. U meerdere dashboards maken en deze toevoegen aan een afspeellijst die een interval configureert voor elk dashboard dat kan worden weergegeven. Selecteer **Afspelen** om de dashboards te bekijken. U ze weergeven op een grote muurmonitor om een statusbord voor uw groep te bieden.

![Voorbeeld van Grafana-afspeellijst](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Grafana-omgeving op Azure hebt ingesteld, worden er kosten in rekening gebracht wanneer VM's worden uitgevoerd, ongeacht of u ze gebruikt of niet. Als u wilt voorkomen dat er extra kosten in rekening worden gebracht, ruimt u de resourcegroep op die in dit artikel is gemaakt.

1. Klik in het linkermenu in de Azure-portal op **Resourcegroepen** en klik vervolgens op **Grafana**.
2. Klik op de pagina Resourcegroep op **Verwijderen,** typ **Grafana** in het tekstvak en klik vervolgens op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen
* [Overzicht van Azure Monitor Metrics](data-platform.md)

