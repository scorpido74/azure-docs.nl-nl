---
title: Veelvoorkomende problemen met SAS-URI'S en oplossingen-Azure Marketplace
description: Veelvoorkomende problemen en oplossingen worden aanbevolen bij het werken met hand tekeningen voor gedeelde toegang.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266238"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Veelvoorkomende problemen met SAS-URI'S en oplossingen

> [!IMPORTANT]
> We verplaatsen het beheer van uw Azure Virtual Machine-aanbiedingen van Cloud Partner-portal naar het partner centrum. Totdat uw aanbiedingen zijn gemigreerd, volgt u de instructies in [algemene SAS URI-problemen en oplossingen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues) voor Cloud Partner-Portal voor het beheren van uw aanbiedingen.

Hieronder volgen enkele veelvoorkomende problemen bij het werken met hand tekeningen voor gedeelde toegang (die worden gebruikt om de geüploade Vhd's voor uw oplossing te identificeren en te delen), samen met voorgestelde oplossingen.

| **Probleem** | **Fout bericht** | **Fix** |
| --------- | ------------------- | ------- |
| *Fout bij het kopiëren van installatie kopieën* |  |  |
| '? ' is niet gevonden in de SAS-URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS-URI bij met aanbevolen hulpprogram ma's. |
| de para meters "St" en "SE" niet in SAS-URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS-URI met de juiste **begin datum** en **eind datum** waarde bij. |
| "SP = RL" niet in SAS-URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS-URI bij met machtigingen `Read` die `List`zijn ingesteld als en. |
| SAS-URI bevat spaties in naam van VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS-URI bij om spaties te verwijderen. |
| Fout met SAS URI-autorisatie | `Failure: Copying Images. Not able to download blob due to authorization error.` | Controleer de SAS URI-indeling en corrigeer deze. Genereer indien nodig opnieuw. |
| De para meters van de SAS-URI "St" en "SE" hebben geen volledige datum-tijd specificatie | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | De **begin** -en **eind** datum parameters van de`st` SAS `se` -URI (en subtekenreeksen) moeten de volledige datum-tijd `11-02-2017T00:00:00Z`notatie hebben, zoals. Verkorte versies zijn ongeldig (sommige opdrachten in azure CLI kunnen standaard verkorte waarden genereren). |
|  |  |  |

Zie [using Shared Access signatures (SAS) (Engelstalig)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)voor meer informatie.
