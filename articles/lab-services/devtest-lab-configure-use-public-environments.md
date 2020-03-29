---
title: Openbare omgevingen configureren en gebruiken in Azure DevTest Labs | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u openbare omgevingen (Azure Resource Manager-sjablonen in een Git-repo) configureert en gebruikt in Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 127a6986e04cf90f69b2a8ec70b90b877e534708
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721690"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Openbare omgevingen configureren en gebruiken in Azure DevTest Labs
Azure DevTest Labs heeft een [openbare opslagplaats van Azure Resource Manager-sjablonen](https://github.com/Azure/azure-devtestlab/tree/master/Environments) die u gebruiken om omgevingen te maken zonder zelf verbinding te hoeven maken met een externe GitHub-bron. Deze opslagplaats bevat veelgebruikte sjablonen zoals Azure Web Apps, Service Fabric Cluster en de SharePoint Farm-omgeving voor ontwikkeling. Deze functie is vergelijkbaar met de openbare opslagplaats van artefacten die is opgenomen voor elk lab dat u maakt. Met de omgevingsopslagplaats u snel aan de slag met vooraf geschreven omgevingssjablonen met minimale invoerparameters om u een soepele aanstartervaring te bieden voor PaaS-bronnen in laboratoria. 

## <a name="configuring-public-environments"></a>Openbare omgevingen configureren
Als eigenaar van een lab u tijdens de creatie van het lab de openbare omgevingrepository voor uw lab inschakelen. Als u openbare omgevingen voor uw lab wilt inschakelen, selecteert u **Op** voor het veld **Openbare omgevingen** terwijl u een lab maakt. 

![Openbare omgeving inschakelen voor een nieuw lab](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


Voor bestaande labs is de openbare omgevingsrepository niet ingeschakeld. Handmatig inschakelen om sjablonen te gebruiken in de repository. Voor labs die zijn gemaakt met Resource Manager-sjablonen, is de opslagplaats standaard ook uitgeschakeld.

U openbare omgevingen voor uw lab in- en uitschakelen en ook alleen specifieke omgevingen beschikbaar maken voor labgebruikers met behulp van de volgende stappen: 

1. Selecteer **Configuratie en beleid** voor uw lab. 
2. Selecteer **openbare omgevingen**in de sectie **VIRTUELE MACHINEBASES** .
3. Als u openbare omgevingen voor het lab wilt inschakelen, selecteert u **Ja**. Anders selecteert u **Nee**. 
4. Als u openbare omgevingen hebt ingeschakeld, zijn alle omgevingen in de repository standaard ingeschakeld. U een omgeving deselecteren om deze niet beschikbaar te maken voor uw labgebruikers. 

![Pagina openbare omgevingen](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Omgevingssjablonen gebruiken als labgebruiker
Als labgebruiker u een nieuwe omgeving maken vanuit de ingeschakelde lijst met omgevingssjablonen door **simpelweg +Toevoegen te** selecteren op de gereedschapsbalk op de labpagina. De lijst met bases bevat de sjablonen voor openbare omgevingen die door uw labbeheerder bovenaan de lijst zijn ingeschakeld.

![Sjablonen voor openbare omgeving](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Volgende stappen
Deze repository is een open-source repository die u bijdragen aan het toevoegen van veel gebruikte en nuttige Resource Manager sjablonen van uw eigen. Om bij te dragen, gewoon een pull request tegen de repository.  
