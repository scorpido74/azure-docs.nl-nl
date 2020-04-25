---
title: Veelvoorkomende problemen met SAS-URL'S en oplossingen voor Azure Marketplace
description: Veelvoorkomende problemen met het gebruik van de Uri's van gedeelde Access-hand tekeningen en mogelijke oplossingen weer geven.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: 723762695d34380b7f237fa9082dc470dafcc8df
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147048"
---
# <a name="common-sas-url-issues-and-fixes"></a>Veelvoorkomende problemen en oplossingen voor SAS-URL'S

> [!IMPORTANT]
> Vanaf 13 april 2020 gaan we het beheer van uw Azure Virtual Machine-aanbiedingen naar het partner centrum verplaatsen. Na de migratie maakt en beheert u uw aanbiedingen in partner centrum. Volg de instructies in [veelvoorkomende problemen met SAS-url's en oplossingen](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues) voor het beheren van uw gemigreerde aanbiedingen.

De volgende tabel bevat een aantal veelvoorkomende problemen bij het werken met hand tekeningen voor gedeelde toegang (die worden gebruikt om de geüploade Vhd's voor uw oplossing te identificeren en te delen), samen met suggesties voor oplossingen.

| **Probleem** | **Fout bericht** | **Fix** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Fout bij het kopiëren van installatie kopieën* |  |  |
| '? ' is niet gevonden in de SAS-URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS-URL bij met aanbevolen hulpprogram ma's. |
| de para meters "St" en "SE" niet in SAS-URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | De SAS-URL met de juiste **begin** -en **eind datum** waarden bijwerken. | 
| "SP = RL" niet in SAS-URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Werk de SAS-URL bij met machtigingen `Read` die `List`zijn ingesteld als en. | 
| SAS-URL heeft spaties in VHD-naam | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS-URL bij om spaties te verwijderen. |
| Verificatie fout SAS-URL | `Failure: Copying Images. Not able to download blob due to authorization error` | Controleer de SAS URI-indeling en corrigeer deze. Genereer indien nodig opnieuw. |
| De para meters van de SAS-URL "St" en "SE" hebben geen volledige datum-tijd specificatie | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | De **begin** -en **eind datum** -para METERs van de SAS-URL`st` (en `se` subtekenreeksen) moeten een volledige `11-02-2017T00:00:00Z`datum-/tijdnotatie hebben, zoals. Verkorte versies zijn niet geldig. (Sommige opdrachten in azure CLI kunnen standaard verkorte waarden genereren.) | 
|  |  |  |

Zie [using Shared Access signatures (SAS) (Engelstalig)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)voor meer informatie.
