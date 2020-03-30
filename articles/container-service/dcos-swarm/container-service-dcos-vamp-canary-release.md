---
title: (AFGESCHAFT) Canary-release met Vamp op Azure DC/OS-cluster
description: Vamp gebruiken om services voor canary release uit te brengen en slimme verkeersfiltering toe te passen op een Azure Container Service DC/OS-cluster
author: gggina
ms.service: container-service
ms.topic: conceptual
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 2af20a1ddf4239b7eec6cceabf2ff9711959c128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77189116"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>(AFGESCHAFT) Canary release microservices met Vamp op een Azure Container Service DC/OS-cluster

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

In deze walkthrough stellen we Vamp op Azure Container Service in met een DC/OS-cluster. We kunnenarie release van de Vamp demo dienst "sava", en vervolgens een onverenigbaarheid van de dienst met Firefox op te lossen door het toepassen van slimme verkeer filtering. 

> [!TIP] 
> In deze walkthrough draait Vamp op een DC/OS cluster, maar je Vamp met Kubernetes ook gebruiken als orchestrator.
>

## <a name="about-canary-releases-and-vamp"></a>Over canary releases en Vamp


[Canary-release](https://martinfowler.com/bliki/CanaryRelease.html) is een slimme implementatiestrategie die wordt aangenomen door innovatieve organisaties zoals Netflix, Facebook en Spotify. Het is een aanpak die zinvol is, omdat het problemen vermindert, vangnetten introduceert en innovatie verhoogt. Dus waarom gebruiken niet alle bedrijven het? Het uitbreiden van een CI/CD-pijplijn met kanariestrategieën voegt complexiteit toe en vereist uitgebreide kennis en ervaring. Dat is genoeg om zowel kleinere bedrijven als bedrijven te blokkeren voordat ze aan de slag gaan. 

[Vamp](https://vamp.io/) is een open-source systeem ontworpen om deze overgang te vergemakkelijken en kanarie vrijgeven functies te brengen aan uw favoriete container planner. Vamp's kanariefunctionaliteit gaat verder dan op percentage gebaseerde implementaties. Verkeer kan worden gefilterd en gesplitst op een breed scala van voorwaarden, bijvoorbeeld om specifieke gebruikers, IP-bereiken of apparaten te targeten. Vamp houdt prestatiestatistieken bij en analyseert deze, waardoor automatisering mogelijk is op basis van gegevens uit de echte wereld. U automatische terugdraaiing instellen op fouten of afzonderlijke servicevarianten schalen op basis van belasting of latentie.

## <a name="set-up-azure-container-service-with-dcos"></a>Azure Container Service instellen met DC/OS



1. [Implementeer een DC/OS-cluster](container-service-deployment.md) met één master en twee agents met de standaardgrootte. 

2. [Maak een SSH-tunnel](../container-service-connect.md) om verbinding te maken met het DC/OS-cluster. In dit artikel wordt ervan uitgegaan dat u tunnelt naar het cluster op lokale poort 80.


## <a name="set-up-vamp"></a>Vamp instellen

Nu u een actief DC/OS-cluster hebt, u Vamp installeren\/via de DC/OS-gebruikersinterface (http: /localhost:80). 

![DC/OS-webgebruikersinterface](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

De installatie gebeurt in twee fasen:

1. **Elasticsearch implementeren**.

2. Implementeer **Vamp** vervolgens door het Vamp DC/OS-universum-pakket te installeren.

### <a name="deploy-elasticsearch"></a>Elasticsearch implementeren

Vamp vereist Elasticsearch voor het verzamelen en samenvoegen van metrische gegevens. U de [magneticio Docker-afbeeldingen](https://hub.docker.com/r/magneticio/elastic/) gebruiken om een compatibele Vamp Elasticsearch-stack te implementeren.

1. Ga in de gebruikersinterface DC/OS naar **Services** en klik op **Service implementeren**.

2. Selecteer **de JSON-modus** in de pop-up **Nieuwe service implementeren.**

   ![Json-modus selecteren](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Plak in de volgende JSON. Deze configuratie voert de container uit met 1 GB RAM en een basisstatuscontrole op de Elasticsearch-poort.
  
   ```JSON
   {
    "id": "elasticsearch",
    "instances": 1,
    "cpus": 0.2,
    "mem": 1024.0,
    "container": {
      "docker": {
        "image": "magneticio/elastic:2.2",
        "network": "HOST",
        "forcePullImage": true
      }
    },
    "healthChecks": [
      {
        "protocol": "TCP",
        "gracePeriodSeconds": 30,
        "intervalSeconds": 10,
        "timeoutSeconds": 5,
        "port": 9200,
        "maxConsecutiveFailures": 0
      }
    ]
   }
   ```
  

3. Klik op **Implementeren**.

   DC/OS implementeert de Elasticsearch-container. U de voortgang bijhouden op de pagina **Services.**  

   ![implementeren? Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Vamp implementeren

Zodra Elasticsearch rapporteert als **Running,** u het Vamp DC/OS Universe-pakket toevoegen. 

1. Ga naar **Universe** en zoek naar **vamp.** 
   ![Vamp op DC / OS universum](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Klik **op installeren** naast het vamp-pakket en kies Geavanceerde **installatie**.

3. Scroll naar beneden en voer de `http://elasticsearch.marathon.mesos:9200`volgende elasticsearch-url in: . 

   ![De URL van Elasticsearch invoeren](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Klik **op Controleren en installeren**en klik vervolgens op **Installeren** om de implementatie te starten.  

   DC/OS implementeert alle vereiste Vamp-componenten. U de voortgang bijhouden op de pagina **Services.**
  
   ![Vamp implementeren als universum-pakket](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Zodra de implementatie is voltooid, hebt u toegang tot de vamp-gebruikersinterface:

   ![Vamp-service op DC/OS](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
   ![Vamp UI](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Uw eerste service implementeren

Nu Vamp operationeel is, implementeert u een service vanuit een blauwdruk. 

In de eenvoudigste vorm beschrijft een [Vamp-blauwdruk](https://docs.vamp.io/how-vamp-works/vamp-and-kubernetes#vamp-deployments) de eindpunten (gateways), clusters en services die moeten worden geïmplementeerd. Vamp gebruikt clusters om verschillende varianten van dezelfde service te groeperen in logische groepen voor het vrijgeven van kanarieof A/B-tests.  

Dit scenario maakt gebruik van een voorbeeld monolithische toepassing genaamd [**sava**](https://github.com/magneticio/sava), die op versie 1.0. De monoliet is verpakt in een Docker container, die in Docker Hub onder magneticio /sava:1.0.0. De app draait normaal gesproken op poort 8080, maar je wilt hem in dit geval onder poort 9050 blootleggen. Implementeer de app via Vamp met behulp van een eenvoudige blauwdruk.

1. Ga naar **Implementaties.**

2. Klik op**toevoegen**.

3. Plak in de volgende blauwdruk YAML. Deze blauwdruk bevat één cluster met slechts één servicevariant, die we in een latere stap wijzigen:

   ```YAML
   name: sava                        # deployment name
   gateways:
    9050: sava_cluster/webport      # stable endpoint
   clusters:
    sava_cluster:               # cluster to create
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
   ```

4. Klik op **Opslaan**. Vamp initieert de implementatie.

De implementatie wordt weergegeven op de pagina **Implementaties.** Klik op de implementatie om de status ervan te controleren.

![Vamp UI - implementeren van sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![sava-service in Vamp UI](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Er worden twee gateways gemaakt die worden vermeld op de pagina **Gateways:**

* een stabiel eindpunt voor toegang tot de lopende service (poort 9050) 
* een vamp-beheerde interne gateway (later meer over deze gateway). 

![Vamp UI - sava gateways](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

De sava-service is nu geïmplementeerd, maar u hebt geen externe toegang omdat de Azure Load Balancer nog niet weet dat het verkeer naar het verkeer moet worden doorgestuurd. Als u toegang wilt krijgen tot de service, werkt u de Azure-netwerkconfiguratie bij.


## <a name="update-the-azure-network-configuration"></a>De Azure-netwerkconfiguratie bijwerken

Vamp heeft de sava-service geïmplementeerd op de DC/OS-agentknooppunten, waardoor een stabiel eindpunt in poort 9050 wordt blootgesteld. Als u de service van buiten het DC/OS-cluster wilt openen, voert u de volgende wijzigingen aan in de Azure-netwerkconfiguratie in uw clusterimplementatie: 

1. **Configureer de Azure Load Balancer** voor de agents (de bron genaamd **dcos-agent-lb-xxxx)** met een statussonde en een regel om verkeer op poort 9050 door te sturen naar de sava-exemplaren. 

2. **Werk de netwerkbeveiligingsgroep** voor de openbare agenten (de bron genaamd **XXXX-agent-public-nsg-XXXX)** bij om verkeer op poort 9050 toe te staan.

Zie [Openbare toegang tot een Azure Container Service-toepassing inschakelen](container-service-enable-public-access.md)voor gedetailleerde stappen om deze taken uit te voeren met de Azure-portal. Geef poort 9050 op voor alle poortinstellingen.


Zodra alles is gemaakt, ga naar het **overzichtsblad** van de DC / OS agent load balancer (de bron genaamd **dcos-agent-lb-xxxx**). Zoek het **openbare IP-adres**en gebruik het adres om toegang te krijgen tot sava in poort 9050.

![Azure-portal - openbaar IP-adres opvragen](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![Sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Voer een canary release uit

Stel dat u een nieuwe versie van deze toepassing die u wilt canary release in productie. Je hebt het gecontaineriseerd als magneticio / sava: 1.1.0 en zijn klaar om te gaan. Met Vamp u eenvoudig nieuwe services toevoegen aan de lopende implementatie. Deze 'samengevoegde' services worden naast de bestaande services in het cluster geïmplementeerd en krijgen een gewicht van 0%. Er wordt geen verkeer doorgestuurd naar een nieuw samengevoegde service totdat u de verkeersverdeling hebt aangepast. De gewichtsschuifregelaar in de Vamp-gebruikersinterface geeft u volledige controle over de verdeling, waardoor incrementele aanpassingen (canary release) of een onmiddellijke terugdraaiing mogelijk zijn.

### <a name="merge-a-new-service-variant"></a>Een nieuwe servicevariant samenvoegen

Ga als volgende voor een bewerking op de nieuwe sava 1.1-service:

1. Klik in de vamp-gebruikersinterface op **Blauwdrukken**.

2. Klik **op Toevoegen** en plakken in de volgende blauwdruk YAML: in deze blauwdruk wordt een nieuwe servicevariant (sava:1.1.0) beschreven die binnen het bestaande cluster (sava_cluster) moet worden geïmplementeerd.

   ```YAML
   name: sava:1.1.0      # blueprint name
   clusters:
    sava_cluster:       # cluster to update
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
   ```
  
3. Klik op **Opslaan**. De blauwdruk wordt opgeslagen en vermeld op de **blueprints** pagina.

4. Open het actiemenu op de blauwdruk van sava:1.1 en klik op **Samenvoegen tot**.

   ![Vamp UI - blauwdrukken](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Selecteer de **sava-implementatie** en klik op **Samenvoegen**.

   ![Vamp UI - blauwdruk samenvoegen naar implementatie](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp implementeert de nieuwe sava:1.1.0-servicevariant die in de blauwdruk wordt beschreven naast sava:1.0.0 in de **sava_cluster** van de lopende implementatie. 

![Vamp UI - bijgewerkte sava-implementatie](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

De **sava/sava_cluster/webport gateway** (het clustereindpunt) wordt ook bijgewerkt, waardoor een route wordt toegevoegd aan de nieuw geïmplementeerde sava:1.1.0. Op dit moment wordt hier geen verkeer omgeleid (het **GEWICHT** is ingesteld op 0%).

![Vamp UI - clustergateway](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Canarische release

Als beide versies van sava in hetzelfde cluster zijn geïmplementeerd, past u de verdeling van het verkeer tussen deze versies aan door de **schuifregelaar GEWICHT** te verplaatsen.

1. Klik ![op Vamp UI - bewerken](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) naast **GEWICHT**.

2. Stel de gewichtsverdeling in op 50%/50% en klik op **Opslaan**.

   ![Vamp UI - schuifregelaar voor gatewaygewicht](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Ga nog een paar keer terug naar je browser en vernieuw de sava-pagina. De sava-toepassing schakelt nu tussen een sava:1.0-pagina en een sava:1.1-pagina.

   ![afwisselend sava1.0 en sava1.1 diensten](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Deze afwisseling van de pagina werkt het beste met de "Incognito" of "Anonieme" modus van uw browser vanwege het incachen van statische assets.
  >

### <a name="filter-traffic"></a>Filterverkeer

Stel dat u na de implementatie een incompatibiliteit in sava:1.1.0 hebt ontdekt die weergaveproblemen veroorzaakt in Firefox-browsers. U Vamp instellen om binnenkomend verkeer te filteren en alle Firefox-gebruikers terug te leiden naar de bekende stabiele sava:1.0.0. Dit filter lost de verstoring voor Firefox-gebruikers onmiddellijk op, terwijl iedereen blijft genieten van de voordelen van de verbeterde sava:1.1.0.

Vamp gebruikt **voorwaarden** om verkeer tussen routes in een gateway te filteren. Het verkeer wordt eerst gefilterd en geleid volgens de voorwaarden die op elke route worden toegepast. Al het resterende verkeer wordt verdeeld volgens de instelling van het gatewaygewicht.

U een voorwaarde maken om alle Firefox-gebruikers te filteren en deze door te verwijzen naar de oude sava:1.0.0:

1. Klik op de pagina sava/sava_cluster/webport **Gateways** ![op](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) Vamp UI - bewerken om een **voorwaarde** toe te voegen aan de route sava/sava_cluster/sava:1.0.0/webport. 

2. Voer de conditie **user-agent == Firefox** in en klik op ![Vamp UI - opslaan](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

   Vamp voegt de voorwaarde met een standaardsterkte van 0% toe. Als u het verkeer wilt filteren, moet u de conditiesterkte aanpassen.

3. Klik ![op Vamp UI - bewerk](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) om de **sterkte** te wijzigen die op de voorwaarde is toegepast.
 
4. Stel de **STERKTE** in op ![100%](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) en klik op Vamp UI - opslaan om op te slaan.

   Vamp stuurt nu al het verkeer dat overeenkomt met de voorwaarde (alle Firefox-gebruikers) naar sava:1.0.0.

   ![Vamp UI - voorwaarde toepassen op gateway](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Pas ten slotte het gatewaygewicht aan om al het resterende verkeer (alle niet-Firefox-gebruikers) naar de nieuwe sava:1.1.0 te sturen. Klik ![op Vamp UI - bewerk](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) naast **WEIGHT** en stel de gewichtsverdeling in, zodat 100% wordt doorgestuurd naar de route sava/sava_cluster/sava:1.1.0/webport.

   Al het verkeer dat niet door de voorwaarde wordt gefilterd, wordt nu naar de nieuwe sava:1.1.0 geleid.

6. Als u het filter in actie wilt zien, opent u twee verschillende browsers (een Firefox en een andere browser) en opent u de sava-service van beide. Alle Firefox-verzoeken worden verzonden naar sava:1.0.0, terwijl alle andere browsers zijn gericht op sava:1.1.0.

   ![Vamp UI - filterverkeer](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Samenvatting

Dit artikel was een snelle introductie tot Vamp op een DC / OS cluster. Om te beginnen hebt u Vamp up and running op uw Azure Container Service DC/OS-cluster, een service met een Vamp-blauwdruk geïmplementeerd en deze geopend op het blootgestelde eindpunt (gateway).

We hebben ook gewezen op een aantal krachtige functies van Vamp: het samenvoegen van een nieuwe service variant om de lopende implementatie en de invoering van het stapsgewijs, dan filteren verkeer om een bekende onverenigbaarheid op te lossen.


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het beheren van Vamp-acties via de [Vamp REST API](https://docs.vamp.io/how-vamp-works/events-and-metrics#events).

* Bouw vamp-automatiseringsscripts in Node.js en voer ze uit als [Vamp-werkstromen.](https://docs.vamp.io/how-vamp-works/concepts-and-components#workflows)

* Zie extra [VAMP-zelfstudies](https://docs.vamp.io/tutorials/).

