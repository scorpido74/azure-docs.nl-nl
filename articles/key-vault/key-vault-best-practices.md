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
ms.openlocfilehash: cb4bec8170466f0fc667b592d44b0858c41ccd84
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184824"
---
# <a name="best-practices-to-use-key-vault"></a>Aanbevolen procedures voor het gebruik van Key Vault

## <a name="control-access-to-your-vault"></a>Toegang tot uw kluis beheren

Azure Key Vault is een Cloud service die versleutelings sleutels en geheimen beveiligt, zoals certificaten, verbindings reeksen en wacht woorden. Omdat deze gegevens gevoelig en zakelijk kritiek zijn, moet u de toegang tot uw sleutel kluizen beveiligen door alleen geautoriseerde toepassingen en gebruikers toe te staan. In dit [artikel](key-vault-secure-your-key-vault.md) vindt u een overzicht van het toegangs model van Key Vault. Hierin worden verificatie en autorisatie uitgelegd en wordt beschreven hoe u de toegang tot uw sleutel kluizen kunt beveiligen.

Suggesties voor het beheren van de toegang tot uw kluis zijn als volgt:
1. De toegang tot uw abonnement, resource groep en sleutel kluizen (RBAC) vergren delen
2. Toegangs beleid maken voor elke kluis
3. Access principal met minimale bevoegdheden gebruiken om toegang te verlenen
4. Firewall-en [VNET-service-eind punten](key-vault-overview-vnet-service-endpoints.md) inschakelen

## <a name="use-separate-key-vault"></a>Afzonderlijke Key Vault gebruiken

Onze aanbeveling is een kluis per toepassing per omgeving te gebruiken (ontwikkeling, voor bereiding en productie). Zo kunt u geen geheimen delen tussen omgevingen en vermindert u ook de dreiging in het geval van een schending.

## <a name="backup"></a>Back-up maken

Zorg ervoor dat u regel matig back-ups van uw [kluis](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) onderneemt op het bijwerken/verwijderen/maken van objecten in een kluis.

## <a name="turn-on-logging"></a>Logboek registratie inschakelen

[Schakel logboek registratie in](key-vault-logging.md) voor uw kluis. Stel ook waarschuwingen in.

## <a name="turn-on-recovery-options"></a>Herstel opties inschakelen

1. Schakel [zacht verwijderen](key-vault-ovw-soft-delete.md)in.
2. Schakel de optie Beveiliging opschonen in als u wilt dat de geheime/kluis wordt verwijderd, zelfs nadat de functie voor het uitvoeren van een tijdelijke verwijdering is ingeschakeld.
