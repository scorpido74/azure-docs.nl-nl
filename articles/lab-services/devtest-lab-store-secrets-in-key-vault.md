---
title: Geheimen opslaan in een sleutelkluis in Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over het opslaan van geheimen in een Azure Key Vault en deze gebruiken tijdens het maken van een VM, formule of een omgeving.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: 0ca36a7081aaf70ee2045ee7586184c89591df16
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461511"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Geheimen opslaan in een sleutelkluis in Azure DevTest Labs
Mogelijk moet u een complex geheim invoeren bij het gebruik van Azure DevTest Labs: wachtwoord voor uw Windows VM, openbare SSH-sleutel voor uw Linux-VM of persoonlijke toegangstoken om uw Git-repo te klonen via een artefact. Geheimen zijn meestal lang en hebben willekeurige tekens. Daarom kan het invoeren van hen lastig en lastig zijn, vooral als u hetzelfde geheim meerdere keren gebruikt.

Om dit probleem op te lossen en ook uw geheimen op een veilige plaats te bewaren, ondersteunt DevTest Labs het opslaan van geheimen in een [Azure-sleutelkluis.](../key-vault/general/overview.md) Wanneer een gebruiker voor het eerst een geheim opslaat, maakt de DevTest Labs-service automatisch een sleutelkluis in dezelfde resourcegroep die het lab bevat en het geheim opslaat in de sleutelkluis. DevTest Labs maakt een aparte sleutelkluis voor elke gebruiker. 

Houd er rekening mee dat de labgebruiker eerst een virtuele labmachine moet maken voordat hij een geheim in de sleutelkluis kan maken. Dit komt omdat DevTest Lab-service de labgebruiker moet koppelen aan een geldig gebruikersdocument voordat ze geheimen mogen maken en opslaan in hun sleutelkluis. 


## <a name="save-a-secret-in-azure-key-vault"></a>Een geheim opslaan in Azure Key Vault
Ga als volgt te werk om uw geheim op te slaan in Azure Key Vault:

1. Selecteer **Mijn geheimen** in het linkermenu.
2. Voer een **naam** in voor het geheim. U ziet deze naam in de vervolgkeuzelijst bij het maken van een vm, formule of een omgeving. 
3. Voer het geheim in als de **waarde**.

    ![Winkelgeheim](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Een geheim gebruiken uit Azure Key Vault
Wanneer u een geheim moet invoeren om een VM, formule of een omgeving te maken, u handmatig een geheim invoeren of een opgeslagen geheim uit de sleutelkluis selecteren. Ga als volgt te werk om een geheim te gebruiken dat is opgeslagen in uw sleutelkluis:

1. Selecteer **Een opgeslagen geheim gebruiken**. 
2. Selecteer je geheim in de vervolgkeuzelijst voor **Kies een geheim**. 

    ![Geheim gebruiken in VM](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Een geheim gebruiken in een Azure Resource Manager-sjabloon
U uw geheime naam opgeven in een Azure Resource Manager-sjabloon die wordt gebruikt om een vm te maken, zoals in het volgende voorbeeld wordt weergegeven:

![Gebruik geheim in formule of omgeving](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Volgende stappen

- [Een VM maken met het geheim](devtest-lab-add-vm.md) 
- [Een formule maken met het geheim](devtest-lab-manage-formulas.md)
- [Een omgeving maken met behulp van het geheim](devtest-lab-create-environment-from-arm.md)
