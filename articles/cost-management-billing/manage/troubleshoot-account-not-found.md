---
title: Problemen met het bekijken van uw factureringsrekening in de Azure-portal oplossen
description: Dit artikel helpt u bij het oplossen van problemen bij het weergeven van uw factureringsrekening in de Azure-portal.
author: amberbhargava
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/24/2020
ms.author: banders
ms.openlocfilehash: 671189a6a328a77653b61542e3e30c848a75cb65
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85364263"
---
# <a name="troubleshoot-viewing-your-billing-account-in-the-azure-portal"></a>Problemen met het bekijken van uw factureringsrekening in de Azure-portal oplossen

Er wordt een factureringsaccount gemaakt wanneer u zich aanmeldt om Azure te gebruiken. U gebruikt uw factureringsaccount om uw facturen en betalingen te beheren en kosten bij te houden. U kunt toegang hebben tot meerdere factureringsrekeningen. U hebt zich bijvoorbeeld mogelijk bij Azure geregistreerd voor persoonlijk gebruik. U kunt ook toegang hebben tot Azure via de Enterprise Agreement van uw organisatie of de Microsoft-klantovereenkomst. Voor elk van deze scenario's hebt u een afzonderlijk factureringsaccount. Dit artikel helpt u bij het oplossen van problemen bij het weergeven van uw factureringsrekening in de Azure-portal.

U kunt uw factureringsrekeningen bekijken op de pagina [Azure Cost Management en facturering](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade).

Zie [Uw factureringsrekeningen weergeven in de Azure-portal](view-all-accounts.md) voor meer informatie over factureringsrekeningen en het bepalen van uw type factureringsrekening.

Als u uw factureringsrekening niet in de Azure-portal kunt zien, kunt u de volgende opties proberen:

## <a name="sign-in-to-a-different-tenant"></a>Aanmelden met een andere tenant

Uw factureringsrekening is gekoppeld aan één Azure Active Directory-tenant. U ziet uw factureringsrekening niet op de pagina Kostenbeheer en facturering als u bent aangemeld bij een onjuiste tenant. Gebruik de volgende stappen om over te schakelen naar een andere tenant in de Azure-portal en uw factureringsrekeningen in die tenant weer te geven.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer uw profiel (e-mailadres) in de rechterbovenhoek van de pagina.
1. Selecteer **Schakelen tussen directory's**.  
    ![Schermopname van het selecteren van Schakelen tussen directory's in de portal](./media/troubleshoot-account-not-found/select-switch-directory.png)
1. Selecteer een directory in de sectie **Alle directory's**.  
    ![Schermopname van het selecteren van een directory in de portal](./media/troubleshoot-account-not-found/select-directory.png)

## <a name="sign-in-with-a-different-email-address"></a>Aanmelden met een ander e-mailadres

Sommige gebruikers hebben meerdere e-mailadressen om zich aan te melden bij de [Azure-portal](https://portal.azure.com). Niet alle e-mailadressen hebben toegang tot een factureringsrekening. Als u zich aanmeldt met een e-mailadres dat is gemachtigd om resources te beheren, maar niet voor het weergeven van een factureringsrekening, ziet u de factureringsrekening niet op de pagina [Kostenbeheer en facturering](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) in de Azure-portal.

Meld u aan bij de Azure-portal met een e-mailadres dat is gemachtigd voor de factureringsrekening om toegang te krijgen tot uw factureringsrekening.

## <a name="sign-in-with-a-different-identity"></a>Aanmelden met een andere identiteit

Sommige klanten hebben twee identiteiten met hetzelfde e-mailadres: een werk- of schoolaccount en een persoonlijk account. Normaal gesproken heeft slechts een van de identiteiten machtigingen voor het weergeven van een factureringsrekening. Mogelijk hebt u twee identiteiten met één e-mailadres. Wanneer u zich aanmeldt met een identiteit die geen machtiging heeft voor het weergeven van een factureringsrekening, wordt de factureringsrekening niet weergegeven op de pagina [Kostenbeheer en facturering](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade). Gebruik de volgende stappen om van identiteit te wisselen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) in een InPrivate-/Incognito-venster.
1. Als uw e-mailadres twee identiteiten heeft, ziet u een optie voor het selecteren van een persoonlijk account of een werk- of schoolaccount. Selecteer een van de accounts.
1. Als u de factureringsrekening niet ziet op de pagina Kostenbeheer en facturering in de Azure-portal, herhaalt u stap 1 en 2 en selecteert u de andere identiteit.

## <a name="contact-us-for-help"></a>Contact met ons opnemen voor ondersteuning

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

De volgende artikelen over facturering en abonnementen kunnen nuttig zijn bij het oplossen van problemen.

- [Geweigerde kaart](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [Problemen met aanmelding bij een abonnement](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue)
- [Geen abonnementen gevonden](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [Weergave voor bedrijfskosten is uitgeschakeld](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)
