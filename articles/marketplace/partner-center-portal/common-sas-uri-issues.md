---
title: Veelvoorkomende problemen met SAS-URI'S en oplossingen-Azure Marketplace
description: Veelvoorkomende problemen en oplossingen worden aanbevolen bij het werken met hand tekeningen voor gedeelde toegang.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 56db3562efdc0406e745fd38b73df0a473d0ecd5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83724595"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Veelvoorkomende problemen met SAS-URI'S en oplossingen

Hieronder volgen enkele veelvoorkomende problemen bij het werken met hand tekeningen voor gedeelde toegang (die worden gebruikt om de geüploade Vhd's voor uw oplossing te identificeren en te delen), samen met voorgestelde oplossingen.

| **Probleem** | **Fout bericht** | **Fix** |
| --------- | ------------------- | ------- |
| *Fout bij het kopiëren van installatie kopieën* |  |  |
| '? ' is niet gevonden in de SAS-URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS-URI bij met aanbevolen hulpprogram ma's. |
| de para meters "St" en "SE" niet in SAS-URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS-URI met de juiste **begin datum** en **eind datum** waarde bij. |
| "SP = RL" niet in SAS-URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS-URI bij met machtigingen die zijn ingesteld als `Read` en `List` . |
| SAS-URI bevat spaties in naam van VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS-URI bij om spaties te verwijderen. |
| Fout met SAS URI-autorisatie | `Failure: Copying Images. Not able to download blob due to authorization error.` | Controleer de SAS URI-indeling en corrigeer deze. Genereer indien nodig opnieuw. |
| De para meters van de SAS-URI "St" en "SE" hebben geen volledige datum-tijd specificatie | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | De **begin** -en **eind** datum parameters van de SAS-URI ( `st` en `se` subtekenreeksen) moeten de volledige datum-tijd notatie hebben, zoals `11-02-2017T00:00:00Z` . Verkorte versies zijn ongeldig (sommige opdrachten in azure CLI kunnen standaard verkorte waarden genereren). |
|  |  |  |

Zie [using Shared Access signatures (SAS) (Engelstalig)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)voor meer informatie.
