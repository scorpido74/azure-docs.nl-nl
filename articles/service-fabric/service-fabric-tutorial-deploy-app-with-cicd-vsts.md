---
title: Een app implementeren met CI-en Azure-pijp lijnen
description: In deze zelfstudie leert u hoe u een continue integratie en implementatie instelt voor een Service Fabric-toepassing met behulp van Azure Pipelines.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 11485d22abcf0b8e1eb13d8123ff21c7fe0079f8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75614139"
---
# <a name="tutorial-deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Zelfstudie: Een toepassing met CI/CD implementeren in een Service Fabric-cluster

Deze zelfstudie is deel vier van een reeks. Hierin wordt beschreven hoe u een continue integratie en implementatie instelt voor een Azure Service Fabric-toepassing met behulp van Azure Pipelines.  Er is een bestaande Service Fabric-toepassing vereist. De toepassing die in [Een .NET-toepassing bouwen](service-fabric-tutorial-create-dotnet-app.md) is gemaakt, wordt als voorbeeld gebruikt.

In deel drie van de serie leert u het volgende:

> [!div class="checklist"]
> * Broncodebeheer aan uw project toevoegen
> * Een build-pijplijn in Azure Pipelines maken
> * Een release-pijplijn in Azure Pipelines maken
> * Automatisch een upgrade en een toepassing implementeren

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * [Een .NET Service Fabric-toepassing bouwen](service-fabric-tutorial-create-dotnet-app.md)
> * [De toepassing implementeren in een extern cluster](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Een HTTPS-eindpunt toevoegen aan een front-endservice van ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * CI/CD configureren met behulp van Azure-pijplijnen
> * [Controle en diagnostische gegevens voor de toepassing instellen](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan
* [Installeer Visual Studio 2019](https://www.visualstudio.com/) en installeer de werk belasting van **Azure Development** en **ASP.net en Web Development** .
* [Installeer de Service Fabric-SDK](service-fabric-get-started.md).
* Maak een Windows Service Fabric-cluster in Azure, bijvoorbeeld door [deze zelfstudie te volgen](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Maak een [Azure DevOps-organisatie](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student). Hiermee kunt u een project maken in Azure DevOps en Azure Pipelines gebruiken.

## <a name="download-the-voting-sample-application"></a>De voorbeeldtoepassing om te stemmen downloaden

Als u de stem voorbeeld toepassing in [deel één van deze zelfstudie reeks](service-fabric-tutorial-create-dotnet-app.md)niet hebt gemaakt, kunt u deze downloaden. Voer in een opdrachtvenster de volgende opdracht uit om de opslagplaats van de voorbeeld-app te klonen op uw lokale computer.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Een publicatieprofiel voorbereiden

U hebt [een toepassing gemaakt](service-fabric-tutorial-create-dotnet-app.md) en [de toepassing in Azure geïmplementeerd](service-fabric-tutorial-deploy-app-to-party-cluster.md). U kunt nu continue integratie instellen.  Bereid eerst binnen uw toepassing een publicatieprofiel voor, dat kan worden gebruikt door het implementatieproces dat in Azure Pipelines wordt uitgevoerd.  Het publicatieprofiel moet worden geconfigureerd zodat het cluster dat u eerder hebt gemaakt als doel kan dienen.  Start Visual Studio en open een bestaand Service Fabric-toepassingsproject.  Klik in **Solution Explorer** met de rechtermuisknop op de toepassing en selecteer **Publish...**.

Kies een doelprofiel in het toepassingsproject, dat voor de werkstroom voor continue integratie kan worden gebruikt, bijvoorbeeld Cloud.  Geef het eindpunt voor de clusterverbinding op.  Schakel het selectievakje bij **Upgrade the Application** in, zodat voor elke implementatie in Azure DevOps een upgrade voor de toepassing wordt uitgevoerd.  Klik op de hyperlink **Save** om de instellingen op te slaan in het publicatieprofiel. Klik vervolgens op **Cancel** om het dialoogvenster te sluiten.

![Push-profiel][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>Uw Visual Studio-oplossing delen met een nieuwe Git-opslagplaats van Azure DevOps

Deel de bronbestanden van uw toepassing met een project in Azure DevOps zodat u builds kunt genereren.

Maak een nieuwe lokale Git-opslag plaats voor uw project -> **door op de status balk in de** rechter bovenhoek van Visual Studio **toevoegen aan broncode beheer te**selecteren.

Ga naar de **Push**-weergave in **Team Explorer** en selecteer onder **Push to Azure DevOps** de knop **Publish Git Repo**.

![Git-opslagplaats pushen][push-git-repo]

Controleer uw e-mail en selecteer uw account in de vervolgkeuzelijst **Azure DevOps Domain**. Voer de naam van de opslagplaats in en selecteer **Publish repository**.

![Git-opslagplaats pushen][publish-code]

Als u de opslagplaats pusht, wordt er een nieuw project voor uw account gemaakt met dezelfde naam als de lokale opslagplaats. Als u de opslagplaats in een bestaand project wilt maken, klikt u naast de naam van de **opslagplaats** op **Advanced** en selecteert u een project. U kunt uw code op het web weergeven door **See it on the web** te selecteren.

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Continue levering configureren met Azure Pipelines

In een pijp lijn voor het bouwen van Azure-pijp lijnen wordt een werk stroom beschreven die bestaat uit een reeks bouw stappen die opeenvolgend worden uitgevoerd. Maak een build-pijplijn die een Service Fabric-toepassingspakket maakt en andere artefacten, om deze in een Service Fabric-cluster te implementeren. Meer informatie over [build-pijplijnen van Azure Pipelines](https://www.visualstudio.com/docs/build/define/create). 

Een release-pijplijn van Azure Pipelines beschrijft een werkstroom waarmee een toepassingspakket in een cluster wordt geïmplementeerd. Als de build-pijplijn en de release-pijplijn samen worden gebruikt, wordt hiermee de hele werkstroom uitgevoerd, te beginnen met bronbestanden en te eindigen met het uitvoeren van een toepassing in uw cluster. Meer informatie over de [release pijplijnen van Azure pijp lijnen](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition).

### <a name="create-a-build-pipeline"></a>Een build-pijplijn maken

Open een webbrowser en ga naar uw nieuwe project op: [https://&lt;myaccount&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting).

Selecteer het tabblad **Pijplijnen**, vervolgens **Builds** en klik daarna op **Nieuwe pijplijn**.

![Nieuwe pijplijn][new-pipeline]

Selecteer **Azure Repos Git** als de bron, **Voting** Team project, **Voting** Repository en **master** standaardbranch voor handmatige en geplande builds.  Klik vervolgens op **door gaan**.

![Opslagplaats selecteren][select-repo]

Selecteer in **een sjabloon selecteren**de sjabloon **Azure service Fabric-toepassing** en klik op **Toep assen**.

![Build-sjabloon kiezen][select-build-template]

Voer in **Taken** Hosted VS2017 in als de **Agentgroep**.

![Taken selecteren][save-and-queue]

Schakel onder **Triggers** continue integratie in door **Enable continuous integration** in te schakelen. Binnen **Branchfilters** zijn de standaardwaarden voor **Branchspecificatie** ingesteld op **master**. Selecteer **Save and queue** om handmatig een build te starten.

![Triggers selecteren][save-and-queue2]

Hiermee worden ook triggers gebouwd na pushen of inchecken. Als u de voortgang van de build wilt controleren, gaat u naar het tabblad **builds** .  Nadat u hebt gecontroleerd of de build is uitgevoerd, definieert u een release pijplijn die uw toepassing implementeert in een cluster.

### <a name="create-a-release-pipeline"></a>Een release-pijplijn maken

Selecteer het tabblad **Pijplijnen**, vervolgens **Releases** en daarna **+ Nieuwe pijplijn**.  Selecteer in **Select a template** de sjabloon **Azure Service Fabric Deployment** in de lijst en vervolgens **Apply**.

![Release-sjabloon kiezen][select-release-template]

Selecteer **taken**->**omgeving 1** en vervolgens **+ Nieuw** om een nieuwe cluster verbinding toe te voegen.

![Clusterverbinding toevoegen][add-cluster-connection]

Selecteer in de weergave **Add new Service Fabric Connection****Certificate Based**- of **Azure Active Directory**-verificatie.  Geef de verbindingsnaam mysftestcluster op en het clustereindpunt tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000 (of het eindpunt van het cluster waarin de implementatie wordt uitgevoerd).

Voor verificatie op basis van certificaten voegt u de **vinger afdruk van het server certificaat** toe van het server certificaat dat is gebruikt om het cluster te maken.  Voeg in het **clientcertificaat** de basis-64-codering van het certificaatbestand van de client toe. Zie het pop-upitem met de Help voor dat veld voor informatie over hoe u de basis-64-representatie van het certificaat ophaalt. Voeg ook het **wachtwoord** voor het certificaat toe.  U kunt het cluster- of het servercertificaat gebruiken als u geen apart clientcertificaat hebt.

Voor Azure Active Directory-referenties voegt u de **vingerafdruk voor servercertificaat** toe van het servercertificaat dat is gebruikt om het cluster te maken en in de velden **Username** en **Password** de referenties die u wilt gebruiken om het cluster te verbinden.

Klik op **Add** om de clusterverbinding op te slaan.

Voeg vervolgens een build-artefact toe aan de pijplijn, zodat met de release-pijplijn de uitvoer van de build kan worden gevonden. Selecteer **Pipeline** en **Artifacts**->**+Add**.  Selecteer in **Source (Build definition)** de build-pijplijn die u eerder hebt gemaakt.  Klik op **Add** om het build-artefact op te slaan.

![Artefact toevoegen][add-artifact]

Schakel een trigger voor continue implementatie in, zodat automatisch een release wordt gemaakt als de build wordt voltooid. Klik op het bliksempictogram in het artefact, schakel de trigger in en klik op **Save** om de release-pijplijn op te slaan.

![Trigger inschakelen][enable-trigger]

Selecteer **+ release** -> **Create a release** -> **Create** om hand matig een release te maken. U kunt de voortgang van de release volgen op het tabblad **Releases**.

Controleer of de implementatie is gelukt en de toepassing in het cluster wordt uitgevoerd.  Open een webbrowser en ga naar `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`.  Noteer de versie van de toepassing (in dit voorbeeld 1.0.0.20170616.3).

## <a name="commit-and-push-changes-trigger-a-release"></a>Wijzigingen doorvoeren en pushen, een release activeren

Controleer of de pijplijn voor continue integratie correct functioneert door enkele codewijzigingen aan te brengen in Azure DevOps.

Terwijl u de code schrijft, worden de wijzigingen automatisch in Visual Studio bijgehouden. Voer wijzigingen door in de lokale Git-opslagplaats door het pictogram voor wijzigingen in behandeling (![In behandeling][pending]) te selecteren op de statusbalk rechtsonder.

Voeg aan de weergave **Changes** in Team Explorer een bericht toe waarin u de update beschrijft en voer de wijzigingen door.

![Alles doorvoeren][changes]

Selecteer het pictogram voor de statusbalk met niet-gepubliceerde wijzigingen (![Unpublished changes][unpublished-changes]) of de synchronisatieweergave in Team Explorer. Selecteer **Push** om de code in Azure Pipelines bij te werken.

![Wijzigingen pushen][push]

Als u de wijzigingen naar Azure Pipelines pusht, wordt er automatisch een build geactiveerd.  Als de build-pijplijn wordt voltooid, wordt er automatisch een release gemaakt en wordt de toepassing in het cluster bijgewerkt.

Als u de voortgang van de build wilt controleren, schakelt u over naar het tabblad **Builds** in **Team Explorer** in Visual Studio.  Als u hebt gecontroleerd of de build correct wordt uitgevoerd, definieert u een release-pijplijn waarmee uw toepassing in een cluster wordt geïmplementeerd.

Controleer of de implementatie is gelukt en de toepassing in het cluster wordt uitgevoerd.  Open een webbrowser en ga naar `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`.  Noteer de versie van de toepassing (in dit voorbeeld 1.0.0.20170815.3).

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>De toepassing bijwerken

Breng in de toepassing wijzigingen aan de code aan.  Sla de wijzigingen op en voer ze door aan de hand van de voorgaande stappen.

Zodra de upgrade van de toepassing wordt uitgevoerd, kunt u de voortgang ervan volgen in Service Fabric Explorer:

![Service Fabric Explorer][sfx2]

Het kan enkele minuten duren voordat de toepassing is bijgewerkt. Als het bijwerken is voltooid, wordt de volgende versie door de toepassing uitgevoerd.  In dit voorbeeld 1.0.0.20170815.4.

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Broncodebeheer aan uw project toevoegen
> * Een build-pijplijn maken
> * Een release-pijplijn maken
> * Automatisch een upgrade en een toepassing implementeren

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [Controle en diagnostische gegevens voor de toepassing instellen](service-fabric-tutorial-monitoring-aspnet.md)

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewPipeline.png
[select-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectRepo.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-AzureDevOpsServices]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
