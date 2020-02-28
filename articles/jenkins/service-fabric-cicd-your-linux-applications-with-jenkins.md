---
title: Doorlopende build voor Linux-toepassingen met Jenkins
description: Doorlopend bouwen en integreren voor uw Service Fabric Linux-toepassing met behulp van Jenkins
keywords: Jenkins, azure, devops, cicd, Linux
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 96e8c3721b5849b874878ea6a846271fb6760ffd
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77675237"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Jenkins gebruiken om uw Linux-toepassingen te bouwen en te implementeren

In deze zelf studie worden verschillende manieren beschreven waarop u uw Jenkins-omgeving kunt instellen, evenals verschillende manieren om uw toepassing te implementeren op een Service Fabric-cluster nadat het is gemaakt. Volg deze algemene stappen voor het configureren van Jenkins, het verzamelen van wijzigingen van GitHub, het bouwen van uw toepassing en het implementeren ervan in uw cluster:

1. Zorg ervoor dat u de [vereisten](#prerequisites)installeert.
1. Volg vervolgens de stappen in een van deze secties om Jenkins in te stellen:
   * [Jenkins instellen in een service Fabric cluster](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [Jenkins instellen buiten een service Fabric cluster](#set-up-jenkins-outside-a-service-fabric-cluster)of
   * [Installeer de service Fabric-invoeg toepassing in een bestaande Jenkins-omgeving](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
1. Nadat u Jenkins hebt ingesteld, volgt u de stappen in [een Jenkins-taak maken en configureren](#create-and-configure-a-jenkins-job) om github in te stellen voor het activeren van Jenkins wanneer er wijzigingen worden aangebracht in uw toepassing en om uw Jenkins-taak pijplijn te configureren via de build-stap om de wijzigingen van github te halen en uw toepassing te bouwen. 
1. Configureer tot slot de Jenkins-taak die na de build moet worden gemaakt om uw toepassing te implementeren in uw Service Fabric-cluster. Er zijn twee manieren om Jenkins te configureren voor het implementeren van uw toepassing in een cluster:    
   * Gebruik voor ontwikkel-en test omgevingen de [implementatie configureren met behulp van het eind punt van het cluster beheer](#configure-deployment-using-cluster-management-endpoint). Dit is de eenvoudigste implementatie methode voor het instellen van.
   * Gebruik voor productie omgevingen [implementatie configureren met behulp van Azure-referenties](#configure-deployment-using-azure-credentials). Micro soft raadt deze methode aan voor productie omgevingen omdat met Azure-referenties u de toegang kunt beperken die een Jenkins-taak heeft voor uw Azure-resources. 

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat Git lokaal is geïnstalleerd. U kunt de juiste Git-versie installeren vanaf [de pagina Git-down loads](https://git-scm.com/downloads) op basis van uw besturings systeem. Als u geen ervaring hebt met git, kunt u meer informatie hierover vinden in de [Git-documentatie](https://git-scm.com/docs).
- In dit artikel wordt gebruikgemaakt van het *service Fabric aan* de slag op GitHub: [https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started) om de toepassing te bouwen en te implementeren. U kunt deze opslag plaats splitsen om door te gaan of, met een wijziging in de instructies, uw eigen GitHub-project gebruiken.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Service Fabric-invoeg toepassing installeren in een bestaande Jenkins-omgeving

Als u de Service Fabric-invoeg toepassing toevoegt aan een bestaande Jenkins-omgeving, moet u de volgende stappen uitvoeren:

- [Service Fabric cli (sfctl)](../service-fabric/service-fabric-cli.md). Installeer de CLI op het systeem niveau in plaats van op het niveau van de gebruiker, zodat Jenkins CLI-opdrachten kan uitvoeren. 
- Als u Java-toepassingen wilt implementeren, installeert u beide [Gradle en opent u JDK 8,0](../service-fabric/service-fabric-get-started-linux.md#set-up-java-development). 
- Als u .NET Core 2,0-toepassingen wilt implementeren, installeert u de [.net core 2,0 SDK](../service-fabric/service-fabric-get-started-linux.md#set-up-net-core-20-development). 

Nadat u de vereiste onderdelen voor uw omgeving hebt geïnstalleerd, kunt u zoeken naar de Azure Service Fabric-invoeg toepassing in Jenkins Marketplace en deze installeren.

Nadat u de invoeg toepassing hebt geïnstalleerd, gaat u verder naar [een Jenkins-taak maken en configureren](#create-and-configure-a-jenkins-job).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Jenkins instellen in een Service Fabric-cluster

U kunt Jenkins instellen binnen of buiten een Service Fabric-cluster. In de volgende secties ziet u hoe u deze instelt in een cluster terwijl u een Azure-opslag account gebruikt om de status van de container instantie op te slaan.

1. Zorg ervoor dat u een Service Fabric Linux-cluster hebt waarop docker is geïnstalleerd. Voor Service Fabric clusters die worden uitgevoerd in azure, is docker al geïnstalleerd. Als u het cluster lokaal uitvoert (OneBox dev Environment), controleert u of docker op uw computer is geïnstalleerd met de `docker info` opdracht. Als deze niet is geïnstalleerd, installeert u deze met behulp van de volgende opdrachten:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Zorg ervoor dat de 8081-poort is opgegeven als een aangepast eind punt op het cluster. Als u een lokaal cluster gebruikt, zorg er dan voor dat poort 8081 is geopend op de hostmachine en dat de computer een openbaar IP-adres heeft.
   >

1. Kloon de toepassing met behulp van de volgende opdrachten:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. De status van de Jenkins-container in een bestands share behouden:
   1. Maak een Azure-opslag account in **dezelfde regio** als uw cluster met een naam zoals `sfjenkinsstorage1`.
   1. Maak een **Bestands share** onder het opslag account met een naam zoals `sfjenkins`.
   1. Klik op **verbinding maken** voor het bestands share en noteer de waarden die worden weer gegeven onder **verbinding maken vanuit Linux**. de waarde moet er ongeveer als volgt uitzien:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Als u CIFS-shares wilt koppelen, moet u het CIFS-hulppr-pakket in de cluster knooppunten hebben geïnstalleerd.      
   >

1. Werk de waarden van de tijdelijke aanduidingen in het `setupentrypoint.sh` script bij met de Azure-Storage-gegevens uit stap 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Vervang `[REMOTE_FILE_SHARE_LOCATION]` door de waarde `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` uit de uitvoer van de verbinding in stap 2 hierboven.
   * Vervang `[FILE_SHARE_CONNECT_OPTIONS_STRING]` door de waarde `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` uit stap 2 hierboven.

1. **Alleen beveiligd cluster:** 
   
   Als u de implementatie van toepassingen op een beveiligd cluster vanaf Jenkins wilt configureren, moet het cluster certificaat toegankelijk zijn in de Jenkins-container. Voeg in het bestand *ApplicationManifest. XML* onder het label **ContainerHostPolicies** deze certificaat verwijzing toe en werk de vingerafdruk waarde bij met het cluster certificaat.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Voeg bovendien de volgende regels toe onder de tag **ApplicationManifest** (root) in het bestand *ApplicationManifest. XML* en werk de vingerafdruk waarde bij met die van het cluster certificaat.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Maak verbinding met het cluster en installeer de container toepassing.

   **Beveiligd cluster**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   Met de voor gaande opdracht wordt het certificaat in PEM-indeling gebruikt. Als uw certificaat in PFX-indeling is, kunt u de volgende opdracht gebruiken om het te converteren. Als uw PFX-bestand niet met een wacht woord is beveiligd, geeft u de para meter **Passin** op als `-passin pass:`.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Niet-beveiligd cluster**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   Hiermee wordt een Jenkins-container in het cluster geïnstalleerd. Dit kan worden bewaakt met de Service Fabric Explorer.

   > [!NOTE]
   > Het kan enkele minuten duren voordat de Jenkins-afbeelding in het cluster is gedownload.
   >

1. Ga in uw browser naar `http://PublicIPorFQDN:8081`. Hier vindt u het pad naar het eerste beheerderswachtwoord dat nodig is om u aan te melden. 
1. Bekijk de Service Fabric Explorer om te bepalen op welk knoop punt de Jenkins-container wordt uitgevoerd. Meld u aan bij dit knoop punt.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Haal de exemplaar-id van de container op met `docker ps -a`.
1. Meld u aan bij de container met de Secure Shell (SSH) en plak het pad dat u in de Jenkins-Portal hebt weer gegeven. Als er in de portal bijvoorbeeld het pad `PATH_TO_INITIAL_ADMIN_PASSWORD`wordt weer gegeven, voert u de volgende opdrachten uit:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. Kies op de pagina Jenkins aan de slag de optie invoeg toepassingen selecteren om te installeren, selecteer het selectie vakje **geen** en klik op installeren.
1. Maak een gebruiker of Selecteer om door te gaan als beheerder.

Nadat u Jenkins hebt ingesteld, gaat u verder met [het maken en configureren van een Jenkins-taak](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Jenkins instellen buiten een Service Fabric-cluster

U kunt Jenkins instellen binnen of buiten een Service Fabric-cluster. In de volgende secties ziet u hoe u Jenkins instelt buiten een cluster.

1. Zorg ervoor dat docker op uw computer is geïnstalleerd door `docker info` in de terminal uit te voeren. De uitvoer geeft aan of de docker-service wordt uitgevoerd.

1. Als docker niet is geïnstalleerd, voert u de volgende opdrachten uit:

    ```sh
    sudo apt-get install wget
    wget -qO- https://get.docker.io/ | sh
    ```

1. Haal de Service Fabric Jenkins container-installatiekopie op: `docker pull rapatchi/jenkins:latest`. Deze installatiekopie wordt geleverd met de Service Fabric Jenkins-invoegtoepassing die vooraf is geïnstalleerd.
1. Voer de installatiekopie van de container uit: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Haal de id op van het exemplaar van de installatiekopie van de container. U kunt een lijst van alle Docker-containers bekijken met de opdracht `docker ps –a`
1. Meld u aan bij de Jenkins-Portal met de volgende stappen:

   1. Meld u vanaf uw host aan bij een Jenkins-shell. Gebruik de eerste vier cijfers van de container-ID. Als de container-ID bijvoorbeeld `2d24a73b5964`is, gebruikt u `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. Haal in de Jenkins-shell het beheerders wachtwoord op voor uw container instantie:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Open de volgende URL in een webbrowser om u aan te melden bij het Jenkins-dash board: `http://<HOST-IP>:8080`. Gebruik het wacht woord uit de vorige stap om Jenkins te ontgrendelen.
   1. (Optioneel.) Nadat u zich voor de eerste keer hebt aangemeld, kunt u uw eigen gebruikers account maken en gebruiken voor de volgende stappen, of u kunt het beheerders account blijven gebruiken. Als u een gebruiker maakt, moet u door gaan met die gebruiker.
1. Stel GitHub in om met Jenkins te werken met behulp van de stappen in [een nieuwe SSH-sleutel genereren en toevoegen aan de SSH-agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
   * Volg de instructies van GitHub om de SSH-sleutel te genereren en deze toe te voegen aan het GitHub-account waarop de opslagplaats wordt gehost.
   * Voer de opdrachten die in de vorige koppeling zijn genoemd, uit in de Jenkins Docker-shell (en niet op uw host).
   * Gebruik de volgende opdracht om u vanaf uw host aan te melden bij de Jenkins-shell:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Zorg ervoor dat het cluster of de computer waarop de Jenkins-container installatie kopie wordt gehost, een openbaar IP-adres heeft. Alleen zo kan het Jenkins-exemplaar meldingen ontvangen van GitHub.

Nadat u Jenkins hebt ingesteld, gaat u verder met de volgende sectie, [een Jenkins-taak maken en configureren](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Een Jenkins-taak maken en configureren

De stappen in deze sectie laten zien hoe u een Jenkins-taak kunt configureren om te reageren op wijzigingen in een GitHub-opslag plaats, de wijzigingen op te halen en ze te bouwen. Aan het einde van deze sectie gaat u naar de laatste stappen om de taak te configureren voor het implementeren van uw toepassing, op basis van het feit of u implementeert in een ontwikkel-of test omgeving of in een productie omgeving. 

1. Klik op het Jenkins-dash board op **Nieuw item**.
1. Voer een itemnaam in (bijvoorbeeld **MyJob**). Selecteer **free-style project** en klik op **OK**.
1. De pagina taak configuratie wordt geopend. (Als u de configuratie van het Jenkins-dash board wilt ophalen, klikt u op de taak en klikt u vervolgens op **configureren**).

1. Schakel op het tabblad **Algemeen** het selectie vakje voor **github-project**in en geef de URL van uw github-project op. Dit is de URL waarop de Service Fabric Java-toepassing wordt gehost die u wilt integreren met de CI-/CD-stroom van Jenkins (Continue integratie, Continue implementatie), bijvoorbeeld `https://github.com/{your-github-account}/service-fabric-java-getting-started`.

1. Selecteer **Git**op het tabblad **broncode beheer** . Geef de opslagplaats-URL op waarop de Service Fabric Java-toepassing wordt gehost die u wilt integreren met de CI-/CD-stroom van Jenkins, bijvoorbeeld `https://github.com/{your-github-account}/service-fabric-java-getting-started`. U kunt ook opgeven welke vertakking u wilt maken (bijvoorbeeld `/master`).
1. Configureer uw *github* -opslag plaats om te praten met Jenkins:

   a. Ga op de pagina GitHub-opslag plaats naar **instellingen** > **integraties en-services**.

   b. Selecteer **Add Service**, typ **Jenkins** en selecteer de **Jenkins-GitHub-invoegtoepassing**.

   c. Geef de Jenkins-webhook-URL op (standaard is dit `http://<PublicIPorFQDN>:8081/github-webhook/`). Klik op **add/update service**.

   d. Er wordt een testgebeurtenis verzonden naar uw Jenkins-exemplaar. Er komt een groen vinkje bij de webhook te staan in GitHub en uw project wordt gemaakt.

1. Selecteer op het tabblad **Build triggers** in Jenkins de gewenste opbouw optie. Voor dit voor beeld wilt u een build activeren wanneer er een push-bericht naar de opslag plaats plaatsvindt, dus Selecteer **github Hook-trigger voor GITScm polling**. (Voorheen heette deze optie **Build when a change is pushed to GitHub**.)
1. Voer op het tabblad **Build** een van de volgende handelingen uit, afhankelijk van of u een Java-toepassing of een .net-kern toepassing bouwt:

   * **Voor Java-toepassingen:** Selecteer in de vervolg keuzelijst **Build-stap toevoegen** de optie **Gradle-script aanroepen**. Klik op **Advanced**. Geef in het menu Geavanceerd het pad op naar het **hoofd script** voor uw toepassing. Het haalt build.gradle op uit het opgegeven pad en werkt dienovereenkomstig. Voor de [toepassing ActorCounter](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter)is dit: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Service Fabric Jenkins Build action](./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png)

   * **Voor .net core-toepassingen:** Selecteer in de vervolg keuzelijst **Build-stap toevoegen** de optie **shell uitvoeren**. In het opdracht venster dat wordt weer gegeven, moet de Directory eerst worden gewijzigd in het pad waar het build.sh-bestand zich bevindt. Zodra de Directory is gewijzigd, kan het build.sh-script worden uitgevoerd en wordt de toepassing gemaakt.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     De volgende scherm afbeelding toont een voor beeld van de opdrachten die worden gebruikt voor het samen stellen van het [Counter service](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) -voor beeld met de Jenkins-taak naam CounterServiceApplication.

      ![Service Fabric Jenkins Build action](./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png)

1. Als u Jenkins wilt configureren voor het implementeren van uw app in een Service Fabric-cluster in de acties na het bouwen, hebt u de locatie van het certificaat van dat cluster in uw Jenkins-container nodig. Kies een van de volgende opties, afhankelijk van of de Jenkins-container binnen of buiten uw cluster wordt uitgevoerd en noteer de locatie van het cluster certificaat:

   * **Voor Jenkins die binnen uw cluster worden uitgevoerd:** Het pad naar het certificaat kan worden gevonden door de waarde van de *Certificates_JenkinsOnSF_Code_MyCert_PEM* omgevings variabele in de container te echo's.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **Voor Jenkins die buiten uw cluster worden uitgevoerd:** Volg deze stappen om het cluster certificaat naar uw container te kopiëren:
     1. Uw certificaat moet de PEM-indeling hebben. Als u geen PEM-bestand hebt, kunt u er een maken op basis van het PFX-bestand van het certificaat. Als uw PFX-bestand niet met een wacht woord is beveiligd, voert u de volgende opdracht uit vanaf uw host:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        Als het PFX-bestand met een wacht woord is beveiligd, neemt u het wacht woord op in de para meter `-passin`. Bijvoorbeeld:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. Voer `docker ps` uit vanaf uw host om de container-ID voor uw Jenkins-container op te halen.
     1. Kopieer het PEM-bestand naar de container met de volgende docker-opdracht:
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

U bent bijna klaar. Zorg ervoor dat de Jenkins-taak is geopend. De enige resterende taak is het configureren van de stappen na het maken van de build om uw toepassing te implementeren op uw Service Fabric-cluster:

* Als u wilt implementeren in een ontwikkel-of test omgeving, volgt u de stappen in [implementatie configureren met behulp van het eind punt voor cluster beheer](#configure-deployment-using-cluster-management-endpoint).
* Als u wilt implementeren in een productie omgeving, volgt u de stappen in [implementatie configureren met behulp van Azure-referenties](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Implementatie met behulp van Cluster beheer eindpunt configureren

Voor ontwikkel-en test omgevingen kunt u het eind punt voor cluster beheer gebruiken om uw toepassing te implementeren. Als u de actie na het maken wilt configureren met het eind punt van het cluster beheer om uw toepassing te implementeren, moet u Mini maal instellen. Als u implementeert in een productie omgeving, gaat u verder met het [configureren van de implementatie met behulp van Azure-referenties](#configure-deployment-using-azure-credentials) om een Azure Active Directory Service-Principal te configureren die tijdens de implementatie moet worden gebruikt.    

1. Klik in de Jenkins-taak op het tabblad **acties na het maken** . 
1. Selecteer in de vervolgkeuzelijst **Post-Build Actions** de optie **Post-Build Actions**. 
1. Onder **service Fabric cluster configuratie**selecteert u het keuze rondje **service Fabric Management-eind punt vullen** .
1. Voor **Management host**voert u het verbindings eindpunt voor uw cluster in. bijvoorbeeld `{your-cluster}.eastus.cloudapp.azure.com`.
1. Voer voor **client sleutel** en **client certificaat**de locatie in van het PEM-bestand in de Jenkins-container. bijvoorbeeld `/var/jenkins_home/clustercert.pem`. (U hebt de locatie van het certificaat in de laatste stap voor het [maken en configureren van een Jenkins-taak](#create-and-configure-a-jenkins-job)gekopieerd.)
1. Configureer onder **toepassings configuratie**de **toepassings naam**, het **toepassings type**en het (relatieve) **pad naar de manifest** velden van de toepassing.

   ![Service Fabric Jenkins na build actie beheer eindpunt configureren](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Klik op **configuratie verifiëren**. Klik bij geslaagde verificatie op **Opslaan**. Uw Jenkins-taak pijplijn is nu volledig geconfigureerd. Ga verder met de [volgende stappen](#next-steps) om uw implementatie te testen.

## <a name="configure-deployment-using-azure-credentials"></a>Implementatie met behulp van Azure-referenties configureren

Voor productie omgevingen wordt het ten zeerste aanbevolen om een Azure-referentie voor de implementatie van uw toepassing te configureren. In deze sectie wordt beschreven hoe u een Azure Active Directory Service-Principal configureert om te gebruiken voor het implementeren van uw toepassing in de actie na het maken. U kunt Service-principals toewijzen aan rollen in uw Directory om de machtigingen van de Jenkins-taak te beperken. 

Voor ontwikkel-en test omgevingen kunt u Azure-referenties of het cluster beheer eindpunt configureren om uw toepassing te implementeren. Zie voor meer informatie over het configureren van een cluster beheer-eind punt [implementatie configureren met cluster beheer eindpunt](#configure-deployment-using-cluster-management-endpoint).   

1. Volg de stappen in [de portal gebruiken om een Azure Active Directory toepassing en Service-Principal te maken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)om een Azure Active Directory Service-Principal te maken en deze machtigingen toe te wijzen aan uw Azure-abonnement. Let op het volgende:

   * Zorg er tijdens het volgen van de stappen in het onderwerp voor dat u de volgende waarden kopieert en opslaat: *toepassings-id*, *toepassings sleutel*, *Directory-id (Tenant-ID)* en *abonnements-id*. U hebt deze nodig voor het configureren van de Azure-referenties in Jenkins.
   * Als u niet over de [vereiste machtigingen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) voor uw Directory beschikt, moet u een beheerder vragen om u de machtigingen te verlenen of de service-principal voor u te maken, of u moet het beheer eindpunt voor uw cluster configureren in de **acties na het maken** van de opdracht voor uw taak in Jenkins.
   * In de sectie [een Azure Active Directory-toepassing maken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) kunt u een juist opgemaakte URL voor de **aanmeldings-URL**invoeren.
   * In de sectie [toepassing toewijzen aan een rol](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) kunt u uw toepassing de rol van *lezer* toewijzen voor de resource groep voor uw cluster.

1. Klik in de Jenkins-taak op het tabblad **acties na het maken** .
1. Selecteer in de vervolgkeuzelijst **Post-Build Actions** de optie **Post-Build Actions**. 
1. Klik onder **service Fabric cluster configuratie**op **het service Fabric cluster selecteren**. Klik op **toevoegen** naast **Azure-referenties**. Klik op **Jenkins** om de Jenkins-referentie provider te selecteren.
1. Selecteer in de Jenkins-referentie provider **Microsoft Azure Service-Principal** in de vervolg keuzelijst **type** .
1. Gebruik de waarden die u hebt opgeslagen bij het instellen van de Service-Principal in stap 1 om de volgende velden in te stellen:

   * **Client-id**: *toepassings-id*
   * **Client geheim**: *toepassings sleutel*
   * **Tenant-id**: *map-id*
   * **Abonnements-id**: *abonnements-id*
1. Voer een beschrijvende **id** in die u gebruikt om de referentie in Jenkins en een korte **Beschrijving**te selecteren. Klik vervolgens op **Service-Principal controleren**. Als de verificatie slaagt, klikt u op **toevoegen**.

   ![Azure-referenties Service Fabric Jenkins invoeren](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. Ga terug naar **service Fabric cluster configuratie**en zorg ervoor dat uw nieuwe referentie is geselecteerd voor **Azure-referenties**. 
1. Selecteer in de vervolg keuzelijst **resource groep** de resource groep van het cluster waarop u de toepassing wilt implementeren.
1. Selecteer in de vervolg keuzelijst **service Fabric** het cluster waarop u de toepassing wilt implementeren.
1. Voer voor **client sleutel** en **client certificaat**de locatie in van het PEM-bestand in de Jenkins-container. Bijvoorbeeld `/var/jenkins_home/clustercert.pem`. 
1. Configureer onder **toepassings configuratie**de **toepassings naam**, het **toepassings type**en het (relatieve) **pad naar de manifest** velden van de toepassing.
    ![Service Fabric Jenkins na het bouwen: Azure-referenties configureren](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Klik op **configuratie verifiëren**. Klik bij geslaagde verificatie op **Opslaan**. Uw Jenkins-taak pijplijn is nu volledig geconfigureerd. Ga door met de [volgende stappen](#next-steps) om uw implementatie te testen.

## <a name="troubleshooting-the-jenkins-plugin"></a>Problemen met de Jenkins-invoegtoepassing oplossen

Als u problemen ondervindt met de Jenkins-invoegtoepassingen, kunt u in [Jenkins JIRA](https://issues.jenkins-ci.org/) een ticket openen voor het specifieke onderdeel.

## <a name="ideas-to-try"></a>Ideeën om te proberen

GitHub en Jenkins zijn nu geconfigureerd. U kunt een voor beeld van een wijziging in het `reliable-services-actor-sample/Actors/ActorCounter` project in uw Fork van de opslag plaats maken, https://github.com/Azure-Samples/service-fabric-java-getting-started. Push uw wijzigingen naar de externe `master`-vertakking (of een vertakking die u hebt geconfigureerd om met te werken). Op die manier wordt de geconfigureerde Jenkins-taak `MyJob` geactiveerd. Hiermee worden de wijzigingen van GitHub opgehaald, gebouwd en wordt de toepassing geïmplementeerd in het cluster dat u hebt opgegeven bij acties na het maken.  

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Jenkins op Azure](/azure/Jenkins/)