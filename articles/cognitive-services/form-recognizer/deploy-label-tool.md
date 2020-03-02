---
title: Het hulp programma voor het labelen van het voor beeld van een formulier herkenning implementeren
titleSuffix: Azure Cognitive Services
description: Meer informatie over de verschillende manieren waarop u het hulp programma voor het labelen van het voor beeld van een formulier herkenning kunt implementeren voor meer informatie
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: pafarley
ms.openlocfilehash: fa419d7dd9668ac2ce8f2b0eb904117c7e22692d
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207842"
---
# <a name="deploy-the-sample-labeling-tool"></a>Het hulp programma voor het labelen van het voor beeld implementeren

Het hulp programma voor het labelen van het voor beeld van een formulier herkenning is een toepassing die wordt uitgevoerd in een docker-container. Het biedt een handige gebruikers interface die u kunt gebruiken om formulier documenten hand matig te labelen met het oog op het leren van toezicht. In de Snelstartgids [van de trein met labels](./quickstarts/label-tool.md) ziet u hoe u het hulp programma uitvoert op de lokale computer. Dit is het meest voorkomende scenario. 

In deze hand leiding worden alternatieve manieren beschreven waarop u het hulp programma voor het labelen van het voor beeld kunt implementeren en uitvoeren. 

## <a name="deploy-with-azure-container-instances"></a>Implementeren met Azure Container Instances

U kunt het hulp programma label uitvoeren in een docker-web-app-container. Maak eerst [een nieuwe web-app-resource](https://ms.portal.azure.com/#create/Microsoft.WebSite) op het Azure Portal. Vul het formulier in met de details van uw abonnement en resource groep. Voer de volgende gegevens in de vereiste velden in:
* **Publiceren**: docker-container
* **Besturings** Systeem: Linux

Vul op de volgende pagina de volgende velden in voor de installatie van de docker-container:

* **Opties**: één container
* **Bron van installatie kopie**: Azure container Registry
* **Toegangs type**: openbaar
* **Afbeelding en tag**: mcr.Microsoft.com/azure-Cognitive-Services/Custom-Form/labeltool:Latest

De volgende stappen zijn optioneel. Zodra de implementatie van uw app is voltooid, kunt u deze uitvoeren en het hulp programma label online bekijken.

### <a name="connect-to-azure-ad-for-authorization"></a>Verbinding maken met Azure AD voor autorisatie

We raden u aan om uw web-app te verbinden met Azure Active Director (AAD) zodat alleen iemand met uw referenties zich kan aanmelden en de app kan gebruiken. Volg de instructies in [uw app service-app configureren](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) om verbinding te maken met Aad.

## <a name="next-steps"></a>Volgende stappen

Ga terug naar de cursus [Train with labels](./quickstarts/label-tool.md) voor meer informatie over het gebruik van het hulp programma voor het hand matig labelen van trainings gegevens en het leren van Super visie.
