---
title: Azure Service Fabric-plug-in voor Eclipse
description: Meer informatie over aan de slag gaan met Azure Service Fabric in Java met eclipse en de meegeleverde plug-in Service Fabric.
author: rapatchi
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: rapatchi
ms.openlocfilehash: b779873488f1fff754d4105249b28f545738c11b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258419"
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Service Fabric-invoegtoepassing voor de ontwikkeling van Eclipse Java-toepassingen
Eclipse is een van de meest gebruikte Integrated Development Environments (IDE's) voor Java-ontwikkelaars. In dit artikel wordt beschreven hoe u een Eclipse-ontwikkelomgeving instelt voor gebruik met Azure Service Fabric. Ontdek hoe u de Service Fabric-invoegtoepassing installeert en een Service Fabric-toepassing implementeert in een lokaal of extern Service Fabric-cluster in Eclipse. 

> [!NOTE]
> De Eclipse-invoegtoepassing wordt momenteel niet ondersteund in Windows. 

> [!IMPORTANT]
> Zorg ervoor dat JDK 8 op het systeem is geïnstalleerd en geselecteerd in Eclipse.

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse"></a>De Service Fabric-invoegtoepassing installeren of bijwerken in Eclipse
U kunt een Service Fabric-invoegtoepassing in Eclipse installeren. De invoegtoepassing vereenvoudigt het proces voor het maken en implementeren van Java-services.

> [!IMPORTANT]
> Voor de Service Fabric-invoegtoepassing is Eclipse Neon of een hogere versie vereist. Zie de instructies na deze opmerking voor het controleren van uw Eclipse-versie. Als er een oudere versie van Eclipse is geïnstalleerd, kunt u een nieuwere versie downloaden van de [site van Eclipse](https://www.eclipse.org). Het wordt afgeraden een oudere versie van Eclipse te overschrijven met een nieuwere versie. Verwijder de oudere versie voordat u het installatieprogramma uitvoert of installeer de nieuwe versie in een andere map. 
> 
> Voor Ubuntu wordt u aangeraden de installatie rechtstreeks vanaf de site van Eclipse uit te voeren en niet door middel van een installatieprogramma voor pakketten (`apt` of `apt-get`). Daardoor weet u zeker dat u de meest recente versie van Eclipse hebt. 

Installeer Eclipse Neon of hoger vanaf de [Eclipse-site](https://www.eclipse.org).  Installeer ook versie 2.2.1 of hoger van Buildship (de Service Fabric-invoegtoepassing is niet compatibel met oudere versies van Buildship):
-   Om de versies van geïnstalleerde componenten te controleren, in Eclipse, ga naar **Help** > **About Eclipse** > **Installatie details**.
-   Zie [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: Eclipse-invoegtoepassingen voor Gradle) als u Buildship wilt bijwerken.
-   Als u updates voor Eclipse wilt controleren en installeren, gaat u naar **Help** > **controleren op updates**.

Installeer de Service Fabric-plug-in in Eclipse en ga naar **Help nieuwe** > software te**installeren.**
1. Voer in het vak Werken\/ **met** https in: /dl.microsoft.com/eclipse.
2. Klik op**toevoegen**.

   ![De Service Fabric-invoegtoepassing voor Eclipse][sf-eclipse-plugin-install]
3. Selecteer de Fabric Service-invoegtoepassing en klik op **Next**.
4. Voer de installatiestappen uit en accepteer de licentievoorwaarden voor Microsoft-software.
  
Als u de Service Fabric-invoegtoepassing al hebt geïnstalleerd, zorgt u ervoor dat u de meest recente versie hebt geïnstalleerd. 
1. Ga naar **Help** > **About Eclipse** > **Installation Details**om te controleren op beschikbare updates. 
2. Selecteer Service Fabric in de lijst met geïnstalleerde invoegtoepassingen en klik op **Update**. Beschikbare updates worden geïnstalleerd.
3. Als u de Service Fabric-invoegtoepassing bijwerkt, moet u ook het Gradle-project vernieuwen.  Klik met de rechtermuisknop op **build.gradle** en selecteer vervolgens **Vernieuwen**.

> [!NOTE]
> Als de installatie of update van de Service Fabric-invoegtoepassing traag verloopt, kan dit het gevolg zijn van een instelling in Eclipse. Eclipse verzamelt metagegevens over alle wijzigingen in updatesites die zijn geregistreerd bij uw exemplaar van Eclipse. Als u het proces voor het controleren op en installeren van updates van Service Fabric-invoegtoepassingen wilt versnellen, gaat u naar **Available Software Sites**. Schakel de selectievakjes voor alle sites uit, behalve de selectievakjes die\/naar de plug-inlocatie Service Fabric verwijst (https: /dl.microsoft.com/eclipse/azure/servicefabric).

> [!NOTE]
>Als Eclipse op uw Mac niet werkt zoals u verwacht of als u het moet uitvoeren als supergebruiker), gaat u naar de map **ECLIPSE_INSTALLATION_PATH** en vervolgens naar de submap **Eclipse.app/Contents/MacOS**. Start Eclipse door `./eclipse` uit te voeren.


## <a name="create-a-service-fabric-application-in-eclipse"></a>Een Service Fabric-toepassing maken in Eclipse

1.  Ga in Eclipse naar **Bestand** > **Nieuwe** > **Andere**. Selecteer **Fabric Service Project** en klik op **Next**.

    ![Nieuw Service Fabric-project pagina 1][create-application/p1]

2.  Voer een naam in voor het project en klik op **Next**.

    ![Nieuw Service Fabric-project pagina 2][create-application/p2]

3.  Selecteer **Service Template** in de lijst met sjablonen. Selecteer het type servicesjabloon (Actor, Stateless, Container of Guest Binary) en klik op **Next**.

    ![Nieuw Service Fabric-project pagina 3][create-application/p3]

4.  Voer de servicenaam en servicegegevens in en klik op **Finish**.

    ![Nieuw Service Fabric-project pagina 4][create-application/p4]

5. Wanneer u uw eerste Service Fabric-project maakt, klikt u in het dialoogvenster **Open Associated Perspective** op **Yes**.

    ![Nieuw Service Fabric-project pagina 5][create-application/p5]

6.  Het nieuwe project ziet er als volgt uit:

    ![Nieuw Service Fabric-project pagina 6][create-application/p6]

## <a name="build-a-service-fabric-application-in-eclipse"></a>Bouw een Service Fabric applicatie in Eclipse

1.  Klik met de rechtermuisknop op de nieuwe Service Fabric-toepassing en selecteer **Service Fabric**.

    ![Snelmenu van Service Fabric][publish/RightClick]

2. Selecteer in het contextmenu een van de volgende opties:
    -   Klik op **Build Application** als u de toepassing wilt maken zonder op te schonen.
    -   Klik op **Rebuild Application** als u een schone build van de toepassing wilt maken.
    -   Klik op **Clean Application** als u de gebouwde artefacts uit de toepassing wilt verwijderen.
     
## <a name="deploy-a-service-fabric-application-to-the-local-cluster-with-eclipse"></a>Een Service Fabric-toepassing implementeren in het lokale cluster met Eclipse

Nadat u uw Service Fabric-toepassing hebt gebouwd, voert u deze stappen uit om deze te implementeren in het lokale cluster.

1. Als u het lokale cluster niet hebt gestart, volgt u de instructies in [Een lokaal cluster instellen](./service-fabric-get-started-linux.md#set-up-a-local-cluster) om uw lokale cluster te starten en te controleren of het wordt uitgevoerd.
2. Klik met de rechtermuisknop op de servicefabric-toepassing en selecteer **Vervolgens Service Fabric**.

    ![Snelmenu van Service Fabric][publish/RightClick]

3.  Klik in het contextmenu op **Toepassing implementeren**.
4.  U de voortgang van de implementatiebewerking volgen in het consolevenster.
5.  Als u wilt controleren of uw toepassing wordt uitgevoerd, opent [http://localhost:19080/Explorer](http://localhost:19080/Explorer)u Service Fabric Explorer op uw lokale cluster in een browservenster. Vouw het knooppunt **Toepassingen** uit en zorg ervoor dat uw toepassing wordt uitgevoerd. 

Zie [Een Java-service in Eclipse debuggen voor](./service-fabric-debugging-your-application-java.md)meer informatie over het opsporen van uw toepassing in Eclipse.

U uw toepassing ook implementeren in het lokale cluster met de opdracht **Toepassing publiceren:**

1. Klik met de rechtermuisknop op de servicefabric-toepassing en selecteer **Vervolgens Service Fabric**.
2. Klik in het contextmenu op **Toepassing publiceren...**.
3. Kies in het venster **Toepassingspubliceren** de optie **Profielen/Lokaal.json publiceren** als doelprofiel en klik op **Publiceren**.

    ![Het dialoogvenster Publiceren voor Local](./media/service-fabric-get-started-eclipse/localjson.png)

    Standaard is het publicatieprofiel Local.json ingesteld om te publiceren naar het lokale cluster. Zie de volgende sectie voor meer informatie over de verbindings- en eindpuntparameters in publicatieprofielen.

## <a name="publish-your-service-fabric-application-to-azure-with-eclipse"></a>Uw Service Fabric-toepassing publiceren naar Azure met Eclipse

Voer de volgende stappen uit om uw toepassing naar de cloud te publiceren:

1. Als u uw toepassing wilt publiceren naar een beveiligd cluster in de cloud, hebt u een X.509-certificaat nodig om met uw cluster te communiceren. In test- en ontwikkelomgevingen is het gebruikte certificaat vaak het clustercertificaat. In productieomgevingen moet het certificaat een clientcertificaat zijn dat zich onderscheidt van het clustercertificaat. Je hebt zowel het certificaat als de privésleutel nodig. Het certificaatbestand (en sleutel) moet opgemaakt zijn met PEM. U een PEM-bestand maken dat de certificaat- en privésleutel bevat uit een PFX-bestand met de volgende opdracht openssl:

    ```bash
    openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
    ```

   Als het PFX-bestand niet `--passin pass:` met een wachtwoord is beveiligd, gebruikt u de laatste parameter.

2. Open het **cloud.json-bestand** onder de map **Publicatieprofielen.** U moet het clustereindpunt en de beveiligingsreferenties op de juiste manier configureren voor uw cluster.

   - Het `ConnectionIPOrURL` veld bevat het IP-adres of de URL van uw cluster. Houd er rekening mee dat de`https://`waarde niet het URL-schema bevat ( ).
   - Standaard zou `ConnectionPort` het `19080`veld moeten zijn, tenzij u deze poort expliciet hebt gewijzigd voor uw cluster.
   - Het `ClientKey` veld moet wijzen op een PEM-geformatteerd .pem- of .key-bestand op uw lokale machine dat de privésleutel voor uw client of clustercertificaat bevat.
   - Het `ClientCert` veld moet wijzen op een PEM-geformatt- of .crt-bestand op uw lokale machine dat de certificaatgegevens voor uw client of cluster bevat. Certificaat. 

     ```bash
     {
         "ClusterConnectionParameters":
         {
            "ConnectionIPOrURL": "lnxxug0tlqm5.westus.cloudapp.azure.com",
            "ConnectionPort": "19080",
            "ClientKey": "[path_to_your_pem_file_on_local_machine]",
            "ClientCert": "[path_to_your_pem_file_on_local_machine]"
         }
     }
     ```

2. Klik met de rechtermuisknop op de servicefabric-toepassing en selecteer **Vervolgens Service Fabric**.
3. Klik in het contextmenu op **Toepassing publiceren...**.
3. Kies in het venster **Toepassings publiceren** de optie **PublishProfiles/Cloud.json** als doelprofiel en klik op **Publiceren**.

    ![Het dialoogvenster Publiceren voor Cloud](./media/service-fabric-get-started-eclipse/cloudjson.png)

4. U de voortgang van de publicatiebewerking volgen in het consolevenster.
5. Als u wilt controleren of uw toepassing wordt uitgevoerd, opent u Service Fabric Explorer op uw Azure-cluster in een browservenster. Voor het bovenstaande voorbeeld zou `https://lnxxug0tlqm5.westus.cloudapp.azure.com:19080/Explorer`dit zijn: . Vouw het knooppunt **Toepassingen** uit en zorg ervoor dat uw toepassing wordt uitgevoerd. 


Als uw toepassing betrouwbare services bevat, moet u bij beveiligde Linux-clusters ook een certificaat configureren dat uw services kunnen gebruiken om Service Fabric runtime API's aan te roepen. Zie Een [app Voor betrouwbare services configureren om te draaien op Linux-clusters](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters)voor meer informatie.

Zie [Quickstart: Een Java Reliable Services-toepassing implementeren](./service-fabric-quickstart-java-reliable-services.md)voor een snelle wandeling door het implementeren van een servicefabric betrouwbare services-toepassing die in Java is geschreven naar een beveiligd Linux-cluster.

## <a name="deploy-a-service-fabric-application-by-using-eclipse-run-configurations"></a>Een Service Fabric-toepassing implementeren met eclipse-runconfiguraties

U kunt de Service Fabric-toepassing ook implementeren met behulp van Eclipse-uitvoerconfiguraties.

1. Ga in Eclipse naar **Configuratie uitvoeren.** > **Run Configurations**
2. Selecteer onder **Gradle Project** de uitvoerconfiguratie **ServiceFabricDeployer**.
3. Controleer in het rechterdeelvenster op het tabblad **Argumenten** of **de**ip-, **poort-,** **client-, clientSleutel-** en **clientSleutelparameters** op de juiste manier zijn ingesteld voor uw implementatie. Standaard worden de parameters geïmplementeerd om te worden geïmplementeerd in het lokale cluster, zoals in de volgende schermafbeelding. Als u uw app wilt publiceren naar Azure, u de parameters wijzigen om de eindpuntgegevens en beveiligingsreferenties voor uw Azure-cluster te bevatten. Zie uw [Service Fabric-toepassing publiceren naar Azure met Eclipse](#publish-your-service-fabric-application-to-azure-with-eclipse)voor meer informatie.

    ![Configuratiedialoogvenster lokaal uitvoeren](./media/service-fabric-get-started-eclipse/run-config-local.png)

5. Zorg ervoor dat **Working Directory** verwijst naar de toepassing die u wilt implementeren. Als u de toepassing wilt wijzigen, klikt u op de knop **Workspace** en selecteert u de gewenste toepassing.
6. Klik op **Apply** en vervolgens op **Run**.

De toepassing is binnen enkele ogenblikken gemaakt en geïmplementeerd. U kunt de implementatiestatus controleren in Service Fabric Explorer.  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>Een Service Fabric-service toevoegen aan uw Service Fabric-toepassing

Voer de volgende stappen uit als u een Service Fabric-service aan een bestaande Service Fabric-toepassing wilt toevoegen:

1.  Klik met de rechtermuisknop op het project waaraan u een service wilt toevoegen en klik vervolgens op **Service Fabric**.

    ![Service aan Service Fabric toevoegen pagina 1][add-service/p1]

2.  Klik op **Add Service Fabric Service** en voer de stappen uit om een service aan het project toe te voegen.
3.  Selecteer de servicesjabloon die u aan het project wilt toevoegen en klik op **Next**.

    ![Service aan Service Fabric toevoegen pagina 2][add-service/p2]

4.  Voer de servicenaam (en andere gevraagde informatie) in en klik op de knop **Add Service**.  

    ![Service aan Service Fabric toevoegen pagina 3][add-service/p3]

5.  Nadat de service is toegevoegd, ziet de algehele projectstructuur er ongeveer als volgt uit:

    ![Service aan Service Fabric toevoegen pagina 4][add-service/p4]

## <a name="edit-manifest-versions-of-your-service-fabric-java-application"></a>Manifestversies van uw Service Fabric Java-toepassing bewerken

Als u manifestversies wilt bewerken, klikt u met de rechtermuisknop op het project, gaat u naar **Service Fabric** en selecteert u **Edit Manifest Versions...** in de vervolgkeuzelijst. In de wizard kunt u de manifestversies bijwerken voor het toepassingsmanifest, servicemanifest en de versies voor de pakketten **Code**, **Config** en **Data**.

Als u de optie **Automatically update application and service versions** inschakelt en vervolgens een versie bijwerkt, worden de manifestversies automatisch bijgewerkt. Als u bijvoorbeeld eerst het selectievakje inschakelt, vervolgens de versie van **Code** bijwerkt van 0.0.0 naar 0.0.1 en op **Finish** klikt, worden de versies van het servicemanifest en toepassingsmanifest automatisch bijgewerkt naar 0.0.1.

## <a name="upgrade-your-service-fabric-java-application"></a>Uw Service Fabric Java-toepassing upgraden

Stel dat u voor een upgradescenario het project **App1** hebt gemaakt met behulp van de Service Fabric-invoegtoepassing in Eclipse. U hebt dit vervolgens met behulp van de invoegtoepassing geïmplementeerd om een toepassing met de naam **fabric:/App1Application** te maken. Het toepassingstype is **App1ApplicationType**en de toepassingsversie is 1.0. Nu wilt u een toepassingsupgrade uitvoeren zonder de beschikbaarheid van de toepassing te onderbreken.

Breng eerst eventuele wijzigingen aan in de toepassing en bouw vervolgens de gewijzigde service opnieuw op. Werk het manifestbestand van de gewijzigde service (ServiceManifest.xml) bij met de bijgewerkte versies voor de service (en code, configuratie of gegevens, indien van toepassing). Wijzig ook het toepassingsmanifest (ApplicationManifest.xml) met het bijgewerkte versienummer voor de toepassing en de gewijzigde service.  

Als u de toepassingsupgrade wilt uitvoeren met behulp van Eclipse, kunt u een dubbel uitvoerconfiguratieprofiel maken. Vervolgens gebruikt u dit om de toepassingsupgrade uit te voeren.

1.  Ga naar**Configuraties** **uitvoeren** > . Klik in het linkerdeelvenster op de kleine pijl, links van **Gradle Project**.
2.  Klik met de rechtermuisknop op **ServiceFabricDeployer** en selecteer **Duplicate**. Voer een nieuwe naam in voor deze configuratie, bijvoorbeeld **ServiceFabricUpgrader**.
3.  Ga in het rechterdeelvenster naar het tabblad **Arguments** en wijzig **-Pconfig='deploy'** in **-Pconfig=upgrade**. Klik vervolgens op **Apply**.

Met dit proces wordt een uitvoerconfiguratieprofiel gemaakt en opgeslagen dat u op elk gewenst moment kunt gebruiken om een upgrade van uw toepassing uit te voeren. Hiermee wordt ook de laatst bijgewerkte versie van het toepassingstype opgehaald uit het manifest-bestand van de toepassing.

De toepassingsupgrade duurt enkele minuten. U kunt de upgrade van de toepassing bewaken in Service Fabric Explorer.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Oude Service Fabric Java-toepassingen migreren die moeten worden gebruikt met Maven
We hebben onlangs de bibliotheken van Java Service Fabric verplaatst van de Service Fabric Java SDK naar de Maven-opslagplaats. De nieuwe toepassingen die u genereert met behulp van Eclipse produceren projecten die u zonder problemen kunt gebruiken met Maven. Uw bestaande stateless Service Fabric- of Java-actortoepassingen zult u echter moeten bijwerken om ze te laten werken met de Service Fabric Java-afhankelijkheden van Maven. Deze oudere toepassingen maken namelijk nog gebruik van de Service Fabric Java SDK. Volg [deze stappen](service-fabric-migrate-old-javaapp-to-use-maven.md) om te controleren of een oudere toepassing werkt met Maven.

## <a name="next-steps"></a>Volgende stappen

- Zie [Quickstart: Een Java Reliable Services-toepassing implementeren](./service-fabric-quickstart-java-reliable-services.md)voor snelle stappen bij het bouwen van java betrouwbare servicetoepassingen en deze lokaal en naar Azure te implementeren.
- Zie [Een Java-service in Eclipse debuggen](./service-fabric-debugging-your-application-java.md)voor meer informatie over het opsporen van een Java-toepassing op uw lokale cluster.
- Zie Services controleren [en diagnosticeren in een lokale machineontwikkelingsinstallatie](./service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)voor meer informatie over het monitoren en diagnosticeren van Service Fabric-toepassingen.

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-eclipse/service-fabric-eclipse-plugin.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
