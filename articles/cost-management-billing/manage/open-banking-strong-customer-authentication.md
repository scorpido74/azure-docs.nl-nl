---
title: Open Banking (PSD2) en Sterke Klantverificatie (SCA) voor Azure-klanten
description: Dit artikel legt uit waarom meervoudige verificatie is vereist voor sommige Azure-aankopen en hoe u de verificatie kunt uitvoeren.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: banders
ms.openlocfilehash: 847e9b692ddbd7880c37d1068d61447dcccb9c80
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747149"
---
# <a name="open-banking-psd2-and-strong-customer-authentication-sca-for-azure-customers"></a>Open Banking (PSD2) en Sterke Klantverificatie (SCA) voor Azure-klanten

Vanaf 14 september 2019 zijn banken in de 31 landen/regio's van de [Europese Economische Ruimte](https://en.wikipedia.org/wiki/European_Economic_Area) verplicht om de identiteit van de persoon die een onlineaankoop doet, te verifiëren voordat de betaling wordt verwerkt. Deze controle vereist meervoudige verificatie om ervoor te zorgen dat uw online-aankopen veilig en beschermd zijn. Voor sommige landen/regio's wordt de datum voor deze verificatieverplichting uitgesteld. Voor meer informatie raadpleegt u de [Veelgestelde vragen van Microsoft over PSD2](https://support.microsoft.com/en-us/help/4517854?preview).

## <a name="what-psd2-means-for-azure-customers"></a>Wat PSD2 betekent voor Azure-klanten

Als u voor Azure betaalt met een creditcard die is uitgegeven door een bank in de [Europese Economische Ruimte](https://en.wikipedia.org/wiki/European_Economic_Area), moet u mogelijk meervoudige verificatie doorlopen voor de betalingswijze die in uw account is ingesteld. Het kan ook zijn dat u wordt gevraagd meervoudige verificatie te doorlopen bij het registreren of upgraden van uw Azure-account, zelfs als u op dat moment geen aankoop doet. Bovendien kan u worden gevraagd meervoudige verificatie te doorlopen wanneer u de betalingswijze van uw Azure-account wijzigt, uw uitgavenlimiet verwijdert of een rechtstreekse betaling verricht vanuit de Azure-portal, zoals het vereffenen van een openstaand saldo of het kopen van Azure-tegoed.

Als uw bank uw betaling voor de maandelijkse Azure-kosten weigert, ontvangt u van Azure een e-mail over de achterstallige betaling met instructies om het probleem te verhelpen. U kunt de meervoudige verificatie doorlopen en uw openstaande kosten in de Azure-portal voldoen.

## <a name="complete-multi-factor-authentication-in-the-azure-portal"></a>Meervoudige verificatie doorlopen in de Azure-portal

In de volgende gedeelten wordt beschreven hoe u meervoudige verificatie doorloopt in de Azure-portal. Gebruik de informatie die op u van toepassing is.

### <a name="change-the-active-payment-method-of-your-azure-account"></a>De actieve betalingswijze van uw Azure-account wijzigen

U kunt de actieve betalingswijze van uw Azure-account wijzigen door deze stappen te volgen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als accountbeheerder en navigeer naar **Kostenbeheer + facturering**.
2. Selecteer op de pagina **Overzicht** het betreffende abonnement in het raster **Mijn abonnementen**.
3. Selecteer onder Facturering de optie **Betalingswijzen**. U kunt een nieuwe creditcard toevoegen of een bestaande kaart instellen als de actieve betalingswijze voor het abonnement. Als uw bank meervoudige verificatie vereist, wordt u gevraagd om deze verificatie tijdens het proces te doorlopen.

Voor meer informatie raadpleegt u [Creditcard toevoegen, bijwerken of verwijderen voor Azure](change-credit-card.md).

### <a name="settle-outstanding-charges-for-azure-services"></a>Openstaande kosten verrekenen voor Azure-services

Als uw bank de kosten weigert, verandert de status van uw Azure-account in de Azure-portal in **Achterstallig**. U kunt de status van uw account controleren door deze stappen te volgen:

1. Meld u als accountbeheerder aan bij [Azure Portal](https://portal.azure.com/).
2. Zoek op **Kostenbeheer en facturering**.
3. Ga op de pagina **Overzicht** van **Kostenbeheer + facturering** naar het raster **Mijn abonnementen** en bekijk de statuskolom.
4. Als uw abonnement de status **Achterstallig** heeft, klikt u op **Saldo vereffenen**. U wordt gevraagd om tijdens het proces meervoudige verificatie te doorlopen.

### <a name="settle-outstanding-charges-for-marketplace-and-reservation-purchases"></a>Openstaande kosten verrekenen voor Marketplace- en reserveringsaankopen

Marketplace- en reserveringsaankopen worden niet samen met de Azure-services gefactureerd. Als uw bank de Marketplace- of reserveringskosten weigert, krijgt uw factuur de status achterstallig en wordt in de Azure-portal de optie **Nu betalen** weergegeven. U kunt achterstallige Marketplace- en reserveringsfacturen betalen door deze stappen te volgen:

1. Meld u als accountbeheerder aan bij [Azure Portal](https://portal.azure.com/).
2. Zoek naar **Kostenbeheer en facturering**.
3. Onder Facturering selecteert u **Facturen**.
5. Selecteer in de vervolgkeuzelijst van het filter voor abonnementen het abonnement dat is gekoppeld aan uw Marketplace- of reserveringsaankoop.
6. Bekijk de typekolom in het raster met facturen. Als het type **Azure Marketplace en reserveringen** is, ziet u de koppeling **Nu betalen** als de factuurdatum vervalt of al vervallen is. Als **Nu betalen**niet wordt weergegeven, betekent dit dat uw factuur al betaald is. U wordt gevraagd om voor Nu betalen meervoudige verificatie te doorlopen.

## <a name="next-steps"></a>Volgende stappen
- Raadpleeg [Achterstallig saldo voor uw Azure-abonnement vereffenen](resolve-past-due-balance.md) als u een Azure-rekening moet betalen.
