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
ms.openlocfilehash: beffb2babefd86c2807e21e9337cba66f42fcfc2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678506"
---
Installeer en importeert voordat u met de configuratie begint, de vereiste modules. U hebt beheerdersrechten nodig om modules in PowerShell te installeren.

1. Installeer en importeer de Az-module.
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Installeer en importeer de Az.Peering module.
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Controleer met deze opdracht of de modules correct zijn geïmporteerd:
    ```powershell
    Get-Module
    ```
1. Meld u aan bij uw Azure-account met deze opdracht:
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
> Als u uw ASN en abonnement nog niet hebt gekoppeld, volgt u de stappen in [Associate Peer ASN](../howto-subscription-association-powershell.md). Deze actie is vereist om een peering aan te vragen.

> [!NOTE]
> De locatie van een resourcegroep is onafhankelijk van de locatie waar u kiest voor het instellen van een peering.
&nbsp;
