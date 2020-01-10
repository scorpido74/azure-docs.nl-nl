---
title: bestand opnemen
titleSuffix: Azure
description: bestand opnemen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3a5f7157ef8f3645dd03ec93684238dd8bbc067e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774222"
---
Installeer en importeer de vereiste modules voordat u begint met de configuratie. U hebt beheerders bevoegdheden nodig om modules in Power shell te installeren.

1. Installeren en importeren AZ module
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Installeren en importeren AZ. peering module
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Controleer of de modules nauw keurig zijn geïmporteerd met behulp van de onderstaande opdracht.
    ```powershell
    Get-Module
    ```
1. Meld u aan bij uw Azure-account met de volgende opdracht.
    ```powershell
    Connect-AzAccount
    ```
1. Controleer de abonnementen voor het account en selecteer het abonnement waarin u een peering wilt maken.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Als u nog geen resource groep hebt, moet u er een maken voordat u een peering maakt. U kunt dit doen door het uitvoeren van de volgende opdracht uit:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Als u uw ASN en abonnement nog niet hebt gekoppeld, voert u de stappen uit voor het [koppelen van peer-ASN](../howto-subscription-association-powershell.md). Dit is vereist om een peering aan te vragen.

> [!NOTE]
> De locatie van de resource groep is onafhankelijk van de locatie waar u voor het instellen van een peering kiest.
&nbsp;
