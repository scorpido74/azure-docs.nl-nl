---
title: Koppelingen implementeren met beleid
description: Meer informatie over het implementeren van koppelingen voor een aangepaste provider met Azure Policy-service.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: 536b95eb07619d0ce2d02ec01e1f51ed52c1b5e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650472"
---
# <a name="deploy-associations-for-a-custom-provider-using-azure-policy"></a>Koppelingen voor een aangepaste provider implementeren met Azure-beleid

Azure-beleid kan worden gebruikt om koppelingen te implementeren om resources te koppelen aan een aangepaste provider. In dit artikel beschrijven we een ingebouwd beleid dat koppelingen implementeert en hoe u dat beleid gebruiken.

## <a name="built-in-policy-to-deploy-associations"></a>Ingebouwd beleid voor het inzetten van koppelingen

Koppelingen voor een aangepaste provider implementeren is een ingebouwd beleid dat kan worden gebruikt om koppeling te implementeren om een resource te koppelen aan een aangepaste provider. Het beleid accepteert drie parameters:

- Aangepaste provider-id - Deze id is de resource-id van de aangepaste provider waaraan de resources moeten worden gekoppeld.
- Resourcetypen die moeten worden gekoppeld: deze resourcetypen zijn de lijst met resourcetypen die aan de aangepaste provider moeten worden gekoppeld. U meerdere resourcetypen koppelen aan een aangepaste provider met hetzelfde beleid.
- Voorvoegsel voor koppelingsnaam - Deze tekenreeks is het voorvoegsel dat moet worden toegevoegd aan de naam van de koppelingsbron die wordt gemaakt. De standaardwaarde is 'GeïmplementeerdBeleid'.

Het beleid maakt gebruik van Evaluatie DeployIfNotExists. Het wordt uitgevoerd nadat een resourceprovider een resourceaanvraag voor maken of bijwerken heeft verwerkt en de evaluatie een successtatuscode heeft geretourneerd. Daarna wordt de koppelingsbron geïmplementeerd met behulp van een sjabloonimplementatie.
Zie Onboarding van [Azure Custom Providers-bron onboarding](./concepts-resource-onboarding.md) voor meer informatie over koppelingen

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Het ingebouwde beleid voor implementeren van koppelingen gebruiken 

### <a name="prerequisites"></a>Vereisten
Als de aangepaste provider machtigingen nodig heeft voor het bereik van het beleid om een actie uit te voeren, werkt de beleidsimplementatie van koppelingsresources niet zonder de machtigingen te verlenen.

### <a name="policy-assignment"></a>Beleidstoewijzing
Als u het ingebouwde beleid wilt gebruiken, maakt u een beleidstoewijzing en wijst u de koppelingen implementeren toe voor een aangepast providerbeleid. Het beleid identificeert vervolgens niet-conforme resources en implementeert associatie voor die resources.

![Ingebouwd beleid toewijzen](media/concepts-built-in-policy/assign-builtin-policy-customprovider.png)

## <a name="getting-help"></a>Ondersteuning vragen

Als u vragen hebt over de ontwikkeling van Azure Custom Resource Providers, probeert u deze te vragen op [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Een soortgelijke vraag kan al zijn beantwoord, dus controleer eerst voor het posten. Voeg de ```azure-custom-providers``` tag toe om snel te reageren!

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over het gebruik van ingebouwd beleid om koppelingen te implementeren. Zie deze artikelen voor meer informatie:

- [Concepten: Azure Custom Providers resource onboarding](./concepts-resource-onboarding.md)
- [Zelfstudie: Resource onboarding met aangepaste providers](./tutorial-resource-onboarding.md)
- [Zelfstudie: Aangepaste acties en resources maken in Azure](./tutorial-get-started-with-custom-providers.md)
- [Snelstart: een aangepaste resourceprovider maken en aangepaste resources implementeren](./create-custom-provider.md)
- [How to: Aangepaste acties toevoegen aan een Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [How to: Aangepaste resources toevoegen aan een Azure REST API](./custom-providers-resources-endpoint-how-to.md)
