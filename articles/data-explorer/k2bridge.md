---
title: Gegevens visualiseren vanuit Azure Data Explorer met behulp van Kibana
description: In dit artikel leert u hoe u Azure Data Explorer kunt instellen als gegevens bron voor Kibana
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 30d74f36c6462d1fba039595d2ed6fe722b742e8
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79164811"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>Gegevens visualiseren vanuit Azure Data Explorer in Kibana met de K2Bridge-open-source-connector

Met K2Bridge (Kibana-Kusto Bridge) kunt u Azure Data Explorer als gegevens bron gebruiken om die gegevens te visualiseren in Kibana. K2Bridge is een [open-source](https://github.com/microsoft/K2Bridge) container toepassing die fungeert als een proxy tussen een Kibana-exemplaar en een Azure Data Explorer-cluster. In dit artikel wordt beschreven hoe u K2Bridge kunt gebruiken om die verbinding te maken.

K2Bridge zet Kibana-query's om naar Kusto query language (KQL) en stuurt de Data Explorer resultaten van Azure terug naar Kibana. 

   ![grafiek](media/k2bridge/k2bridge-chart.png)

K2Bridge ondersteunt het tabblad Discover van Kibana, waar u het volgende kunt doen:
* De gegevens zoeken en verkennen
* Resultaten filteren
* Velden toevoegen aan of verwijderen uit het resultaten raster
* Record inhoud weer geven
* Zoek opdrachten opslaan en delen

In de onderstaande afbeelding ziet u een Kibana-exemplaar dat is gebonden aan Azure Data Explorer door K2Bridge. De gebruikers ervaring in Kibana is ongewijzigd.

   [![pagina Kibana](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Vereisten

Voordat u gegevens kunt visualiseren vanuit Azure Data Explorer in Kibana, moet u de volgende voor bereidingen hebben:

* [Helm v3](https://github.com/helm/helm#install), het Kubernetes-pakket beheer
* Het cluster Azure Kubernetes service (AKS) of een ander Kubernetes-cluster (versie 1,14 naar versie 1,16 zijn getest en gecontroleerd). Als u een AKS-cluster nodig hebt, raadpleegt u een AKS-cluster implementeren [met behulp van de Azure cli](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) of [met behulp van de Azure Portal](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
* Een [Azure Data Explorer-cluster](create-cluster-database-portal.md), met inbegrip van:
    * De URL van het Azure Data Explorer-cluster 
    * De database naam
    
* Een Azure AD-service-principal die is gemachtigd voor het weer geven van gegevens in azure Data Explorer, waaronder:
    * De client-ID 
    * Het client geheim

    Een service-principal met de machtiging ' Viewer ' wordt aanbevolen. Het wordt afgeraden om hogere machtigingen te gebruiken.

    * [Stel de machtigingen voor de weer gave van het cluster in voor de Azure AD-Service-Principal](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

    Zie [een Azure AD-service-principal maken](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)voor meer informatie over de Azure AD-Service-Principal.

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>K2Bridge uitvoeren op Azure Kubernetes service (AKS)

K2Bridges's helm-grafiek verwijst standaard naar een openbaar beschik bare afbeelding die zich bevindt op de Container Registry van micro soft (MCR). MCR vereist geen referenties en werkt out-of-the-box.

1. Down load de vereiste helm-grafieken.

1. Voeg de Elasticsearch-afhankelijkheid toe aan helm. 
    De Elasticsearch-afhankelijkheid is dat K2Bridge een interne kleine Elasticsearch-instantie gebruikt om aanvragen te verwerken die betrekking hebben op meta gegevens (zoals index patronen en opgeslagen query's). Er worden geen bedrijfs gegevens opgeslagen in dit interne exemplaar en het kan worden beschouwd als implementatie details. 

    1. De Elasticsearch-afhankelijkheid toevoegen aan helm:

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. Als u de K2Bridge-grafiek wilt ophalen uit de map Charts van de GitHub-opslag plaats:
        1. Kloon de opslag plaats vanuit [github](https://github.com/microsoft/K2Bridge).
        1. Ga naar de K2Bridges root repository Directory.
        1. Uitvoeren:

            ```bash
            helm dependency update charts/k2bridge
            ```

1. K2Bridge implementeren:

    1. Stel de variabelen in met de juiste waarden voor uw omgeving:

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. Beschrijving Schakel Azure-toepassing Insights-telemetrie in. 
        Als dit de eerste keer is dat u Azure-toepassing Insights gebruikt, moet u eerst [een Application Insights resource maken](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource). U moet [de instrumentatie sleutel](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) naar een variabele kopiëren: 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>De K2Bridge-grafiek installeren:

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        In [configuratie](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md) vindt u de volledige set configuratie opties.

    1. Met de opdracht uitvoer wordt de volgende helm-opdracht voorgesteld om uit te voeren voor de implementatie van Kibana. Voer desgewenst de volgende handelingen uit:

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
    1. Gebruik poort door sturen om toegang te krijgen tot Kibana op localhost: 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
    1. Maak verbinding met Kibana door naar http://127.0.0.1:5601te bladeren.

    1. Maak Kibana beschikbaar voor de eind gebruikers. Er zijn meerdere methoden om dit te doen. De methode die u gebruikt, is afhankelijk van uw use-case.

        Bijvoorbeeld:

        De service beschikbaar maken als een Load Balancer-service. U doet dit door de volgende para meter toe te voegen aan de installatie opdracht K2Bridge helm ([hierboven](#install-k2bridge-chart)):

        `--set service.type=LoadBalancer`
    
        Voer vervolgens

           ```bash
           kubectl get service -w -n k2bridge
           ```   
        De uitvoer moet er als volgt uitzien: 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
        U kunt vervolgens het gegenereerde externe IP-adres gebruiken dat wordt weer gegeven en gebruiken voor toegang tot Kibana door een browser te openen voor het volgende: `\<EXTERNAL-IP>:5601`.

1. Configureer index patronen voor toegang tot uw gegevens:  
In een nieuw Kibana-exemplaar:
     1. Open Kibana.
     1. Ga naar beheer.
     1. Selecteer **index patronen**. 
     1. Maak een index patroon.
De naam van de index moet exact overeenkomen met de naam van de tabel of de functie naam, zonder een asterisk. U kunt de relevante regel uit de lijst kopiëren.

> [!Note]
> Als u wilt uitvoeren op andere Kubernetes-providers, wijzigt u de Elasticsearch storageClassName in `values.yaml` zodat deze past bij de provider.

## <a name="visualize-data"></a>Gegevens visualiseren

Wanneer Azure Data Explorer is geconfigureerd als een gegevens bron voor Kibana, kunt u Kibana gebruiken om de gegevens te verkennen. 

1. Selecteer in Kibana in het menu links het tabblad **ontdekken** .

1. Selecteer in de vervolg keuzelijst links een index patroon (in dit geval een Azure Data Explorer-tabel) waarmee de gegevens bron wordt gedefinieerd die u wilt verkennen.
    
   ![Een index patroon selecteren](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. Als uw gegevens een veld met een tijd filter hebben, kunt u het tijds bereik opgeven. Stel in de rechter bovenhoek van de pagina een tijd filter in. Discover toont standaard gegevens gedurende de laatste 15 minuten.

   ![Tijd filter](media/k2bridge/k2bridge-time-filter.png)
    
1. In de resultaten tabel worden de eerste 500 records weer gegeven. U kunt een document uitbreiden om de veld gegevens in JSON-of tabel indelingen te controleren.

   ![Een record uitbreiden](media/k2bridge/k2bridge-expand-record.png)

1. De resultaten tabel bevat standaard kolommen voor het document _source en het veld tijd (indien aanwezig). U kunt opgeven welke kolommen moeten worden toegevoegd aan de resultaten tabel door **toe te voegen** naast de veld naam in de zijbalk links.

   ![Specifieke kolommen](media/k2bridge/k2bridge-specific-columns.png)
    
1. Op de query balk kunt u de gegevens doorzoeken door:
    * Een zoek term invoeren
    * De Lucene-query syntaxis gebruiken. 
    Bijvoorbeeld:
        * Zoek "fout" om alle records te vinden die deze waarde bevatten. 
        * Zoek naar ' status: 200 ', om alle records met de status waarde 200 te verkrijgen. 
    * Logische Opera tors gebruiken (en, of, niet)
    * Joker tekens gebruiken (sterretje "\*" of vraag teken "?") Bijvoorbeeld:
        * De query `"destination_city: L*"` komt overeen met records waarbij de waarde van de doel plaats begint met ' l ' (K2Bridge is niet hoofdletter gevoelig).

    ![Query uitvoeren](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > In [zoeken](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)vindt u meer zoek regels en logica.

1. Gebruik de **lijst met velden** op de rechter zijbalk van de pagina om de zoek resultaten te filteren. 
    De lijst met velden is de locatie waar u het volgende kunt zien:
    * De bovenste vijf waarden voor het veld
    * Het aantal records dat het veld bevat
    * Het percentage records dat elke waarde bevat. 
    
    >[!Tip]
    > Gebruik het pictogram van het vergroot glas (+) om alle records met een specifieke waarde te vinden.
    
    ![Velden lijst](media/k2bridge/k2bridge-field-list.png)
   
    U kunt de resultaten ook filteren met behulp van het pictogram van het vergroot glas (+) in de weer gave resultaat tabel indeling van elke record in de tabel met resultaten.
    
     ![Tabel lijst](media/k2bridge/k2bridge-table-list.png)
    
1. Selecteer ofwel om uw zoek opdracht op te **slaan** of te **delen** .

     ![Zoek opdracht opslaan](media/k2bridge/k2bridge-save-search.png)
