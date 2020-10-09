---
title: Op rollen gebaseerd toegangs beheer-Custom Vision
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u toegangs beheer op basis van rollen voor uw Custom Vision-projecten kunt configureren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: 4eea87d7b5ef543551b789ddc6b045358fa98631
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826929"
---
# <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Custom Vision biedt ondersteuning voor op rollen gebaseerd toegangsbeheer voor Azure (Azure RBAC), een autorisatiesysteem voor het beheren van afzonderlijke toegang tot Azure-resources. Met RBAC kunt u verschillende team leden verschillende machtigings niveaus voor uw Custom Vision projecten toewijzen. Zie de [documentatie van Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/)voor meer informatie over RBAC.

## <a name="add-role-assignment-to-custom-vision-resource"></a>Roltoewijzing toevoegen aan Custom Vision resource

U kunt Azure RBAC toewijzen aan een Custom Vision bron. Als u toegang wilt verlenen aan een Azure-resource, voegt u een roltoewijzing toe.
1. Selecteer in [Azure Portal](https://ms.portal.azure.com/) de optie **Alle services**. 
1. Selecteer vervolgens de **Cognitive Services**en navigeer naar uw specifieke Custom Vision training-resource.
   > [!NOTE]
   > U kunt RBAC ook instellen voor hele resource groepen, abonnementen of beheer groepen. Hiertoe selecteert u het gewenste bereik niveau en gaat u vervolgens naar het gewenste item (bijvoorbeeld **resource groepen** selecteren en klikt u vervolgens op de gewenste resource groep).
1. Selecteer **toegangs beheer (IAM)** in het navigatie deel venster aan de linkerkant.
1. Selecteer het **tabblad roltoewijzingen om de roltoewijzingen** voor dit bereik weer te geven.
1. Selecteer **Add**  ->  **functie toewijzing**toevoegen toevoegen.
1. Selecteer in de vervolg keuzelijst **rol** een rol die u wilt toevoegen.
1. Selecteer een gebruiker, groep, Service-Principal of beheerde identiteit in de **selectie** lijst. Als de beveiligingsprincipal niet in de lijst wordt weer gegeven, kunt u het selectie vakje typen om in de map te zoeken naar weergave namen, e-mail adressen en object-id's.
1. Selecteer **Opslaan** om de rol toe te wijzen.

Binnen een paar minuten wordt het doel toegewezen aan de geselecteerde rol bij het geselecteerde bereik.

## <a name="custom-vision-role-types"></a>Custom Vision typen rollen

Gebruik de volgende tabel om de toegangs behoeften voor uw Custom Vision-resources te bepalen.

|Rol  |Machtigingen  |
|---------|---------|
|`Cognitive Services Custom Vision Contributor`     | Volledige toegang tot de projecten, inclusief de mogelijkheid om een project te maken, bewerken of verwijderen.        |
|`Cognitive Services Custom Vision Trainer`     | Volledige toegang, behalve de mogelijkheid om een project te maken of te verwijderen. Trainers kunnen projecten bekijken en bewerken en de modellen trainen, publiceren, publiceren ongedaan maken of exporteren.        |
|`Cognitive Services Custom Vision Labeler`     | De mogelijkheid om trainings afbeeldingen te uploaden, te bewerken of te verwijderen en tags te maken, toe te voegen, te verwijderen of te verwijderen. Met labelers kunnen projecten worden weer gegeven, maar kunnen ze niet worden bijgewerkt dan de afbeeldingen en tags van de training.         |
|`Cognitive Services Custom Vision Deployment`     | De mogelijkheid om de modellen te publiceren, ongedaan te maken of te exporteren. Met deployers kunnen projecten worden weer gegeven, maar er kan geen project, trainings afbeeldingen of tags worden bijgewerkt.        |
|`Cognitive Services Custom Vision Reader`     | De mogelijkheid om projecten weer te geven. Lezers kunnen geen wijzigingen aanbrengen.        |
