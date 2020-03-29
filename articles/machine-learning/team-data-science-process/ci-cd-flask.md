---
title: Een CI/CD-pijplijn maken met Azure Pipelines - Team Data Science Process
description: Maak een continue integratie- en continue delivery pipeline voor Kunstmatige Intelligentie (AI)-toepassingen met Docker en Kubernetes.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: 42433ec419ac9e02077cd0359e18b5114206f27d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721826"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>CI/CD-pijplijnen maken voor AI-apps met Azure Pipelines, Docker en Kubernetes

Een Kunstmatige Intelligentie (AI) applicatie is applicatiecode ingebed met een voorgetraind machine learning (ML) model. Er zijn altijd twee werkstromen voor een AI-toepassing: gegevenswetenschappers bouwen het ML-model en app-ontwikkelaars bouwen de app en stellen deze bloot aan eindgebruikers om te consumeren. In dit artikel wordt beschreven hoe u een CI/CD-pijplijn (continuous integration and continuous delivery) implementeert voor een AI-toepassing die het ML-model insluit in de broncode van de app. De voorbeeldcode en -zelfstudie gebruiken een Python Flask-webtoepassing en halen een vooraf opgeleid model op uit een privé-Azure blob-opslagaccount. U ook een AWS S3-opslagaccount gebruiken.

> [!NOTE]
> Het volgende proces is een van de vele manieren om CI / CD te doen. Er zijn alternatieven voor deze tooling en de voorwaarden.

## <a name="source-code-tutorial-and-prerequisites"></a>Broncode, zelfstudie en vereisten

U [broncode](https://github.com/Azure/DevOps-For-AI-Apps) en een [gedetailleerde zelfstudie](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) van GitHub downloaden. Volg de zelfstudiestappen om een CI/CD-pijplijn voor uw eigen toepassing te implementeren.

Als u de gedownloade broncode en zelfstudie wilt gebruiken, hebt u de volgende vereisten nodig: 

- De [broncoderepository](https://github.com/Azure/DevOps-For-AI-Apps) is gevorkt naar uw GitHub-account
- Een [Azure DevOps-organisatie](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Azure-CLI](/cli/azure/install-azure-cli)
- Een [Azure Container Service voor Kubernetes (AKS) cluster](/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) om opdrachten uit te voeren en configuratie op te halen vanuit het AKS-cluster 
- Een [ACR-account (Azure Container Registry)](/azure/container-registry/container-registry-get-started-portal)

## <a name="cicd-pipeline-summary"></a>CI/CD-pijplijnoverzicht

Elke nieuwe Git commit start de Build-pijplijn. De build haalt veilig het nieuwste ML-model uit een blob-opslagaccount en verpakt het met de app-code in één container. Deze ontkoppeling van de applicatieontwikkeling en data science werkstromen zorgt ervoor dat de productie-app altijd de nieuwste code draait met het nieuwste ML-model. Als de app het testen doorstaat, slaat de pijplijn de buildafbeelding veilig op in een Docker-container in ACR. De releasepijplijn implementeert de container vervolgens met AKS. 

## <a name="cicd-pipeline-steps"></a>CI/CD-pijplijnstappen

In het volgende diagram en de volgende stappen worden de CI/CD-pijplijnarchitectuur beschreven:

![CI/CD-pijplijnarchitectuur](./media/ci-cd-flask/architecture.png)

1. Ontwikkelaars werken aan de toepassingscode in de IDE van hun keuze.
2. De ontwikkelaars verbinden de code aan Azure Repos, GitHub of andere Git source control provider. 
3. Afzonderlijk werken gegevenswetenschappers aan de ontwikkeling van hun ML-model.
4. De gegevenswetenschappers publiceren het voltooide model naar een modelopslagplaats, in dit geval een blob-opslagaccount. 
5. Azure Pipelines start een build op basis van de Git commit.
6. De Build-pijplijn haalt het nieuwste ML-model uit blobopslag en maakt een container.
7. De pijplijn duwt de buildafbeelding naar de private image repository in ACR.
8. De Release-pijplijn start op basis van de succesvolle build.
9. De pijplijn haalt de nieuwste afbeelding uit ACR en implementeert deze in het Kubernetes-cluster op AKS.
10. Gebruikersverzoeken voor de app gaan via de DNS-server.
11. De DNS-server geeft de aanvragen door aan een load balancer en stuurt antwoorden terug naar de gebruikers.

## <a name="see-also"></a>Zie ook

- [Team Data Science Process (TDSP)](/azure/machine-learning/team-data-science-process/)
- [Azure Machine Learning (AML)](/azure/machine-learning/)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Azure Kubernetes Services (AKS)](/azure/aks/intro-kubernetes)
