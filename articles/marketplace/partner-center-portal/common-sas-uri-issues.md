---
title: Veelvoorkomende SAS URI-problemen en -oplossingen voor Azure Marketplace
description: Er zijn veelvoorkomende problemen opgetreden en voorgestelde oplossingen bij het werken met handtekeningen voor gedeelde toegang.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266238"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Veelvoorkomende SAS URI-problemen en -oplossingen

> [!IMPORTANT]
> We verplaatsen het beheer van uw Azure Virtual Machine-aanbiedingen van Cloud Partner Portal naar Partner Center. Totdat uw aanbiedingen zijn gemigreerd, volgt u de instructies in [algemene SAS URI-problemen en oplossingen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues) voor Cloud Partner Portal om uw aanbiedingen te beheren.

Hieronder volgen veelvoorkomende problemen bij het werken met handtekeningen voor gedeelde toegang (die worden gebruikt om de geüploade VHD's voor uw oplossing te identificeren en te delen), samen met voorgestelde oplossingen.

| **Probleem** | **Foutbericht** | **Fix** |
| --------- | ------------------- | ------- |
| *Fout bij het kopiëren van afbeeldingen* |  |  |
| "?" is niet te vinden in SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS URI bij met behulp van aanbevolen hulpprogramma's. |
| "st" en "se" parameters niet in SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS URI bij met de juiste **begin-** en **einddatumwaarden.** |
| "sp=rl" niet in SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS URI bij `Read` `List`met machtigingen die zijn ingesteld als en . |
| SAS URI heeft witte ruimtes in VHD-naam | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS URI bij om witte spaties te verwijderen. |
| SAS URI-autorisatiefout | `Failure: Copying Images. Not able to download blob due to authorization error.` | Controleer en corrigeer het SAS URI-formaat. Regenereren indien nodig. |
| SAS URI "st" en "se" parameters hebben geen volledige datum-tijd specificatie | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | SAS **URI-begin-** en`st` **einddatumparameters** (en `se` subtekenreeksen) moeten een `11-02-2017T00:00:00Z`volledige datum-tijdnotatie hebben, zoals . Verkorte versies zijn ongeldig (sommige opdrachten in Azure CLI kunnen standaard verkorte waarden genereren). |
|  |  |  |

Zie [SAS (Shared Access Signatures) gebruiken voor](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)meer informatie.
