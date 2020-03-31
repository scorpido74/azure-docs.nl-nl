---
title: Een Git-repository toevoegen aan een lab in Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over het toevoegen van een GitHub- of Azure DevOps Services Git-repository voor uw aangepaste artefactenbron in Azure DevTest Labs.
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 1e7587c60e180fb35e1a2bed735b053b6b0c388a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294611"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Een Git-opslagplaats toevoegen om aangepaste artefacten en Resource Manager-sjablonen op te slaan

U [aangepaste artefacten maken](devtest-lab-artifact-author.md) voor de VM's in uw lab of [Azure Resource Manager-sjablonen gebruiken om een aangepaste testomgeving te maken.](devtest-lab-create-environment-from-arm.md) U moet een privé-Git-opslagplaats toevoegen voor de artefacten of Resource Manager-sjablonen die uw team maakt. De repository kan worden gehost op [GitHub](https://github.com) of op [Azure DevOps Services.](https://visualstudio.com)

We bieden een [GitHub-opslagplaats met artefacten](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) die je implementeren zoals het is, of je ze aanpassen voor je labs. Wanneer u een artefact aanpast of maakt, u het artefact niet opslaan in de openbare opslagplaats. U moet uw eigen privérepo maken voor aangepaste artefacten en voor artefacten die u maakt. 

Wanneer u een vm maakt, u de sjabloon Resourcebeheer opslaan, deze aanpassen als u wilt en deze later gebruiken om meer VM's te maken. U moet uw eigen privéopslagplaats maken om uw aangepaste Resource Manager-sjablonen op te slaan.  

* Zie [GitHub Bootcamp](https://help.github.com/categories/bootcamp/)voor meer informatie over het maken van een GitHub-repository.
* Zie [Verbinding maken met Azure DevOps Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)voor meer informatie over het maken van een Azure DevOps Services-project met een Git-repository.

De volgende figuur is een voorbeeld van hoe een opslagplaats met artefacten eruit kan zien in GitHub:  

![Voorbeeld van GitHub-artefacten repo](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>De informatie en referenties van de opslagplaats opvragen
Als u een opslagplaats aan uw lab wilt toevoegen, krijgt u eerst belangrijke informatie uit uw opslagplaats. In de volgende secties wordt beschreven hoe u vereiste informatie krijgen voor opslagplaatsen die worden gehost op GitHub of Azure DevOps Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Download de URL voor de GitHub-repository-kloon en het persoonlijke toegangstoken

1. Ga naar de startpagina van de GitHub-opslagplaats met de definities van de sjabloonartefact of Resource Manager.
2. Selecteer **Klonen of downloaden**.
3. Als u de URL naar het klembord wilt kopiëren, selecteert u de **URL-knop HTTPS-kloon.** Sla de URL op voor later gebruik.
4. Selecteer in de rechterbovenhoek van GitHub de profielafbeelding en selecteer **Vervolgens Instellingen**.
5. Selecteer persoonlijke **toegangstokens**in het menu **Persoonlijke instellingen** aan de linkerkant.
6. Selecteer **Nieuw token genereren**.
7. Voer op de pagina **Nieuwe persoonlijke toegangtoken** onder **Token-beschrijving**een beschrijving in. Accepteer de standaarditems onder **Bereik selecteren**en selecteer **Token genereren**.
8. Sla het gegenereerde token op. Je gebruikt het token later.
9. Sluit GitHub.   
10. Ga verder naar de [sectie Uw lab verbinden met de opslagplaats.](#connect-your-lab-to-the-repository)

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Download de URL voor de Azure Repos-kloon en het persoonlijke toegangstoken

1. Ga naar de startpagina van uw teamverzameling `https://contoso-web-team.visualstudio.com`(bijvoorbeeld) en selecteer vervolgens uw project.
2. Selecteer Op de startpagina van het project de optie **Code**.
3. Als u de kloon-URL wilt weergeven, selecteert u op de pagina **projectcode** de optie **Kloon**.
4. Sla de URL op. U gebruikt de URL later.
5. Als u een persoonlijk toegangstoken wilt maken, selecteert u in het vervolgkeuzemenu gebruikersaccount **Mijn profiel**.
6. Selecteer **beveiliging**op de pagina profielinformatie .
7. Selecteer **op** het tabblad Beveiliging de optie **Toevoegen**.
8. Ga op de pagina **Een persoonlijke toegangstoken maken:**
   1. Voer een **beschrijving** voor het token in.
   2. Selecteer **180 dagen**in de lijst **Verloopt in** .
   3. Selecteer **alle** toegankelijke **accounts**in de lijst Accounts .
   4. Selecteer de optie **Alleen-lezen** selecteren.
   5. Selecteer **Token maken**.
9. Het nieuwe token wordt weergegeven in de lijst **Met persoonlijke toegangstokens.** Selecteer **Token kopiëren**en sla de tokenwaarde op voor later gebruik.
10. Ga verder naar de [sectie Uw lab verbinden met de opslagplaats.](#connect-your-lab-to-the-repository)

## <a name="connect-your-lab-to-the-repository"></a>Verbind je lab met de repository
1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecteer **Meer services**en selecteer Vervolgens **DevTest Labs** in de lijst met services.
3. Selecteer in de lijst met labs uw lab. 
4. Selecteer**Repositories** >  **voor configuratie en beleid** > **+ Toevoegen**.

    ![De knop Opslagplaats toevoegen](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Geef op de tweede **pagina Repositories** de volgende informatie op:
   1. **Naam**. Voer een naam in voor de opslagplaats.
   2. **Git Clone Url**. Voer de URL van de Git HTTPS-kloon in die u eerder hebt gekopieerd van GitHub of Azure DevOps Services.
   3. **Tak**. Voer de branch in om aan uw definities te komen.
   4. **Persoonlijke toegangstoken**. Voer het persoonlijke toegangstoken in dat u eerder hebt gekregen van GitHub of Azure DevOps Services.
   5. **Mappaden**. Voer ten minste één mappad in ten opzichte van de kloon-URL die de definities van de artefact- of Resource Manager-sjabloon bevat. Wanneer u een submap opgeeft, moet u de slash doorsturen opnemen in het mappad.

      ![Repositories gebied](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Selecteer **Opslaan**.

### <a name="related-blog-posts"></a>Gerelateerde blogberichten
* [Problemen met falende artefacten in DevTest Labs oplossen](devtest-lab-troubleshoot-artifact-failure.md)
* [Een VM deelnemen aan een bestaand Active Directory-domein met behulp van een resourcemanagersjabloon in DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Volgende stappen
Nadat u uw privé-Git-repository hebt gemaakt, u een of beide van de volgende handelingen doen, afhankelijk van uw behoeften:
* Bewaar uw [aangepaste artefacten.](devtest-lab-artifact-author.md) U ze later gebruiken om nieuwe VM's te maken.
* [Maak multi-VM-omgevingen en PaaS-resources met behulp van Resource Manager-sjablonen](devtest-lab-create-environment-from-arm.md). Vervolgens u de sjablonen opslaan in uw privérepo.

Wanneer u een vm maakt, u controleren of de artefacten of sjablonen worden toegevoegd aan uw Git-opslagplaats. Ze zijn onmiddellijk beschikbaar in de lijst met artefacten of sjablonen. De naam van uw privérepo wordt weergegeven in de kolom die de bron opgeeft. 
