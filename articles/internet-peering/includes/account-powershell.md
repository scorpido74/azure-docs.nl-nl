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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774222"
---
Installeer en importeer de vereiste modules voordat u met de configuratie begint. U hebt beheerdersrechten nodig om modules in PowerShell te installeren.

1. Az-module installeren en importeren
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Az.Peering-module installeren en importeren
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Controleer of de modules met onderstaande opdracht prima worden geïmporteerd.
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
1. Als u nog geen resourcegroep hebt, moet u er een maken voordat u een peering maakt. U dit doen door de volgende opdracht uit te voeren:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Als u uw ASN en abonnement nog niet hebt gekoppeld, voert u stappen uit voor [Associate Peer ASN](../howto-subscription-association-powershell.md). Dit is vereist om een peering aan te vragen.

> [!NOTE]
> De locatie van de resourcegroep is onafhankelijk van de locatie waar u kiest voor het instellen van een peering.
&nbsp;
