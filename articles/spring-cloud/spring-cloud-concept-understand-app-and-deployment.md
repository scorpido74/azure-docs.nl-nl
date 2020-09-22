---
title: Meer informatie over app en implementatie in azure lente Cloud
description: Het verschil tussen toepassing en implementatie in azure lente-Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9e909db0041979eb7bc4fc30bd9551382e83c488
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90892505"
---
# <a name="understand-app-and-deployment-in-azure-spring-cloud"></a>Meer informatie over app en implementatie in azure lente Cloud

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C #

**App** en **implementatie** zijn de twee belang rijke concepten in het resource model van Azure lente Cloud. In azure lente Cloud is een *app* een abstractie van één zakelijke app of een micro service.  Een versie van code of binair wordt geïmplementeerd als de *app* wordt uitgevoerd in een *implementatie*.  Apps worden uitgevoerd in een *Azure lente-Cloud service-exemplaar*of gewoon *service-exemplaar*, zoals hieronder wordt weer gegeven.

 ![Apps en implementaties](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

U kunt meerdere service-exemplaren binnen één Azure-abonnement hebben, maar de Azure lente-Cloud service is het gemakkelijkst te gebruiken wanneer alle apps die een zakelijke app of micro service vormen, zich in één service-exemplaar bevinden.

In de Cloud Standard-laag van Azure lente kan één app één productie-implementatie en één faserings implementatie hebben, zodat u deze eenvoudig kunt uitvoeren op een blauw/groen-implementatie.

## <a name="app"></a>App
De volgende functies/eigenschappen worden gedefinieerd op app-niveau.

| Enum | Definitie |
|:--|:----------------|
| Openbaar</br>Eindpunt | De URL voor toegang tot de app |
| Aangepast</br>Domain | CNAME-record dat het aangepaste domein beveiligt |
| Service</br>Binding | Eigenschappen van bindings configuratie die zijn ingesteld in de function.jsvoor het bestand en het kenmerk *ServiceBusTrigger* |
| Beheerd</br>Identiteit | Met beheerde identiteit door Azure Active Directory kan uw app eenvoudig toegang krijgen tot andere met Azure AD beveiligde resources, zoals Azure Key Vault |
| Permanent</br>Storage | Instelling die ervoor zorgt dat gegevens behouden blijven na het opnieuw opstarten van de app |

## <a name="deployment"></a>Implementatie

De volgende functies/eigenschappen worden gedefinieerd op implementatie niveau en worden uitgewisseld bij het wisselen van productie-en faserings implementatie.

| Enum | Definitie |
|:--|:----------------|
| CPU | Aantal vcores per app-exemplaar |
| Geheugen | Instelling die geheugen toewijst om implementaties omhoog of uit te schalen |
| Exemplaar</br>Aantal | Het aantal app-exemplaren, hand matig of automatisch instellen |
| Automatisch schalen | Aantal exemplaren automatisch schalen op basis van vooraf gedefinieerde regels en schema's |
| JVM</br>Opties | instelling: JAVA_OPTS |
| Omgeving</br>Variabelen | Instellingen die van toepassing zijn op de volledige Azure veer-cloud omgeving |
| Runtime</br>Versie | Java 8/Java 11|

## <a name="restrictions"></a>Beperkingen

* **Een app moet één productie-implementatie hebben**: het verwijderen van een productie-implementatie wordt geblokkeerd door de API. Deze moet worden omgewisseld naar staging voordat ze worden verwijderd.
* **Een app kan Maxi maal twee implementaties hebben**: het maken van meer dan twee implementaties wordt geblokkeerd door de API. Implementeer uw nieuwe binaire bestand naar de bestaande productie-of faserings implementatie.
* **Implementatie beheer is niet beschikbaar in de Basic-laag**: gebruik de Standard-laag voor de implementatie van blauw en groen.

## <a name="see-also"></a>Zie ook
* [Een faserings omgeving instellen in azure lente-Cloud](spring-cloud-howto-staging-environment.md)
