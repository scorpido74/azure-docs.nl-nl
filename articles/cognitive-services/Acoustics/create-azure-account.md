---
title: Project Acoustics Azure Batch Account Setup
titlesuffix: Azure Cognitive Services
description: Hiermee wordt beschreven het instellen van een Azure Batch-account voor gebruik met Project Acoustics Unity en Unreal engine-integraties.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 8f0f726d9d23f20698d3510ad674331ad74fb703
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68855081"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Project Acoustics Azure Batch Account Setup
Hiermee wordt beschreven het instellen van een Azure Batch-account voor gebruik met Project Acoustics Unity en Unreal engine-integraties.

## <a name="get-an-azure-subscription"></a>Neem een abonnement op Azure
Een [Azure-abonnement](https://azure.microsoft.com/free/) is vereist voordat batch- en opslagaccounts worden ingesteld. Als u zich voor de eerste keer aanmeldt, biedt Azure een aantal tijdbeperkte gratis resources en $ 200-tegoed.

## <a name="create-azure-batch-and-storage-accounts"></a>Azure-batch- en opslagaccounts maken
Volg vervolgens [deze instructies](https://docs.microsoft.com/azure/batch/batch-account-create-portal) om uw Azure Batch en bijbehorende Azure Storage-accounts in te stellen.

Kies standaardopties voor zowel Batch- als Opslagaccounts:
  
  ![Schermafbeelding van nieuwe accountsopties voor Azure Batch met standaardinstellingen](media/new-batch-account-create.png)

  ![Schermafbeelding van nieuwe accountsopties voor Azure Storage met standaardinstellingen](media/batch-storage-account-create.png)

Het duurt een paar minuten voordat Azure de accounts implementeert. Zoek naar een voltooiingsmelding in de rechterbovenhoek van het portaal.
  
  ![Schermafbeelding van azure-accounts die zijn geïmplementeerd, wordt geïmplementeerd](media/batch-accounts-deploy-notification.png)

Uw accounts moeten nu zichtbaar zijn op uw dashboard.
  
  ![Schermafbeelding van het Azure-portaldashboard met een Batch- en Opslagaccount](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Gebruikersinterface voor het bakken van akoestiek instellen met Azure-referenties
Klik op de koppeling Batch-account op het dashboard en klik vervolgens op de koppeling **Sleutels** op de pagina Batch-account om toegang te krijgen tot uw referenties.
  
  ![Schermafbeelding van Azure Batch-account met koppeling naar pagina Sleutels gemarkeerd](media/batch-access-keys.png)

  ![Schermafbeelding van de pagina Azure Batch-accountsleutels met toegangssleutels](media/batch-keys-info.png)

Klik op de koppeling **Opslagaccount** op de pagina om toegang te krijgen tot uw Azure Storage-accountreferenties.
  
  ![Schermafbeelding van de pagina Azure Storage-accountsleutels met toegangssleutels](media/storage-keys-info.png)

Voer deze referenties in de [Unity bake plugin](unity-baking.md) of Unreal bake [plugin](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Knooppunttypen en regioondersteuning
Project Acoustics vereist voor Fsv2- en H-serie compute optimized Azure VM-knooppunten die mogelijk niet in alle Azure-regio's worden ondersteund. Controleer [deze tabel](https://azure.microsoft.com/global-infrastructure/services) om er zeker van te zijn dat u de juiste locatie kiest voor uw Batch-account.
![Schermafbeelding van Azure Virtual Machines by Region](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Uw quotum upgraden
Azure Batch-accounts zijn ingericht voor het maken van accounts met een limiet van 20 rekenkernen. We willen deze limiet misschien verhogen voor snellere baktijden, omdat u uw akoestiekwerklast paralleleren over veel knooppunten, tot het aantal sondepunten in uw scène. U een quotumverhoging aanvragen door op de koppeling **Quota** op uw Azure Batch-portalpagina te klikken en vervolgens op **Verhoging van het aanvraagquotum**te klikken:

![Schermafbeelding van de pagina Azure Quota](media/azure-quotas.png)

## <a name="next-steps"></a>Volgende stappen
* Integreer de Project Acoustics-plug-in in uw [Unity-](unity-integration.md) of [Unreal-project](unreal-integration.md)

