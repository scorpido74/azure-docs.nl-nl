---
title: Jenkins-implementaties schalen met Azure VM-agents.
description: Voeg meer capaciteit toe aan uw Jenkins-pijplijnen met virtuele Azure-machines met de Jenkins Azure VM Agent-invoegtoepassing.
keywords: ansible, azure, devops, virtuele machine, agents
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 2e811d628c017316a5bc50a8ddc22ee24d6f744e
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158527"
---
# <a name="scale-your-jenkins-deployments-to-meet-demand-with-azure-vm-agents"></a>Uw Jenkins-implementaties schalen met Azure VM-agents om te voldoen aan de vraag

In dezelfde zelfstudie leert u hoe u de [Azure VM Agents-invoegtoepassing](https://plugins.jenkins.io/azure-vm-agents) voor Jenkins gebruikt om meer capaciteit op aanvraag toe te voegen met virtuele Linux-machines in Azure.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De Azure VM Agents-invoegtoepassing installeren
> * De invoegtoepassing configureren om resources te maken in uw Azure-abonnement
> * De rekenresources instellen die voor elke agent beschikbaar zijn
> * Het besturingssysteem en de hulpprogramma's instellen die op elke agent zijn geïnstalleerd
> * Een nieuwe Jenkins-freestyle-taak maken
> * De taak uitvoeren in een Azure VM-agent

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents/player]

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement
* Een Jenkins-masterserver. Als u er geen hebt, raadpleegt u de [snelstart](install-jenkins-solution-template.md) om er een in Azure in te stellen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-vm-agents-plugin"></a>Azure VM Agents-invoegtoepassing installeren

> [!TIP]
> De Azure VM Agent-invoegtoepassing is al geïnstalleerd als u Jenkins hebt geïnstalleerd met het [oplossingssjabloon](install-jenkins-solution-template.md).

1. Selecteer in het Jenkins-dashboard **Jenkins beheren** en vervolgens **Plugins beheren**.
1. Selecteer het tabblad **Beschikbaar** en zoek vervolgens naar **Azure VM-agents**. Vink het selectievakje naast de vermelding voor de invoegtoepassing aan en selecteer **Installeren zonder opnieuw opstarten** onderaan het dashboard.

## <a name="configure-the-azure-vm-agents-plugin"></a>De Azure VM Agents-invoegtoepassing configureren

1. Selecteer in het Jenkins-dashboard **Jenkins beheren** en vervolgens **Systeem configureren**.
1. Ga naar de onderkant van de pagina en zoek naar de sectie **Cloud** met de vervolgkeuzelijst **Nieuwe cloud toevoegen** en kies **Microsoft Azure VM-agents**.
1. Selecteer een bestaande service-principal uit de vervolgkeuzelijst **Toevoegen** in de sectie **Azure-referenties**. Als er geen staat vermeld, voert u de volgende stappen uit om een [service-principal te maken](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager) voor uw Azure-account en deze toe te voegen aan uw Jenkins-configuratie:   

    a. Selecteer **Toevoegen** naast **Azure-referenties** en kies **Jenkins**.   
    b. In het dialoogvenster **Referenties toevoegen** selecteert u **Microsoft Azure-service-principal** uit de vervolgkeuzelijst **Soort**.   
    c. Maak een Active Directory-service-principal vanuit de Azure CLI of [Cloud Shell](/azure/cloud-shell/overview).
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
    d. Voer de referenties vanuit de service-principal in het dialoogvenster **Referenties toevoegen** in. Als u uw Azure-abonnements-id niet weet, kunt u deze opvragen via de CLI:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    De voltooide service-principal moet het veld `id` gebruiken voor **Abonnements-id**, de waarde `appId` voor **Client-id**, `password` voor **Clientgeheim** en `tenant` voor **Tenant-id**. Selecteer **Toevoegen** om de service-principal toe te voegen en vervolgens de invoegtoepassing te configureren om met de nieuwe referentie te gebruiken.

    ![Azure Service Principal configureren](./media/jenkins-azure-vm-agents/new-service-principal.png)

    

1. In de sectie **Naam resourcegroep** selecteert u **Nieuwe maken** en voert u `myJenkinsAgentGroup` in.
1. Selecteer **Configuratie verifiëren** om verbinding te maken met Azure en de profielinstellingen te testen.
1. Selecteer **Toepassen** om de configuratie van de invoegtoepassing bij te werken.

## <a name="configure-agent-resources"></a>Agent-resources configureren

Configureer een sjabloon dat u wilt gebruiken om een Azure VM-agent te definiëren. Dit sjabloon definieert de rekenresources die elke agent bij het maken heeft.

1. Selecteer **Toevoegen** naast **Sjabloon voor virtuele Azure-machine toevoegen**.
1. Voer `defaulttemplate` in als **Naam**
1. Voer `ubuntu` in als **Label**
1. Selecteer de gewenste [Azure-regio](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) uit de keuzelijst met invoervak.
1. Selecteer een [VM-grootte](/azure/virtual-machines/linux/sizes) in de vervolgkeuzelijst onder **Virtual Machine Size**. Een algemene grootte van `Standard_DS1_v2` is prima voor deze zelfstudie.   
1. Houd de **Retentietijd** op `60`. Deze instelling bepaalt het aantal minuten dat Jenkins moet wachten voordat de toewijzing van inactieve agents ongedaan wordt gemaakt. Geef 0 op als u niet wilt dat inactieve agents automatisch worden verwijderd.

   ![Algemene VM-configuratie](./media/jenkins-azure-vm-agents/general-config.png)

## <a name="configure-agent-operating-system-and-tools"></a>Besturingssysteem en hulpprogramma's van agent configureren

In de sectie **Configuratie van installatiekopie** in de configuratie van de invoertoepassing selecteert u **Ubuntu 16.04 LTS**. Vink de vakjes naast **Git installeren (Meest recente)** , **Maven installeren (V3.5.0)** en **Docker installeren** aan om deze hulpprogramma's te installeren op de nieuwe agents.

![Besturingssysteem en hulpprogramma's VM configureren](./media/jenkins-azure-vm-agents/jenkins-os-config.png)

Selecteer **Toevoegen** naast **Beheerreferenties** en selecteer vervolgens **Jenkins**. Voer een gebruikersnaam en wachtwoord in die worden gebruikt voor aanmelding bij de agents en zorg ervoor dat deze voldoen aan het [beleid voor gebruiksnaam en wachtwoord](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) voor administratieve accounts op virtuele Azure-machines.

Selecteer **Sjabloon verifiëren** om de configuratie te verifiëren en selecteer vervolgens **Opslaan** om uw wijzigingen op te slaan en terug te keren naar het Jenkins-dashboard.

## <a name="create-a-job-in-jenkins"></a>Een taak maken in Jenkins

1. Klik in het dashboard van Jenkins op **New Item**. 
1. Voer `demoproject1` in voor de naam en selecteer **Freestyle-project** en vervolgens **OK**.
1. Schakel op het tabblad **Algemeen** het selectievakje **Beperken waar dit project kan worden uitgevoerd** in en typ `ubuntu` in het vak **Labelexpressie**. U ziet een bevestigingsbericht dat het label wordt bediend door de cloudconfiguratie die in de vorige stap is gemaakt. 
   ![Een taak instellen](./media/jenkins-azure-vm-agents/job-config.png)
1. Selecteer **Git** in het tabblad **Broncodebeheer** en voeg de volgende URL toe in het veld **URL van opslagplaats**: `https://github.com/spring-projects/spring-petclinic.git`
1. In het tabblad **Compileren** selecteert u **Compilatiestap toevoegen** en vervolgens **Top-level Maven-doelstellingen aanroepen**. Voer `package` in het veld **Doelstellingen** in.
1. Selecteer **Opslaan** om de taakdefinitie op te slaan.

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>De nieuwe taak in een Azure VM-agent bouwen

1. Ga terug naar het dashboard van Jenkins.
1. Selecteer de taak die u in de vorige stap hebt gemaakt en klik op **Nu compileren**. Er wordt een nieuwe build in de wachtrij geplaatst, maar deze begint niet totdat een VM-agent is gemaakt in uw Azure-abonnement.
1. Zodra de bewerking is voltooid, gaat u naar **Console Output**. U ziet dat de bewerking op afstand is uitgevoerd in een Azure-agent.

![Console-uitvoer](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Problemen met de Jenkins-invoegtoepassing oplossen

Als u problemen ondervindt met de Jenkins-invoegtoepassingen, kunt u in [Jenkins JIRA](https://issues.jenkins-ci.org/) een ticket openen voor het specifieke onderdeel.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [CI/CD naar Azure App Service](java-deploy-webapp-tutorial.md)