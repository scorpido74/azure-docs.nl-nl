---
title: Avere vFXT non-owner workaround - Azure
description: Tijdelijke oplossing om gebruikers zonder toestemming van de eigenaar van een abonnement toestemming te geven om Avere vFXT voor Azure te implementeren
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 1b411fe465a67f8ea5421ac0dc93348b4e92e8ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153272"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Toestaan dat niet-eigenaren Avere vFXT implementeren

Deze instructies zijn een tijdelijke oplossing waarmee een gebruiker zonder bevoegdheden voor abonnementseigenaar een Avere vFXT voor Azure-systeem kan maken.

(De aanbevolen manier om het Avere vFXT-systeem te implementeren, is om een gebruiker met eigenaarbevoegdheden de creatiestappen te laten uitvoeren, zoals uitgelegd in [Voorbereiden op het maken van de Avere vFXT](avere-vfxt-prereqs.md).)  

De tijdelijke oplossing omvat het maken van een extra toegangsrol die de gebruikers voldoende machtigingen geeft om het cluster te installeren. De rol moet worden gemaakt door een eigenaar van een abonnement en een eigenaar moet deze toewijzen aan de juiste gebruikers.

Een abonnement eigenaar moet ook [de gebruiksvoorwaarden](avere-vfxt-prereqs.md) voor de Avere vFXT marktplaats afbeelding te accepteren.

> [!IMPORTANT]
> Al deze stappen moeten worden uitgevoerd door een gebruiker met eigenaarbevoegdheden voor het abonnement dat voor het cluster wordt gebruikt.

1. Kopieer deze regels en sla ze op `averecreatecluster.json`in een bestand (bijvoorbeeld). Gebruik uw abonnements-ID in de `AssignableScopes` verklaring.

   ```json
   {
       "AssignableScopes": ["/subscriptions/<SUBSCRIPTION_ID>"],
       "Name": "avere-create-cluster",
       "IsCustom": "true"
       "Description": "Can create Avere vFXT clusters",
       "NotActions": [],
       "Actions": [
           "Microsoft.Authorization/*/read",
           "Microsoft.Authorization/roleAssignments/*",
           "Microsoft.Authorization/roleDefinitions/*",
           "Microsoft.Compute/*/read",
           "Microsoft.Compute/availabilitySets/*",
           "Microsoft.Compute/virtualMachines/*",
           "Microsoft.Network/*/read",
           "Microsoft.Network/networkInterfaces/*",
           "Microsoft.Network/routeTables/write",
           "Microsoft.Network/routeTables/delete",
           "Microsoft.Network/routeTables/routes/delete",
           "Microsoft.Network/virtualNetworks/subnets/join/action",
           "Microsoft.Network/virtualNetworks/subnets/read",

           "Microsoft.Resources/subscriptions/resourceGroups/read",
           "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
           "Microsoft.Storage/*/read",
           "Microsoft.Storage/storageAccounts/listKeys/action"
       ],
   }
   ```

1. Voer deze opdracht uit om de rol te maken:

   `az role definition create --role-definition <PATH_TO_FILE>`

    Voorbeeld:

    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. Wijs deze rol toe aan de gebruiker die het cluster maakt:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

Na het voltooien van dit proces geeft de rol elke gebruiker die deze de volgende machtigingen voor het abonnement heeft toegewezen:

* De netwerkinfrastructuur maken en configureren
* De clustercontroller maken
* Clustercreatiescripts uitvoeren vanuit de clustercontroller om het cluster te maken
