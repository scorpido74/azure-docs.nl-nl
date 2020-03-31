---
title: Koppelingen voor beheerde toepassing implementeren met beleid
description: Meer informatie over het implementeren van koppelingen voor een beheerde toepassing met Azure Policy-service.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: ec0fe8f66ef2ad2458b4ffad0e848591793e5b05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650940"
---
# <a name="deploy-associations-for-a-managed-application-using-azure-policy"></a>Koppelingen voor een beheerde toepassing implementeren met Azure-beleid

Azure-beleid kan worden gebruikt om koppelingen te implementeren om resources te koppelen aan een beheerde toepassing. In dit artikel beschrijven we een ingebouwd beleid dat koppelingen implementeert en hoe u dat beleid gebruiken.

## <a name="built-in-policy-to-deploy-associations"></a>Ingebouwd beleid voor het inzetten van koppelingen

Koppelingen voor een beheerde toepassing implementeren is een ingebouwd beleid dat kan worden gebruikt om koppeling te implementeren om een resource aan een beheerde toepassing te koppelen. Het beleid accepteert drie parameters:

- Beheerde toepassings-ID - Deze id is de resource-id van de beheerde toepassing waaraan de resources moeten worden gekoppeld.
- Resourcetypen die moeten worden gekoppeld : deze resourcetypen zijn de lijst met resourcetypen die aan de beheerde toepassing moeten worden gekoppeld. U meerdere resourcetypen aan een beheerde toepassing koppelen met hetzelfde beleid.
- Voorvoegsel voor koppelingsnaam - Deze tekenreeks is het voorvoegsel dat moet worden toegevoegd aan de naam van de koppelingsbron die wordt gemaakt. De standaardwaarde is 'GeïmplementeerdBeleid'.

Het beleid maakt gebruik van Evaluatie DeployIfNotExists. Deze wordt uitgevoerd nadat een resourceprovider een claim voor maken of bijwerken van het geselecteerde resourcetype(s) heeft verwerkt en de evaluatie een code voor de status van succes heeft geretourneerd. Daarna wordt de koppelingsbron geïmplementeerd met behulp van een sjabloonimplementatie.
Zie Onboarding van [Azure Custom Providers-bron onboarding](../custom-providers/concepts-resource-onboarding.md) voor meer informatie over koppelingen

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Het ingebouwde beleid voor implementeren van koppelingen gebruiken 

### <a name="prerequisites"></a>Vereisten
Als de beheerde toepassing machtigingen voor het abonnement nodig heeft om een actie uit te voeren, werkt de beleidsimplementatie van koppelingsbron niet zonder de machtigingen toe te kennen.

### <a name="policy-assignment"></a>Beleidstoewijzing
Als u het ingebouwde beleid wilt gebruiken, maakt u een beleidstoewijzing en wijst u de koppelingen implementeren toe voor een beheerd toepassingsbeleid. Zodra het beleid is toegewezen, identificeert het beleid niet-conforme resources en implementeert het koppelingskoppeling voor die resources.

![Ingebouwd beleid toewijzen](media/concepts-built-in-policy/assign-builtin-policy-managedapp.png)

## <a name="getting-help"></a>Ondersteuning vragen

Als u vragen hebt over de ontwikkeling van Azure Custom Resource Providers, probeert u deze te vragen op [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Een soortgelijke vraag kan al zijn beantwoord, dus controleer eerst voor het posten. Voeg de ```azure-custom-providers``` tag toe om snel te reageren!

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over het gebruik van ingebouwd beleid om koppelingen te implementeren. Zie deze artikelen voor meer informatie:

- [Concepten: Azure Custom Providers resource onboarding](../custom-providers/concepts-resource-onboarding.md)
- [Zelfstudie: Resource onboarding met aangepaste providers](../custom-providers/tutorial-resource-onboarding.md)
- [Zelfstudie: Aangepaste acties en resources maken in Azure](../custom-providers/tutorial-get-started-with-custom-providers.md)
- [Snelstart: een aangepaste resourceprovider maken en aangepaste resources implementeren](../custom-providers/create-custom-provider.md)
- [How to: Aangepaste acties toevoegen aan een Azure REST API](../custom-providers/custom-providers-action-endpoint-how-to.md)
- [How to: Aangepaste resources toevoegen aan een Azure REST API](../custom-providers/custom-providers-resources-endpoint-how-to.md)
