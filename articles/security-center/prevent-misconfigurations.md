---
title: Onjuiste configuraties voor komen met Azure Security Center
description: Meer informatie over het gebruik van de opties voor afdwingen en weigeren van Security Center op de pagina met details van aanbevelingen
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 19e9a33350b6a1a67986dc35a372f737e45ab39a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906391"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Onjuiste configuraties voor komen met aanbevelingen voor afdwingen/weigeren

Onjuiste beveiligingsconfiguraties zijn een belangrijke oorzaak van beveiligingsincidenten. Security Center biedt nu de mogelijkheid om onjuiste configuratie van nieuwe resources met betrekking tot specifieke aanbevelingen te helpen *voorkomen*. 

Deze functie kan u helpen uw workloads veilig te houden en uw beveiligingsscore stabiel te houden.

Het afdwingen van een veilige configuratie op basis van een specifieke aanbeveling wordt aangeboden in twee modi:

- Met behulp van de optie **Weigeren** van Azure Policy, kunt u het maken van resources die niet in orde zijn, stoppen
- Met de optie **Afdwingen** kunt u profiteren van het effect **DeployIfNotExist** van Azure Policy en niet-compatibele resources automatisch herstellen wanneer deze worden gemaakt

Deze vindt u boven aan de pagina Resource Details voor geselecteerde beveiligings aanbevelingen (Zie [aanbevelingen met opties voor weigeren/afdwingen](#recommendations-with-denyenforce-options)).

## <a name="prevent-resource-creation"></a>Het maken van resources voor komen

1. Open de aanbeveling waaraan uw nieuwe resources moeten voldoen en selecteer de knop **weigeren** boven aan de pagina.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="De pagina aanbeveling met de knop weigeren gemarkeerd":::

    In het deel venster configuratie worden de scope opties weer gegeven. 

1. Stel het bereik in door het relevante abonnement of beheer groep te selecteren.

    > [!TIP]
    > U kunt de drie punten aan het einde van de rij gebruiken om één abonnement te wijzigen of de selectie vakjes gebruiken om meerdere abonnementen of groepen te selecteren. Selecteer vervolgens **wijzigen in weigeren**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="De pagina aanbeveling met de knop weigeren gemarkeerd":::


## <a name="enforce-a-secure-configuration"></a>Een veilige configuratie afdwingen

1. Open de aanbeveling voor het implementeren van een sjabloon implementatie voor als nieuwe resources niet voldoen en selecteer de knop **afdwingen** boven aan de pagina.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="De pagina aanbeveling met de knop weigeren gemarkeerd":::

    Het configuratie venster wordt geopend met alle configuratie opties voor het beleid. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="De pagina aanbeveling met de knop weigeren gemarkeerd":::

1. Stel het bereik, de naam van de toewijzing en andere relevante opties in.

1. Selecteer **Controleren + maken**.

## <a name="recommendations-with-denyenforce-options"></a>Aanbevelingen met opties voor weigeren/afdwingen

Deze aanbevelingen kunnen worden gebruikt in combi natie met de optie **weigeren** :

- Virtuele machines moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources
- Opslagaccounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources
- Alle autorisatieregels behalve RootManageSharedAccessKey moeten worden verwijderd uit de Event hub-naamruimte
- Alle autorisatieregels met uitzondering van RootManageSharedAccessKey moeten worden verwijderd uit Service Bus-naamruimte
- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld
- Alleen beveiligde verbindingen met uw Redis Cache moeten zijn ingeschakeld
- Automation-accountvariabelen moeten worden versleuteld
- Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor clientverificatie
- Voor Service Fabric-clusters moet de eigenschap ClusterProtectionLevel zijn ingesteld op EncryptAndSign
- Onbeperkte netwerktoegang tot opslagaccounts controleren


Deze aanbevelingen kunnen worden gebruikt in combi natie met de optie **afdwingen** :

- Diagnostische logboeken in Logic Apps moeten zijn ingeschakeld
- Diagnostische logboeken in Data Lake Analytics moeten zijn ingeschakeld
- Diagnostische logboeken in IoT Hub moeten zijn ingeschakeld
- Diagnostische logboeken in Batch-accounts moeten worden ingeschakeld
- Diagnostische logboeken in Azure Stream Analytics moeten zijn ingeschakeld
- Diagnostische logboeken in Service Bus moeten zijn ingeschakeld
- Diagnostische logboeken in zoekservices moeten zijn ingeschakeld
- Diagnostische logboeken in Event Hub moeten zijn ingeschakeld
- Diagnostische logboeken in Microsoft Azure Virtual Machine Scale Sets moeten zijn ingeschakeld
- Diagnostische logboeken in Key Vault moeten zijn ingeschakeld
- Controle op SQL Server moet zijn ingeschakeld
- Advanced Data Security moet zijn ingeschakeld op uw SQL-servers



