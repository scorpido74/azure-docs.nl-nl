---
title: Jenkins bouwt voort op containerexemplaar
description: Meer informatie over het configureren van een Jenkins-server om buildtaken on-demand uit te voeren in Azure Container Instances
ms.topic: article
ms.date: 08/31/2018
ms.openlocfilehash: 8bb84895fb581053248fbad326ea7b2c8d1873a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617968"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Azure Container Instances gebruiken als jenkins-buildagent

Azure Container Instances (ACI) biedt een on-demand, burstable en geïsoleerde omgeving voor het uitvoeren van containerized workloads. Vanwege deze kenmerken is ACI een geweldig platform voor het op grote schaal uitvoeren van Jenkins-buildtaken. Dit artikel loopt door het implementeren en gebruiken van een Jenkins-server die vooraf is geconfigureerd met ACI als builddoel.

Zie [Over Azure Container Instances](../container-instances/container-instances-overview.md)voor meer informatie over Azure Container Instances .

## <a name="deploy-a-jenkins-server"></a>Een Jenkins-server implementeren

1. Selecteer in de Azure-portal De optie **Een bron maken** en zoeken naar **Jenkins**. Selecteer het Jenkins-aanbod met een uitgever van **Microsoft**en selecteer **Vervolgens Maken**.

2. Voer de volgende informatie in op het **formulier Basisbeginselen** en selecteer **OK**.

   - **Naam:** Voer een naam in voor de Jenkins-implementatie.
   - **Gebruikersnaam:** Voer een naam in voor de beheerdersgebruiker van de virtuele jenkins-machine.
   - **Verificatietype:** We raden een Openbare SSH-sleutel aan voor verificatie. Als u deze optie selecteert, plakt u een openbare SSH-sleutel in om in te loggen op de virtuele jenkins-machine.
   - **Subscription**: selecteer een Azure-abonnement.
   - **Resourcegroep**: maak een nieuwe resourcegroep of selecteer een bestaande.
   - **Locatie:** Selecteer een locatie voor de Jenkins-server.

   ![Basisinstellingen voor de implementatie van Jenkins-portalen](./media/container-instances-jenkins/jenkins-portal-01.png)

3. Vul op het formulier **Aanvullende instellingen** de volgende items in:

   - **Grootte:** Selecteer de juiste maatoptie voor uw virtuele Jenkins-machine.
   - **VM-schijftype:** Geef **hdd** (harde schijf) of **SSD** (solid-state drive) op voor de Jenkins-server.
   - **Virtueel netwerk**: Selecteer de pijl als u de standaardinstellingen wilt wijzigen.
   - **Subnetten:** Selecteer de pijl, controleer de informatie en selecteer **OK**.
   - **Openbaar IP-adres:** selecteer de pijl om het openbare IP-adres een aangepaste naam te geven, configureer de SKU en stel de toewijzingsmethode in.
   - **Label voor domeinnaam:** geef een waarde op om een volledig gekwalificeerde URL naar de virtuele jenkins-machine te maken.
   - **Jenkins release type:** Selecteer het gewenste releasetype uit de opties: **LTS,** **Weekly build**of **Azure Verified**.

   ![Aanvullende instellingen voor de implementatie van Jenkins-portalen](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Selecteer voor serviceprincipal-integratie De optie **Auto(MSI)** om [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) te beheren, maakt automatisch een verificatie-identiteit voor het voorbeeld Jenkins. Selecteer **Handmatig** om uw eigen servicehoofdreferenties op te geven.

5. Cloudagents configureren een cloudgebaseerd platform voor Jenkins build-taken. Selecteer IN het belang van dit artikel **ACI**. Met de ACI-cloudagent wordt elke Jenkins-buildtaak uitgevoerd in een containerinstantie.

   ![Cloudintegratie-instellingen voor jenkins-portalimplementatie](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Wanneer u klaar bent met de integratie-instellingen, selecteert u **OK**en selecteert u **ok** opnieuw in het validatieoverzicht. Selecteer **Maken** in het overzicht van de **gebruiksvoorwaarden.** De Jenkins-server duurt een paar minuten om te implementeren.

## <a name="configure-jenkins"></a>Jenkins configureren

1. Blader in de Azure-portal naar de jenkins-brongroep, selecteer de virtuele jenkins-machine en noteer de DNS-naam.

   ![DNS-naam in details over de virtuele machine jenkins](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Blader naar de DNS-naam van de Jenkins VM en kopieer de geretourneerde SSH-tekenreeks.

   ![Jenkins inloginstructies met SSH-tekenreeks](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Open een terminalsessie op uw ontwikkelingssysteem en plak de SSH-tekenreeks vanaf de laatste stap. Werk `username` bij naar de gebruikersnaam die u hebt opgegeven toen u de Jenkins-server hebt geïmplementeerd.

4. Nadat de sessie is verbonden, voert u de volgende opdracht uit om het oorspronkelijke beheerderswachtwoord op te halen:

   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Laat de SSH-sessie en tunnel `http://localhost:8080` draaien en ga naar in een browser. Plak het oorspronkelijke beheerderswachtwoord in het vak en selecteer **Doorgaan**.

   ![Scherm 'Jenkins ontgrendelen' met de doos voor het beheerderswachtwoord](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Selecteer **Aanbevolen plug-ins installeren** om alle aanbevolen Jenkins-plug-ins te installeren.

   !["Jenkins aanpassen" scherm met "Installeer voorgestelde plugins" geselecteerd](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Maak een beheerdersgebruikersaccount aan. Dit account wordt gebruikt om in te loggen op en te werken met uw Jenkins-exemplaar.

   ![Scherm 'Eerste beheerder maken' scherm, met ingevulde referenties](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Selecteer **Opslaan en voltooien**en selecteer Vervolgens Jenkins **starten** om de configuratie te voltooien.

Jenkins is nu geconfigureerd en klaar om code te bouwen en te implementeren. In dit voorbeeld wordt een eenvoudige Java-toepassing gebruikt om een Jenkins-build op Azure Container Instances te demonstreren.

## <a name="create-a-build-job"></a>Een buildtaak maken

Nu wordt een Jenkins-buildtaak gemaakt om jenkins te demonstreren die voortbouwt op een Azure-containerexemplaar.

1. Selecteer **Nieuw item,** geef het buildproject een naam zoals **aci-demo,** selecteer **Freestyle-project**en selecteer **OK**.

   ![Vak voor de naam van de buildtaak en lijst met projecttypen](./media/container-instances-jenkins/jenkins-new-job.png)

2. Controleer **onder Algemeen**of Beperken waar dit project kan worden **uitgevoerd,** is geselecteerd. Voer **linux** in voor de labelexpressie. Deze configuratie zorgt ervoor dat deze buildtaak wordt uitgevoerd op de ACI-cloud.

   ![Tabblad 'Algemeen' met configuratiegegevens](./media/container-instances-jenkins/jenkins-job-01.png)

3. Selecteer **onder Bouwen**de optie **Buildstap toevoegen** en selecteer Shell **uitvoeren**. Voer `echo "aci-demo"` in als opdracht.

   ![Tabblad 'Bouwen' met selecties voor de buildstap](./media/container-instances-jenkins/jenkins-job-02.png)

5. Selecteer **Opslaan**.

## <a name="run-the-build-job"></a>De bouwtaak uitvoeren

Als u de buildtaak wilt testen en Azure Container Instances als het buildplatform wilt observeren, start u handmatig een build.

1. Selecteer **Nu bouwen** om een buildtaak te starten. Het duurt een paar minuten voordat de klus begint. U ziet een status die vergelijkbaar is met de volgende afbeelding:

   ![Informatie over 'Geschiedenis bouwen' met functiestatus](./media/container-instances-jenkins/jenkins-job-status.png)

2. Terwijl de taak wordt uitgevoerd, opent u de Azure-portal en bekijkt u de jenkins-brongroep. U moet zien dat er een containerinstantie is gemaakt. De Jenkins-klus loopt in dit geval.

   ![Containerinstantie in de resourcegroep](./media/container-instances-jenkins/jenkins-aci.png)

3. Aangezien Jenkins meer taken uitvoert dan het geconfigureerde aantal Jenkins-uitvoerders (standaard 2), worden meerdere containerexemplaren gemaakt.

   ![Nieuw gemaakte containerexemplaren](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Nadat alle bouwtaken zijn voltooid, worden de containerexemplaren verwijderd.

   ![Resourcegroep waarbij containerexemplaren zijn verwijderd](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Problemen met de Jenkins-invoegtoepassing oplossen

Als u bugs tegenkomt met de Jenkins-plug-ins, dient u een probleem op in de [Jenkins JIRA](https://issues.jenkins-ci.org/) voor de specifieke component.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [CI/CD naar Azure App Service](java-deploy-webapp-tutorial.md)