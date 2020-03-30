---
title: Lege naamruimten van Azure Access Control Service (ACS) opnieuw activeren
description: Zoek en schakel uw ACS-naamruimten (Azure Access Control Service) in en vraag een extensie aan om deze ingeschakeld te houden tot 4 februari 2019.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ff0ce05b13fea8409475e3415c5d810d7c79769a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154862"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>How to: Naamruimten voor uitgeschakelde toegangscontroleservice opnieuw activeren

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

In november 2017 hebben we aangekondigd dat Microsoft Azure Access Control Service (ACS), een service van Azure Active Directory (Azure AD), op 7 november 2018 wordt uitgeschakeld.

Sindsdien hebben we e-mails verzonden naar de admin-e-mail van de ACS-abonnementen over de ACS-pensioenleeftijd van 12 maanden, 9 maanden, 6 maanden, 3 maanden, 1 maand, 2 weken, 1 week en 1 dag voor de pensioendatum van 7 november 2018.

Op 3 oktober 2018 hebben we (via e-mail en [een blogpost)](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)een uitbreidingsaanbieding aangekondigd aan klanten die hun migratie niet voor 7 november 2018 kunnen voltooien. De aankondiging had ook instructies voor het aanvragen van de uitbreiding.

## <a name="why-your-namespace-is-disabled"></a>Waarom uw naamruimte is uitgeschakeld

Als je je nog niet hebt aangemeld voor de extensie, beginnen we acs-naamruimten vanaf 7 november 2018 uit te schakelen. Je moet de verlenging tot 4 februari 2019 al hebben aangevraagd; anders u de naamruimten niet inschakelen via PowerShell.

> [!NOTE]
> U moet een servicebeheerder of medebeheerder van het abonnement zijn om de PowerShell-opdrachten uit te voeren en een extensie aan te vragen.

## <a name="find-and-enable-your-acs-namespaces"></a>Uw ACS-naamruimten zoeken en inschakelen

U ACS PowerShell gebruiken om al uw ACS-naamruimten weer op te sommen en de uitgeschakelde ruimten opnieuw te activeren.

1. ACS PowerShell downloaden en installeren:
    1. Ga naar de PowerShell Gallery en download [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. Installeer de module:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Ontvang een lijst met alle mogelijke opdrachten:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Voer het gewenste hulp aan voor een specifieke opdracht uit om hulp te krijgen bij een specifieke opdracht:

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        waar `[Command-Name]` is de naam van de ACS-opdracht.
1. Maak verbinding met ACS met de **Connect-AcsAccount-cmdlet.** 

    Mogelijk moet u uw uitvoeringsbeleid wijzigen door **Set-ExecutionPolicy** uit te voeren voordat u de opdracht uitvoeren.
1. Vermeld uw beschikbare Azure-abonnementen met de cmdlet **Get-AcsSubscription.**
1. Vermeld uw ACS-naamruimten met de cmdlet **Get-AcsNamespace.**
1. Controleer of de naamruimten zijn uitgeschakeld `State` `Disabled`door te bevestigen dat dit is .

    [![Controleren of de naamruimten zijn uitgeschakeld](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    U `nslookup {your-namespace}.accesscontrol.windows.net` ook gebruiken om te bevestigen of het domein nog actief is.

1. Schakel uw ACS-naamruimte(en) in met de cmdlet **Enable-AcsNamespace.**

    Zodra u uw naamruimte(s) hebt ingeschakeld, u een extensie aanvragen, zodat de naamruimte(en) niet meer wordt uitgeschakeld vóór 4 februari 2019. Na die datum mislukken alle aanvragen aan ACS.

## <a name="request-an-extension"></a>Een verlenging aanvragen

Vanaf 21 januari 2019 nemen we nieuwe uitbreidingsaanvragen aan.

We zullen beginnen met het uitschakelen van naamruimten voor klanten die verlengingen hebben aangevraagd tot 4 februari 2019. U naamruimten nog steeds opnieuw inschakelen via PowerShell, maar de naamruimten worden na 48 uur opnieuw uitgeschakeld.

Na 4 maart 2019 kunnen klanten geen naamruimten meer inschakelen via PowerShell.

Verdere extensies worden niet langer automatisch goedgekeurd. Als u extra tijd nodig hebt om te migreren, neemt u contact op met [Azure-ondersteuning](https://portal.azure.com/#create/Microsoft.Support) om een gedetailleerde migratietijdlijn te bieden.

### <a name="to-request-an-extension"></a>Een verlenging aanvragen

1. Meld u aan bij de Azure-portal en maak een [nieuw ondersteuningsverzoek.](https://portal.azure.com/#create/Microsoft.Support)
1. Vul het nieuwe ondersteuningsaanvraagformulier in zoals in het volgende voorbeeld.

    | Veld Ondersteuningsaanvraag | Waarde |
    |-----------------------|--------------------|
    | **Type probleem** | `Technical` |
    | **Abonnement** | Instellen op uw abonnement |
    | **Service** | `All services` |
    | **Resource** | `General question/Resource not available` |
    | **Probleemtype** | `ACS to SAS Migration` |
    | **Onderwerp** | Het probleem beschrijven |

   ![Toont een voorbeeld van een nieuw verzoek voor technische ondersteuning](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

<!--

1. Navigate to your ACS namespace's management portal by going to `https://{your-namespace}.accesscontrol.windows.net`.
1. Select the **Read Terms** button to read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/), which will direct you to a page with the updated Terms of Use.

    [![Select the Read Terms button](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Select **Request Extension** on the banner at the top of the page. The button will only be enabled after you read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/).

    [![Select the Request Extension button](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. After the extension request is registered, the page will refresh with a new banner at the top of the page.

    [![Updated page with refreshed banner](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)
-->

## <a name="help-and-support"></a>Help en ondersteuning

- Als u problemen ondervindt nadat u deze how-to hebt gevolgd, neemt u contact op met [Azure-ondersteuning](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Als je vragen of feedback hebt over acsfeedback@microsoft.comacs-pensioen, neem dan contact met ons op via .

## <a name="next-steps"></a>Volgende stappen

- Bekijk de informatie over ACS-pensioen in [Hoe: Migreren vanuit de Azure Access Control Service](active-directory-acs-migration.md).
