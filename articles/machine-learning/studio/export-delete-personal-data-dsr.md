---
title: Uw gegevens exporteren en verwijderen
titleSuffix: ML Studio (classic) - Azure
description: In-productgegevens die zijn opgeslagen door Azure Machine Learning Studio (klassiek) zijn beschikbaar voor export en verwijdering via de Azure-portal en ook via geverifieerde REST-API's. Telemetriegegevens zijn toegankelijk via de Azure Privacy Portal. Dit artikel laat zien hoe.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: c380d10d0c68794ec3810cea25341d68bb41400d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251685"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio-classic"></a>Gebruikersgegevens in het product exporteren en verwijderen uit Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

U in-productgegevens die zijn opgeslagen door Azure Machine Learning Studio (klassiek) verwijderen of exporteren met behulp van de Azure-portal, de Studio-interface (klassieke) interface, PowerShell en geverifieerde REST-API's. Dit artikel vertelt je hoe. 

Telemetriegegevens zijn toegankelijk via de Azure Privacy-portal. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>Welke soorten gebruikersgegevens verzamelt Studio (klassiek)?

Voor deze service bestaan gebruikersgegevens uit informatie over gebruikers die gemachtigd zijn om toegang te krijgen tot werkruimten en telemetrierecords van gebruikersinteracties met de service.

Er zijn twee soorten gebruikersgegevens in Machine Learning Studio (klassiek):
- **Persoonlijke accountgegevens:** Account-id's en e-mailadressen die zijn gekoppeld aan een account.
- **Klantgegevens:** Gegevens die u hebt geüpload om te analyseren.

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>Studio (klassieke) accounttypen en hoe gegevens worden opgeslagen

Er zijn drie soorten accounts in Machine Learning Studio (klassiek). Het soort account dat u hebt, bepaalt hoe uw gegevens worden opgeslagen en hoe u deze verwijderen of exporteren.

- Een **gastwerkruimte** is een gratis, anoniem account. U meldt zich aan zonder referenties op te geven, zoals een e-mailadres of wachtwoord.
    -  Gegevens worden verwijderd nadat de gastwerkruimte is verlopen.
    - Gastgebruikers kunnen klantgegevens exporteren via het UI-, REST-API's of PowerShell-pakket.
- Een **gratis werkruimte** is een gratis account waarop u zich aanmeldt met Microsoft-accountgegevens - een e-mailadres en wachtwoord.
    - U persoonlijke en klantgegevens exporteren en verwijderen, die onderworpen zijn aan verzoeken om gegevensover de persoon (DSR).
    - U klantgegevens exporteren via het UI-, REST-API's of PowerShell-pakket.
    - Voor gratis werkruimten die geen Azure AD-accounts gebruiken, kan telemetrie worden geëxporteerd met behulp van de privacyportal.
    - Wanneer u de werkruimte verwijdert, verwijdert u alle persoonlijke klantgegevens.
- Een **standaardwerkruimte** is een betaald account dat u met aanmeldingsreferenties gebruikt.
    - U persoonlijke en klantgegevens exporteren en verwijderen, die onderhevig zijn aan DSR-verzoeken.
    - U hebt toegang tot gegevens via de Azure Privacy portal
    - U persoonlijke en klantgegevens exporteren via het UI-, REST-API's of PowerShell-pakket
    - U uw gegevens verwijderen in de Azure-portal.

## <a name="delete-workspace-data-in-studio-classic"></a><a name="delete"></a>Werkruimtegegevens verwijderen in Studio (klassiek) 

### <a name="delete-individual-assets"></a>Afzonderlijke elementen verwijderen

Gebruikers kunnen elementen in een werkruimte verwijderen door ze te selecteren en vervolgens de knop Verwijderen te selecteren.

![Assets verwijderen in Machine Learning Studio (klassiek)](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Een hele werkruimte verwijderen

Gebruikers kunnen ook hun hele werkruimte verwijderen:
- Betaalde werkruimte: verwijder via de Azure-portal.
- Vrije werkruimte: gebruik de knop Verwijderen in het deelvenster **Instellingen.**

![Een gratis werkruimte in Machine Learning Studio verwijderen (klassiek)](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>Export Studio (klassieke) data met PowerShell
Gebruik PowerShell om al uw gegevens te exporteren naar een draagbaar formaat vanuit Azure Machine Learning Studio (klassiek) met behulp van opdrachten. Zie de [PowerShell-module voor het artikel van Azure Machine Learning Studio (klassiek)](powershell-module.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Machine Learning Studio (klassieke) REST API-referentie](https://docs.microsoft.com/rest/api/machinelearning/)voor documentatie over webservices en facturering van toezeggingsplannen. 
