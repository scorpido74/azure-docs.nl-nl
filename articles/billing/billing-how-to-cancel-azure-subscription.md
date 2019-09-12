---
title: Uw Azure-abonnement annuleren | Microsoft Docs
description: Hierin wordt beschreven hoe u uw Azure-abonnement annuleert, zoals het gratis proef abonnement
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: banders
ms.openlocfilehash: 7756174f01e3fede17bec3e2ac185e89caddc097
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666433"
---
# <a name="cancel-your-azure-subscription"></a>Uw Azure-abonnement annuleren

U kunt uw Azure-abonnement in de Azure Portal annuleren als u het abonnement niet meer nodig hebt. 

Voordat u uw abonnement annuleert:
* Maak een back-up van uw gegevens. Als u bijvoorbeeld gegevens in azure Storage of SQL opslaat, downloadt u een kopie. Als u een virtuele machine hebt, slaat u deze lokaal op.
* Sluit uw services af. Ga naar de [pagina Resources in de beheer Portal](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)en **Stop** alle actieve virtuele machines, toepassingen of andere services.
* U kunt uw gegevens migreren. Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../azure-resource-manager/resource-group-move-resources.md).
* Verwijder alle resources en alle resource groepen. U moet deze verwijderen voordat u een abonnement kunt annuleren. Elke resource groep moet afzonderlijk worden verwijderd. Tijdens het verwijderen van de resource groep moet u het verwijderen bevestigen door de naam van de resource groep op te geven.
* Als u aangepaste rollen hebt die verwijzen naar dit abonnement in `AssignableScopes`, moet u die aangepaste rollen bijwerken om het abonnement te verwijderen. Als u een aangepaste rol probeert bij te werken nadat u een abonnement hebt geannuleerd, wordt er mogelijk een fout bericht weer geven. Zie [problemen oplossen met aangepaste rollen](../role-based-access-control/troubleshooting.md#problems-with-custom-roles) en [aangepaste rollen voor Azure-resources](../role-based-access-control/custom-roles.md)voor meer informatie.

Als u een betaald ondersteunings abonnement voor Azure annuleert, wordt u gefactureerd voor de rest van de abonnements periode. Zie Azure-ondersteunings [abonnementen](https://azure.microsoft.com/support/plans/)voor meer informatie.

## <a name="cancel-subscription-in-the-azure-portal"></a>Het abonnement op het Azure Portal annuleren

1. Selecteer uw abonnement op de [pagina Abonnementen in het Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Selecteer het abonnement dat u wilt annuleren.
3. Selecteer **overzicht**en selecteer vervolgens **abonnement annuleren**.
    ![Scherm opname van de knop Annuleren](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Volg de aanwijzingen en voltooi de annulering.


## <a name="who-can-cancel-a-subscription"></a>Wie kan een abonnement annuleren?

In de volgende tabel wordt de machtiging beschreven die is vereist om een abonnement te annuleren.

|Abonnementstype     |Wie kan annuleren  |
|---------|---------|
|Abonnementen die worden gemaakt wanneer u zich aanmeldt voor Azure via de Azure-website. Als u zich bijvoorbeeld aanmeldt voor een [gratis Azure-account](https://azure.microsoft.com/offers/ms-azr-0044p/), [account met betalen naar](https://azure.microsoft.com/offers/ms-azr-0003p/) gebruik-tarieven of als een [Visual Studio-abonnee](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Account beheerder, eigen aren en mede werkers voor het abonnement  |
|[Micro soft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) en [Enter prise dev/test](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Eigenaar, eigen aren en mede werkers van het abonnement       |
|[Azure-abonnement](https://azure.microsoft.com/offers/ms-azr-0017g/) en [Azure-abonnement voor DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Eigen aren en mede werkers voor het abonnement      |


## <a name="what-happens-after-i-cancel-my-subscription"></a>Wat gebeurt er nadat ik mijn abonnement heb geannuleerd?

Nadat u hebt geannuleerd, wordt de facturering onmiddellijk gestopt. Het kan echter Maxi maal tien minuten duren voordat de annulering wordt weer gegeven in de portal. Als u het midden van een facturerings periode annuleert, sturen we de uiteindelijke factuur op uw gebruikelijke factuur datum na afloop van de periode.

Nadat u de bewerking hebt geannuleerd, zijn uw services uitgeschakeld. Dit betekent dat uw virtuele machines zijn toegewezen, tijdelijke IP-adressen worden vrijgemaakt en dat opslag alleen-lezen is.

We wachten 90 dagen voordat u uw gegevens permanent verwijdert, voor het geval u er toegang toe hebt of u van gedachten verandert. U wordt niet in rekening gebracht voor het bewaren van de gegevens. Zie voor meer informatie [micro soft vertrouwens centrum-hoe we uw gegevens beheren](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Abonnement opnieuw activeren

Als u uw abonnement per ongeluk betaalt met betalen naar gebruik, kunt u [dit opnieuw activeren in het account centrum](billing-subscription-become-disable.md).

Als uw abonnement geen abonnement is met betalen per gebruik-tarieven, neemt u contact op met de ondersteuning binnen 90 dagen na de annulering om uw abonnement opnieuw te activeren.

## <a name="need-help-contact-us"></a>Hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, kunt u [een ondersteunings aanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).
