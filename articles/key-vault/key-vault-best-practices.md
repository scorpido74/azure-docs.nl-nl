---
title: Aanbevolen procedures voor het gebruik van Key Vault - Azure Key Vault | Microsoft Documenten
description: In dit document worden enkele van de aanbevolen procedures voor het gebruik van Key Vault uitgelegd
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: cb4bec8170466f0fc667b592d44b0858c41ccd84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270990"
---
# <a name="best-practices-to-use-key-vault"></a>Aanbevolen procedures voor het gebruik van Key Vault

## <a name="control-access-to-your-vault"></a>Toegang tot uw kluis beheren

Azure Key Vault is een cloudservice die versleutelingssleutels en -geheimen zoals certificaten, verbindingstekenreeksen en wachtwoorden beschermt. Omdat deze gegevens gevoelig en bedrijfskritisch zijn, moet u de toegang tot uw sleutelkluizen beveiligen door alleen geautoriseerde toepassingen en gebruikers toe te staan. In [dit artikel](key-vault-secure-your-key-vault.md) vindt u een overzicht van het Key Vault-toegangsmodel. Het verklaart verificatie en autorisatie en beschrijft hoe u de toegang tot uw sleutelkluizen beveiligen.

Suggesties terwijl u de toegang tot uw kluis regelt, zijn als volgt:
1. Toegang tot uw abonnement, resourcegroep en Key Vaults (RBAC) vergrendelen
2. Toegangsbeleid maken voor elke kluis
3. Gebruik de hoofdsom van de minste bevoegdheden om toegang te verlenen
4. Firewall- en [VNET-serviceeindpunten](key-vault-overview-vnet-service-endpoints.md) inschakelen

## <a name="use-separate-key-vault"></a>Aparte Sleutelkluis gebruiken

Onze aanbeveling is om een kluis per toepassing per omgeving te gebruiken (Ontwikkeling, Pre-Productie en Productie). Dit helpt u niet delen geheimen tussen omgevingen en vermindert ook de dreiging in het geval van een schending.

## <a name="backup"></a>Back-up

Zorg ervoor dat u regelmatig back-ups van uw [kluis](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) neemt bij het bijwerken/verwijderen/maken van objecten in een Vault.

## <a name="turn-on-logging"></a>Logboekregistratie inschakelen

[Schakel logboekregistratie in](key-vault-logging.md) voor je Vault. Stel ook waarschuwingen in.

## <a name="turn-on-recovery-options"></a>Herstelopties inschakelen

1. Soft [Delete](key-vault-ovw-soft-delete.md)inschakelen .
2. Schakel zuiveringsbeveiliging in als u wilt waken tegen het verwijderen van de geheime / kluis, zelfs nadat soft delete is ingeschakeld.
