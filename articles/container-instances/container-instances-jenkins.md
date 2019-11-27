---
title: Jenkins-Build op container exemplaar
description: Meer informatie over het configureren van een Jenkins-server voor het uitvoeren van build-taken op aanvraag in Azure Container Instances
ms.topic: article
ms.date: 08/31/2018
ms.openlocfilehash: e63ade82d4efeed40a9fba6f11d16131e8c728e7
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484065"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Azure Container Instances gebruiken als een Jenkins-build-agent

Azure Container Instances (ACI) biedt een on-demand, bebreekbaar en geïsoleerde omgeving voor het uitvoeren van container werkbelastingen. Vanwege deze kenmerken maakt ACI een geweldig platform voor het uitvoeren van Jenkins-build-taken op grote schaal. In dit artikel wordt uitgelegd hoe u een Jenkins-server implementeert die vooraf is geconfigureerd met ACI als een build-doel.

Zie [About Azure container instances][about-aci](Engelstalig) voor meer informatie over Azure container instances.

## <a name="deploy-a-jenkins-server"></a>Een Jenkins-server implementeren

1. Selecteer in de Azure Portal **een resource maken** en zoek naar **Jenkins**. Selecteer de Jenkins-aanbieding met een uitgever van **micro soft**en selecteer **maken**.

2. Voer de volgende informatie in het formulier **basis beginselen** in en selecteer **OK**.

   - **Naam**: Voer een naam in voor de Jenkins-implementatie.
   - **Gebruikers naam**: Voer een naam in voor de gebruiker met beheerders rechten van de virtuele machine Jenkins.
   - **Verificatie type**: we raden u aan een open bare SSH-sleutel te verifiëren voor authenticatie. Als u deze optie selecteert, plakt u een open bare SSH-sleutel die moet worden gebruikt voor aanmelding bij de virtuele machine Jenkins.
   - **Subscription**: selecteer een Azure-abonnement.
   - **Resourcegroep**: maak een nieuwe resourcegroep of selecteer een bestaande.
   - **Locatie**: Selecteer een locatie voor de Jenkins-server.

   ![Basis instellingen voor de implementatie van de Jenkins-Portal](./media/container-instances-jenkins/jenkins-portal-01.png)

3. Voer op het formulier **extra instellingen** de volgende items uit:

   - **Grootte**: Selecteer de juiste formaat optie voor de virtuele Jenkins-machine.
   - **VM-schijf type**: Geef **HDD** (harde schijf station) of **SSD** (Solid-State Drive) voor de Jenkins-server op.
   - **Virtueel netwerk**: Selecteer de pijl als u de standaard instellingen wilt wijzigen.
   - **Subnetten**: Selecteer de pijl, Controleer de gegevens en selecteer **OK**.
   - **Openbaar IP-adres**: Selecteer de pijl om het open bare IP-adres een aangepaste naam te geven, CONFIGUREER de SKU en stel de toewijzings methode in.
   - **Domeinnaam label**: Geef een waarde op voor het maken van een volledig gekwalificeerde URL naar de virtuele Jenkins-machine.
   - **Release type Jenkins**: Selecteer het gewenste release type uit de opties: **LTS**, **wekelijkse build**of **Azure geverifieerd**.

   ![Aanvullende instellingen voor de implementatie van de Jenkins-Portal](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Voor Service-Principal-integratie selecteert u **auto (MSI)** zodat [beheerde identiteiten voor Azure-resources][managed-identities-azure-resources] automatisch een verificatie-identiteit voor het Jenkins-exemplaar maken. Selecteer **hand matig** om uw eigen Service-Principal-referenties op te geven.

5. Cloud agenten configureren een op de cloud gebaseerd platform voor Jenkins build-taken. Selecteer **ACI**voor het omwille van dit artikel. Met de ACI-Cloud agent wordt elke Jenkins-build-taak uitgevoerd in een container exemplaar.

   ![Instellingen voor Cloud integratie voor de implementatie van de Jenkins-Portal](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Wanneer u klaar bent met de integratie-instellingen, selecteert u **OK**en selecteert u vervolgens **OK** op de samen vatting validatie. Selecteer **maken** in het **Gebruiksvoorwaarden** overzicht. Het duurt enkele minuten om de Jenkins-server te implementeren.

## <a name="configure-jenkins"></a>Jenkins configureren

1. Blader in het Azure Portal naar de resource groep Jenkins, selecteer de virtuele machine Jenkins en noteer de DNS-naam.

   ![DNS-naam in details over de virtuele machine Jenkins](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Blader naar de DNS-naam van de Jenkins-VM en kopieer de geretourneerde SSH-teken reeks.

   ![Jenkins-aanmeld instructies met SSH-teken reeks](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Open een terminal sessie in uw ontwikkel systeem en plak de SSH-teken reeks in de laatste stap. Werk `username` bij naar de gebruikers naam die u hebt opgegeven tijdens de implementatie van de Jenkins-server.

4. Wanneer de sessie is verbonden, voert u de volgende opdracht uit om het eerste beheerders wachtwoord op te halen:

   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Zorg ervoor dat de SSH-sessie en de tunnel actief blijven en ga naar `http://localhost:8080` in een browser. Plak het oorspronkelijke beheerders wachtwoord in het vak en selecteer vervolgens **door gaan**.

   ![Scherm ' Jenkins ontgrendelen ' met het vak voor het beheerders wachtwoord](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Selecteer **Aanbevolen invoeg toepassingen installeren** om alle aanbevolen Jenkins-invoeg toepassingen te installeren.

   ![Scherm ' Jenkins aanpassen ' met ' voorgestelde invoeg toepassingen installeren ' geselecteerd](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Een gebruikers account voor beheerders maken. Dit account wordt gebruikt voor aanmelding bij en het werken met uw Jenkins-exemplaar.

   ![Scherm eerste gebruiker voor beheerder maken, met referenties ingevuld](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Selecteer **opslaan en volt ooien**en selecteer vervolgens **starten met Jenkins** om de configuratie te volt ooien.

Jenkins is nu geconfigureerd en klaar om code te bouwen en te implementeren. Voor dit voor beeld wordt een eenvoudige Java-toepassing gebruikt om een Jenkins-Build op Azure Container Instances te demonstreren.

## <a name="create-a-build-job"></a>Een build-taak maken

Nu wordt een Jenkins-build-taak gemaakt om Jenkins-builds op een Azure-container exemplaar te demonstreren.

1. Selecteer **Nieuw item**, geef het build-project een naam zoals **ACI-demo**, selecteer **Freestyle project**en selecteer **OK**.

   ![In voor de naam van de build-taak en een lijst met project typen](./media/container-instances-jenkins/jenkins-new-job.png)

2. Zorg er onder **Algemeen**voor dat u wilt **beperken waar dit project kan worden uitgevoerd** is geselecteerd. Voer **Linux** in voor de label expressie. Deze configuratie zorgt ervoor dat deze build-taak wordt uitgevoerd op de ACI-Cloud.

   ![Tabblad algemeen met Configuratie Details](./media/container-instances-jenkins/jenkins-job-01.png)

3. Selecteer **Build-stap toevoegen** onder **Build**en selecteer **shell uitvoeren**. Voer `echo "aci-demo"` in als de opdracht.

   ![Tabblad samen stellen met selecties voor de stap build](./media/container-instances-jenkins/jenkins-job-02.png)

5. Selecteer **Opslaan**.

## <a name="run-the-build-job"></a>De build-taak uitvoeren

Als u de build-taak wilt testen en Azure Container Instances als het build-platform wilt observeren, moet u hand matig een build starten.

1. Selecteer **nu bouwen** om een build-taak te starten. Het duurt enkele minuten voordat de taak is gestart. U moet een status zien die vergelijkbaar is met de volgende afbeelding:

   ![Informatie over de build-geschiedenis met de taak status](./media/container-instances-jenkins/jenkins-job-status.png)

2. Terwijl de taak wordt uitgevoerd, opent u de Azure Portal en kijkt u naar de resource groep Jenkins. U ziet dat er een container exemplaar is gemaakt. De Jenkins-taak wordt uitgevoerd binnen dit exemplaar.

   ![Container instantie in de resource groep](./media/container-instances-jenkins/jenkins-aci.png)

3. Naarmate Jenkins meer taken uitvoert dan het geconfigureerde aantal Jenkins-uitvoerende machines (standaard 2), worden er meerdere container instanties gemaakt.

   ![Nieuw gemaakte container instanties](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Nadat alle build-taken zijn voltooid, worden de container instanties verwijderd.

   ![Resource groep waarvoor container instanties zijn verwijderd](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Problemen met de Jenkins-invoegtoepassing oplossen

Als u problemen ondervindt met de Jenkins-invoegtoepassingen, kunt u in [Jenkins JIRA](https://issues.jenkins-ci.org/) een ticket openen voor het specifieke onderdeel.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Jenkins op Azure [Azure en Jenkins][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-identities-azure-resources]: ../active-directory/managed-identities-azure-resources/overview.md
