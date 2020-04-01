---
title: Continue build voor Linux applicaties met Jenkins
description: Continue build en integratie voor uw Service Fabric Linux applicatie met Jenkins
keywords: jenkins, azure, devops, cicd, linux
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 96e8c3721b5849b874878ea6a846271fb6760ffd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77675237"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Jenkins gebruiken om uw Linux-applicaties te bouwen en te implementeren

Deze zelfstudie behandelt verschillende mogelijke manieren om uw Jenkins-omgeving in te stellen, evenals verschillende manieren om uw toepassing te implementeren in een Service Fabric-cluster nadat deze is gebouwd. Volg deze algemene stappen om Jenkins succesvol te configureren, wijzigingen van GitHub op te halen, uw toepassing te bouwen en deze te implementeren in uw cluster:

1. Zorg ervoor dat u de [voorwaarden installeert.](#prerequisites)
1. Volg vervolgens de stappen in een van deze secties om Jenkins in te stellen:
   * [Jenkins instellen in een cluster van servicefabric,](#set-up-jenkins-inside-a-service-fabric-cluster) 
   * [Jenkins instellen buiten een cluster van servicefabric'](#set-up-jenkins-outside-a-service-fabric-cluster)s of
   * [Installeer de Service Fabric-plug-in in een bestaande Jenkins-omgeving.](#install-service-fabric-plugin-in-an-existing-jenkins-environment)
1. Nadat u Jenkins hebt ingesteld, volgt u de stappen in [Create en configureert u een Jenkins-taak](#create-and-configure-a-jenkins-job) om GitHub in te stellen om Jenkins te activeren wanneer er wijzigingen zijn aangebracht in uw toepassing en om uw Jenkins-taakpijplijn te configureren via de buildstap om de wijzigingen van GitHub te verwijderen en uw toepassing te bouwen. 
1. Configureer ten slotte de stap Jenkins-taak na het bouwen om uw toepassing te implementeren in uw Service Fabric-cluster. Jenkins op twee manieren worden geconfigureerd om uw toepassing naar een cluster te implementeren:    
   * Voor ontwikkel- en testomgevingen gebruikt u [Implementatie configureren met behulp van eindpunt clusterbeheer](#configure-deployment-using-cluster-management-endpoint). Dit is de eenvoudigste implementatiemethode die u instellen.
   * Voor productieomgevingen gebruikt u [Implementatie configureren met Azure-referenties.](#configure-deployment-using-azure-credentials) Microsoft raadt deze methode aan voor productieomgevingen, omdat u met Azure-referenties de toegang die een Jenkins-taak heeft, beperken tot uw Azure-resources. 

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat Git lokaal is geïnstalleerd. U de juiste Git-versie installeren op [de pagina Git-downloads](https://git-scm.com/downloads) op basis van uw besturingssysteem. Als git nieuw voor je bent, lees je er meer over in de [Git-documentatie.](https://git-scm.com/docs)
- In dit artikel wordt gebruik gemaakt van het *voorbeeld servicestof aan de slag* op GitHub: [https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started) voor het bouwen en implementeren van de toepassing. U deze opslagplaats om mee te volgen, of, met enige wijziging van de instructies, gebruik maken van uw eigen GitHub project.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Service Fabric-plug-in installeren in een bestaande Jenkins-omgeving

Als u de plug-in Service Fabric toevoegt aan een bestaande Jenkins-omgeving, moet u de volgende stappen uitvoeren:

- [Service Fabric CLI (sfctl)](../service-fabric/service-fabric-cli.md). Installeer de CLI op systeemniveau in plaats van op gebruikersniveau, zodat Jenkins CLI-opdrachten kan uitvoeren. 
- Als u Java-toepassingen wilt implementeren, installeert u zowel Gradle als [Open JDK 8.0](../service-fabric/service-fabric-get-started-linux.md#set-up-java-development). 
- Als u .NET Core 2.0-toepassingen wilt implementeren, installeert u de [.NET Core 2.0 SDK](../service-fabric/service-fabric-get-started-linux.md#set-up-net-core-20-development). 

Nadat u de vereisten hebt geïnstalleerd die nodig zijn voor uw omgeving, u zoeken naar de Azure Service Fabric-plug-in in jenkins-marktplaats en deze installeren.

Nadat u de plug-in hebt geïnstalleerd, gaat u verder naar [Het maken en configureren van een Jenkins-taak.](#create-and-configure-a-jenkins-job)

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Jenkins instellen in een Service Fabric-cluster

U kunt Jenkins instellen binnen of buiten een Service Fabric-cluster. In de volgende secties wordt uitgelegd hoe u het in een cluster instellen terwijl u een Azure-opslagaccount gebruikt om de status van de containerinstantie op te slaan.

1. Zorg ervoor dat u een Service Fabric Linux-cluster hebt met Docker geïnstalleerd. Servicefabricclusters die in Azure worden uitgevoerd, hebben Docker al geïnstalleerd. Als u het cluster lokaal uitvoert (OneBox-dev-omgeving), controleert u met `docker info` de opdracht of Docker op uw machine is geïnstalleerd. Als deze niet is geïnstalleerd, installeert u het met behulp van de volgende opdrachten:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Controleer of de 8081-poort is opgegeven als een aangepast eindpunt in het cluster. Als u een lokaal cluster gebruikt, moet u ervoor zorgen dat poort 8081 is geopend op de hostmachine en dat deze een openbaar IP-adres heeft.
   >

1. Kloon de toepassing met de volgende opdrachten:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. Ga door tot de status van de Jenkins-container in een bestandsshare:
   1. Maak een Azure-opslagaccount in **dezelfde regio** als `sfjenkinsstorage1`uw cluster met een naam zoals .
   1. Maak een **bestandsshare onder** het opslagaccount `sfjenkins`met een naam zoals .
   1. Klik op **Connect** voor de file-share en let op de waarden die worden weergegeven onder **Verbinding van Linux,** de waarde moet lijken op de onderstaande:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Als u cifs-shares wilt monteren, moet u het cifs-utils-pakket in de clusterknooppunten hebben geïnstalleerd.      
   >

1. Werk de tijdelijke aanduidingswaarden in het `setupentrypoint.sh` script bij met de azure-storage-details van stap 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Vervang `[REMOTE_FILE_SHARE_LOCATION]` de `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` waarde van de uitvoer van de verbinding in stap 2 hierboven.
   * Vervang `[FILE_SHARE_CONNECT_OPTIONS_STRING]` met `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` de waarde van stap 2 hierboven.

1. **Alleen beveiligde cluster:** 
   
   Als u de implementatie van toepassingen op een beveiligd cluster van Jenkins wilt configureren, moet het clustercertificaat toegankelijk zijn binnen de Jenkins-container. Voeg in het bestand *ApplicationManifest.xml* onder de tag **ContainerHostPolicies** deze certificaatverwijzing toe en werk de duimafdrukwaarde bij met die van het clustercertificaat.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Voeg bovendien de volgende regels toe onder de **tag ApplicationManifest** (root) in het bestand *ApplicationManifest.xml* en werk de duimafdrukwaarde bij met die van het clustercertificaat.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Maak verbinding met het cluster en installeer de containertoepassing.

   **Veilig cluster**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   De vorige opdracht neemt het certificaat in PEM-formaat. Als uw certificaat pfx-indeling heeft, u de volgende opdracht gebruiken om het te converteren. Als uw PFX-bestand niet met een wachtwoord `-passin pass:`is beveiligd, geeft u de **parameter passin** op als .
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Onveilig cluster**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   Hiermee wordt een Jenkins-container in het cluster geïnstalleerd. Dit kan worden bewaakt met de Service Fabric Explorer.

   > [!NOTE]
   > Het kan een paar minuten duren voordat de Jenkins-afbeelding op het cluster is gedownload.
   >

1. Ga in uw browser naar `http://PublicIPorFQDN:8081`. Hier vindt u het pad naar het eerste beheerderswachtwoord dat nodig is om u aan te melden. 
1. Kijk naar de Service Fabric Explorer om te bepalen op welk knooppunt de Jenkins-container wordt uitgevoerd. Secure Shell (SSH) meld u aan bij dit knooppunt.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Haal de exemplaar-id van de container op met `docker ps -a`.
1. Secure Shell (SSH) log in bij de container en plak het pad dat u op de Jenkins-portal werd getoond. Als in de portal bijvoorbeeld het `PATH_TO_INITIAL_ADMIN_PASSWORD`pad wordt weergegeven, voert u de volgende opdrachten uit:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. Kies op de pagina Jenkins Aan de slag de optie Plug-ins selecteren om te installeren, schakel het selectievakje **Geen** in en klik op installeren.
1. Maak een gebruiker of selecteer om verder te gaan als beheerder.

Nadat u Jenkins hebt ingesteld, gaat u verder naar [Maken en configureert u een Jenkins-taak.](#create-and-configure-a-jenkins-job)  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Jenkins instellen buiten een Service Fabric-cluster

U kunt Jenkins instellen binnen of buiten een Service Fabric-cluster. In de volgende secties ziet u hoe u Jenkins instelt buiten een cluster.

1. Zorg ervoor dat Docker op uw `docker info` machine is geïnstalleerd door in de terminal te draaien. De uitvoer geeft aan of de Docker-service wordt uitgevoerd.

1. Als Docker niet is geïnstalleerd, voert u de volgende opdrachten uit:

    ```sh
    sudo apt-get install wget
    wget -qO- https://get.docker.io/ | sh
    ```

1. Haal de Service Fabric Jenkins container-installatiekopie op: `docker pull rapatchi/jenkins:latest`. Deze installatiekopie wordt geleverd met de Service Fabric Jenkins-invoegtoepassing die vooraf is geïnstalleerd.
1. Voer de installatiekopie van de container uit: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Haal de id op van het exemplaar van de installatiekopie van de container. U kunt een lijst van alle Docker-containers bekijken met de opdracht `docker ps –a`
1. Meld u aan bij de Jenkins-portal met de volgende stappen:

   1. Meld je aan bij een Jenkins-granaat van je gastheer. Gebruik de eerste vier cijfers van de container-ID. Als de container-ID `2d24a73b5964`bijvoorbeeld `2d24`gebruik .

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. Haal vanuit de jenkins-shell het beheerderswachtwoord voor uw containerexemplaar op:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Als u zich wilt aanmelden bij het Jenkins-dashboard, opent u de volgende URL in een webbrowser: `http://<HOST-IP>:8080`. Gebruik het wachtwoord van de vorige stap om Jenkins te ontgrendelen.
   1. (Optioneel.) Nadat u zich voor de eerste keer hebt aangemeld, u uw eigen gebruikersaccount aanmaken en dat gebruiken voor de volgende stappen, of u het beheerdersaccount blijven gebruiken. Als u een gebruiker maakt, moet u doorgaan met die gebruiker.
1. Stel GitHub in om met Jenkins te werken door de stappen te gebruiken in [het genereren van een nieuwe SSH-sleutel en deze toe te voegen aan de SSH-agent.](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)
   * Volg de instructies van GitHub om de SSH-sleutel te genereren en deze toe te voegen aan het GitHub-account waarop de opslagplaats wordt gehost.
   * Voer de opdrachten die in de vorige koppeling zijn genoemd, uit in de Jenkins Docker-shell (en niet op uw host).
   * Gebruik de volgende opdracht om u vanaf uw host aan te melden bij de Jenkins-shell:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Zorg ervoor dat het cluster of de machine waarin de Jenkins-containerafbeelding wordt gehost, een ip-adres heeft dat in het openbaar wordt weergegeven. Alleen zo kan het Jenkins-exemplaar meldingen ontvangen van GitHub.

Nadat u Jenkins hebt ingesteld, gaat u verder naar de volgende sectie, [Een Jenkins-taak maken en configureren.](#create-and-configure-a-jenkins-job)

## <a name="create-and-configure-a-jenkins-job"></a>Een Jenkins-taak maken en configureren

De stappen in deze sectie laten zien hoe u een Jenkins-taak configureert om te reageren op wijzigingen in een GitHub-repo, de wijzigingen op te halen en ze te bouwen. Aan het einde van deze sectie wordt u naar de laatste stappen geleid om de taak te configureren om uw toepassing te implementeren op basis van of u implementeert in een ontwikkel-/testomgeving of naar een productieomgeving. 

1. Klik op het Jenkins-dashboard op **Nieuw item**.
1. Voer een itemnaam in (bijvoorbeeld **MyJob**). Selecteer **free-style project** en klik op **OK**.
1. De pagina Taakconfiguratie wordt geopend. (Als u de configuratie wilt vinden vanuit het Jenkins-dashboard, klikt u op de taak en klikt u vervolgens op **Configureren).**

1. Schakel op het tabblad **Algemeen** het selectievakje voor **GitHub-project**in en geef de URL van het GitHub-project op. Dit is de URL waarop de Service Fabric Java-toepassing wordt gehost die u wilt integreren met de CI-/CD-stroom van Jenkins (Continue integratie, Continue implementatie), bijvoorbeeld `https://github.com/{your-github-account}/service-fabric-java-getting-started`.

1. Selecteer **Git**op het tabblad **Broncodebeheer** . Geef de opslagplaats-URL op waarop de Service Fabric Java-toepassing wordt gehost die u wilt integreren met de CI-/CD-stroom van Jenkins, bijvoorbeeld `https://github.com/{your-github-account}/service-fabric-java-getting-started`. U ook opgeven welke branch `/master`u wilt bouwen (bijvoorbeeld).
1. Configureer uw *GitHub-repository* om met Jenkins te praten:

   a. Ga op de pagina GitHub-repository naar **Instellingen** > **integraties en services**.

   b. Selecteer **Add Service**, typ **Jenkins** en selecteer de **Jenkins-GitHub-invoegtoepassing**.

   c. Geef de Jenkins-webhook-URL op (standaard is dit `http://<PublicIPorFQDN>:8081/github-webhook/`). Klik op **add/update service**.

   d. Er wordt een testgebeurtenis verzonden naar uw Jenkins-exemplaar. Er komt een groen vinkje bij de webhook te staan in GitHub en uw project wordt gemaakt.

1. Selecteer op het tabblad **Triggers bouwen** in Jenkins welke buildoptie u wilt. In dit voorbeeld wilt u een build activeren wanneer er een push naar de repository plaatsvindt, dus selecteer **GitHub-haaktrigger voor GITScm-polling.** (Voorheen heette deze optie **Build when a change is pushed to GitHub**.)
1. Ga op het tabblad **Bouwen** een van de volgende handelingen uit, afhankelijk van of u een Java-toepassing of een .NET Core-toepassing bouwt:

   * **Voor Java-toepassingen:** Selecteer In de vervolgkeuzestap **Toevoegen** de optie **Gradle Script aanroepen**. Klik op **Geavanceerd**. Geef in het geavanceerde menu het pad naar **root-buildscript** voor uw toepassing op. Het haalt build.gradle op uit het opgegeven pad en werkt dienovereenkomstig. Voor de [ActorCounter-toepassing](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter) `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`is dit: .

     ![Service Fabric Jenkins Build-actie](./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png)

   * **Voor .NET Core-toepassingen:** Selecteer **Shell uitvoeren**in de vervolgkeuze **stap Toevoegen** . In het opdrachtvak dat wordt weergegeven, moet de map eerst worden gewijzigd in het pad waar het build.sh-bestand zich bevindt. Zodra de map is gewijzigd, kan het build.sh script worden uitgevoerd en zal de toepassing te bouwen.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     De volgende schermafbeelding toont een voorbeeld van de opdrachten die worden gebruikt om het [voorbeeld van counterservice](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) te bouwen met een Jenkins-taaknaam van CounterServiceApplication.

      ![Service Fabric Jenkins Build-actie](./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png)

1. Als u Jenkins wilt configureren om uw app te implementeren in een cluster van Servicefabric in de post-build-acties, hebt u de locatie van het certificaat van dat cluster in uw Jenkins-container nodig. Kies een van de volgende opties, afhankelijk van of uw Jenkins-container binnen of buiten uw cluster wordt uitgevoerd en noteer de locatie van het clustercertificaat:

   * **Voor Jenkins die in uw cluster wordt uitgevoerd:** Het pad naar het certificaat kan worden gevonden door de waarde van de *Certificates_JenkinsOnSF_Code_MyCert_PEM* omgevingsvariabele vanuit de container te weerklinken.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **Voor Jenkins die buiten uw cluster wordt uitgevoerd:** Voer de volgende stappen uit om het clustercertificaat naar uw container te kopiëren:
     1. Uw certificaat moet in PEM-formaat zijn. Als u geen PEM-bestand hebt, u er een maken uit het PFX-certificaatbestand. Als uw PFX-bestand niet met een wachtwoord is beveiligd, voert u de volgende opdracht uit vanaf uw host:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        Als het PFX-bestand met een wachtwoord `-passin` is beveiligd, neemt u het wachtwoord op in de parameter. Bijvoorbeeld:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. Als u de container-ID voor `docker ps` uw Jenkins-container wilt ophalen, loopt u vanaf uw host.
     1. Kopieer het PEM-bestand naar uw container met de volgende opdracht Docker:
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

Je bent bijna klaar! Hou de Jenkins baan open. De enige resterende taak is het configureren van de stappen na het bouwen om uw toepassing te implementeren in uw Service Fabric-cluster:

* Als u wilt implementeren in een ontwikkel- of testomgeving, voert u de stappen uit in [Implementatie configureren met eindpunt voor clusterbeheer.](#configure-deployment-using-cluster-management-endpoint)
* Als u wilt implementeren in een productieomgeving, voert u de stappen uit in [Implementatie configureren met Azure-referenties.](#configure-deployment-using-azure-credentials)

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Implementatie configureren met behulp van eindpunt clusterbeheer

Voor ontwikkel- en testomgevingen u het eindpunt voor clusterbeheer gebruiken om uw toepassing te implementeren. Voor het configureren van de post-buildactie met het eindpunt voor clusterbeheer om uw toepassing te implementeren, is de minste set-up vereist. Als u implementeert in een productieomgeving, gaat u verder met [Implementatie configureren met Azure-referenties](#configure-deployment-using-azure-credentials) om een Azure Active Directory-serviceprincipal te configureren die tijdens de implementatie moet worden gebruikt.    

1. Klik in de taak Jenkins op het tabblad **Acties voor het plaatsen.** 
1. Selecteer in de vervolgkeuzelijst **Post-Build Actions** de optie **Post-Build Actions**. 
1. Selecteer **onder Clusterconfiguratie servicestructuur**de knop **Endpoint-keuzerondje voor servicestructuurbeheer invullen.**
1. Voer **voor Beheerhost**het verbindingseindpunt voor uw cluster in; bijvoorbeeld `{your-cluster}.eastus.cloudapp.azure.com`.
1. Voer **voor clientsleutel** en **clientcert**de locatie van het PEM-bestand in uw Jenkins-container in; bijvoorbeeld `/var/jenkins_home/clustercert.pem`. (U hebt de locatie van het certificaat de laatste stap van [Een Jenkins-taak maken en configureren](#create-and-configure-a-jenkins-job)gekopieerd .)
1. Configureer **onder Toepassingsconfiguratie**de velden **Toepassingsnaam**, **Toepassingstype**en (relatief) **pad naar toepassingsmanifest.**

   ![Service Fabric Jenkins Post-Build Action configuremanagement endpoint](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Klik **op Configuratie verifiëren**. Klik bij geslaagde verificatie op **Opslaan**. Uw Jenkins-taakpijplijn is nu volledig geconfigureerd. Ga verder naar [volgende stappen](#next-steps) om uw implementatie te testen.

## <a name="configure-deployment-using-azure-credentials"></a>Implementatie configureren met Azure-referenties

Voor productieomgevingen wordt het configureren van een Azure-referentie voor het implementeren van uw toepassing ten zeerste aanbevolen. In deze sectie ziet u hoe u een Azure Active Directory-serviceprincipal configureert om uw toepassing te implementeren in de actie na de build. U serviceprincipals toewijzen aan rollen in uw map om de machtigingen van de Jenkins-taak te beperken. 

Voor ontwikkel- en testomgevingen u Azure-referenties of het eindpunt voor clusterbeheer configureren om uw toepassing te implementeren. Zie [Implementatie configureren met eindpunt voor clusterbeheer voor](#configure-deployment-using-cluster-management-endpoint)meer informatie over het configureren van een eindpunt voor clusterbeheer.   

1. Als u een Azure Active Directory-serviceprincipal wilt maken en machtigingen wilt toewijzen in uw Azure-abonnement, voert u de stappen uit in [De portal gebruiken om een Azure Active Directory-toepassing en serviceprincipal te maken.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) Let op het volgende:

   * Terwijl u de stappen in het onderwerp volgt, moet u de volgende waarden kopiëren en opslaan: *Toepassings-id,* *toepassingssleutel*, *directory-id (tenant-id)* en *abonnements-id*. U hebt ze nodig om de Azure-referenties in Jenkins te configureren.
   * Als u niet over de [vereiste machtigingen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) op uw directory beschikt, moet u een beheerder vragen om u de machtigingen toe te kennen of de serviceprincipal voor u te maken, of moet u het beheereindpunt voor uw cluster configureren in de **post-buildacties** voor uw taak in Jenkins.
   * In de sectie [Een Azure Active Directory-toepassing maken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) u een goed gevormde URL invoeren voor de URL **aanmelding**.
   * In de [sectie Toepassing Toewijzen aan een rol](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) u uw toepassing de *leesrol* toewijzen aan de brongroep voor uw cluster.

1. Klik terug in de **jenkins-taak** op het tabblad Acties na bouwen.
1. Selecteer in de vervolgkeuzelijst **Post-Build Actions** de optie **Post-Build Actions**. 
1. Klik onder **Clusterconfiguratie servicestructuur**op **Het cluster servicestructuur selecteren**. Klik **op Toevoegen** naast **Azure-referenties**. Klik **op Jenkins** om de Jenkins Credentials Provider te selecteren.
1. Selecteer microsoft Azure Service **Principal** in de vervolgkeuzelijst **'Referentieprovider Jenkins'** in de vervolgkeuzelijst 'Type'.
1. Gebruik de waarden die u hebt opgeslagen bij het instellen van de serviceprincipal in stap 1 om de volgende velden in te stellen:

   * **Client-id**: *toepassings-id*
   * **Clientgeheim:** *toepassingssleutel*
   * **Tenant-id:** *directory-id*
   * **Abonnement-id**: *Abonnements-id*
1. Voer een beschrijvende **id** in die u gebruikt om de referentie in Jenkins en een korte **beschrijving**te selecteren. Klik vervolgens op **Serviceprincipal verifiëren**. Als de verificatie slaagt, klikt u op **Toevoegen**.

   ![Servicefabric Jenkins voert Azure-referenties in](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. Controleer terug onder **clusterconfiguratie van Servicefabric**en controleer of uw nieuwe referentie is geselecteerd voor **Azure-referenties.** 
1. Selecteer in de vervolgkeuzelijst **Resourcegroep** de resourcegroep van het cluster waarop u de toepassing wilt implementeren.
1. Selecteer in de vervolgkeuzelijst **Service Fabric** het cluster waarop u de toepassing wilt implementeren.
1. Voer **voor clientsleutel** en **clientcert**de locatie van het PEM-bestand in uw Jenkins-container in. Bijvoorbeeld `/var/jenkins_home/clustercert.pem`. 
1. Configureer **onder Toepassingsconfiguratie**de velden **Toepassingsnaam**, **Toepassingstype**en (relatief) **pad naar toepassingsmanifest.**
    ![Actie voor servicefabric Jenkins na de build - Azure-referenties configureren](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Klik **op Configuratie verifiëren**. Klik bij geslaagde verificatie op **Opslaan**. Uw Jenkins-taakpijplijn is nu volledig geconfigureerd. Ga verder naar [Volgende stappen](#next-steps) om uw implementatie te testen.

## <a name="troubleshooting-the-jenkins-plugin"></a>Problemen met de Jenkins-invoegtoepassing oplossen

Als u bugs tegenkomt met de Jenkins-plug-ins, dient u een probleem op in de [Jenkins JIRA](https://issues.jenkins-ci.org/) voor de specifieke component.

## <a name="ideas-to-try"></a>Ideeën om te proberen

GitHub en Jenkins zijn nu geconfigureerd. Overweeg om een voorbeeldwijziging aan te brengen in `reliable-services-actor-sample/Actors/ActorCounter` https://github.com/Azure-Samples/service-fabric-java-getting-startedhet project in uw fork van de opslagplaats, . Duw uw wijzigingen `master` naar de externe vertakking (of een vertakking waarmee u hebt geconfigureerd). Op die manier wordt de geconfigureerde Jenkins-taak `MyJob` geactiveerd. Het haalt de wijzigingen op van GitHub, bouwt ze en implementeert de toepassing in het cluster dat u hebt opgegeven in post-buildacties.  

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Jenkins op Azure](/azure/Jenkins/)