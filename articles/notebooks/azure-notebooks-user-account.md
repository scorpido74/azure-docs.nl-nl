---
title: Aanmelden bij Azure Notebooks Preview
description: Configureer uw gebruikersaccount voor Azure-notitieblokken met een Microsoft-account of een werk-/school-account.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 9401a9e483f3f8db5af827f53937cb0f01b27a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646293"
---
# <a name="your-user-account-for-azure-notebooks-preview"></a>Uw gebruikersaccount voor Azure Notebooks Preview

U Azure-notitieblokken gebruiken met of zonder u aan te melden met een gebruikersaccount:

- Zonder u aan te melden, u notitieblokken maken en uitvoeren, maar notitieblokken of gegevensbestanden niet behouden als onderdeel van projecten. Gebruikers die bijvoorbeeld een koppeling naar een Azure Notebook ontvangen, kunnen van het notitieblok genieten zonder dat ze zich hoeven aan te melden.
- Wanneer azure-notitieblokken zijn aangemeld, behouden ze al uw projecten met uw account. Ingelogde gebruikers hebben ook een gebruikersnaam waarmee ze hun projecten en notitieblokken met anderen kunnen delen.
- Wanneer het account dat wordt gebruikt voor Azure Notebooks ook is gekoppeld aan een Azure-abonnement, krijgt u extra voordelen, zoals het uitvoeren van notitieblokken op krachtigere servers, het maken van privénotitieblokken en het verlenen van machtigingen aan notitieblokken aan individuele gebruikers.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Als u zich aanmeldt bij Azure Notebooks, is een Microsoft-account of een werk- of schoolaccount vereist. U wordt gevraagd om uw account wanneer u de opdracht **Aanmelden** selecteert aan de rechterbovenzijde van de pagina Notitieblokken:

![Opdracht Aanmelden voor Azure-notitieblokken](media/accounts/sign-in-command.png)

Al het werk dat u in Azure Notebooks doet, is gekoppeld aan het account dat u gebruikt om u aan te melden. Elk account moet ook een unieke gebruikersnaam in uw [gebruikersprofiel](azure-notebooks-user-profile.md)hebben. Als gevolg hiervan u zich aanmelden bij Azure Notebooks met verschillende accounts als u afzonderlijke sets van projecten en afzonderlijke identiteiten moet onderhouden. Elk lid van een data science-team kan bijvoorbeeld beide afzonderlijke accounts hebben naast een gedeeld groepsaccount dat ze gebruiken om hun werk te presenteren aan mensen buiten hun bedrijf. Cursusleiders kunnen ook een account onderhouden voor hun onderwijsrol die verschilt van een account dat wordt gebruikt in extern advies of open-source werk.

## <a name="microsoft-accounts"></a>Microsoft-accounts

Microsoft-accounts worden gebruikt om u aan te melden bij een aantal Microsoft-producten en -services, zoals Windows, Azure, outlook.com, OneDrive en XBox Live. Als u een van deze services gebruikt, is het waarschijnlijk dat u al een Microsoft-account hebt dat u gebruiken met Azure-notitieblokken.

Als u het niet zeker weet, selecteert u de opdracht **Eén maken** in de accountprompt. U een nieuw Microsoft-account maken met elk e-mailadres van elke provider.

![Opdracht om een nieuw Microsoft-account te maken](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Als u probeert een nieuw account aan te maken met een e-mailadres waaraan al een account is gekoppeld, u het bericht zien: 'U zich hier niet aanmelden met een werk- of schoole-mailadres. Gebruik een persoonlijke e-mail, zoals Gmail of Yahoo!, of ontvang een nieuwe Outlook-e-mail." Probeer je in dat geval aan te melden bij het werk-e-mailadres zonder een nieuw account te maken.

Voor onderliggende accounts wordt de toegang tot Azure Notebooks standaard geblokkeerd. Als u zich aanmeldt met een kinderaccount, wordt de onderstaande fout weergegeven:

![Fout bij het proberen in te loggen met een kinderaccount: er is iets misgegaan, je ouder heeft de toegang geblokkeerd](media/accounts/child-account-error.png)

Om toegang in te schakelen, moet een ouder de volgende stappen uitvoeren:

1. Bezoek `https://account.live.com/mk` en meld u aan met een bovenliggend account.
1. Selecteer in de sectie voor het kind in kwestie **de optie Toegang van dit kind tot apps van derden beheren**.
1. Selecteer op de volgende pagina **Toegang inschakelen**.
1. Wanneer het onderliggende account vervolgens wordt gebruikt om zich aan te melden bij Azure Notebooks, selecteert u **Ja** in de machtigingsprompt die wordt weergegeven.

> [!Warning]
> Als u toegang tot apps van derden voor Azure Notebooks inschakelt, u ook toegang krijgen voor alle andere apps van derden. Ouders wordt geadviseerd om discretie te gebruiken bij het inschakelen van toegang en willen de activiteiten van hun kind mogelijk beter volgen.

## <a name="work-or-school-accounts"></a>Werk- of schoolaccounts

Een werk- of schoolaccount wordt gemaakt door de beheerder van een organisatie om een lid van de organisatie toegang te geven tot Microsoft-cloudservices zoals Office 365, en ook als een account om u aan te melden bij Windows op een computer die is verbonden met een domein. Een werk- of schoolaccount gebruikt doorgaans een any-user@contoso.come-mailadres van een organisatie, zoals .

Als u zich aanmeldt bij Azure Notebooks met een werk- of schoolaccount, kan toestemming van de beheerder nodig zijn omdat Azure Notebooks informatie verzamelt of gebruikt (maar niet openbaar maakt), zoals het e-mailadres van het account en de browsergegevens van de gebruiker. (Browsergegevens worden gebruikt om functies te optimaliseren op basis van populair gebruik.)

De beheerder van een organisatieaccount moet namens gebruikers toestemming geven als gebruikers geen toestemming mogen geven. In dit geval zien gebruikers het bericht 'U hebt geen toegang tot deze toepassing':

![Bericht 'Je hebt geen toegang tot deze applicatie' bericht wanneer je een werk- of schoolaccount gebruikt](media/accounts/consent-permissions-denied.png)

Als u als beheerder toestemming wilt geven, gebruikt u de [toestemmingspagina](https://notebooks.azure.com/account/adminConsent)van de beheerder, die u door het proces leidt.

## <a name="next-steps"></a>Volgende stappen  

> [!div class="nextstepaction"]
> [Uw profiel en gebruikersnaam bewerken](azure-notebooks-user-profile.md)
