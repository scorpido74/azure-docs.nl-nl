---
title: bestand opnemen
description: bestand opnemen
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 07/11/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a9a837d8a486e10ea2a62be69b889e657b1dbc05
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72600918"
---
U kunt Tags Toep assen op uw Azure-resources die meta gegevens geven om ze logisch in een taxonomie te organiseren. Elke tag bestaat uit een naam en een waardepaar. U kunt de naam Omgeving en de waarde Productie bijvoorbeeld toepassen op alle resources in de productie.

Nadat u de tags hebt toegepast, kunt u alle resources in het abonnement ophalen met deze tagnaam en -waarde. Met tags kunt u verwante resources ophalen uit verschillende resource groepen. Deze aanpak is nuttig wanneer u resources moet ordenen voor facturering of beheer.

Uw taxonomie moet rekening houden met een self-service voor het coderen van meta gegevens naast een strategie voor automatisch labelen om de belasting van gebruikers te verminderen en de nauw keurigheid te verg Roten.

Voor tags gelden de volgende beperkingen:

* Niet alle resource typen ondersteunen Tags. Zie [tag-ondersteuning voor Azure-resources](../articles/azure-resource-manager/tag-support.md)om te bepalen of u een tag kunt Toep assen op een resource type.
* Elke resource of resource groep mag Maxi maal 50 label naam/waarde-paren hebben. Opslag accounts ondersteunen momenteel alleen 15 Tags, maar deze limiet wordt verhoogd tot 50 in een toekomstige versie. Als u meer tags wilt Toep assen dan het Maxi maal toegestane aantal, gebruikt u een JSON-teken reeks voor de waarde van de tag. De JSON-tekenreeks kan veel waarden bevatten die worden toegepast op een enkele tagnaam. Een resource groep kan veel resources bevatten die elk een 50-tag/waarde-paren hebben.
* De tagnaam is beperkt tot 512 tekens en de tagwaarde is beperkt tot 256 tekens. Voor opslagaccounts is de tagnaam beperkt tot 128 tekens en de tagwaarde beperkt tot 256 tekens.
* Gegeneraliseerde Vm's ondersteunen geen tags.
* Tags die zijn toegepast op de resourcegroep, worden niet overgenomen door de resources in deze resourcegroep.
* Labels kunnen niet worden toegepast op klassieke resources, zoals Cloud Services.
* Label namen mogen niet deze tekens bevatten: `<`, `>`, `%`, `&`, `\`, `?` `/`

   > [!NOTE]
   > Op het moment dat u Azure DNS zones en Traffic Manager-services, is het gebruik van spaties in het label ook niet toegestaan. 
