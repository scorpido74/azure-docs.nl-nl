---
title: Aanbevolen procedures voor het gebruik van Key Vault-Azure Key Vault | Microsoft Docs
description: In dit document worden enkele van de aanbevolen procedures voor het gebruik van Key Vault beschreven.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 923fb90f7f0e8eefec650515ed2a3b9b75d2ae77
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617917"
---
# <a name="best-practices-to-use-key-vault"></a>Aanbevolen procedures voor het gebruik van Key Vault

## <a name="control-access-to-your-vault"></a>Toegang tot uw kluis beheren

Azure Key Vault is een Cloud service die versleutelings sleutels en geheimen beveiligt, zoals certificaten, verbindings reeksen en wacht woorden. Omdat deze gegevens gevoelig en zakelijk kritiek zijn, moet u de toegang tot uw sleutel kluizen beveiligen door alleen geautoriseerde toepassingen en gebruikers toe te staan. Dit [artikel](secure-your-key-vault.md)bevat een overzicht van het model voor Key Vault toegang. Hierin worden verificatie en autorisatie uitgelegd en wordt beschreven hoe u de toegang tot uw sleutel kluizen kunt beveiligen.

Suggesties voor het beheren van de toegang tot uw kluis zijn als volgt:
1. De toegang tot uw abonnement, resource groep en sleutel kluizen (RBAC) vergren delen
2. Toegangs beleid maken voor elke kluis
3. Access principal met minimale bevoegdheden gebruiken om toegang te verlenen
4. Firewall-en [VNET-service-eind punten](overview-vnet-service-endpoints.md)inschakelen)

## <a name="use-separate-key-vault"></a>Afzonderlijke Key Vault gebruiken

Onze aanbeveling is een kluis per toepassing per omgeving te gebruiken (ontwikkeling, voor bereiding en productie). Zo kunt u geen geheimen delen tussen omgevingen en vermindert u ook de dreiging in het geval van een schending.

## <a name="backup"></a>Backup

Zorg ervoor dat u regel matig back-ups van uw [kluis](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) onderneemt op het bijwerken/verwijderen/maken van objecten in een kluis.

## <a name="turn-on-logging"></a>Logboek registratie inschakelen

[Logboek registratie inschakelen](logging.md)) voor uw kluis. Stel ook waarschuwingen in.

## <a name="turn-on-recovery-options"></a>Herstel opties inschakelen

1. [Zacht verwijderen](overview-soft-delete.md)inschakelen).
2. Schakel opschonen beveiliging in als u wilt dat de geheime sleutel wordt verwijderd tegen het verwijderen van het geheim of de kluis, zelfs nadat het zacht verwijderen is ingeschakeld.
