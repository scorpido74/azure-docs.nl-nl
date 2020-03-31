---
title: Gegevens van Azure Data Explorer visualiseren met Kibana
description: In dit artikel leert u hoe u Azure Data Explorer instelt als gegevensbron voor Kibana
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fac9c78607e50dca384670bf4cc08b50f723312b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065613"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>Gegevens van Azure Data Explorer in Kibana visualiseren met de K2Bridge opensourceconnector

Met K2Bridge (Kibana-Kusto Bridge) u Azure Data Explorer als gegevensbron gebruiken en die gegevens in Kibana visualiseren. K2Bridge is een [open-source](https://github.com/microsoft/K2Bridge) containerized toepassing die fungeert als een proxy tussen een Kibana-instantie en een Azure Data Explorer-cluster. In dit artikel wordt beschreven hoe u K2Bridge gebruiken om die verbinding te maken.

K2Bridge vertaalt Kibana-query's naar Kusto Query Language (KQL) en stuurt de resultaten van Azure Data Explorer terug naar Kibana. 

   ![grafiek](media/k2bridge/k2bridge-chart.png)

K2Bridge ondersteunt het tabblad Discover van Kibana, waar u:
* De gegevens doorzoeken en verkennen
* Resultaten filteren
* Velden in het resultatenraster toevoegen of verwijderen
* Recordinhoud weergeven
* Zoekopdrachten opslaan en delen

De afbeelding hieronder toont een Kibana-instantie die is gekoppeld aan Azure Data Explorer door K2Bridge. De gebruikerservaring in Kibana is ongewijzigd.

   [![Kibana-pagina](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Vereisten

Voordat u gegevens uit Azure Data Explorer in Kibana visualiseren, moet u het volgende klaarhebben:

* [Helm V3](https://github.com/helm/helm#install), de Kubernetes package manager
* Azure Kubernetes Service (AKS) cluster, of een ander Kubernetes-cluster (versie 1.14 naar versie 1.16 zijn getest en geverifieerd). Als u een AKS-cluster nodig hebt, raadpleegt u Een AKS-cluster implementeren met de Azure CLI of de [Azure-portal](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) [gebruiken](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
* Een [Azure Data Explorer-cluster](create-cluster-database-portal.md), inclusief:
    * URL van het Azure Data Explorer-cluster 
    * De databasenaam
    
* Een Azure AD-serviceprincipal die is gemachtigd om gegevens weer te geven in Azure Data Explorer, waaronder:
    * De client-id 
    * Het geheim van de klant

    Een serviceprincipal met 'Viewer'-toestemming wordt aanbevolen. Het wordt afgeraden om hogere machtigingen te gebruiken.

    * [Stel de weergavemachtigingen van het cluster in voor de azure AD-serviceprincipal](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

    Zie [Een Azure AD-serviceprincipal maken](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)voor meer informatie over de azure AD-serviceprincipal .

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>K2Bridge uitvoeren op Azure Kubernetes-service (AKS)

Standaard verwijst de Helm-grafiek van K2Bridges naar een openbaar beschikbare afbeelding in het Container Registry (MCR) van Microsoft. MCR heeft geen referenties nodig en werkt out-of-the-box.

1. Download de vereiste Helmgrafieken.

1. Voeg de afhankelijkheid van Elasticsearch toe aan Helm. 
    De reden voor de elasticsearch-afhankelijkheid is dat K2Bridge een interne kleine Elasticsearch-instantie gebruikt om metagegevensgerelateerde aanvragen te bedienen (zoals indexpatronen en opgeslagen query's). Er worden geen bedrijfsgegevens opgeslagen in dit interne exemplaar en kunnen worden beschouwd als een implementatiedetail. 

    1. Ga als u de afhankelijkheid van Elasticsearch toevoegt aan Helm:

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. Ga als beste uit de K2Bridge-grafiek onder de grafiekenmap van de GitHub-repository:
        1. Kloon de repository van [GitHub.](https://github.com/microsoft/K2Bridge)
        1. Ga naar de K2Bridges root repository directory.
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

    1. (Optioneel) Azure Application Insights-telemetrie inschakelen. 
        Als dit de eerste keer is dat u Azure Application Insights gebruikt, moet u eerst [een Application Insights-bron maken.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) U moet [de instrumentatiesleutel naar](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) een variabele kopiëren: 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>Installeer de K2Bridge-grafiek:

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        In [Configuration](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md) vindt u de volledige set configuratieopties.

    1. De opdrachtuitvoer stelt de volgende opdracht Helm voor om Kibana te implementeren. Optioneel uitvoeren:

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
        
    1. Gebruik port forwarding om toegang te krijgen tot Kibana op localhost: 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
        
    1. Maak verbinding met Kibana door te bladeren naar http://127.0.0.1:5601.

    1. Stel Kibana bloot aan de eindgebruikers. Er zijn meerdere methoden om dit te doen. De methode die u gebruikt, is grotendeels afhankelijk van uw use case.

        Bijvoorbeeld:

        Stel de service bloot als een LoadBalancer-service. Voeg hiervoor de `--set service.type=LoadBalancer` parameter toe aan de installatieopdracht K2Bridge Helm[(hierboven).](#install-k2bridge-chart)        
    
        Voer vervolgens
        
        ```bash
        kubectl get service -w -n k2bridge
        ```
        
        De uitvoer moet eruit zien als: 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
 
        U vervolgens het gegenereerde EXTERNE IP gebruiken dat wordt weergegeven en `<EXTERNAL-IP>:5601`deze gebruiken om toegang te krijgen tot Kibana door een browser te openen voor.

1. Configureer indexpatronen om toegang te krijgen tot uw gegevens:  
In een nieuwe Kibana instantie:
     1. Open Kibana.
     1. Navigeer naar Beheer.
     1. Selecteer **Indexpatronen**. 
     1. Maak een indexpatroon.
De naam van de index moet exact overeenkomen met de tabelnaam of functienaam, zonder sterretje. U de relevante regel uit de lijst kopiëren.

> [!Note]
> Als u op andere Kubernetes-providers wilt draaien, wijzigt u de Elasticsearch-storageClassName in `values.yaml` de door de provider voorgestelde opslag.

## <a name="visualize-data"></a>Gegevens visualiseren

Wanneer Azure Data Explorer is geconfigureerd als gegevensbron voor Kibana, u Kibana gebruiken om de gegevens te verkennen. 

1. Selecteer in Kibana in het linkermenu het tabblad **Ontdekken.**

1. Selecteer in de linkervervolgkeuzelijst een indexpatroon (in dit geval een tabel azure data explorer) waarin de gegevensbron wordt gedefinieerd die u wilt verkennen.
    
   ![Een indexpatroon selecteren](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. Als uw gegevens een tijdfilterveld hebben, u het tijdsbereik opgeven. Rechtsboven op de pagina een tijdfilter instellen. Discover toont standaard gegevens van de laatste 15 minuten.

   ![Tijdfilter](media/k2bridge/k2bridge-time-filter.png)
    
1. De resultatentabel toont de eerste 500 records. U een document uitbreiden om de veldgegevens te onderzoeken in JSON- of tabelindelingen.

   ![Een record uitbreiden](media/k2bridge/k2bridge-expand-record.png)

1. Standaard bevat de resultatentabel kolommen voor het document _source en het tijdveld (als het bestaat). U specifieke kolommen kiezen die aan de resultatentabel moeten worden toegevoegd door **toevoegen** te selecteren naast de veldnaam in de linkerzijbalk.

   ![Specifieke kolommen](media/k2bridge/k2bridge-specific-columns.png)
    
1. In de querybalk u de gegevens doorzoeken op:
    * Een zoekterm invoeren
    * Met behulp van de syntaxis van de Lucene-query. 
    Bijvoorbeeld:
        * Zoek op 'fout' om alle records te vinden die deze waarde bevatten. 
        * Zoek naar "status: 200", om alle records met een statuswaarde van 200 te krijgen. 
    * Logische operatoren gebruiken (EN, OF, NIET)
    * Jokertekens gebruiken (sterretje \* " of vraagteken "?") Bijvoorbeeld:
        * De `"destination_city: L*"` query komt overeen met records waarin de waarde van de doelstad begint met 'l' (K2Bridge is niet hoofdlettergevoelig).

    ![Query uitvoeren](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > In [Zoeken](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)vindt u meer zoekregels en logica.

1. Als u uw zoekresultaten wilt filteren, gebruikt u de **lijst met velden** op de rechterzijbalk van de pagina. 
    In de veldenlijst u het overzicht zien:
    * De top vijf waarden voor het veld
    * Het aantal records dat het veld bevat
    * Het percentage records dat elke waarde bevat. 
    
    >[!Tip]
    > Gebruik het pictogram (+) vergrootglas om alle records met een specifieke waarde te zoeken.
    
    ![Lijst met velden](media/k2bridge/k2bridge-field-list.png)
   
    U de resultaten ook filteren met het pictogram (+) vergrootglas in de weergave met de indeling van de resultatentabel van elke record in de resultatentabel.
    
     ![Tabellijst](media/k2bridge/k2bridge-table-list.png)
    
1. Selecteer uw zoekopdracht **opslaan** of **delen.**

     ![Zoeken opslaan](media/k2bridge/k2bridge-save-search.png)
