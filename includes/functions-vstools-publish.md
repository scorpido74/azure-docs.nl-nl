---
title: bestand opnemen
description: bestand opnemen
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/02/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: b96586c801acfc0419b80fa725a468dcb896f79e
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70197122"
---
1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren**.

2. Gebruik in het dialoog venster **een publicatie doel selecteren** de publicatie opties zoals opgegeven in de tabel onder de afbeelding: 

    ![Kies een publicatiedoel](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png)

    | Optie      | Description                                |
    | ------------ |  -------------------------------------------------- |
    | **Azure Functions verbruiks abonnement** |   Wanneer u uw project publiceert naar een functie-app die wordt uitgevoerd in een [verbruiks abonnement](../articles/azure-functions/functions-scale.md#consumption-plan), betaalt u alleen voor uitvoeringen van uw functions-app. Andere hosting abonnementen nemen hogere kosten in bestaan. Zie [Azure functions schalen en hosten](../articles/azure-functions/functions-scale.md)voor meer informatie. | 
    | **Nieuwe maken** | Er wordt een nieuwe functie-app met gerelateerde resources gemaakt in Azure. Als u **Bestaande selecteren** kiest, worden alle bestanden in de bestaande functie-app in Azure overschreven door bestanden uit het lokale project. Gebruik deze optie alleen als u updates opnieuw publiceert naar een bestaande functie-app. |
    | **Uitvoeren vanuit pakket bestand** | Uw functie-app wordt geïmplementeerd met behulp van [zip-implementatie](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) met de modus voor [uitvoeren vanaf pakket](../articles/azure-functions/run-functions-from-deployment-package.md) ingeschakeld. Dit is de aanbevolen manier om uw functies uit te voeren, wat resulteert in betere prestaties. Als u deze optie niet gebruikt, moet u ervoor zorgen dat uw functie-app-project niet lokaal wordt uitgevoerd voordat u naar Azure publiceert. |


3. Selecteer **Publiceren**. Als u zich nog niet hebt aangemeld bij uw Azure-account vanuit Visual Studio, selecteert u **Aanmelden**. U kunt ook een gratis Azure-account maken.

4. In de **app service: Maak een** nieuw dialoog venster en gebruik de **Hosting** -instellingen zoals opgegeven in de tabel onder de afbeelding:

    ![Het dialoogvenster App Service maken](./media/functions-vstools-publish/functions-visual-studio-publish.png)

    | Instelling      | Voorgestelde waarde  | Description                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app uniek wordt aangeduid. Geldige tekens zijn `a-z`, `0-9` en `-`. |
    | **Abonnement** | Kies uw abonnement | Het te gebruiken Azure-abonnement. |
    | **[Resourcegroep](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Naam van de resourcegroep waarin uw functie-app moet worden gemaakt. Kies **Nieuw** om een nieuwe resourcegroep te maken.|
    | **[Hosting plan](../articles/azure-functions/functions-scale.md)** | Verbruiksabonnement | Zorg ervoor dat u het **verbruik** kiest onder **grootte** nadat u **Nieuw** hebt geselecteerd om een serverloos plan te maken. Kies ook een **Locatie** in een [regio](https://azure.microsoft.com/regions/) bij u in de buurt of in de buurt van andere services die door uw functies worden gebruikt. Wanneer u in een abonnement van een ander type dan **Verbruik** werkt, moet u de [schaal van uw functie-app](../articles/azure-functions/functions-scale.md) beheren.  |
    | **[Azure Storage](../articles/storage/common/storage-quickstart-create-account.md)** | Opslag account voor algemeen gebruik | Er is een Azure-opslagaccount vereist voor de Functions-runtime. Selecteer **Nieuw** om een opslag account voor algemeen gebruik te maken. U kunt ook een bestaand account gebruiken dat voldoet aan de [vereisten voor een opslagaccount](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

5. Selecteer **maken** om een functie-app en gerelateerde resources in azure te maken met deze instellingen en om uw functie project code te implementeren. 

6. Noteer de **Site-URL** wanneer de implementatie is voltooid. Dit is het adres van uw functie-app in Azure.

    ![Succesbericht publiceren](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
