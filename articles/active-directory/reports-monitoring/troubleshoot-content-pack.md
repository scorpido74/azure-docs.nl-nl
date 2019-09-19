---
title: Problemen met Azure Active Directory activiteiten logboeken voor het oplossen van conflicten | Microsoft Docs
description: Voorziet u van een lijst met fout berichten van het inhouds pakket voor de Azure Active Directory-activiteit en de stappen om deze op te lossen.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 06/07/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e50f2b92318ada729ad8e3405af8403f31d7b6e
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129293"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Problemen met Azure Active Directory activiteiten logboeken van inhouds pakketten oplossen 

|  |
|--|
|Op dit moment maakt het inhoudspakket voor Microsoft Azure AD Power BI gebruik van de Azure AD Graph-API's om gegevens op te halen uit uw Azure AD-tenant. Hierdoor ziet u mogelijk enkele verschillen tussen de gegevens die beschikbaar zijn in het inhoudspakket en de gegevens die worden opgehaald met de [Microsoft Graph-API's voor rapportage](concept-reporting-api.md). |
|  |

Wanneer u werkt met het Power BI-inhouds pakket voor Azure Active Directory (Azure AD), is het mogelijk dat u de volgende fouten uitvoert: 

- [Vernieuwen is mislukt](troubleshoot-content-pack.md#refresh-failed) 
- [Kan de referenties van de gegevens bron niet bijwerken](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Het importeren van gegevens duurt te lang](#data-import-is-too-slow) 

In dit artikel vindt u informatie over de mogelijke oorzaken en het oplossen van deze fouten.
 
## <a name="refresh-failed"></a>Vernieuwen is mislukt 
 
**Hoe deze fout wordt opgehaald**: E-mail van de Power BI-of mislukte status in de geschiedenis vernieuwen. 


| Oorzaak | Oplossen |
| ---   | ---        |
| Fout bij het vernieuwen van fouten kan worden veroorzaakt wanneer de referenties van de gebruikers die verbinding maken met het inhouds pakket, opnieuw zijn ingesteld, maar niet zijn bijgewerkt in de verbindings instellingen van het inhouds pakket. | Ga in Power BI naar de gegevensset die overeenkomt met het dash board van Azure AD-activiteiten Logboeken (**Azure Active Directory activiteiten logboeken**), kies planning vernieuwen en voer uw Azure AD-referenties in. |
| Het vernieuwen kan mislukken vanwege grote gegevens sets. | Het Azure AD-inhouds pakket met Power BI kan momenteel alleen kleine gegevens sets (minder dan 500.000 rijen) ondersteunen vanwege enkele beperkingen in de Power BI-service. Als u vertragings fouten ondervindt of als het vernieuwen mislukt als gevolg van time-outproblemen, kan dit ertoe leiden dat u een grote gegevensset probeert op te halen. Verklein de tijds periode in de query en probeer het opnieuw.|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Kan de referenties van de gegevens bron niet bijwerken 
 
**Hoe deze fout wordt opgehaald**: Wanneer u in Power BI verbinding maakt met het inhouds pakket van Azure AD-activiteiten Logboeken. 

| Oorzaak | Oplossen |
| ---   | ---        |
| De gebruiker die verbinding maakt, is geen globale beheerder of beveiligings lezer of beveiligings beheerder. | Gebruik een account dat een globale beheerder of beveiligings lezer is, of een beveiligings beheerder voor toegang tot de inhouds pakketten. |
| Uw Tenant is geen Premium-Tenant of heeft niet ten minste één gebruiker met een Premium-licentie bestand. | [Een ondersteunings ticket indienen](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 


## <a name="data-import-is-too-slow"></a>Het importeren van gegevens is te langzaam 
 
**Hoe deze fout wordt opgehaald**: In Power BI, nadat u uw inhouds pakket hebt verbonden, wordt het gegevens import proces gestart om uw dash board voor te bereiden op Azure AD-activiteiten Logboeken. U ziet het bericht: **Gegevens importeren...** zonder verdere voortgang.  

| Oorzaak | Oplossen |
| ---   | ---        |
| Afhankelijk van de grootte van uw Tenant kan deze stap enkele minuten tot 30 minuten duren. | Als het bericht niet wordt gewijzigd zodat het dash board binnen een uur wordt weer gegeven, moet u [een ondersteunings ticket indienen](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Volgende stappen

* [Installeer Power bi inhouds pakket voor Azure AD-rapporten](quickstart-install-power-bi-content-pack.md).
* [Gebruik Power BI inhouds pakket voor Azure AD-rapporten om uw gegevens te visualiseren](howto-power-bi-content-pack.md)
* [Ondersteuning voor Azure Active Directory verkrijgen](../fundamentals/active-directory-troubleshooting-support-howto.md)
