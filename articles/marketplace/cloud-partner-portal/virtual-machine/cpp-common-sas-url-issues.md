---
title: Veelvoorkomende SAS-URL-problemen en -oplossingen voor de Azure Marketplace
description: Vermeld veelvoorkomende problemen rond het gebruik van URI's met gedeelde toegangen en mogelijke oplossingen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: c575389538230218e1e6e4f172ebcfbee8ee51dc
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273457"
---
# <a name="common-sas-url-issues-and-fixes"></a>Veelvoorkomende SAS-URL-problemen en -oplossingen

> [!IMPORTANT]
> Vanaf 13 april 2020 beginnen we met het verplaatsen van het beheer van uw Azure Virtual Machine-aanbiedingen naar partnercentrum. Na de migratie maak en beheer je je aanbiedingen in partnercentrum. Volg de instructies in [algemene URL-problemen en oplossingen voor SAS](https://aka.ms/AzureSAS_URL_FAQ) om uw gemigreerde aanbiedingen te beheren.

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
