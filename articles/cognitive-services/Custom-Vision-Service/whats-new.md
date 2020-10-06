---
title: Wat is er nieuw in Custom Vision?
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat nieuws over Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 06/29/2020
ms.author: pafarley
ms.openlocfilehash: 019a9264beddafbd4585810967551e064592e94d
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602520"
---
# <a name="whats-new-in-custom-vision"></a>Wat is er nieuw in Custom Vision

Meer informatie over nieuwe functies in de service. Dit kunnen opmerkingen bij de release, video's, blogposts en andere soorten informatie zijn. Voeg een bladwijzer toe aan deze pagina om up-to-date te blijven over de service.

## <a name="july-2020"></a>Juli 2020

### <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

* Custom Vision biedt ondersteuning voor op rollen gebaseerd toegangsbeheer voor Azure (Azure RBAC), een autorisatiesysteem voor het beheren van afzonderlijke toegang tot Azure-resources. Zie [Op rollen gebaseerd toegangsbeheer](./role-based-access-control.md) voor meer informatie over het beheren van de toegang tot uw Custom Vision-projecten.

### <a name="subset-training"></a>Trainen met subsets

* Bij het trainen van objectdetectieprojecten kunt u ervoor kiezen om te trainen op een subset van de door u toegepaste tags. U kunt dit doen als u bepaalde tags nog niet vaak genoeg hebt toegepast, maar u wel voldoende andere codes hebt toegepast. Volg de [Quickstart voor clientbibliotheek](./quickstarts/object-detection.md) voor C# of Python voor meer informatie.

### <a name="azure-storage-notifications"></a>Opslagmeldingen in Azure

* U kunt uw Custom Vision-project integreren met een Azure Blob-opslagwachtrij om pushmeldingen te ontvangen over projecttrainings-/exportactiviteiten en back-ups van gepubliceerde modellen. Deze functie is handig om te voorkomen dat de service voortdurend wordt gecontroleerd op resultaten wanneer lange bewerkingen worden uitgevoerd. In plaats daarvan kunt u de meldingen van de opslagwachtrij in uw werkstroom integreren. Raadpleeg de handleiding [Opslag integreren](./storage-integration.md) voor meer informatie.

### <a name="copy-and-move-projects"></a>Projecten kopiëren en verplaatsen

* U kunt projecten nu vanaf het ene Custom Vision-account naar andere accounts kopiëren. U kunt bijvoorbeeld een project van een ontwikkelingsomgeving naar een productieomgeving verplaatsen of een back-up maken van een project naar een account in een andere Azure-regio voor betere gegevensbeveiliging. Raadpleeg de handleiding [Projecten kopiëren en verplaatsen](./copy-move-projects.md) voor meer informatie.

## <a name="september-2019"></a>September 2019

### <a name="suggested-tags"></a>Voorgestelde tags

* Met het Smart Labeler-hulpprogramma op de [Custom Vision-website](https://www.customvision.ai/) kunt u voorgestelde tags voor uw trainingsafbeeldingen genereren. Zo kunt u sneller een groot aantal afbeeldingen labelen bij het trainen van een Custom Vision-model. Zie [Voorgestelde tags](./suggested-tags.md) voor instructies over het gebruik van deze functie.

## <a name="cognitive-service-updates"></a>Updates van Cognitive Services

[Meldingen van Azure-updates voor Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)