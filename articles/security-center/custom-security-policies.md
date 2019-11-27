---
title: Aangepaste beveiligings beleidsregels maken in Azure Security Center | Microsoft Docs
description: Aangepaste beleids definities van Azure worden bewaakt door Azure Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 3e60c0e165b8f9ec79aac2048011d9e315b0ce18
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483959"
---
# <a name="using-custom-security-policies-preview"></a>Aangepaste beveiligings beleidsregels gebruiken (preview-versie)

Azure Security Center beveiligings aanbevelingen worden gegenereerd om uw systemen en omgeving te beveiligen. Deze aanbevelingen zijn gebaseerd op de best practices van de branche, die zijn opgenomen in het algemene standaard beveiligings beleid dat aan alle klanten wordt verstrekt. Ze kunnen ook afkomstig zijn van de kennis van de branche-en regelgevings normen van Security Center.

Met deze preview-functie kunt u uw eigen *aangepaste* initiatieven toevoegen. U ontvangt dan aanbevelingen als uw omgeving niet voldoet aan het beleid dat u maakt.

Zoals beschreven [in](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location) de documentatie van Azure Policy, moet u, wanneer u een locatie opgeeft voor uw aangepaste initiatief, een beheer groep of een abonnement zijn. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Een aangepast initiatief toevoegen aan uw abonnement 

1. Open de pagina **beveiligings beleid** vanuit de zijbalk van Security Center.

1. Selecteer een abonnement of beheer groep waaraan u een aangepast initiatief wilt toevoegen.

    [![het selecteren van een abonnement waarvoor u uw aangepaste beleid maakt](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > U moet aangepaste standaarden toevoegen op het abonnements niveau (of hoger) om ze te evalueren en weer te geven in Security Center. 
    >
    > Wanneer u een aangepaste standaard toevoegt, wijst deze een *initiatief* toe aan dat bereik. Daarom raden we u aan om het breedste bereik te selecteren dat vereist is voor de toewijzing.

1. Klik op de pagina beveiligings beleid, onder uw aangepaste initiatieven (preview), op **een aangepast initiatief toevoegen**.

    [![op * * een aangepast initiatief toevoegen * *](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    De volgende pagina wordt weer gegeven:

    ![Een beleid maken of toevoegen](media/custom-security-policies/create-or-add-custom-policy.png)

1. Bekijk op de pagina aangepaste initiatieven toevoegen de lijst met aangepaste beleids regels die al zijn gemaakt in uw organisatie. Als u een account wilt toewijzen aan uw abonnement, klikt u op **toevoegen**. Als er geen initiatief in de lijst is die aan uw behoeften voldoet, kunt u deze stap overs Laan.

1. Een nieuw aangepast initiatief maken:

    1. Klik op **nieuwe maken**.
    1. Voer de locatie en naam van de definitie in.
    1. Selecteer het beleid dat u wilt insluiten en klik op **toevoegen**.
    1. Voer de gewenste para meters in.
    1. Klik op **Opslaan**.
    1. Klik op de pagina aangepaste initiatieven toevoegen op vernieuwen en uw nieuwe initiatief wordt weer gegeven als beschikbaar.
    1. Klik op **toevoegen** en wijs deze toe aan uw abonnement.

    > [!NOTE]
    > Voor het maken van nieuwe initiatieven zijn referenties van abonnements eigenaren vereist. Zie [machtigingen in azure Security Center](security-center-permissions.md)voor meer informatie over Azure-rollen.

1. Als u de resulterende aanbevelingen voor uw beleid wilt zien, klikt u op de zijbalk op **aanbevelingen** om de pagina aanbevelingen te openen. De aanbevelingen worden weer gegeven met een aangepast label en zijn binnen ongeveer een uur beschikbaar.

    [Aangepaste aanbevelingen ![](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een aangepast beveiligings beleid maakt. 

Raadpleeg de volgende artikelen voor meer gerelateerde materialen: 

- [Het overzicht van beveiligings beleid](tutorial-security-policy.md)
- [Een lijst met ingebouwde beveiligings beleidsregels](security-center-policy-definitions.md)