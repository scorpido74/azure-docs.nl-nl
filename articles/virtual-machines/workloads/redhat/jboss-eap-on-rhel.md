---
title: Quickstart -JBoss Enterprise Application (EAP) op Red Hat Enterprise Linux (RHEL) naar Azure VM en virtuele-machineschaalset
description: Zakelijke Java-toepassingen implementeren met behulp van Red Hat JBoss EAP op een Azure RHEL VM en virtuele-machineschaalset.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: d97148393d3158e38f9740d4a8f8e17dd04326d5
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93134994"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Zakelijke Java-toepassingen implementeren in Azure met JBoss EAP op Red Hat Enterprise Linux

In deze quickstart-sjablonen wordt uitgelegd hoe u [JBoss EAP](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) implementeert met [RHEL](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) op Azure Virtual Machines (VM) en virtuele-machineschaalsets. U hebt een voorbeeld van een Java-app in de implementatie om de implementatie te valideren. JBoss EAP is een opensource-toepassingsserverplatform. Het biedt beveiliging, schaalbaarheid en prestaties op bedrijfsniveau voor uw Java-toepassingen. RHEL is een opensource-platform voor het besturingssysteem. Het biedt de mogelijkheid om bestaande apps te schalen en nieuwe technologieën in alle omgevingen uit te rollen. JBoss EAP en RHEL bevatten alles wat u nodig hebt om zakelijke Java-toepassingen te bouwen, uit te voeren, te implementeren en te beheren in elke omgeving. Het is een uitstekende opensource-oplossing voor on-premises, virtuele omgevingen en in privé-, openbare of hybride clouds.

## <a name="prerequisite"></a>Vereiste 

* Een Azure-account met een actief abonnement. Als u een Azure-abonnement wilt ontvangen, activeert u uw [Azure-tegoed voor Visual Studio-abonnees](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) of [maakt u gratis een account](https://azure.microsoft.com/pricing/free-trial).

* JBoss EAP-installatie - U moet een Red Hat-account hebben met rechten voor Red Hat Subscription Management (RHSM) voor JBoss EAP. Met deze rechten kunt u de door Red Hat geteste en gecertificeerde JBoss EAP-versie downloaden.  Als u geen EAP-rechten hebt, moet u een [JBoss EAP-evaluatieabonnement verkrijgen](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) voordat u aan de slag gaat. Als u een nieuw Red Hat-abonnement wilt maken, gaat u naar [Red Hat Customer Portal](https://access.redhat.com/) en stelt u een account in.
F
* [Azure-opdrachtregelinterface](https://docs.microsoft.com/cli/azure/overview).

* Opties voor RHEL - Kies tussen betalen per gebruik (PAYG) of uw eigen abonnement (BYOS). Met BYOS moet u uw [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold-installatiekopie activeren voordat u de quickstart-sjabloon implementeert.

## <a name="java-ee--jakarata-ee-application-migration"></a>Java EE/Jakarata EE-toepassingsmigratie

### <a name="migrate-to-jboss-eap"></a>Migreren naar JBoss EAP
JBoss EAP 7.2 en 7.3 zijn gecertificeerde implementaties van de specificaties voor Java Enterprise Edition (Java EE) 8 en Jakarta EE 8. JBoss EAP biedt vooraf geconfigureerde opties voor functies, zoals clustering met hoge beschikbaarheid (HA), berichten en gedistribueerd opslaan in cache. Ook kunnen gebruikers toepassingen schrijven, implementeren en uitvoeren met behulp van de verschillende API's en services die JBoss EAP biedt.  Ga voor meer informatie over JBoss EAP naar [Kennismaking met JBoss EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) of [Kennismaking met JBoss EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index).

 #### <a name="applications-on-jboss-eap"></a>Toepassingen op JBoss EAP

* Webservices-toepassingen - Webservices bieden een standaard manier om te werken met verschillende softwaretoepassingen. Elke toepassing kan worden uitgevoerd op verschillende platforms en frameworks. Deze webservices vergemakkelijken de communicatie van interne en heterogene subsystemen. Ga voor meer informatie naar [Ontwikkelen van webservices-toepassingen op EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) of [Ontwikkelen van webservices-toepassingen op EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index).

* Toepassingen van Enterprise Java Beans (EJB) - EJB 3.2 is een API voor het ontwikkelen van gedistribueerde, transactionele, veilige en draagbare Java EE- en Jakarta EE-toepassingen. EJB maakt gebruik van onderdelen aan de serverzijde met de naam Enterprise Beans voor het implementeren van de bedrijfslogica van een toepassing op een losgekoppelde manier die hergebruik aanmoedigt. Ga voor meer informatie naar [Ontwikkelen van EJB-toepassingen op EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) of [Ontwikkelen van EJB-toepassingen op EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index).

* Toepassingen voor Hibernate - Ontwikkelaars en beheerders kunnen Java Persistence API (JPA)/Hibernate-toepassingen ontwikkelen en implementeren met JBoss EAP. Hibernate Core is een object-relationeel toewijzingsframework voor de Java-taal. Het biedt een framework voor het toewijzen van een objectgeoriënteerd domeinmodel aan een relationele database, zodat toepassingen directe interactie met de database kunnen vermijden. Hibernate Entity Manager implementeert de programmeerinterfaces en levenscyclusregels die zijn gedefinieerd door de [JPA 2.1-specificatie](https://www.jcp.org/en/jsr/overview). In combinatie met Hibernate Annotations, implementeert deze wrapper een volledige (en zelfstandige) JPA-oplossing bovenop de goed functionerende Hibernate Core. Ga voor meer informatie over Hibernate naar [JPA op EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) of  [Jakarta Persistence op EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api).

#### <a name="red-hat-migration-toolkit-for-applications-mta"></a>Red Hat Migration Toolkit for Applications (MTA)
[Red Hat Migration Toolkit for Applications (MTA)](https://developers.redhat.com/products/mta/overview) is een migratiehulpprogramma voor Java-toepassingsservers. Gebruik dit hulpprogramma om te migreren van een andere app-server naar JBoss EAP. Het werkt met IDE-invoegtoepassingen voor [Eclipse IDE](https://www.eclipse.org/ide/), [Red Hat CodeReady Workspaces](https://developers.redhat.com/products/codeready-workspaces/overview) en [Visual Studio Code (VS code)](https://code.visualstudio.com/docs/languages/java) voor Java.  MTA is een gratis en opensource-hulpprogramma dat:
* Toepassingsanalyse automatiseren
* Schatting van inspanning ondersteunen
* Codemigratie versnellen
* Containerisatie ondersteunen
* Kan worden geïntegreerd met Azure Workload Builder

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>JBoss EAP migreren van on-premises naar Azure
Met de Azure Marketplace-aanbieding van JBoss EAP op RHEL worden Azure-VM's in minder dan 20 minuten geïnstalleerd en ingericht. U heeft toegang tot deze aanbiedingen via [Azure Marketplace](https://azuremarketplace.microsoft.com/)

Deze Marketplace-aanbieding omvat diverse combinaties van EAP- en RHEL-versies ter ondersteuning van uw vereisten. Voor JBoss EAP geldt altijd dat u uw eigen abonnement mee moet brengen (BYOS), maar voor een RHEL-besturingssysteem kunt u kiezen tussen BYOS of betalen per gebruik (PAYG). De Azure Marketplace-aanbieding bevat abonnementsopties voor JBoss EAP op RHEL als zelfstandige of geclusterde VM’s:

* JBoss EAP 7.2 op RHEL 7.7 VM (PAYG)
* JBoss EAP 7.2 op RHEL 8.0 VM (PAYG)
* JBoss EAP 7.3 op RHEL 8.0 VM (PAYG)
* JBoss EAP 7.2 op RHEL 7.7 VM (BYOS)
* JBoss EAP 7.2 op RHEL 8.0 VM (BYOS)
* JBoss EAP 7.3 op RHEL 8.0 VM (BYOS)

Naast Azure Marketplace-aanbiedingen zijn er quickstart-sjablonen beschikbaar zodat u aan de slag kunt gaan met uw Azure-migratietraject. Deze quickstarts bevatten vooraf gemaakte Azure Resource Manager (ARM)-sjablonen en script voor het implementeren van JBoss EAP op RHEL in verschillende configuraties en versiecombinaties. U krijgt het volgende:

* Load Balancer (LB)
* Privé-IP voor taakverdeling en VM's
* Virtual Network (VNET) met één subnet
* VM-configuratie (cluster of zelfstandig)
* Een voorbeeld van een Java-toepassing

De oplossingsarchitectuur voor deze sjablonen omvat:

* JBoss EAP op zelfstandige RHEL VM
* JBoss EAP geclusterd op meerdere RHEL VM’s
* JBoss EAP geclusterd met behulp van Azure virtuele-machineschaalset

#### <a name="linux-workload-migration-for-jboss-eap"></a>Migratie van de Linux-werkbelasting voor JBoss EAP
Azure Workload Builder vereenvoudigt Proof-of-Concept (POC), de evaluatie en het migratieproces voor on-premises Java-apps naar Azure. De Workload Builder integreert met Azure Migrate Discovery Tool om JBoss EAP-servers te identificeren. Vervolgens wordt de Ansible-playbook voor JBoss EAP-serverimplementatie dynamisch gegenereerd. Het maakt gebruik van het Red Hat MTA-hulpprogramma voor het migreren van servers van andere app-servers naar JBoss EAP. Stappen voor het vereenvoudigen van de migratie zijn onder andere:
* Evaluatie - JBoss EAP-clusters met behulp van Azure VM of virtuele-machineschaalset
* Beoordeling - Scant toepassingen en infrastructuur
* Infrastructuurconfiguratie - Maakt een werkbelastingsprofiel
* Implementatie en testen - Werkbelasting implementeren, migreren en testen
* Configuratie na implementatie - Integreert met gegevens, bewaking, beveiliging, back-up en meer

## <a name="server-configuration-choice"></a>Keuze voor serverconfiguratie

Voor de implementatie van RHEL VM kunt u kiezen tussen PAYG en BYOS. Installatiekopieën van de [Azure Marketplace](https://azuremarketplace.microsoft.com) staan standaard ingesteld op PAYG. Implementeer een RHEL VM van het type BYOS als u uw eigen RHEL-installatiekopie van het besturingssysteem hebt. Zorg ervoor dat uw RHSM-account BYOS rechten heeft via F Cloud Access voordat u de VM's of virtuele-machineschaalset implementeert.

JBoss EAP heeft krachtige beheermogelijkheden en biedt functionaliteit en API's voor de toepassingen. Deze beheermogelijkheden verschillen, afhankelijk van welke werkingsmodus wordt gebruikt om JBoss EAP te starten. Het wordt ondersteund op RHEL en Windows Server. JBoss EAP biedt een zelfstandige serverwerkingsmodus voor het beheren van afzonderlijke exemplaren. Het biedt ook een beheerd domeinmodus voor het beheren van groepen exemplaren vanuit één besturingspunt. Opmerking: JBoss EAP-beheerde domeinen worden niet ondersteund in Microsoft Azure omdat de functie voor hoge beschikbaarheid (HA) wordt beheerd door Azure-infrastructuurservices. De omgevingsvariabele met de naam *EAP_HOME* wordt gebruikt voor het aanduiden van het pad naar de JBoss EAP-installatie.

**JBoss EAP starten als een zelfstandige server** - met de volgende opdracht wordt de EAP-service in de zelfstandige modus gestart.

```
$EAP_HOME/bin/standalone.sh
```
    
Dit opstartscript maakt gebruik van het EAP_HOME/bin/standalone.conf-bestand om enkele standaardvoorkeuren in te stellen, zoals JVM-opties. Instellingen kunnen worden aangepast in dit bestand. JBoss EAP maakt gebruik van het configuratiebestand standalone.xml om standaard in de zelfstandige modus te starten, maar kan worden gestart met een andere modus. Voor meer informatie over de beschikbare zelfstandige configuratiebestanden en hoe u deze kunt gebruiken, raadpleegt u de sectie [Configuratiebestanden voor zelfstandige server voor EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) of [Configuratiebestanden voor zelfstandige server voor EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files). Gebruik het argument --server-config om JBoss EAP te starten met een andere configuratie. Bijvoorbeeld:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Voor een volledige lijst met alle beschikbare opstartscriptargumenten en hun doeleinden, gebruikt u het argument --help of raadpleegt u de sectie [Server-CLR-argumenten op EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) of [Server-CLR- argumenten op EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime).
    
JBoss EAP kan ook in de clustermodus worden gebruikt. Bekijk [Overzicht van clusters op EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) of [Overzicht van clusters op EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview) voor meer informatie. JBoss EAP-clusterberichten maakt groepering van JBoss EAP-berichtenservers mogelijk voor het delen van de verwerkingsbelasting van berichten. Elk actief knooppunt in het cluster is een actieve JBoss EAP-berichtenserver, die zijn eigen berichten beheert en zijn eigen verbindingen afhandelt.

## <a name="support-and-subscription-notes"></a>Opmerkingen over ondersteuning en abonnementen
Deze quickstart-sjablonen worden aangeboden als: 

- RHEL-besturingssysteem wordt aangeboden als PAYG of BYOS via het Red Hat Gold-installatiekopiemodel
- JBoss EAP wordt alleen aangeboden als BYOS

#### <a name="using-rhel-os-with-payg-model"></a>RHEL-besturingssysteem gebruiken met PAYG-model

Deze quickstart-sjablonen gebruiken standaard de RHEL 7.7 of 8.0 PAYG-installatiekopie op aanvraag uit de Azure Gallery. Voor PAYG-installatiekopieën gelden extra kosten per uur voor RHEL-abonnementen boven op de normale berekenings-, netwerk- en opslagkosten. Tevens wordt het exemplaar geregistreerd bij uw Red Hat-abonnement. Dit betekent dat u een van uw rechten gebruikt. Deze PAYG-installatiekopie leidt tot 'dubbele facturering'. U kunt dit probleem voorkomen door uw eigen RHEL-installatiekopie te bouwen. Lees dit Red Hat Knowledge Base (KB)-artikel voor meer informatie over [Hoe u een RHEL VM kunt inrichten voor Microsoft Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Of activeer de [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold-installatiekopie.

Bekijk de [Red Hat Enterprise Linux-prijzen](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/) voor meer informatie over de prijzen voor PAYG VM'S. Als u RHEL in een PAYG-model wilt gebruiken, hebt u een Azure-abonnement met de opgegeven betalingswijze nodig voor [RHEL 7.7 Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) of [RHEL 8.0 Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM). Voor deze aanbiedingen moet een betalingswijze worden opgegeven in het Azure-abonnement.

#### <a name="using-rhel-os-with-byos-model"></a>RHEL-besturingssysteem gebruiken met het BYOS-model

Als u BYOS wilt gebruiken voor het RHEL-besturingssysteem, moet u een geldig Red Hat-abonnement hebben met rechten om het RHEL-besturingssysteem in Azure te gebruiken. Zorg dat u aan de volgende vereisten voldoet voordat u deze quickstart-sjabloon implementeert:

1. Zorg ervoor dat er rechten voor het RHEL-besturingssysteem en JBoss EAP zijn gekoppeld aan uw Red Hat-abonnement.
2. Geef uw Azure-abonnements-id toestemming voor het gebruik van RHEL BYOS-installatiekopieën. Volg de [Documentatie voor Red Hat Subscription Management (RHSM)](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs) om het proces te voltooien. Dit omvat de volgende stappen:

    2.1 Schakel Microsoft Azure in als provider in het Red Hat Cloud Access Dashboard.

    2.2 Voeg uw Azure-abonnements-id’s toe.

    2.3 Schakel nieuwe producten in voor Cloud Access op Microsoft Azure.
    
    2.4 Activeer Red Hat Gold-installatiekopieën voor uw Azure-abonnement. Lees voor meer informatie het hoofdstuk over [Inschakelen en onderhouden van abonnementen voor Cloud Access](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure).

    2.5 Wacht tot Red Hat Gold-installatiekopieën beschikbaar zijn in uw Azure-abonnement. Deze Gold-installatiekopieën zijn doorgaans binnen 3 uur na verzending beschikbaar.
    
3. Accepteer de Azure Marketplace-voorwaarden voor RHEL BYOS-installatiekopieën. U kunt dit proces voltooien door de opdrachten van de Azure-opdrachtregelinterface (CLI) uit te voeren, zoals hieronder vermeld. Raadpleeg voor meer informatie de documentatie over [RHEL BYOS Gold-installatiekopieën in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos). Het is belangrijk dat u de nieuwste versie van Azure CLI uitvoert.

    3.1 Start een Azure CLI-sessie en verifieer met uw Azure-account. Raadpleeg [Aanmelden met Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) voor hulp. Zorg ervoor dat u de nieuwste versie van Azure CLI gebruikt voordat u doorgaat.

    3.2 Controleer of de RHEL BYOS-installatiekopieën beschikbaar zijn in uw abonnement door de volgende CLI-opdracht uit te voeren. Als u hier geen resultaten krijgt, raadpleegt u #2 en zorgt u ervoor dat uw Azure-abonnement is geactiveerd voor RHEL BYOS-installatiekopieën.
   
   ```
   az vm image list --offer rhel-byos --all
   ```

    3.3 Voer de volgende opdracht uit om de Marketplace-voorwaarden voor respectievelijk RHEL 7.7 BYOS en RHEL 8.0 BYOS te accepteren.
   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
   ``` 

   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
   ``` 
   
4. Uw abonnement is nu klaar om RHEL 7.7 of 8.0 BYOS te implementeren op virtuele machines van Azure.

#### <a name="using-jboss-eap-with-byos-model"></a>JBoss EAP gebruiken met het BYOS-model

JBoss EAP is alleen beschikbaar in Azure via het BYOS-model. Wanneer u deze sjabloon implementeert, moet u uw RHSM-referenties opgeven samen met de RHSM-pool-id met geldige EAP-rechten. Als u geen EAP-rechten hebt, moet u een [JBoss EAP-evaluatieabonnement verkrijgen](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) voordat u aan de slag gaat.

## <a name="how-to-consume"></a>Hoe u dit kunt gebruiken

U kunt de sjabloon op de volgende drie manieren implementeren:

- Gebruik PowerShell - implementeer de sjabloon door de volgende opdrachten uit te voeren: (Bekijk [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) voor meer informatie over het installeren en configureren van Azure PowerShell).

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
    
- Gebruik Azure CLI - voer de volgende opdrachten uit om de sjabloon te implementeren: (Bekijk [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) voor meer informatie over het installeren en configureren van de Azure CLI).

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az group deployment create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

- Gebruik Azure Portal - U kunt implementeren op de Azure Portal door naar de *Azure-quickstart-sjablonen* te gaan zoals vermeld in de volgende sectie. Zodra u in de quickstart bent, klikt u op de knop **Implementeren naar Azure** of **Bladeren op GitHub**.

## <a name="azure-quickstart-templates"></a>Azure-quickstart-sjablonen

U kunt beginnen met een van de quickstart-sjablonen van een combinatie van JBoss EAP op RHEL die voldoet aan uw implementatiedoel. Hier volgt een lijst met beschikbare quickstart-sjablonen.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss EAP op RHEL (zelfstandige VM)</a> - Hiermee wordt een webtoepassing met de naam JBoss-EAP op Azure geïmplementeerd naar JBoss EAP 7.2 of 7.3 dat wordt uitgevoerd op RHEL 7.7 of 8.0 VM.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP op RHEL (geclusterde, multi-VM's)</a> - Hiermee wordt een webtoepassing geïmplementeerd met de naam eap-session-replication naar JBoss EAP 7.2- of 7.3-cluster dat wordt uitgevoerd op 'n'-nummer RHEL 7.7 of 8.0 VM's. De waarde 'n' wordt door de gebruiker bepaald. Alle VM’s worden toegevoegd aan de back-end pool van een Load Balancer.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss EAP op RHEL (geclusterde, virtuele-machineschaalset)</a> - Hiermee wordt een webtoepassing geïmplementeerd met de naam eap-session-replication op JBoss EAP 7.2- of 7.3-cluster dat wordt uitgevoerd op RHEL 7.7 of 8.0 exemplaren van de virtuele-machineschaalset.

## <a name="resource-links"></a>Resource-links:

* [Azure Hybrid Benefit](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)
* [Een Java-app voor Azure App Service configureren](https://docs.microsoft.com/azure/app-service/configure-language-java)
* [JBoss EAP op Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP op Azure App Service Linux](https://docs.microsoft.com/azure/app-service/quickstart-java)-quickstart
* [JBoss EAP implementeren op Azure App Service](https://github.com/JasonFreeberg/jboss-on-app-service)-zelfstudie

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [JBoss EAP 7.2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/)
* Meer informatie over [JBoss EAP 7.3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/)
* Meer informatie over [Red Hat-abonnementsbeheer](https://access.redhat.com/products/red-hat-subscription-management)
* Microsoft documentatie voor [Red Hat op Azure](https://aka.ms/rhel-docs)
* [JBoss EAP implementeren op RHEL VM/virtuele-machineschaalset van Azure Marketplace](https://aka.ms/AMP-JBoss-EAP)
* [JBoss EAP implementeren op RHEL VM/virtuele-machineschaalset van Azure Quickstart](https://aka.ms/Quickstart-JBoss-EAP)
