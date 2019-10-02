---
title: Azure Managed Identities gebruiken om omgevingen te maken in DevTest Labs | Microsoft Docs
description: Meer informatie over het gebruik van beheerde identiteiten in azure voor het implementeren van omgevingen in een lab in Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: spelluru
ms.openlocfilehash: fc06d5d36cb6004c79bec79838c8f0d51961d560
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720154"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Door Azure beheerde identiteiten gebruiken voor het implementeren van omgevingen in een Lab 
Als eigenaar van een lab kunt u een beheerde identiteit gebruiken voor het implementeren van omgevingen in een lab. Deze functie is handig in scenario's waarin de omgeving bevat of verwijzingen naar Azure-resources, zoals sleutel kluizen, gemeen schappelijke afbeeldings galerieën en netwerken die zich buiten de resource groep van de omgeving bevinden. Het maakt het mogelijk sandbox-omgevingen te maken die niet beperkt zijn tot de resource groep van die omgeving.

> [!NOTE]
> Op dit moment wordt één door de gebruiker toegewezen identiteit ondersteund per Lab. 

## <a name="prerequisites"></a>Vereisten
- [Een rol maken, weer geven, verwijderen of toewijzen aan een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

## <a name="use-azure-portal"></a>Azure Portal gebruiken
In deze sectie kunt u als eigenaar van het lab de Azure Portal gebruiken om een door de gebruiker beheerde identiteit toe te voegen aan het lab. 

1. Selecteer op de pagina Lab de optie **configuratie en beleid**. 
1. Selecteer **identiteit** in het gedeelte **instellingen** .
1. Als u een door de gebruiker toegewezen identiteit wilt toevoegen, selecteert u **toevoegen** op de werk balk. 
1. Selecteer een **identiteit** in een vooraf gevulde vervolg keuzelijst.
1. Selecteer **OK**.

    ![Door de gebruiker beheerde identiteit toevoegen](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. U ziet de toegevoegde door de gebruiker beheerde identiteit in de lijst. 

    ![Door de gebruiker beheerde identiteit in de lijst](./media/use-managed-identities-environments/identity-in-list.png)

Wanneer het lab is opgeslagen, gebruikt deze identiteit tijdens het implementeren van alle test omgevingen. U kunt ook toegang krijgen tot de identiteits bron in azure door de identiteit te selecteren in de lijst. 

De eigenaar van het lab hoeft niets te doen bij het implementeren van een omgeving zolang de identiteit die aan het lab is toegevoegd, machtigingen heeft voor de externe bronnen waartoe de omgeving toegang moet hebben. 

Als u de door de gebruiker beheerde identiteit die is toegewezen aan het lab, wilt wijzigen, moet u eerst de identiteit die aan het lab is gekoppeld, verwijderen en vervolgens een andere id toevoegen aan het lab. Als u een aan het lab gekoppelde identiteit wilt verwijderen, selecteert u **... (weglatings tekens)** en klik op **verwijderen**. 

![Door de gebruiker beheerde identiteit in de lijst](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>API gebruiken

1. Nadat u een identiteit hebt gemaakt, noteert u de resource-ID van deze identiteit. Deze moet eruitzien als in het volgende voor beeld: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Voer met behulp van **Fiddler**een put-opdracht uit die vergelijkbaar is met het volgende voor beeld. Voor de naam van de service loper raden we u aan de naam van de identiteit te gebruiken, zelfs als u een wille keurige naam kunt opgeven. 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
    }
    ```
 
    Hier volgt een voorbeeld: 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
    }
    ```
 
Zodra de door de gebruiker toegewezen identiteit aan het lab is toegevoegd, gebruikt de Azure DevTest Labs-service deze tijdens de implementatie van Azure Resource Manager omgevingen. Als u bijvoorbeeld uw Resource Manager-sjabloon nodig hebt om toegang te krijgen tot een installatie kopie van een externe gedeelde installatie kopie, moet u ervoor zorgen dat de identiteit die u aan de Lab hebt toegevoegd, mini maal vereiste machtigingen heeft voor de resource van de gedeelde installatie kopie galerie. 
