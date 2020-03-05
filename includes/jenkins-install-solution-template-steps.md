---
author: tomarcher
ms.service: jenkins
ms.topic: include
ms.date: 03/03/2020
ms.author: tarcher
ms.openlocfilehash: 2468dc72881755a2990e8ddf8112d7fe27f64f4d
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274877"
---
## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement
* Toegang tot SSH vanaf de opdrachtregel van uw computer (zoals de Bash-shell of [PuTTY](https://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>De virtuele machine met Jenkins maken van de oplossingssjabloon
Jenkins biedt ondersteuning voor een model waarin de Jenkins-server werk delegeert aan een of meer agents, zodat een enkele Jenkins-installatie een groot aantal projecten kan hosten of verschillende omgevingen kan leveren die nodig zijn voor maakacties of het uitvoeren van tests. De stappen in deze sectie helpen u bij het installeren en configureren van een Jenkins-server op Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-jenkins"></a>Verbinding maken met Jenkins

Navigeer naar uw virtuele machine (bijvoorbeeld `http://jenkins2517454.eastus.cloudapp.azure.com/`) in uw webbrowser. De Jenkins-console is niet toegankelijk via onbeveiligde HTTP. Om die reden bevat de pagina instructies om de console veilig vanaf uw computer te gebruiken via een SSH-tunnel.

![Jenkins ontgrendelen](./media/jenkins-install-solution-template-steps/jenkins-ssh-instructions.png)

Stel de tunnel op de pagina in met behulp van de opdracht `ssh` op de opdrachtregel. Vervang `username` hierbij door de naam van de gebruiker met beheerdersrechten van de virtuele machine die u eerder hebt gekozen bij het instellen van de virtuele machine op basis van de oplossingssjabloon.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

Nadat u de tunnel hebt gestart, gaat u naar `http://localhost:8080/` op uw lokale computer. 

Vraag het initiële wachtwoord op door de volgende opdracht uit te voeren op de opdrachtregel terwijl u via SSH bent verbonden met de Jenkins-VM.

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Gebruik dit initiële wachtwoord om het Jenkins-dashboard voor de eerste keer te ontgrendelen.

![Jenkins ontgrendelen](./media/jenkins-install-solution-template-steps/jenkins-unlock.png)

Selecteer **Install suggested plugins** op de volgende pagina en maak vervolgens een Jenkins-gebruiker met beheerdersrechten om toegang te krijgen tot het Jenkins-dashboard.

![Jenkins is klaar.](./media/jenkins-install-solution-template-steps/jenkins-welcome.png)

De Jenkins-server is nu klaar voor het bouwen van code.

## <a name="create-your-first-job"></a>Uw eerste taak maken

Selecteer **Create new jobs** in de Jenkins-console, geef het project de naam **mySampleApp**, selecteer **Freestyle project** en selecteer **OK**.

![Een nieuwe taak maken](./media/jenkins-install-solution-template-steps/jenkins-new-job.png) 

Selecteer het tabblad **Source Code Management**, schakel het keuzerondje **Git** in en voer in het veld **Repository URL** de volgende URL in: `https://github.com/spring-guides/gs-spring-boot.git`

![De Git-opslagplaats definiëren](./media/jenkins-install-solution-template-steps/jenkins-job-git-configuration.png) 

Selecteer het tabblad **Build** en selecteer vervolgens **Add build step**, **Invoke Gradle script**. Selecteer **Use Gradle Wrapper** en voer vervolgens `complete` in bij **Wrapper location** en `build` bij **Tasks**.

![De Gradle-wrapper gebruiken om de code te bouwen](./media/jenkins-install-solution-template-steps/jenkins-job-gradle-config.png) 

Selecteer **Geavanceerd** en voer vervolgens in het veld `complete`Root Build script**de waarde** in. Selecteer **Opslaan**.

![Geavanceerde instellingen opgeven in de stap voor het bouwen van de Gradle-wrapper](./media/jenkins-install-solution-template-steps/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>De code bouwen

Selecteer **Build Now** om de code te compileren en een pakket te maken van de voorbeeld-app. Als de build is voltooid, selecteert u de koppeling **Workspace** voor het project.

![Bladeren naar de werkruimte om het JAR-bestand van de build te zoeken](./media/jenkins-install-solution-template-steps/jenkins-access-workspace.png) 

Navigeer naar `complete/build/libs` en controleer of u daar het bestand `gs-spring-boot-0.1.0.jar` ziet, zodat u weet dat de build is gelukt. De Jenkins-server is nu gereed voor het bouwen van uw eigen projecten in Azure.

## <a name="troubleshooting-the-jenkins-solution-template"></a>Het oplossen van problemen met de Jenkins-oplossingssjabloon

Als er zich fouten voordoen met de Jenkins-oplossingssjabloon, dient u een probleem in de [GitHub-opslagplaats van Jenkins ](https://github.com/azure/jenkins/issues) te rapporteren.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Virtuele Azure-machines toevoegen als Jenkins-agents](/azure/jenkins-azure-vm-agents)