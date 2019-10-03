---
title: Uw Azure-abonnement opzeggen | Microsoft Docs
description: Hier wordt beschreven hoe u een Azure-abonnement, zoals het gratis proefabonnement, kunt opzeggen
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 0c681a3374529c4f973c7cd247ad8d387682b564
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719718"
---
# <a name="cancel-your-azure-subscription"></a>Uw Azure-abonnement opzeggen

Als u uw Azure-abonnement niet meer nodig hebt, het opzeggen in de Azure-portal. 

Voordat u uw abonnement opzegt, moet u de volgende zaken doen:
* Maak een back-up van uw gegevens. Als u gegevens opslaat in Azure Storage of SQL, moet u bijvoorbeeld een kopie downloaden. Als u een virtuele machine hebt, slaat u lokaal een kopie van die VM op.
* Sluit uw services af. Ga naar de [pagina met resources in de beheerportal](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) en **Stop** alle actieve virtuele machines, toepassingen of andere services.
* Overweeg uw gegevens te migreren. Zie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../azure-resource-manager/resource-group-move-resources.md).
* Verwijder alle resources en alle resourcegroepen. Deze moeten zijn verwijderd voordat u een abonnement kunt opzeggen. Elke resourcegroep moet afzonderlijk worden verwijderd. Bij het verwijderen van de resourcegroep moet u de verwijdering bevestigen door de naam van de resourcegroep op te geven.
* Als u aangepaste rollen hebt die verwijzen naar dit abonnement in `AssignableScopes`, moet u die aangepaste rollen bijwerken om het abonnement te kunnen verwijderen. Als u een aangepaste rol probeert bij te werken nadat u een abonnement hebt opgezegd, wordt er mogelijk een foutmelding weergegeven. Zie [Problemen met aangepaste rollen oplossen](../role-based-access-control/troubleshooting.md#problems-with-custom-roles) en [Aangepaste rollen voor Azure-resources](../role-based-access-control/custom-roles.md) voor meer informatie.

Als u een betaald Azure-ondersteuningsplan annuleert, wordt u gefactureerd voor de rest van de abonnementsperiode. Zie [Ondersteuningsabonnementen voor Azure](https://azure.microsoft.com/support/plans/) voor meer informatie.

## <a name="cancel-subscription-in-the-azure-portal"></a>Abonnement annuleren in de Azure-portal

1. Selecteer uw abonnement op de [pagina Abonnementen in de Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Selecteer het abonnement dat u wilt opzeggen.
3. Selecteer **Overzicht** en selecteer vervolgens **Abonnement annuleren**.
    ![Schermopname waarin de knop ‘Abonnement annuleren’ wordt weergegeven](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Volg de aanwijzingen om de opzegging te voltooien.


## <a name="who-can-cancel-a-subscription"></a>Wie kan een abonnement annuleren?

In de onderstaande tabel worden de machtigingen beschreven die zijn vereist voor het opzeggen van een abonnement.

|Abonnementstype     |Wie het abonnement kan opzeggen  |
|---------|---------|
|Abonnementen die zijn gemaakt toen u zich aanmeldde voor Azure via de Azure-website. Bijvoorbeeld toen u zich aanmeldde voor een [gratis Azure-account](https://azure.microsoft.com/offers/ms-azr-0044p/), een [account met tarieven voor betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/) of een [Visual Studio-abonnement](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  De accountbeheerder, eigenaren en inzenders voor het abonnement  |
|[Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) en [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  De accounteigenaar, eigenaren en inzenders voor het abonnement       |
|[Azure-plan](https://azure.microsoft.com/offers/ms-azr-0017g/) en [Azure-plan voor DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  De eigenaren en inzenders voor het abonnement      |


## <a name="what-happens-after-i-cancel-my-subscription"></a>Wat gebeurt er nadat ik mijn abonnement heb opgezegd?

Nadat u uw abonnement hebt opgezegd, wordt de facturering onmiddellijk stopgezet. Het kan echter wel maximaal 10 minuten duren voordat de opzegging in de portal wordt weergegeven. Als u uw abonnement halverwege een factureringsperiode opzegt, verzenden we de laatste factuur op de gebruikelijke factuurdatum, na afloop van de periode.

Nadat u het abonnement hebt opgezegd, worden uw services uitgeschakeld. Dit betekent dat de toewijzing uw virtuele machines ongedaan wordt gemaakt, dat uw tijdelijke IP-adressen worden vrijgemaakt en dat uw opslag het kenmerk Alleen-lezen heeft.

We wachten 90 dagen voordat we uw gegevens permanent verwijderen, voor het geval u er toegang toe moet hebben of u van gedachten verandert. Wij brengen u niets in rekening voor het bewaren van de gegevens. Zie [Vertrouwenscentrum van Microsoft - Hoe we uw gegevens beheren](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) voor meer informatie.

## <a name="reactivate-subscription"></a>Abonnement opnieuw activeren

Als u per ongeluk uw abonnement met tarieven voor betalen per gebruik opzegt, kunt u [het opnieuw activeren in het accountcentrum](billing-subscription-become-disable.md).

Als uw abonnement geen abonnement is met tarieven voor betalen per gebruik, neemt u binnen 90 dagen na opzegging contact op met het ondersteuningsteam om uw abonnement opnieuw te activeren.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).
