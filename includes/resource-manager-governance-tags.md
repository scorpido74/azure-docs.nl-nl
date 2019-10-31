---
title: bestand opnemen
description: bestand opnemen
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 10/30/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c141e67157f3ec17d475062ec76406ec765c4f50
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199168"
---
U kunt Tags Toep assen op uw Azure-resources die meta gegevens geven om ze logisch in een taxonomie te organiseren. Elke tag bestaat uit een naam en een waardepaar. U kunt de naam Omgeving en de waarde Productie bijvoorbeeld toepassen op alle resources in de productie.

Nadat u de tags hebt toegepast, kunt u alle resources in het abonnement ophalen met deze tagnaam en -waarde. Met tags kunt u verwante resources ophalen uit verschillende resource groepen. Deze aanpak is nuttig wanneer u resources moet ordenen voor facturering of beheer.

Uw taxonomie moet rekening houden met een self-service voor het coderen van meta gegevens naast een strategie voor automatisch labelen om de belasting van gebruikers te verminderen en de nauw keurigheid te verg Roten.

Voor tags gelden de volgende beperkingen:

* Niet alle resource typen ondersteunen Tags. Zie [tag-ondersteuning voor Azure-resources](../articles/azure-resource-manager/tag-support.md)om te bepalen of u een tag kunt Toep assen op een resource type.
* Elke resource of resource groep mag Maxi maal 50 label naam/waarde-paren hebben. Als u meer tags wilt Toep assen dan het Maxi maal toegestane aantal, gebruikt u een JSON-teken reeks voor de waarde van de tag. De JSON-tekenreeks kan veel waarden bevatten die worden toegepast op een enkele tagnaam. Een resource groep kan veel resources bevatten die elk een 50-tag/waarde-paren hebben.
* De tagnaam is beperkt tot 512 tekens en de tagwaarde is beperkt tot 256 tekens. Voor opslagaccounts is de tagnaam beperkt tot 128 tekens en de tagwaarde beperkt tot 256 tekens.
* Gegeneraliseerde Vm's ondersteunen geen tags.
* Tags die zijn toegepast op de resourcegroep, worden niet overgenomen door de resources in deze resourcegroep.
* Labels kunnen niet worden toegepast op klassieke resources, zoals Cloud Services.
* Label namen mogen niet deze tekens bevatten: `<`, `>`, `%`, `&`, `\`, `?``/`

   > [!NOTE]
   > Op het moment dat u Azure DNS zones en Traffic Manager-services, is het gebruik van spaties in het label ook niet toegestaan. 
