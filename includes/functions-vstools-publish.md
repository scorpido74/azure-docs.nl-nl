---
title: bestand opnemen
description: bestand opnemen
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: db6f5927e1cbdb3527014ccbb277c7ec20b02600
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84231620"
---
1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren**.

1. Gebruik in **een publicatie doel kiezen**de publicatie opties die zijn opgegeven in de volgende tabel: 

    | Optie      | Description                                |
    | ------------ |  -------------------------------------------------- |
    | **Azure Functions verbruiks abonnement** | Een functie-app maken in een Azure-cloud omgeving die wordt uitgevoerd in een [verbruiks abonnement](../articles/azure-functions/functions-scale.md#consumption-plan). Wanneer u een verbruiks abonnement gebruikt, betaalt u alleen voor uitvoeringen van uw functions-app. Andere hosting abonnementen nemen hogere kosten in bestaan. Als u een ander abonnement dan een verbruiks abonnement uitvoert, moet u de [schaal van de functie-app](../articles/azure-functions/functions-scale.md)beheren.| 
    | **Nieuwe maken** | Er wordt een nieuwe functie-app met gerelateerde resources gemaakt in Azure. <br/>Als u **bestaande selecteren**kiest, worden alle bestanden in de bestaande functie-app in azure overschreven door bestanden van het lokale project. Gebruik deze optie alleen wanneer u updates opnieuw publiceert naar een bestaande functie-app. |
    | **Uitvoeren vanuit pakket bestand** | Uw functie-app wordt geïmplementeerd met behulp van [zip-implementatie](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) met de modus voor [uitvoeren vanaf pakket](../articles/azure-functions/run-functions-from-deployment-package.md) ingeschakeld. Deze implementatie, wat resulteert in betere prestaties, is de aanbevolen manier om uw functies uit te voeren. <br/>Als u deze optie niet gebruikt, moet u ervoor zorgen dat uw functie-app-project niet lokaal wordt uitgevoerd voordat u naar Azure publiceert. |

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile.png" alt-text="Een publicatie profiel maken":::


1. Selecteer **Profiel maken**. Als u zich nog niet hebt aangemeld bij uw Azure-account vanuit Visual Studio, selecteert u **Aanmelden**. U kunt ook een gratis Azure-account maken.

1. In **app service: nieuwe maken**, gebruikt u de waarden die zijn opgegeven in de volgende tabel:

    | Instelling      | Waarde  | Beschrijving                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Naam** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app uniek wordt aangeduid. Accepteer deze naam of voer een nieuwe naam in. Geldige tekens zijn: `a-z` , `0-9` en `-` . |
    | **Abonnement** | Uw abonnement | Het te gebruiken Azure-abonnement. Accepteer dit abonnement of selecteer een nieuwe in de vervolg keuzelijst. |
    | **[Resourcegroep](../articles/azure-resource-manager/management/overview.md)** | Naam van de resource groep |  De resource groep waarin u de functie-app wilt maken. Selecteer een bestaande resource groep in de vervolg keuzelijst of kies **Nieuw** om een nieuwe resource groep te maken.|
    | **[Hosting plan](../articles/azure-functions/functions-scale.md)** | De naam van het hosting abonnement | Selecteer **Nieuw** om een serverloos abonnement te configureren. Zorg ervoor dat u het **verbruik** kiest onder **grootte**. Wanneer u uw project publiceert naar een functie-app die wordt uitgevoerd in een [verbruiks abonnement](../articles/azure-functions/functions-scale.md#consumption-plan), betaalt u alleen voor uitvoeringen van uw functions-app. Andere hosting abonnementen nemen hogere kosten in bestaan. Als u een ander abonnement dan **verbruik**gebruikt, moet u de [schaal van uw functie-app](../articles/azure-functions/functions-scale.md)beheren.  |
    | **Locatie** | Locatie van de app service | Kies een **locatie** in een [regio](https://azure.microsoft.com/regions/) bij u in de buurt of andere services waartoe uw functies toegang hebben. |
    | **[Azure Storage](../articles/storage/common/storage-account-create.md)** | Opslag account voor algemeen gebruik | Er is een Azure Storage-account vereist voor de runtime van functions. Selecteer **Nieuw** voor het configureren van een opslag account voor algemeen gebruik. U kunt ook een bestaand account kiezen dat voldoet aan de [vereisten voor het opslag account](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

    ![Het dialoogvenster Create App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Selecteer **maken** om een functie-app en de bijbehorende resources in azure te maken met deze instellingen en om uw functie project code te implementeren. 

1. Selecteer **publiceren** en wacht totdat de implementatie is voltooid. 

    Nadat de implementatie is voltooid, wordt de basis-URL van de functie-app in azure weer gegeven op het tabblad **publiceren** . 
    
1.  Kies beheren op het tabblad publiceren **in Cloud Explorer**. Hiermee opent u de nieuwe functie-app Azure-resource in Cloud Explorer. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Succesbericht publiceren":::
    
    Met Cloud Explorer kunt u Visual Studio gebruiken om de inhoud van de site weer te geven, de functie-app te starten en stoppen en rechtstreeks te bladeren naar app-resources in Azure en in de Azure Portal. 