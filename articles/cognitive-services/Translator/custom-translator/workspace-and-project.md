---
title: Wat is een werkruimte en project? - Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: In dit artikel worden de verschillen tussen een werkruimte en een project en projectcategorieën en labels voor de dienst Aangepaste vertaler uitgelegd.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: d2f7903fa85c645357e46a753d1cb043e0893254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219432"
---
# <a name="what-is-a-custom-translator-workspace"></a>Wat is een werkruimte voor aangepaste vertalers?

Een werkruimte is een werkgebied voor het samenstellen en bouwen van uw aangepaste vertaalsysteem. Een werkruimte kan meerdere projecten, modellen en documenten bevatten. Al het werk dat u doet in Custom Translator bevindt zich in een specifieke werkruimte.

Werkruimte is privé voor u en de mensen die u uitnodigt in uw werkruimte. Ongenode personen hebben geen toegang tot de inhoud van uw werkruimte. U zoveel mensen uitnodigen als u wilt in uw werkruimte en hun toegang op elk gewenst moment wijzigen of verwijderen. U ook een nieuwe werkruimte maken. Standaard bevat een werkruimte geen projecten of documenten die zich binnen uw andere werkruimten bevinden.

## <a name="what-is-a-custom-translator-project"></a>Wat is een custom translator-project?

Een project is een wrapper voor een model, documenten en tests. Elk project bevat automatisch alle documenten die in die werkruimte worden geüpload en die het juiste taalpaar hebben. Als u bijvoorbeeld zowel een Project Engels naar Spaans als een Spaans-Engels project hebt, worden dezelfde documenten in beide projecten opgenomen. Aan elk project is een CategoryID gekoppeld die wordt gebruikt bij het opvragen van de [V3-API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) voor vertalingen. CategoryID is parameter die wordt gebruikt om vertalingen te krijgen van een aangepast systeem dat is gebouwd met Aangepaste vertaler.

## <a name="project-categories"></a>Projectcategorieën

De categorie identificeert het domein - het gebied van terminologie en stijl die u wilt gebruiken - voor uw project. Kies de categorie die het meest relevant is voor uw documenten. In sommige gevallen heeft uw keuze van de categorie rechtstreeks invloed op het gedrag van de aangepaste vertaler.

We hebben twee sets van baseline modellen. Ze zijn algemeen en technologie. Als de categorie **Technologie** is geselecteerd, worden de basislijnmodellen technologie gebruikt. Voor elke andere categorieselectie worden de algemene basislijnmodellen gebruikt. Het baselinemodel technologie doet het goed in technologiedomein, maar het toont een lagere kwaliteit, als de zinnen die worden gebruikt voor vertaling niet binnen het technologiedomein vallen. We raden klanten aan om categorietechnologie alleen te selecteren als zinnen strikt binnen het technologiedomein vallen.

In dezelfde werkruimte u projecten maken voor hetzelfde taalpaar in verschillende categorieën. Aangepaste vertaler voorkomt het maken van een duplicaatproject met hetzelfde taalpaar en dezelfde categorie. Door een label toe te passen op uw project u deze beperking vermijden. Gebruik geen labels tenzij u vertaalsystemen bouwt voor meerdere clients, omdat het toevoegen van een uniek label aan uw project wordt weerspiegeld in uw projecten CategoryID.

## <a name="project-labels"></a>Projectlabels

Met Custom Translator u een projectlabel aan uw project toewijzen. Het projectlabel maakt onderscheid tussen meerdere projecten met hetzelfde taalpaar en dezelfde categorie. Als een best practice, vermijd het gebruik van projectlabels, tenzij dat nodig is.

Het projectlabel wordt gebruikt als onderdeel van de CategoryID. Als het projectlabel niet is ingesteld of identiek is ingesteld voor projecten, delen projecten met dezelfde categorie en *verschillende* taalparen dezelfde CategoryID. Deze aanpak is voordelig omdat u of uw klant hiermee schakelen tussen talen wanneer u de API voor tekstvertaler gebruikt zonder u zorgen te maken over een CategorieID die uniek is voor elk project.

Als ik bijvoorbeeld vertalingen in het domein Technologie van Engels naar Frans en van Frans naar Engels\> wilde inschakelen, zou\> ik twee projecten maken: een voor Engels - Frans en één voor Frans - Engels. Ik zou dezelfde categorie (Technologie) voor beide specificeren en het projectlabel leeg laten. De CategoryID voor beide projecten zou overeenkomen, dus ik kon de API voor zowel Engelse als Franse vertalingen opvragen zonder mijn CategoryID te hoeven wijzigen.

Als u een taalserviceprovider bent en meerdere klanten wilt bedienen met verschillende modellen die dezelfde categorie en het taalpaar behouden, zou het een verstandige beslissing zijn om een projectlabel te gebruiken om onderscheid te maken tussen klanten.

## <a name="next-steps"></a>Volgende stappen

- Lees over [Training en model](training-and-model.md) om te weten, hoe je efficiënt een vertaalmodel bouwen.
