---
title: KEUR Canarische release met een versie van het Azure DC/OS-cluster
description: Met behulp van de versie van het gebruik van de installatie van definitieve pakketten in een Azure Container Service DC/OS-cluster
author: gggina
ms.service: container-service
ms.topic: conceptual
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: dfdf6e1f8edfb4dafaf93e62090ed51878f9b2aa
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734821"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>KEUR Micro services van de Canarische release met een versie van een Azure Container Service DC/OS-cluster

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

In dit scenario hebben we met een DC/OS-cluster een op Azure Container Service ingestelde versie van het besturings systeem. Onze Canarische versie van de Sava-demo service is uitgebracht en vervolgens wordt de incompatibiliteit van de service met Firefox opgelost door slimme verkeer filteren toe te passen. 

> [!TIP] 
> In dit scenario is de installatie van een DC/OS-cluster voltooid, maar u kunt ook met Kubernetes als Orchestrator gebruiken.
>

## <a name="about-canary-releases-and-vamp"></a>Over Canarische releases en de/vernieuwd


Het [vrijgeven](https://martinfowler.com/bliki/CanaryRelease.html) van de distributie is een slimme implementatie strategie die is aangenomen door innovatieve organisaties zoals Netflix, Facebook en Spotify. Het is een aanpak die zinvol is, omdat deze problemen vermindert, veiligheids netten introduceert en innovatie verhoogt. Waarom worden niet alle bedrijven gebruikt? Door een CI/CD-pijp lijn uit te breiden, kunt u ook gebruikmaken van de complexiteits strategie en zijn er uitgebreide devops kennis en ervaring vereist. Dat is voldoende om kleinere bedrijven en ondernemingen te blok keren voordat ze zelfs aan de slag gaan. 

[Er is een](https://vamp.io/) open-source systeem ontworpen om deze overgang te vereenvoudigen en de functies van de Canarische-release uit te voeren voor uw voorkeurs container planner. De Canarische functionaliteit van de vernieuwd gaat verder dan op percentage gebaseerde implementaties. Verkeer kan worden gefilterd en gesplitst in een breed scala aan voor waarden, bijvoorbeeld om specifieke gebruikers, IP-bereiken of apparaten te bereiken. Er wordt getraceerd en geanalyseerd de metrische gegevens over prestaties, waardoor automatisering mogelijk is op basis van de werkelijke informatie. U kunt automatisch terugdraaien op fouten instellen, of afzonderlijke service varianten schalen op basis van de belasting of latentie.

## <a name="set-up-azure-container-service-with-dcos"></a>Azure Container Service instellen met DC/OS



1. [Implementeer een DC/OS-cluster](container-service-deployment.md) met één master en twee agents van de standaard grootte. 

2. [Maak een SSH-tunnel](../container-service-connect.md) om verbinding te maken met het DC/OS-cluster. In dit artikel wordt ervan uitgegaan dat u tunnelt naar het cluster op lokale poort 80.


## <a name="set-up-vamp"></a>Instellen

Nu u een DC/OS-cluster hebt, kunt u deze installeren via de DC/OS-gebruikers interface (http\/:/localhost: 80). 

![DC/OS-webgebruikersinterface](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

De installatie wordt uitgevoerd in twee fasen:

1. **Elasticsearch implementeren**.

2. **Implementeer** vervolgens vervolgens de versie van de versie van het pakket voor de installatie van de versie van het besturings systeem.

### <a name="deploy-elasticsearch"></a>Elasticsearch implementeren

Voor Elasticsearch is de verzameling en aggregatie van metrische gegevens vereist. U kunt de [Magneticio docker-installatie kopieën](https://hub.docker.com/r/magneticio/elastic/) gebruiken voor het implementeren van een compatibele/Elasticsearch-stack.

1. Ga in de DC/OS-gebruikers interface naar **Services** en klik op **service implementeren**.

2. Selecteer **JSON-modus** in het pop-upvenster **nieuwe service implementeren** .

   ![JSON-modus selecteren](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Plak de volgende JSON. Deze configuratie voert de container uit met 1 GB RAM en een basis status controle op de Elasticsearch-poort.
  
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

   DC/OS implementeert de Elasticsearch-container. U kunt de voortgang volgen op de pagina **Services** .  

   ![e implementeren? Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Met de installatie van.

Zodra de Elasticsearch-rapporten zijn **uitgevoerd**, kunt u het pakket voor de versie van het besturings systeem voor de/uit. 

1. Ga naar **universum** en zoek naar/of **vernieuwd**. 
   ![Voor het universum van DC/OS](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Klik op **installeren** naast het pakket bgeavanceerde en kies **Geavanceerde installatie**.

3. Schuif omlaag en voer de volgende elasticsearch-URL in `http://elasticsearch.marathon.mesos:9200`:. 

   ![Elasticsearch-URL invoeren](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Klik op **controleren en installeren**en klik vervolgens op **installeren** om de implementatie te starten.  

   DC/OS implementeert alle vereiste/vernieuwd-onderdelen. U kunt de voortgang volgen op de pagina **Services** .
  
   ![Een versie van het universum-pakket implementeren](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Zodra de implementatie is voltooid, hebt u toegang tot de gebruikers interface van vernieuwd:

   ![De versie van de micro service op DC/OS](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
   ![Gebruikers interface van vernieuwd](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Uw eerste service implementeren

Nu u hebt gemaakt, kunt u een service van een blauw druk implementeren. 

In de meest eenvoudige vorm van een standaard [blauw druk](https://docs.vamp.io/how-vamp-works/vamp-and-kubernetes#vamp-deployments) worden de eind punten (gateways), clusters en services beschreven die moeten worden geïmplementeerd. Er worden clusters gebruikt om verschillende varianten van dezelfde service te groeperen in logische groepen voor het vrijgeven van een groot aantal of A/B-tests.  

In dit scenario wordt gebruikgemaakt van een voor beeld van een monolithische-toepassing met de naam [**Sava**](https://github.com/magneticio/sava-product), die op versie 1,0 is. De op is verpakt in een docker-container, die zich in docker hub onder magneticio/Sava: 1.0.0 bevindt. De app wordt normaal gesp roken uitgevoerd op poort 8080, maar u wilt deze beschikbaar maken onder poort 9050 in dit geval. Implementeer de app met behulp van een eenvoudige blauw druk.

1. Ga naar **implementaties**.

2. Klik op **Toevoegen**.

3. Plak de volgende blauw druk YAML. Deze blauw druk bevat één cluster met slechts één service variant, die in een latere stap wordt gewijzigd:

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

4. Klik op **Opslaan**. Met de installatie wordt de implementatie gestart.

De implementatie wordt weer gegeven op de pagina **implementaties** . Klik op de implementatie om de status ervan te controleren.

![Gebruikers interface voor het implementeren van Sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![Sava-service in de gebruikers interface van vernieuwd](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Er worden twee gateways gemaakt die worden vermeld op de pagina **gateways** :

* een stabiel eind punt voor toegang tot de actieve service (poort 9050) 
* een met een webbeheerde interne gateway (meer op deze gateway). 

![Sava-gateways voor de gebruikers interface](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

De Sava-service is nu geïmplementeerd, maar u kunt deze niet extern openen omdat de Azure Load Balancer nog niet weet dat er verkeer naar wordt doorgestuurd. Werk de Azure-netwerk configuratie bij om toegang te krijgen tot de service.


## <a name="update-the-azure-network-configuration"></a>De Azure-netwerk configuratie bijwerken

Met de Sava-service op de knoop punten van de DC/OS-agent is een stabiel eind punt beschikbaar op poort 9050. Om toegang te krijgen tot de service van buiten het DC/OS-cluster, moet u de volgende wijzigingen aanbrengen in de configuratie van het Azure-netwerk in uw cluster implementatie: 

1. **Configureer de Azure Load Balancer** voor de agents (de resource met de naam **DCOS-agent-lb-xxxx**) met een status test en een regel voor het door sturen van verkeer op poort 9050 naar de Sava-exemplaren. 

2. **Werk de netwerk beveiligings groep** voor de open bare agents (de resource met de naam **xxxx-agent-Public-NSG-xxxx**) bij om verkeer op poort 9050 toe te staan.

Zie [open bare toegang tot een Azure container service toepassing inschakelen](container-service-enable-public-access.md)voor gedetailleerde stappen voor het uitvoeren van deze taken met behulp van de Azure Portal. Geef poort 9050 voor alle poort instellingen op.


Als alles eenmaal is gemaakt, gaat u naar de Blade **overzicht** van de DC/OS-agent Load Balancer (de resource met de naam **DCOS-agent-lb-xxxx**). Zoek het **open bare IP-adres**en gebruik het adres voor toegang tot Sava op poort 9050.

![Azure Portal-openbaar IP-adres ophalen](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Een Canarische versie uitvoeren

Stel dat u een nieuwe versie van deze toepassing hebt die u wilt vrijgeven voor de productie. U hebt deze container als magneticio/Sava: 1.1.0 en bent klaar om te gaan. Met kunt u eenvoudig nieuwe services toevoegen aan de actieve implementatie. Deze samengevoegde services worden geïmplementeerd naast de bestaande services in het cluster en krijgen een gewicht van 0% toegewezen. Er wordt geen verkeer doorgestuurd naar een nieuw samengevoegde service, totdat u de distributie van verkeer hebt aangepast. Met de schuif regelaar voor het gewicht in de gebruikers interface van vernieuwd kunt u volledige controle over de distributie, waardoor incrementele aanpassingen (Canarische release) of een onmiddellijke terugdraai actie worden toegestaan.

### <a name="merge-a-new-service-variant"></a>Een nieuwe service variant samen voegen

De nieuwe Sava 1,1-service samen voegen met de actieve implementatie:

1. Klik in de gebruikers interface van vernieuwd op **blauw drukken**.

2. Klik op **toevoegen** en plakken in de volgende blauw druk YAML: in deze blauw druk wordt een nieuwe service variant (Sava: 1.1.0) beschreven die in het bestaande cluster (sava_cluster) moet worden geïmplementeerd.

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
  
3. Klik op **Opslaan**. De blauw druk wordt opgeslagen en weer gegeven op de pagina **blauw drukken** .

4. Open het menu Actie op de blauw druk Sava: 1.1 en klik op **samen voegen naar**.

   ![De gebruikers interface-blauw drukken](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Selecteer de **Sava** -implementatie en klik op **samen voegen**.

   ![De gebruikers interface-blauw druk voor samen voegen in implementatie](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Met de nieuwe Sava: 1.1.0 Service variant die wordt beschreven in de blauw druk naast Sava: 1.0.0 in de **sava_cluster** van de actieve implementatie. 

![Gebruikers interface-bijgewerkte Sava-implementatie](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

De **Sava/sava_cluster/webport** -gateway (het cluster eindpunt) is ook bijgewerkt, waarbij een route wordt toegevoegd aan de zojuist geïmplementeerde Sava: 1.1.0. Op dit moment wordt er hier geen verkeer gerouteerd (het **gewicht** wordt ingesteld op 0%).

![De gebruikers interface-cluster gateway](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Release van de Canarische

Met beide versies van Sava die zijn geïmplementeerd in hetzelfde cluster, past u de distributie van verkeer ertussen aan door de schuif regelaar voor het **gewicht** te verplaatsen.

1. Klik ![op de nieuwe gebruikers](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) Interface: bewerken naast **gewicht**.

2. Stel de wegings verdeling in op 50%/50% en klik op **Opslaan**.

   ![Webgebruikersinterface-schuif regelaar voor gateway gewicht](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Ga terug naar uw browser en vernieuw de pagina Sava nog enkele keren. De Sava-toepassing schakelt nu tussen een Sava: 1.0-pagina en een Sava: 1.1-pagina.

   ![afwisselend Sava 1.0 en Sava 1.1-services](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Deze afwisseling van de pagina werkt het beste met de modus "incognito" of "Anonymous" van uw browser vanwege het in de cache opslaan van statische activa.
  >

### <a name="filter-traffic"></a>Verkeer filteren

Stel dat u na de implementatie een incompatibiliteit hebt gedetecteerd in Sava: 1.1.0 dat weergave problemen in Firefox-browsers veroorzaakt. U kunt voor het filteren van inkomend verkeer instellen en alle Firefox-gebruikers terugsturen naar de bekende stabiele Sava: 1.0.0. Met dit filter wordt de onderbreking van Firefox-gebruikers onmiddellijk opgelost, terwijl iedereen anders de voor delen van de verbeterde Sava: 1.1.0 blijft benutten.

Er worden **voor waarden** gebruikt voor het filteren van verkeer tussen routes in een gateway. Verkeer wordt eerst gefilterd en doorgestuurd volgens de voor waarden die op elke route worden toegepast. Alle resterende verkeer wordt gedistribueerd op basis van de instelling voor het gewicht van de gateway.

U kunt een voor waarde maken om alle Firefox-gebruikers te filteren en deze te omleiden naar de oude Sava: 1.0.0:

1. Klik op de pagina Sava/sava_cluster/webport- **gateways** op ![de Blade](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) -UI-bewerken om een **voor waarde** toe te voegen aan de route Sava/sava_cluster/Sava: 1.0.0/webport. 

2. Voer de voor waarde **User-agent = = Firefox** in ![en klik op de](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png)gebruiker van de gebruikers interface-opslaan.

   De voor waarde wordt toegevoegd met een standaard sterkte van 0%. Als u het filteren van verkeer wilt starten, moet u de voorwaarde sterkte aanpassen.

3. Klik ![op de gebruikers interface](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) van vernieuwd-bewerken om de **sterkte** van de voor waarde te wijzigen.
 
4. Stel de **sterkte** in op 100% en ![Klik op de functie](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) van de gebruikers interface-opslaan om op te slaan.

   Vervolgens wordt alle verkeer dat overeenkomt met de voor waarde (alle Firefox-gebruikers), verzonden naar Sava: 1.0.0.

   ![Gebruikers Interface: voor waarde Toep assen op Gateway](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Wijzig ten slotte het gewicht van de gateway om alle resterende verkeer (alle niet-Firefox-gebruikers) te verzenden naar de nieuwe Sava: 1.1.0. Klik ![op de nieuwe gebruikers](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) interface-bewerken naast **gewicht** en stel de wegings verdeling in op 100% wordt omgeleid naar de route Sava/sava_cluster/Sava: 1.1.0/webport.

   Al het verkeer dat niet door de voor waarde is gefilterd, wordt nu doorgestuurd naar de nieuwe Sava: 1.1.0.

6. Als u het filter in actie wilt zien, opent u twee verschillende browsers (één Firefox en een andere browser) en opent u de Sava-service van beide. Alle Firefox-aanvragen worden verzonden naar Sava: 1.0.0, terwijl alle andere browsers worden omgeleid naar Sava: 1.1.0.

   ![Gebruikers interface filter voor het filteren van gegevens](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Optellen

Dit artikel is een korte inleiding tot de Snelstartgids op een DC/OS-cluster. Voor starters hebt u de beschikking over uw Azure Container Service DC/OS-cluster, waardoor u een service met een persoonlijke blauw druk hebt geïmplementeerd en deze kunt openen op het blootgestelde eind punt (gateway).

We hebben ook een aantal krachtige functies van het bestand vernieuwd: het samen voegen van een nieuwe service variant voor de implementatie en het incrementeel introduceren van het verkeer om een bekende incompatibiliteit op te lossen.


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het beheren van de handelingen van de/ [vernieuwd rest API](https://docs.vamp.io/how-vamp-works/events-and-metrics#events).

* Ontwikkel documenten voor het maken van een geautomatiseerd Automation-script in [node. js](https://docs.vamp.io/how-vamp-works/concepts-and-components#workflows)en voer deze uit als de.

* Zie extra nieuwe en [zelf studies](https://docs.vamp.io/tutorials/).

