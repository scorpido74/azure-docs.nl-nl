---
title: Zelf studie-CI/CD van Jenkins naar Azure Vm's met Azure DevOps Services
description: In deze zelfstudie leert u hoe u continue integratie (CI) en continue implementatie (CD) van een Node.js-app kunt instellen met Jenkins voor Azure VM's vanuit Release Management in Visual Studio Team Services of Microsoft Team Foundation Server
keywords: Jenkins, azure, devops, virtual machine, cicd
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: cae28b293a6217996b44c839dc8836ec940c3155
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78192605"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-with-using-jenkins-and-azure-devops-services"></a>Zelfstudie: Uw app implementeren voor Linux Virtual Machines in Azure met Jenkins en Azure DevOps Services

Continue integratie en continue implementatie vormen een pijplijn waarmee u uw code kunt maken, vrijgeven en implementeren. Azure DevOps Services bevat een volledige set met CI/CD automatiseringsfuncties voor de implementatie naar Azure. Jenkins is een populair extern CI/CD-serverhulpprogramma dat ook CI/CD-automatisering bevat. U kunt Azure DevOps Services en Jenkins samen gebruiken als u de manier waarop u uw cloud-app of -service levert, wilt aanpassen.

In deze zelfstudie gebruikt u Jenkins om een Node.js-web-app te maken. Vervolgens gebruikt u Azure DevOps om deze te implementeren

naar een [implementatiegroep](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups/index?view=vsts) die virtuele Linux-machines (VM's) bevat. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * De voorbeeld-app downloaden.
> * Jenkins-invoegtoepassingen configureren.
> * Een Jenkins Freestyle-project configureren voor Node.js.
> * Jenkins configureren voor de integratie met Azure DevOps Services.
> * Een Jenkins-service-eindpunt maken.
> * Een implementatiegroep voor de virtuele Azure-machines maken.
> * Een release pijplijn voor een Azure-pijp lijn maken.
> * Handmatige en door CI geactiveerde implementaties uitvoeren.

## <a name="before-you-begin"></a>Voordat u begint

* U hebt toegang nodig tot een Jenkins-server. Zie [Een Jenkins-hoofdserver maken op een virtuele Azure-machine](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template) als u nog geen Jenkins-server hebt gemaakt. 

* Meld u aan bij uw Azure DevOps Services-organisatie (**https://{uworganisatie}.visualstudio.com**). 
  U kunt een [gratis Azure DevOps Services-organisatie](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308) krijgen.

  > [!NOTE]
  > Zie [Verbinding maken met Azure DevOps Services](https://docs.microsoft.com/azure/devops/organizations/projects/connect-to-projects?view=vsts) voor meer informatie.

*  U hebt een virtuele Linux-machine nodig voor een implementatiedoel.  Zie [Virtuele Linux-machines maken en beheren met de Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm) voor meer informatie.

*  Open de binnenkomende poort 80 voor uw virtuele machine. Zie [Netwerkbeveiligingsgroepen maken met Azure Portal](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic) voor meer informatie.

## <a name="get-the-sample-app"></a>De voorbeeld-app downloaden

U hebt een app, die is opgeslagen in een Git-opslagplaats, nodig voor het implementeren.
Voor deze zelfstudie raden we u aan gebruik te maken van [deze voorbeeld-app die beschikbaar is via GitHub](https://github.com/azure-devops/fabrikam-node). Deze zelfstudie bevat een voorbeeldscript dat wordt gebruikt voor het installeren van Node.js en een toepassing. Als u met uw eigen opslagplaats wilt werken, moet u een vergelijkbaar voorbeeld configureren.

Maak een fork van deze app en noteer de locatie (URL) voor gebruik in latere stappen van deze zelfstudie. Zie [Een fork van een opslagplaats maken](https://help.github.com/articles/fork-a-repo/) voor meer informatie.    

> [!NOTE]
> De app is gemaakt met [Yeoman](https://yeoman.io/learning/index.html). Hierin is gebruikgemaakt van Express, Bower en Grunt. Daarnaast bevat deze een aantal npm-pakketten als afhankelijkheden.
> Het voorbeeld bevat ook een script waarmee Nginx wordt ingesteld en de app wordt geïmplementeerd. Deze wordt uitgevoerd op de virtuele machines. Met name met het script:
> 1. Worden Node, Nginx en PM2 geïnstalleerd.
> 2. Worden Nginx en PM2 geconfigureerd.
> 3. Wordt de Node-app gestart.

## <a name="configure-jenkins-plug-ins"></a>Jenkins-invoegtoepassingen configureren

Eerst moet u twee Jenkins-invoegtoepassingen configureren: **NodeJS** en **Continue implementatie van VS Team Services**.

1. Open uw Jenkins-account en selecteer **Jenkins beheren**.
2. Selecteer op de pagina **Jenkins beheren** de optie **Invoegtoepassingen beheren**.
3. Filter de lijst om de invoegtoepassing **NodeJS** te zoeken en selecteer de optie **installeren zonder opnieuw opstarten**.
    ![De invoegtoepassing NodeJS toevoegen aan Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Filter de lijst om de invoegtoepassing **Continue implementatie van VS Team Services** te zoeken en selecteer de optie **Installeren zonder opnieuw opstarten**.
5. Ga terug naar het Jenkins-dashboard en selecteer **Jenkins beheren**.
6. Selecteer **Configuratie van globaal hulpprogramma**. Zoek naar **NodeJS** en selecteer **NodeJS-installaties**.
7. Selecteer de optie **Automatisch installeren** en geef een waarde voor **Naam** op.
8. Selecteer **Opslaan**.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Een Jenkins Freestyle-project configureren voor Node.js

1. Selecteer **Nieuw Item**. Geef een itemnaam op.
2. Selecteer **Freestyle-project**. Selecteer **OK**.
3. Selecteer op het tabblad **Broncodebeheer** de optie **Git** en geef de details op van de opslagplaats en de vertakking met uw app-code.    
    ![Een opslagplaats toevoegen aan uw build](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. Selecteer op het tabblad **Triggers maken** de optie **SCM pollen** en geef het schema `H/03 * * * *` op om de Git-opslagplaats om de drie minuten op wijzigingen te pollen. 
5. Selecteer op het tabblad **Omgeving maken** de optie **Knooppunt opgeven &amp;npm bin/mappad** en selecteer de waarde **NodeJS-installatie**. Laat **npmrc-bestand** ingesteld op **systeemstandaard gebruiken**.
6. Selecteer op de tabblad **Maken** de optie **Shell uitvoeren** en typ de opdracht `npm install` om ervoor te zorgen dat alle afhankelijkheden worden bijgewerkt.


## <a name="configure-jenkins-for-azure-devops-services-integration"></a>Jenkins configureren voor de integratie met Azure DevOps Services

> [!NOTE]
> Zorg ervoor dat het persoonlijke toegangstoken (PAT) dat u gebruikt voor de volgende stappen, de machtiging *Release* (lezen, schrijven, uitvoeren en beheren) in Azure DevOps Services bevat.
 
1.  Maak een PAT in uw Azure DevOps Services-organisatie als u er nog geen hebt. In Jenkins is deze informatie vereist om toegang te krijgen tot uw Azure DevOps Services-organisatie. Zorg ervoor dat u de tokengegevens opslaat voor latere stappen in deze sectie.
  
    Lees [Hoe maak ik een persoonlijk toegangstoken voor Azure DevOps Services?](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=vsts) voor meer informatie over het genereren van een token.
2. Selecteer op het tabblad **Acties na maken** de optie **Actie na maken toevoegen**. Selecteer **De artefacten archiveren**.
3. Geef voor **Te archiveren bestanden** de optie `**/*` op om alle bestanden op te nemen.
4. Selecteer **Actie na maken toevoegen** als u nog een actie wilt maken.
5. Selecteer **Release activeren in TFS/Team Services**. Voer de URI voor uw Azure DevOps Services-organisatie in, bijvoorbeeld **https://{naam-van-uw-organisatie}.visualstudio.com**.
6. Voer de naam van het **project** in.
7. Kies een naam voor de release-pijplijn. (U maakt deze release-pijplijn later in Azure DevOps Services.)
8. Kies referenties om verbinding te maken met uw Azure DevOps-Services of Azure DevOps Server omgeving:
   - Laat **Gebruikersnaam** leeg als u Azure DevOps Services gebruikt. 
   - Voer een gebruikers naam en wacht woord in als u een on-premises versie van Azure DevOps Server gebruikt.    
   ![Jenkins-acties na maken configureren](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. Sla het Jenkins-project op.


## <a name="create-a-jenkins-service-endpoint"></a>Een Jenkins-service-eindpunt maken

Via een service-eindpunt kan Azure DevOps Services verbinding maken met Jenkins.

1. Open de pagina **Services** in Azure DevOps Services, open de lijst **Nieuw service-eindpunt** en selecteer **Jenkins**.
   ![Een Jenkins-eindpunt toevoegen](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Geef een naam voor de verbinding op.
3. Geef de URL van uw Jenkins-server op en selecteer de optie **Niet-vertrouwde SSL-certificaten accepteren**. Een voorbeeld-URL is **http://{UwJenkinsURL}.westcentralus.cloudapp.azure.com**.
4. Geef de gebruikersnaam en het wachtwoord voor uw Jenkins-account op.
5. Selecteer **Verbinding verifiëren** om te controleren of de informatie juist is.
6. Selecteer **OK** om het service-eindpunt te maken.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Een implementatiegroep voor de virtuele Azure-machines maken

U hebt een [implementatiegroep](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) nodig om de Azure DevOps Services-agent te registreren, zodat de release-pijplijn kan worden geïmplementeerd op uw virtuele machine. Met implementatiegroepen kunt u eenvoudig logische groepen met doelmachines voor implementatie definiëren en de vereiste agent installeren op elke machine.

   > [!NOTE]
   > In de volgende procedure moet u ervoor zorgen dat u de vereiste onderdelen hebt geïnstalleerd en *moet u het script niet uitvoeren met sudo-bevoegdheden.*

1. Open het tabblad **Releases** van de hub **Release&amp; maken**, open **Implementatiegroepen** en selecteer **+ nieuw**.
2. Geef een naam voor de implementatiegroep en een optionele beschrijving op. Selecteer vervolgens **Maken**.
3. Kies het besturingssysteem voor de virtuele machine van uw implementatiedoel. Selecteer bijvoorbeeld **Ubuntu 16.04+** .
4. Selecteer **Een persoonlijk toegangstoken in het script gebruiken voor verificatie**.
5. Selecteer de koppeling **Systeemvereisten**. Installeer de vereisten voor uw besturingssysteem.
6. Selecteer **Script kopiëren naar Klembord** om het script te kopiëren.
7. Meld u aan bij uw virtuele machine van uw implementatiedoel en voer het script uit. Voer het script niet uit met sudo-bevoegdheden.
8. Na de installatie wordt u gevraagd om implementatiegroepstags. Accepteer de standaardwaarden.
9. Controleer in Azure DevOps Services uw zojuist geregistreerde virtuele machine in **Doelen** onder **Implementatiegroepen**.

## <a name="create-an-azure-pipelines-release-pipeline"></a>Een release pijplijn voor een Azure-pijp lijn maken

Met een release-pijplijn geeft u het proces op dat in Azure DevOps Services wordt gebruikt om de app te implementeren. In dit voorbeeld voert u een shellscript uit.

De release-pijplijn in Azure Pipelines maken:

1. Open het tabblad **Releases** van de hub **Release &amp; maken** en selecteer **Release-pijplijn maken**. 
2. Selecteer de sjabloon **Leeg** door eerst een **Leeg proces** te kiezen.
3. Selecteer in de sectie **Artefacten** de optie **+ artefacten toevoegen** en kies **Jenkins** voor **Brontype**. Selecteer de verbinding voor uw Jenkins-service-eindpunt. Selecteer vervolgens de Jenkins-brontaak en selecteer **Toevoegen**.
4. Selecteer het beletselteken naast **Omgeving 1**. Selecteer **Implementatiegroepsfase toevoegen**.
5. Kies uw implementatiegroep.
5. Selecteer **+** als u een taak wilt toevoegen aan **Implementatiegroepsfase**.
6. Selecteer de taak **Shellscript** en selecteer **Toevoegen**. De taak **Shellscript** bevat de configuratie voor een script dat moet worden uitgevoerd op elke server om Node.js te installeren en de app te starten.
8. Voor **Scriptpad** typt u **$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh**.
9. Selecteer **Geavanceerd**, en schakel vervolgens **Werkmap opgeven** in.
10. Voor **Werkmap** typt u **$(System.DefaultWorkingDirectory)/Fabrikam-Node**.
11. Wijzig de naam van de release-pijplijn in de naam die u hebt opgegeven op het tabblad **Acties na maken** van de build in Jenkins. In Jenkins is deze naam vereist om een nieuwe release te activeren wanneer de bronartefacten zijn bijgewerkt.
12. Selecteer **Opslaan** en selecteer **OK** om de release-pijplijn op te slaan.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Handmatige en door CI geactiveerde implementaties uitvoeren

1. Selecteer **+ release** en selecteer **Release maken**.
2. Selecteer de build die u hebt voltooid in de gemarkeerde vervolgkeuzelijst en selecteer **Wachtrij**.
3. Kies de releasekoppeling in het pop-upbericht. Bijvoorbeeld: 'Release **Release 1** is gemaakt'.
4. Open het tabblad **Logboeken** om de uitvoer van de releaseconsole te bekijken.
5. Open in uw browser de URL van een van de servers die u hebt toegevoegd aan de implementatiegroep. Typ bijvoorbeeld **http://{ip-adres-van-uw-server}** .
6. Ga naar de Git-bronopslagplaats en wijzig de inhoud van de kop **h1** in het bestand app/views/index.jade met gewijzigde tekst.
7. Voer uw wijziging door.
8. Na een paar minuten ziet u dat er een nieuwe release is gemaakt op de pagina **Releases** van Azure DevOps. Open de release om te zien welke implementatie wordt uitgevoerd. Gefeliciteerd!

## <a name="troubleshooting-the-jenkins-plugin"></a>Problemen met de Jenkins-invoegtoepassing oplossen

Als u problemen ondervindt met de Jenkins-invoegtoepassingen, kunt u in [Jenkins JIRA](https://issues.jenkins-ci.org/) een ticket openen voor het specifieke onderdeel.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de implementatie van een app in Azure geautomatiseerd met Jenkins voor de build en Azure DevOps Services voor de release. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Uw app maakt in Jenkins.
> * Jenkins configureren voor de integratie met Azure DevOps Services.
> * Een implementatiegroep voor de virtuele Azure-machines maken.
> * Maak een Azure-pijp lijn waarmee de Vm's worden geconfigureerd en de app wordt geïmplementeerd.

Raadpleeg voor meer informatie over het gebruik van Azure-pijp lijnen voor zowel de build-als [de](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-linuxvm-deploygroups)release-procedure.

Ga verder met de volgende zelf studie voor meer informatie over het maken van een YAML CI/CD-pijp lijn om te implementeren op Vm's.

> [!div class="nextstepaction"]
> [Jenkins op Azure](/azure/Jenkins/)
