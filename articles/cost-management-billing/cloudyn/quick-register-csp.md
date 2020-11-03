---
title: Met CSP-partnergegevens registeren bij Cloudyn in Azure
description: Meer informatie over het registratieproces dat door partners wordt gebruikt voor het uitvoeren van onboarding van hun klanten voor de Cloudyn-portal.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: 95d8a425314ad1c968915085b35ca89aa3f62711
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92543318"
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>Registreren met het CSP-partnerprogramma en kostengegevens weergeven

Als CSP-partner en geregistreerde Cloudyn-gebruiker kunt u uw clouduitgaven in Cloudyn bekijken en analyseren. [Azure Cost Management is standaard beschikbaar voor directe partners](../costs/get-started-partners.md) die voor hun klanten een Microsoft-klantovereenkomst hebben geregeld en een Azure-plan hebben gekocht.

Uw registratie biedt toegang tot de Cloudyn-portal. In deze quickstart vindt u de details van de registratieprocedure voor het maken van een Cloudyn-proefabonnement en het aanmelden bij de Cloudyn-portal.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Indirecte CSP-toegang in Cloudyn configureren

De Partner Center API is standaard alleen toegankelijk voor directe CSP's. Een directe CSP-provider kan echter toegang configureren voor hun indirecte CSP-klanten of -partners met behulp van entiteitsgroepen in Cloudyn.

Als u toegang wilt inschakelen voor indirecte CSP-klanten of -partners, voert u de volgende stappen uit voor het segmenteren van indirecte CSP-gegevens met behulp van Cloudyn-entiteitsgroepen. Wijs daarna de juiste gebruikersmachtigingen toe aan de entiteitsgroepen.

1. Maak met deze gegevens een entiteitsgroep via [Entiteiten maken](tutorial-user-access.md#create-and-manage-entities).
2. Volg de stappen in [Abonnementen toewijzen aan kostentiteiten](https://www.youtube.com/watch?v=d9uTWSdoQYo). Koppel de indirecte CSP-klant en de bijbehorende Azure-abonnementen aan de entiteit die u eerder hebt gemaakt.
3. Volg de stappen in [Een gebruiker met beheerderstoegang maken](tutorial-user-access.md#create-a-user-with-admin-access) om een gebruikersaccount met beheerderstoegang te maken. Controleer vervolgens of het gebruikersaccount beheerderstoegang heeft tot de specifieke entiteiten die u eerder hebt gemaakt voor het indirecte account.

Indirecte CSP-partners kunnen zich aanmelden bij de Cloudyn-portal met behulp van de accounts die u voor hen hebt gemaakt.


[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u de CSP-gegevens van uw Azure-abonnement gebruikt om u te registreren bij Cloudyn. U hebt zich ook aangemeld bij de Cloudyn-portal en de gegevenskosten weergegeven. Ga voor meer informatie over Cloudyn verder met de zelfstudie voor Cloudyn.

> [!div class="nextstepaction"]
> [Gebruik en kosten controleren](tutorial-review-usage.md)