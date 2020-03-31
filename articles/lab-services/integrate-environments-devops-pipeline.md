---
title: Omgevingen integreren in Azure Pipelines in Azure DevTest Labs
description: Meer informatie over het integreren van Azure DevTest Labs-omgevingen in uw Azure DevOps continuous integration (CI) en cd-pijplijnen (continuous delivery).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 3d7e481879326ac30093bd116222bddc28640398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169412"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Omgevingen integreren in uw Azure DevOps CI/CD-pijplijnen
U de Azure DevTest Labs Tasks-extensie die is geïnstalleerd in Azure DevOps Services (voorheen Bekend als Visual Studio Team Services) gebruiken om uw back-continuous integration (CI)/ continuous delivery (CD) build-and-release-pijplijn eenvoudig te integreren met Azure DevTest Labs. Deze extensies maken het eenvoudiger om snel een [omgeving](devtest-lab-test-env.md) te implementeren voor een specifieke testtaak en deze vervolgens te verwijderen wanneer de test is voltooid. 

In dit artikel ziet u hoe u een omgeving maakt en implementeert en vervolgens de omgeving verwijdert, allemaal in één volledige pijplijn. U zou normaal gesproken elk van deze taken afzonderlijk uitvoeren in uw eigen aangepaste build-test-deploy-pijplijn. De extensies die in dit artikel worden gebruikt, sluiten aan bij deze [DTL VM-taken maken/verwijderen:](devtest-lab-integrate-ci-cd-vsts.md)

- Een omgeving maken
- Een omgeving verwijderen

## <a name="before-you-begin"></a>Voordat u begint
Voordat u uw CI/CD-pijplijn integreren met Azure DevTest Labs, installeert u [azure DevTest Labs-takenextensie](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) van Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Het lab maken en configureren voor omgevingen
In deze sectie wordt beschreven hoe u een lab maken en configureren waar de Azure-omgeving wordt geïmplementeerd.

1. [Maak een lab](devtest-lab-create-lab.md) als je er nog geen hebt. 
2. Configureer het lab en maak een omgevingssjabloon door instructies uit dit artikel te volgen: [Meerdere VM-omgevingen en PaaS-resources maken met Azure Resource Manager-sjablonen.](devtest-lab-create-environment-from-arm.md)
3. Gebruik in dit voorbeeld een bestaande [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/)Azure Quickstart-sjabloon .
4. Kopieer de map **201-web-app-redis-cache-sql-database** naar de **Map ArmTemplate** in de repository die in stap 2 is geconfigureerd.

## <a name="create-a-release-definition"></a>Release-definitie maken
Ga als volgt te werk om de releasedefinitie te maken:

1.  Selecteer op het tabblad **Releases** van de **hub & maken**de knop **Plusteken (+).**
2.  Selecteer in het venster **Releasedefinitie maken** de sjabloon **Leeg** maken en selecteer **Volgende**.
3.  Selecteer **Later kiezen**en selecteer Vervolgens **Maken** om een nieuwe releasedefinitie te maken met één standaardomgeving en geen gekoppelde artefacten.
4.  Als u het snelmenu wilt openen, selecteert u in de nieuwe releasedefinitie de **ellips (...)** naast de omgevingsnaam en selecteert u **Variabelen configureren**.
5.  Voer in het **venster Configureren - omgeving** de volgende waarden in voor de variabelen die u gebruikt in de releasedefinitietaken:
1.  Voer **voor administratorLogin**de inlognaam van SQL Administrator in.
2.  Voer **voor administratorLoginPassword**het wachtwoord in dat door de SQL Administrator-login moet worden gebruikt. Gebruik het pictogram Hangslot om het wachtwoord te verbergen en te beveiligen.
3.  Voer **voor databaseNaam**de sql-databasenaam in.
4.  Deze variabelen zijn specifiek voor de voorbeeldomgevingen, verschillende omgevingen kunnen verschillende variabelen hebben.

## <a name="create-an-environment"></a>Een omgeving maken
De volgende fase van de implementatie is het creëren van de omgeving die moet worden gebruikt voor ontwikkelings- of testdoeleinden.

1. Selecteer taken **toevoegen**in de releasedefinitie .
2. Voeg op het tabblad **Taken** een taak Azure DevTest Labs Create Create toe. Configureer de taak als volgt:
    1. Selecteer voor **Azure RM-abonnement**een verbinding in de lijst **Beschikbare Azure-serviceverbindingen** of maak een verbinding met beperktere machtigingen met uw Azure-abonnement. Zie [Eindpunt azure resource manager-service voor](/azure/devops/pipelines/library/service-endpoints)meer informatie .
2. Selecteer **voor Labname**de naam van de instantie die u eerder hebt gemaakt*.
3. Selecteer **voor Repository Name**de opslagplaats waar naar de sjabloon Resourcemanager (201) is gepusht*.
4. Selecteer **bij Sjabloonnaam**de naam van de omgeving die u hebt opgeslagen in uw broncodeopslagplaats*. 
5. De **labnaam**, **repositorynaam**en **sjabloonnaam** zijn de vriendelijke weergaven van de Azure-bron-id's. Als u handmatig de vriendelijke naam invoert, worden fouten veroorzaakt, gebruikt u de vervolgkeuzelijsten om de informatie te selecteren.
6. Voer **voor Omgevingsnaam**een naam in om de omgevingsinstantie in het lab op unieke wijze te identificeren.  Het moet uniek zijn in het lab.
7. Met **het parameterbestand** en de **parameters**kunnen aangepaste parameters worden doorgegeven aan de omgeving. Een of beide kan worden gebruikt om de parameterwaarden in te stellen. Voor dit voorbeeld wordt de sectie Parameters gebruikt. Gebruik bijvoorbeeld de namen van de variabelen die u in de omgeving hebt gedefinieerd:`-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. Informatie binnen de omgevingssjabloon kan worden doorgegeven in het uitvoergedeelte van de sjabloon. Schakel **Uitvoervariabelen maken op basis van de uitvoer van de omgevingssjabloon** in, zodat andere taken de gegevens kunnen gebruiken. `$(Reference name.Output Name)`is het patroon te volgen. Als de referentienaam bijvoorbeeld DTL was en de uitvoernaam in `$(DTL.location)`de sjabloon locatie was, zou de variabele .

## <a name="delete-the-environment"></a>De omgeving verwijderen
De laatste stap is het verwijderen van de omgeving die u hebt geïmplementeerd in uw Azure DevTest Labs-exemplaar. U verwijdert de omgeving normaal gesproken nadat u de dev-taken hebt uitgevoerd of de tests uitvoert die u nodig hebt op de geïmplementeerde resources.

Selecteer in de releasedefinitie **Taken toevoegen**en voeg vervolgens op het tabblad **Implementeren** een taak Voor het verwijderen van **Azure DevTest Labs** toe. Configureer het als volgt:

1. Zie [Azure DevTest Labs-taken](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)als u de VM wilt verwijderen:
    1. Selecteer voor **Azure RM-abonnement**een verbinding in de lijst **Beschikbare Azure-serviceverbindingen** of maak een verbinding met beperktere machtigingen met uw Azure-abonnement. Zie [Eindpunt azure resource manager-service voor](/azure/devops/pipelines/library/service-endpoints)meer informatie .
    2. Selecteer **voor Labname**het lab waar de omgeving bestaat.
    3. Voer **bij Omgevingsnaam**de naam in van de te verwijderen omgeving.
2. Voer een naam in voor de releasedefinitie en sla deze op.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen: 
- [Meerdere VM-omgevingen maken met Resource Manager-sjablonen](devtest-lab-create-environment-from-arm.md).
- Quickstart Resource Manager-sjablonen voor DevTest Labs-automatisering vanuit de [GitHub-repository van DevTest Labs.](https://github.com/Azure/azure-quickstart-templates)
- [PAGINA VSTS-probleemoplossing](/azure/devops/pipelines/troubleshooting)

