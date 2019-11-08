---
title: Veelvoorkomende problemen met SAS-URL'S en oplossingen voor Azure Marketplace
description: Veelvoorkomende problemen met het gebruik van de Uri's van gedeelde Access-hand tekeningen en mogelijke oplossingen weer geven.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 09/27/2018
ms.author: pabutler
ms.openlocfilehash: 502ba1a65f9b0740a51c7a4da219cc87af494f27
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813319"
---
# <a name="common-sas-url-issues-and-fixes"></a>Veelvoorkomende problemen en oplossingen voor SAS-URL'S

De volgende tabel bevat een aantal veelvoorkomende problemen bij het werken met hand tekeningen voor gedeelde toegang (die worden gebruikt om de geüploade Vhd's voor uw oplossing te identificeren en te delen), samen met suggesties voor oplossingen.

| **Name** | **Fout bericht** | **Holpen** | 
| --------- | ------------------- | ------- | 
| &emsp;is *mislukt bij het kopiëren van afbeeldingen* |  |  |
| '? ' is niet gevonden in de SAS-URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS-URL bij met aanbevolen hulpprogram ma's. |
| de para meters "St" en "SE" niet in SAS-URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | De SAS-URL met de juiste **begin** -en **eind datum** waarden bijwerken. | 
| "SP = RL" niet in SAS-URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Werk de SAS-URL bij met machtigingen die zijn ingesteld als `Read` en `List`. | 
| SAS-URL heeft spaties in VHD-naam | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Werk de SAS-URL bij om spaties te verwijderen. |
| Verificatie fout SAS-URL | `Failure: Copying Images. Not able to download blob due to authorization error` | Controleer de SAS URI-indeling en corrigeer deze. Genereer indien nodig opnieuw. |
| De para meters van de SAS-URL "St" en "SE" hebben geen volledige datum-tijd specificatie | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | De para meters voor de **begin** -en **eind datum** van SAS-url's (`st` en `se` subtekenreeksen) moeten een volledige datum notatie hebben, zoals `11-02-2017T00:00:00Z`. Verkorte versies zijn niet geldig. (Sommige opdrachten in azure CLI kunnen standaard verkorte waarden genereren.) | 
|  |  |  |

Zie [using Shared Access signatures (SAS) (Engelstalig)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)voor meer informatie.
