---
title: Azure Key Vault een kluis naar een andere regio verplaatsen | Microsoft Docs
description: Richt lijnen voor het verplaatsen van een sleutel kluis naar een andere regio.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: e65a723ac9daafdc09896a50e197034104408df2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254146"
---
# <a name="moving-an-azure-key-vault-across-regions"></a>Een Azure Key Vault verplaatsen tussen regio's

## <a name="overview"></a>Overzicht

Key Vault biedt geen ondersteuning voor het verplaatsen van resources, waardoor het verplaatsen van een sleutel kluis naar een andere regio is toegestaan. In dit artikel worden tijdelijke oplossingen behandeld als u een belang rijke kluis moet verplaatsen naar een andere regio. Elke optie heeft beperkingen en het is essentieel om de implicaties van deze tijdelijke oplossingen te begrijpen voordat u ze in een productie omgeving probeert te proberen.

Als u een sleutel kluis naar een andere regio wilt verplaatsen, is de oplossing het maken van een nieuwe sleutel kluis in de gewenste regio en het hand matig kopiëren van elk afzonderlijk geheim van uw bestaande sleutel kluis naar de nieuwe sleutel kluis. Deze bewerking kan worden uitgevoerd op een van de volgende manieren.

## <a name="design-considerations"></a>Overwegingen bij het ontwerp

* Key Vault namen zijn wereld wijd uniek. U kunt dezelfde kluis naam niet opnieuw gebruiken.

* U moet toegangs beleid en netwerk configuratie-instellingen opnieuw configureren in de nieuwe sleutel kluis.

* U moet zacht verwijderen opnieuw configureren en de beveiliging opschonen in de nieuwe sleutel kluis.

* Bij de back-up-en herstel bewerking blijven de instellingen voor automatisch draaien mogelijk dat u deze instellingen opnieuw moet configureren.

## <a name="option-1---use-the-key-vault-backup-and-restore-commands"></a>Optie 1: de back-up-en herstel opdrachten van de sleutel kluis gebruiken

U kunt met behulp van de back-upopdracht back-ups maken van elk afzonderlijk geheim, dezelfde sleutel en elk certificaat in uw kluis. Uw geheimen worden gedownload als een versleutelde blob. U kunt de BLOB vervolgens herstellen in de nieuwe sleutel kluis. De opdrachten worden beschreven in de onderstaande koppeling.

[Azure Key Vault-opdrachten](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault)

### <a name="limitations"></a>Beperkingen

* U kunt geen back-up van een sleutel kluis in één geografie maken en deze in een andere geografie herstellen. Meer informatie over Azure-geografi. [Koppeling](https://azure.microsoft.com/global-infrastructure/geographies/)

* Met de back-upopdracht maakt u een back-up van alle versies van elk geheim. Als u een geheim hebt met een groot aantal eerdere versies (meer dan 10), is er een kans dat de aanvraag de Maxi maal toegestane aanvraag grootte overschrijdt en kan de bewerking mislukken.

## <a name="option-2---manually-download-and-upload-secrets"></a>Optie 2: geheimen hand matig downloaden en uploaden

Bepaalde geheime typen kunnen hand matig worden gedownload. U kunt certificaten bijvoorbeeld downloaden als een. pfx-bestand. Met deze optie worden de geografische beperkingen voor sommige geheime typen, zoals certificaten, geëlimineerd. U kunt de pfx-bestanden uploaden naar elke sleutel kluis in elke gewenste regio. Uw geheim wordt gedownload in een indeling die niet met een wacht woord is beveiligd. U bent verantwoordelijk voor het beveiligen van uw geheimen wanneer ze Key Vault verlaten terwijl de verplaatsing wordt uitgevoerd.
