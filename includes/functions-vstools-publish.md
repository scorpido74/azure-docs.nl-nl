---
title: Include-bestand
description: Include-bestand
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: db6f5927e1cbdb3527014ccbb277c7ec20b02600
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84231620"
---
1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren**.

1. Gebruik in **Een publicatiedoel kiezen** de publicatieopties die zijn weergegeven in de volgende tabel: 

    | Optie      | Beschrijving                                |
    | ------------ |  -------------------------------------------------- |
    | **Azure Functions-verbruiksabonnement** | Maak een functie-app in een Azure-cloudomgeving die in een [verbruiksabonnement](../articles/azure-functions/functions-scale.md#consumption-plan) wordt uitgevoerd. Wanneer u een verbruiksabonnement gebruikt, betaalt u alleen voor uitvoerbewerkingen van uw functie-app. Andere hostingabonnement kosten meer. Als u in een abonnement van een ander type dan Verbruik werkt, moet u de [schaal van uw functie-app](../articles/azure-functions/functions-scale.md) beheren.| 
    | **Create New** | Er wordt een nieuwe functie-app met gerelateerde resources gemaakt in Azure. <br/>Als u **Bestaande selecteren** kiest, worden alle bestanden in de bestaande functie-app in Azure overschreven door bestanden uit het lokale project. Gebruik deze optie alleen als u updates opnieuw publiceert naar een bestaande functie-app. |
    | **Uitvoeren uit pakketbestand** | Uw functie-app wordt geïmplementeerd met [Zip-implementeren](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) met de modus [Uitvoeren vanuit pakket](../articles/azure-functions/run-functions-from-deployment-package.md) ingeschakeld. Deze implementatie leidt tot betere prestaties en is de aanbevolen manier om uw functies uit te voeren. <br/>Als u deze optie niet gebruikt, moet u ervoor zorgen dat uw functie-app-project niet lokaal wordt uitgevoerd voordat u deze publiceert in Azure. |

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile.png" alt-text="Een publicatieprofiel maken":::


1. Selecteer **Profiel maken**. Als u zich nog niet hebt aangemeld bij uw Azure-account in Visual Studio, selecteert u **Aanmelden**. U kunt ook een gratis Azure-account maken.

1. In **App Service: Nieuwe maken**, gebruikt u de waarden in die zijn opgegeven in de volgende tabel:

    | Instelling      | Waarde  | Beschrijving                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Naam** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app uniek wordt aangeduid. Accepteer deze naam of voer een nieuwe in. Geldige tekens zijn `a-z`, `0-9` en `-`. |
    | **Abonnement** | Uw abonnement | Het te gebruiken Azure-abonnement. Accepteer dit abonnement of selecteer een nieuwe uit de vervolgkeuzelijst. |
    | **[Resourcegroep](../articles/azure-resource-manager/management/overview.md)** | Naam van uw resourcegroep |  De resourcegroep waarin uw functie-app moet worden gemaakt. Selecteer een bestaande resourcegroep uit de vervolgkeuzelijst of kies **Nieuwe** om een nieuwe resourcegroep te maken.|
    | **[Hostingabonnement](../articles/azure-functions/functions-scale.md)** | De naam van uw hostingabonnement | Selecteer **Nieuwe** om een serverloos abonnement te configureren. Zorg dat u het **Verbruik** kiest onder **Grootte**. Wanneer u uw project publiceert in een functie-app die wordt uitgevoerd in een [verbruiksabonnement](../articles/azure-functions/functions-scale.md#consumption-plan), betaalt u alleen voor uitvoeringen van uw functie-app. Andere hostingabonnement kosten meer. Als u in een abonnement van een ander type dan **Verbruik** werkt, moet u de [schaal van uw functie-app](../articles/azure-functions/functions-scale.md) beheren.  |
    | **Locatie** | Locatie van de app-service | Kies een **Locatie** in een [regio](https://azure.microsoft.com/regions/) bij u in de buurt of in de buurt van andere services die door uw functies worden gebruikt. |
    | **[Azure Storage](../articles/storage/common/storage-account-create.md)** | Storage-account voor algemeen gebruik | Er is een Azure Storage-account vereist voor de Functions-runtime. Selecteer **Nieuw** om een algemeen opslagaccount te configureren. U kunt ook een bestaand account kiezen dat voldoet aan de [vereisten voor een opslagaccount](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

    ![Het dialoogvenster Create App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Selecteer **Maken** om een functie-app en verwante resources met deze instellingen te maken in Azure en implementeer de code voor uw functieproject. 

1. Selecteer **Publiceren** en wacht tot de implementatie is voltooid. 

    Nadat de implementatie de hoofd0URL van de functie-app voltooid in Azure, wordt deze weergegeven in het tabblad **Publiceren**. 
    
1.  Kies **Beheren in Cloud Explorer** in het tabblad Publiceren. Hiermee wordt de nieuwe Azure-resource van de functie-app geopend in Cloud Explorer. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Succesbericht publiceren":::
    
    Met Cloud Explorer kunt u Visual Studio gebruiken om de inhoud van de site te bekijken, de functie-app te starten en stoppen en direct bladeren in resources van de functie-app in Azure en in Azure Portal. 