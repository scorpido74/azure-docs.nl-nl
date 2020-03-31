---
title: Aangepaste beveiligingsbeleidsregels maken in Azure Security Center | Microsoft Documenten
description: Aangepaste azure-beleidsdefinities die worden gecontroleerd door Azure Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: memildin
ms.openlocfilehash: c709890ae6c57a001c6a0e9df4e973bd3bd24602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258257"
---
# <a name="using-custom-security-policies"></a>Aangepast beveiligingsbeleid gebruiken

Azure Security Center genereert beveiligingsaanbevelingen om uw systemen en omgeving te beveiligen. Deze aanbevelingen zijn gebaseerd op best practices uit de branche, die zijn opgenomen in het algemene, standaardbeveiligingsbeleid dat aan alle klanten wordt geleverd. Ze kunnen ook afkomstig zijn van security center kennis van de industrie en regelgeving normen.

Met deze functie u uw eigen *aangepaste* initiatieven toevoegen. U ontvangt dan aanbevelingen als uw omgeving niet voldoet aan het beleid dat u maakt. Alle aangepaste initiatieven die u maakt, worden weergegeven naast de ingebouwde initiatieven in het dashboard voor naleving van de regelgeving die wordt beschreven in de zelfstudie [Verbeter uw naleving van de regelgeving.](security-center-compliance-dashboard.md)

Zoals [hier](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location) besproken in de Azure Policy-documentatie, moet het een beheergroep of een abonnement zijn wanneer u een locatie opgeeft voor uw aangepaste initiatief. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Een aangepast initiatief toevoegen aan uw abonnement 

1. Open de pagina **Beveiligingsbeleid** vanaf de zijbalk van beveiligingscentrum.

1. Selecteer een abonnement of beheergroep waaraan u een aangepast initiatief wilt toevoegen.

    [![Een abonnement selecteren waarvoor u uw aangepaste beleid maakt](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > U moet aangepaste standaarden op abonnementsniveau (of hoger) toevoegen om deze te kunnen evalueren en weerte geven in Het Beveiligingscentrum. 
    >
    > Wanneer u een aangepaste standaard toevoegt, wordt een *initiatief* aan dat bereik toegevoegd. We raden u dan ook aan de breedste scope te selecteren die nodig is voor die opdracht.

1. Klik op de pagina Beveiligingsbeleid onder Uw aangepaste initiatieven op **Een aangepast initiatief toevoegen**.

    [![Klik op **Een aangepast initiatief toevoegen**](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    De volgende pagina wordt weergegeven:

    ![Een beleid maken of toevoegen](media/custom-security-policies/create-or-add-custom-policy.png)

1. Bekijk op de pagina Aangepaste initiatieven toevoegen de lijst met aangepaste beleidsregels die al in uw organisatie zijn gemaakt. Als u er een ziet die u aan uw abonnement wilt toewijzen, klikt u op **Toevoegen**. Als er geen initiatief in de lijst staat dat aan uw behoeften voldoet, slaat u deze stap over.

1. Een nieuw initiatief op maat maken:

    1. Klik **op Nieuw maken**.
    1. Voer de locatie en naam van de definitie in.
    1. Selecteer het beleid dat u wilt opnemen en klik op **Toevoegen**.
    1. Voer de gewenste parameters in.
    1. Klik op **Opslaan**.
    1. Klik op de pagina Aangepaste initiatieven toevoegen op Vernieuwen en uw nieuwe initiatief wordt weergegeven als beschikbaar.
    1. Klik **op Toevoegen** en wijs het toe aan uw abonnement.

    > [!NOTE]
    > Voor het maken van nieuwe initiatieven zijn referenties van abonnementseigenaren vereist. Zie Machtigingen in Azure [Security Center](security-center-permissions.md)voor meer informatie over Azure-rollen.

    Uw nieuwe initiatief treedt in werking en u de impact op de volgende twee manieren zien:

    * Selecteer in de zijbalk van het beveiligingscentrum onder Beleid & Naleving de optie **Naleving van de regelgeving**. Het compliance dashboard wordt geopend om uw nieuwe aangepaste initiatief te tonen naast de ingebouwde initiatieven.
    
    * Je krijgt aanbevelingen als je omgeving niet voldoet aan het beleid dat je hebt gedefinieerd.

1. Als u de resulterende aanbevelingen voor uw beleid wilt bekijken, klikt u op **Aanbevelingen** van de zijbalk om de aanbevelingenpagina te openen. De aanbevelingen worden weergegeven met een label "Aangepast" en zijn binnen ongeveer een uur beschikbaar.

    [![Aangepaste aanbevelingen](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u aangepast beveiligingsbeleid maken. 

Zie voor ander gerelateerd materiaal de volgende artikelen: 

- [Het overzicht van beveiligingsbeleid](tutorial-security-policy.md)
- [Een lijst met het ingebouwde beveiligingsbeleid](security-center-policy-definitions.md)