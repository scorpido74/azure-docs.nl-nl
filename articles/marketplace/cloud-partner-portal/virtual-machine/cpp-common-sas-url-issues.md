---
title: Veelvoorkomende SAS-URL-problemen en -oplossingen voor de Azure Marketplace
description: Vermeld veelvoorkomende problemen rond het gebruik van URI's met gedeelde toegangen en mogelijke oplossingen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: 47702959474a352a8e13710ec850f789dee4d517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278158"
---
# <a name="common-sas-url-issues-and-fixes"></a>Veelvoorkomende SAS-URL-problemen en -oplossingen

In de volgende tabel worden enkele van de veelvoorkomende problemen weergegeven bij het werken met gedeelde toegangshandtekeningen (die worden gebruikt om de geüploade VHD's voor uw oplossing te identificeren en te delen), samen met voorgestelde oplossingen.

| **Probleem** | **Foutbericht** | **Fix** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Fout bij het kopiëren van afbeeldingen* |  |  |
| "?" is niet te vinden in de Url van SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS-URL bij met behulp van aanbevolen hulpprogramma's. |
| "st" en "se" parameters niet in SAS URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS-URL bij met de juiste **begin-** en **einddatumwaarden.** | 
| "sp=rl" niet in SAS URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Werk de SAS-URL bij `Read` `List`met machtigingen die zijn ingesteld als en . | 
| SAS URL heeft witte spaties in VHD-naam | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS-URL bij om witte spaties te verwijderen. |
| SAS URL-autorisatiefout | `Failure: Copying Images. Not able to download blob due to authorization error` | Controleer en corrigeer het SAS URI-formaat. Regenereren indien nodig. |
| SAS URL "st" en "se" parameters hebben geen volledige datum-tijd specificatie | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | De **begin-** en **einddatum** parameters`st` `se` (en subtekenreeksen) van de SAS-URL zijn vereist voor een volledige datumnotatie, zoals `11-02-2017T00:00:00Z`. Verkorte versies zijn niet geldig. (Sommige opdrachten in Azure CLI genereren standaard mogelijk verkorte waarden.) | 
|  |  |  |

Zie [Gedeelde toegangshandtekeningen (SAS) gebruiken voor](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)meer informatie.
