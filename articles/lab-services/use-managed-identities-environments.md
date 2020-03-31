---
title: Azure-beheerde identiteiten gebruiken om omgevingen te maken in DevTest Labs | Microsoft Documenten
description: Meer informatie over het gebruik van beheerde identiteiten in Azure om omgevingen in een lab in Azure DevTest Labs te implementeren.
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
ms.openlocfilehash: a4ba4206c01e492f2ae980c5806de1e72c7051c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931160"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Azure-beheerde identiteiten gebruiken om omgevingen in een lab te implementeren 
Als eigenaar van een lab u een beheerde identiteit gebruiken om omgevingen in een lab te implementeren. Deze functie is handig in scenario's waarin de omgeving verwijzingen bevat of bevat naar Azure-bronnen, zoals sleutelkluizen, gedeelde afbeeldingsgalerieën en netwerken die zich buiten de brongroep van de omgeving bevinden. Hiermee u sandbox-omgevingen maken die niet beperkt zijn tot de resourcegroep van die omgeving.

> [!NOTE]
> Momenteel wordt één door de gebruiker toegewezen identiteit per lab ondersteund. 

## <a name="prerequisites"></a>Vereisten
- [Een rol maken, aanbieden, verwijderen of toewijzen aan een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure-portal.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 

## <a name="use-azure-portal"></a>Azure Portal gebruiken
In deze sectie gebruikt u als eigenaar van een lab de Azure-portal om een door de gebruiker beheerde identiteit aan het lab toe te voegen. 

1. Selecteer op de labpagina **Configuratie en beleid**. 
1. Selecteer **Identiteit** in de sectie **Instellingen.**
1. Als u een door een gebruiker toegewezen identiteit wilt toevoegen, selecteert u **Toevoegen** op de werkbalk. 
1. Selecteer een **identiteit** in een vooraf ingevulde vervolgkeuzelijst.
1. Selecteer **OK**.

    ![Door de gebruiker beheerde identiteit toevoegen](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. U ziet de toegevoegde door de gebruiker beheerde identiteit in de lijst. 

    ![Door de gebruiker beheerde identiteit in de lijst](./media/use-managed-identities-environments/identity-in-list.png)

Eenmaal opgeslagen, zal het lab deze identiteit gebruiken tijdens het implementeren van alle labomgevingen. U ook toegang krijgen tot de identiteitsbron in Azure door de identiteit in de lijst te selecteren. 

De eigenaar van het lab hoeft niets speciaals te doen tijdens het implementeren van een omgeving, zolang de identiteit die aan het lab is toegevoegd, machtigingen heeft voor de externe bronnen die de omgeving moet openen. 

Als u de door de gebruiker beheerde identiteit wilt wijzigen die aan het lab is toegewezen, verwijdert u eerst de identiteit die aan het lab is gekoppeld en voegt u er vervolgens nog een toe aan het lab. Als u een identiteit wilt verwijderen die aan het lab is gekoppeld, selecteert u **... (ellips)** en klik op **Verwijderen**. 

![Door de gebruiker beheerde identiteit in de lijst](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>API gebruiken

1. Noteer na het maken van een identiteit de bron-id van deze identiteit. Het moet er uitzien als de volgende steekproef: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Voer een PUT Https-methode `ServiceRunner` uit om een nieuwe bron toe te voegen aan het lab, vergelijkbaar met het volgende voorbeeld. Service runner resource is een proxy resource voor het beheren en beheren van beheerde identiteiten in DevTest Labs. De naam van de serviceagent kan een geldige naam zijn, maar we raden u aan de naam van de beheerde identiteitsbron te gebruiken. 
 
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
        "properties":{
            "identityUsageType":"Environment"
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
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
Zodra de door de gebruiker toegewezen identiteit aan het lab is toegevoegd, gebruikt de Azure DevTest Labs-service deze terwijl azure resource manager-omgevingen worden geïmplementeerd. Als u bijvoorbeeld de sjabloon Resourcemanager nodig hebt om toegang te krijgen tot een externe afbeelding van een gedeelde afbeelding, moet u ervoor zorgen dat de identiteit die u aan het lab hebt toegevoegd, minimaal vereiste machtigingen heeft voor de bron van de gedeelde afbeeldingsgalerie. 
