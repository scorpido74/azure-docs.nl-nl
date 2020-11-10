---
title: Aanbevolen procedures voor het gebruik van Key Vault-Azure Key Vault | Microsoft Docs
description: Meer informatie over aanbevolen procedures voor Azure Key Vault, waaronder het beheren van toegang, wanneer u afzonderlijke sleutel kluizen, back-ups, logboek registratie en herstel opties gebruikt.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 3d959cccd4fbce24e36b1ed62bc6bf417af23c82
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445503"
---
# <a name="best-practices-to-use-key-vault"></a>Aanbevolen procedures voor het gebruik van Key Vault

## <a name="control-access-to-your-vault"></a>Toegang tot uw kluis beheren

Azure Key Vault is een cloudservice die versleutelingssleutels en geheimen, zoals certificaten, verbindingsreeksen en wachtwoorden, beveiligt. Omdat deze gegevens gevoelig en zakelijk kritiek zijn, moet u de toegang tot uw sleutel kluizen beveiligen door alleen geautoriseerde toepassingen en gebruikers toe te staan. In dit [artikel](secure-your-key-vault.md) vindt u een overzicht van het toegangs model van Key Vault. Hierin worden verificatie en autorisatie uitgelegd en wordt beschreven hoe u de toegang tot uw sleutel kluizen kunt beveiligen.

Suggesties voor het beheren van de toegang tot uw kluis zijn als volgt:
1. Vergren deling van toegang tot uw abonnement, resource groep en sleutel kluizen (Azure RBAC)
2. Toegangs beleid maken voor elke kluis
3. Access principal met minimale bevoegdheden gebruiken om toegang te verlenen
4. Firewall-en [VNET-service-eind punten](overview-vnet-service-endpoints.md) inschakelen

## <a name="use-separate-key-vault"></a>Afzonderlijke Key Vault gebruiken

Onze aanbeveling is een kluis per toepassing per omgeving te gebruiken (ontwikkeling, voor bereiding en productie). Zo kunt u geen geheimen delen tussen omgevingen en vermindert u ook de dreiging in het geval van een schending.

## <a name="backup"></a>Backup

Zorg ervoor dat u regel matig back-ups van uw kluis onderneemt op het bijwerken/verwijderen/maken van objecten in een kluis.

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell-back-upopdrachten

* [Back-upcertificaat](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate?view=azurermps-6.13.0)
* [Back-upsleutel](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey?view=azurermps-6.13.0)
* [Back-upgeheim](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret?view=azurermps-6.13.0)

### <a name="azure-cli-backup-commands"></a>Back-upopdrachten van Azure CLI

* [Back-upcertificaat](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-backup)
* [Back-upsleutel](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-backup)
* [Back-upgeheim](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-backup)


## <a name="turn-on-logging"></a>Logboek registratie inschakelen

[Schakel logboek registratie in](logging.md) voor uw kluis. Stel ook waarschuwingen in.

## <a name="turn-on-recovery-options"></a>Herstel opties inschakelen

1. Schakel [zacht verwijderen](soft-delete-overview.md)in.
2. Schakel opschonen beveiliging in als u wilt dat de geheime sleutel wordt verwijderd tegen het verwijderen van het geheim of de kluis, zelfs nadat het zacht verwijderen is ingeschakeld.