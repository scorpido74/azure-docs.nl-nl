---
title: Aanmeldingsproblemen voor Azure-abonnementen oplossen
description: Hulp bij het oplossen van problemen waardoor u zich niet kunt aanmelden bij de Azure-portal of het Azure-accountcentrum.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: v-miegge
ms.openlocfilehash: 3ee600cb72d06781f87c8f68640576afa50cea06
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686493"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Aanmeldingsproblemen voor Azure-abonnementen oplossen

Deze handleiding helpt bij het oplossen van problemen waardoor u zich niet kunt aanmelden bij Azure Portal of het Azure-accountcentrum.

> [!NOTE]
> Als u problemen ondervindt bij het aanmelden voor een nieuw Azure-account, raadpleegt u [Problemen met de registratie van Azure-abonnementen oplossen](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-azure-sign-up).

## <a name="page-hangs-in-the-loading-status"></a>De pagina blijft hangen tijdens het laden

Als uw internetbrowserpagina vastloopt, voert u elk van de volgende stappen uit totdat de Azure-portal bereikbaar is.

- Vernieuw de pagina.
- Gebruik een andere internetbrowser.
- Gebruik de modus voor privé browsen van uw browser:

   - **Edge:** Open **Instellingen** (de drie puntjes bij uw profielafbeelding), selecteer **Nieuw InPrivate-venster**en ga vervolgens naar de [Azure-portal](https://portal.azure.com/) of het [Azure-accountcentrum](https://account.azure.com/Subscriptions). 
   - **Chrome:** Kies de **Incognito**-modus.
   - **Safari:** Kies **Bestand** en vervolgens **Nieuw privévenster**.

- Wis de cache en verwijder internetcookies:

   - **Edge:** Open **Instellingen** en selecteer **Privacy en services**. Volg de stappen onder **Browsegegevens wissen**. Controleer of de selectievakjes voor **Browsegeschiedenis**, **Downloadgeschiedenis**, en **In cache opgeslagen afbeeldingen en bestanden** zijn geselecteerd, en selecteer vervolgens **Verwijderen**.
   - **Chrome:** Kies **Instellingen** en selecteer **Browsegegevens wissen** onder **Privacy en beveiliging**.

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>U wordt automatisch aangemeld als een andere gebruiker

Dit probleem kan optreden als u meer dan één gebruikersaccount gebruikt in een internetbrowser.

Probeer een van de volgende methoden om dit probleem op te lossen:

- Wis de cache en verwijder internetcookies.

   - **Edge:** Open **Instellingen** en selecteer **Privacy en services**. Volg de stappen onder **Browsegegevens wissen**. Controleer of de selectievakjes voor **Browsegeschiedenis**, **Downloadgeschiedenis**, **Cookies** en **In cache opgeslagen afbeeldingen en bestanden** zijn geselecteerd, en selecteer vervolgens **Verwijderen**.
   - **Chrome:** Kies **Instellingen** en selecteer **Browsegegevens wissen** onder **Privacy en beveiliging**.
- Stel uw browserinstellingen opnieuw in op de standaardwaarden.
- Gebruik de modus voor privé browsen van uw browser. 
   - **Edge:** Open **Instellingen** (de drie puntjes bij uw profielafbeelding), selecteer **Nieuw InPrivate-venster**en ga vervolgens naar de [Azure-portal](https://portal.azure.com/) of het [Azure-accountcentrum](https://account.azure.com/Subscriptions). 
   - **Chrome:** Kies de **Incognito**-modus.
   - **Safari:** Kies **Bestand** en vervolgens **Nieuw privévenster**.

## <a name="i-can-sign-in-but-i-see-the-error-no-subscriptions-found"></a>Ik kan me aanmelden, maar ik zie de fout dat er geen abonnementen zijn gevonden

Dit probleem treedt op als u de verkeerde map hebt geselecteerd of als uw account niet over voldoende machtigingen beschikt.

**Scenario 1:** U ziet het probleem bij het aanmelden bij de [Azure-portal](https://portal.azure.com/)

Dit probleem oplossen:

- Controleer of de juiste Azure-map is geselecteerd door in de rechterbovenhoek uw account te selecteren.
- Als de juiste Azure-map is geselecteerd maar u nog steeds het foutbericht ontvangt, moet uw account worden [toegevoegd als eigenaar](https://docs.microsoft.com/azure/cost-management-billing/manage/add-change-subscription-administrator).

**Scenario 2:** U ziet het probleem bij het aanmelden bij het [Azure-accountcentrum](https://account.windowsazure.com/Subscriptions)

Controleer of het account dat u gebruikt de accountbeheerder is. Voer de volgende stappen uit om te controleren wie de accountbeheerder is:

1.  Meld u aan bij de [Abonnementenweergave in de Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1.  Selecteer het abonnement dat u wilt controleren en selecteer vervolgens **Instellingen**.
1.  Selecteer **Eigenschappen**. De accountbeheerder van het abonnement wordt weergegeven in het vak **Accountbeheerder**.

## <a name="additional-help-resources"></a>Aanvullende ondersteuningsresources

Andere artikelen met probleemoplossingen voor Azure Billing en abonnementen

- [Geweigerde kaart](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [Registratieproblemen voor abonnementen](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-azure-sign-up)
- [Geen abonnementen gevonden](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [Weergave voor bedrijfskosten is uitgeschakeld](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)
- [Documentatie voor Azure Billing](https://docs.microsoft.com/azure/cost-management-billing/)

## <a name="contact-us-for-help"></a>Contact met ons opnemen voor ondersteuning

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
