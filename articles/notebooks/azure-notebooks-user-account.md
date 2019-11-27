---
title: Meld u aan bij Azure-laptops
description: Configureer uw gebruikersaccount voor Azure-laptops met behulp van een Microsoft-account of een werk/school-account.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: a2d8c8180dfb5dc31e273c7953a57f40cf31238d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277625"
---
# <a name="your-user-account-for-azure-notebooks"></a>Uw gebruikersaccount voor Azure-laptops

U kunt Azure-Notebooks gebruiken met of zonder aanmelden met een gebruikersaccount:

- Zonder dat u aangemeld, kunt u maken en notitieblokken uitvoeren, maar laptops of gegevensbestanden worden opgeslagen als onderdeel van de projecten kan niet worden behouden. Gebruikers die een koppeling naar een Azure-notitieblok, ontvangen zo kunnen profiteren van de notebook zonder te hoeven aanmelden.
- Wanneer u bent aangemeld, behoudt Azure notitieblokken al uw projecten met uw account. Aangemelde gebruikers hebben ook een gebruikers-ID waarmee ze hun projecten en -laptops met anderen delen.
  - Wanneer het account dat wordt gebruikt voor Azure-laptops ook gekoppeld aan een Azure-abonnement is, krijgt u extra voordelen, zoals het uitvoeren van notitieblokken op krachtigere servers, het maken van persoonlijke laptops en het verlenen van machtigingen tot notitieblokken naar afzonderlijke gebruikers.

Aanmelden bij Azure-notitieblokken vereist een Microsoft-Account of een 'Werk of School'-account. U wordt gevraagd om uw account te selecteren wanneer u de **aanmeld** opdracht in de rechter bovenhoek van de pagina met notitie blokken selecteert:

![Meld u aan opdracht voor Azure-laptops](media/accounts/sign-in-command.png)

Al het werk u in Azure-laptops doet is gekoppeld aan het account waarmee u zich aanmeldt. Elk account moet ook een unieke gebruikers-ID in uw [gebruikers profiel](azure-notebooks-user-profile.md)hebben. Als gevolg hiervan kunt u zich in de Azure-laptops met verschillende accounts als u wilt onderhouden verschillende sets van projecten en afzonderlijke identiteiten. Elk lid van een team van gegevenswetenschappers mogelijk bijvoorbeeld zowel individuele accounts samen als een gedeelde groepsaccount die ze gebruiken voor hun werk te presenteren aan mensen buiten het bedrijf. Docenten, mogelijk op dezelfde manier een account voor hun rol onderwijs die verschilt van een account dat wordt gebruikt voor externe consulting of open-source-werk bijhouden.

## <a name="microsoft-accounts"></a>Microsoft-accounts

Microsoft-accounts worden gebruikt voor aanmelding bij een willekeurig aantal Microsoft-producten en services, zoals Windows, Azure, outlook.com, OneDrive en XBox Live. Als u een van deze services gebruikt, is het waarschijnlijk dat er al een Microsoft-Account die u met Azure-Notebooks gebruiken kunt.

Als u niet zeker weet, selecteert u de opdracht **een maken** in het account prompt. U kunt een nieuw Microsoft-account met behulp van een e-mailadres van een provider maken.

![Opdracht voor het maken van een nieuw Microsoft-account](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Als u probeert een nieuw account te maken met een e-mail adres waaraan al een account is gekoppeld, wordt het volgende bericht weer gegeven: ' u kunt u niet aanmelden met een werk-of school-e-mail adres. Gebruik een persoonlijk e-mail adres, zoals Gmail of Yahoo!, of ontvang een nieuwe Outlook-e-mail. Probeer in dat geval u aan te melden met het werk-e-mail adres zonder een nieuw account te maken.

Voor accounts van kinderen, wordt de toegang tot Azure-notitieblokken standaard geblokkeerd. Aanmelden met een kinderaccount, worden de onderstaande fout weergegeven:

![Fout bij het aanmelden met een kinderaccount: Er is iets misgegaan, je ouders heeft de toegang geblokkeerd](media/accounts/child-account-error.png)

Als u wilt toegang inschakelt, moet een bovenliggende doen de volgende stappen uit:

1. Ga naar `https://account.live.com/mk` en meld u aan met een bovenliggend account.
1. Selecteer in de sectie voor het onderliggende item de optie de **toegang van dit kind beheren voor apps**van derden.
1. Selecteer op de volgende pagina **toegang inschakelen**.
1. Wanneer het onderliggende account wordt gebruikt om u aan te melden bij Azure Notebooks, selecteert u **Ja** in de machtigings prompt die wordt weer gegeven.

> [!Warning]
> Toegang tot apps van derden inschakelen voor Azure-laptops, kunt ook de toegang voor alle andere apps van derden. Ouders aangeraden Wees voorzichtig bij het inschakelen van toegang en wellicht de voorkeur voor het bewaken van de hun onderliggende activiteit beter aansluiten.

## <a name="work-or-school-accounts"></a>Werk- of schoolaccounts

Een account voor werk of school is gemaakt door de beheerder van een organisatie voor een lid van de organisatie voor toegang tot Microsoft-cloudservices zoals Office 365, en ook als een account aanmelden bij Windows op een computer voor het domein. Een werk-of school account gebruikt meestal een organisatie-e-mail adres, zoals any-user@contoso.com.

Aanmelden bij Azure-laptops met een account voor werk of school mogelijk administrator toestemming vereist omdat Azure notitieblokken worden verzameld of wordt gebruikt (maar niet bekendmaakt) gegevens zoals e-mailadres van het account en gegevens van de browser van de gebruiker. (Browsergegevens wordt gebruikt voor het optimaliseren van functies op basis van gebruik van populaire.)

De beheerder van een organisatie-account moet toestemming namens gebruikers opgeven als gebruikers afzonderlijk ontmoedigt worden beperkt. Gebruikers zien in dit geval wordt het bericht 'U geen toegang tot deze toepassing':

![Bericht 'U geen toegang tot deze toepassing' bij het gebruik van een account voor werk of school](media/accounts/consent-permissions-denied.png)

Als u toestemming wilt geven als beheerder, gebruikt u de [pagina toestemming van beheerder](https://notebooks.azure.com/account/adminConsent), die u door het proces leidt.

## <a name="next-steps"></a>Volgende stappen  

> [!div class="nextstepaction"]
> [Uw profiel en gebruikers-ID bewerken](azure-notebooks-user-profile.md)
