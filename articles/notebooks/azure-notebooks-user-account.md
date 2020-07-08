---
title: Meld u aan Azure Notebooks preview
description: Configureer uw gebruikers account voor Azure Notebooks met behulp van een Microsoft-account of een werk-of school account.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 929a21785b5b48c81c2d2016874feed61021d3c0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85832182"
---
# <a name="your-user-account-for-azure-notebooks-preview"></a>Uw gebruikers account voor Azure Notebooks preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

U kunt Azure Notebooks gebruiken met of zonder u aan te melden met een gebruikers account:

- U kunt zonder u aan te melden, notitie blokken maken en uitvoeren, maar u kunt geen notitie blokken of gegevens bestanden behouden als onderdeel van projecten. Gebruikers die een koppeling naar een Azure-notebook ontvangen, kunnen bijvoorbeeld genieten van het notitie blok zonder dat ze zich hoeven aan te melden.
- Wanneer u bent aangemeld, worden alle projecten met uw account door Azure Notebooks bewaard. Aangemelde gebruikers hebben ook een gebruikers-ID waarmee ze hun projecten en notitie blokken met anderen kunnen delen.
- Wanneer het account dat wordt gebruikt voor Azure Notebooks ook is gekoppeld aan een Azure-abonnement, krijgt u extra voor delen, zoals het uitvoeren van notebooks op krachtigere servers, het maken van persoonlijke notitie blokken en het verlenen van machtigingen aan notebooks aan individuele gebruikers.

Voor het aanmelden bij Azure Notebooks is een micro soft-account of een werk-of school account vereist. U wordt gevraagd om uw account te selecteren wanneer u de **aanmeld** opdracht in de rechter bovenhoek van de pagina met notitie blokken selecteert:

![Aanmeld opdracht voor Azure Notebooks](media/accounts/sign-in-command.png)

Al het werk dat u in Azure Notebooks doet, is gekoppeld aan het account waarmee u zich aanmeldt. Elk account moet ook een unieke gebruikers-ID in uw [gebruikers profiel](azure-notebooks-user-profile.md)hebben. Als gevolg hiervan kunt u zich aanmelden bij Azure Notebooks met verschillende accounts als u afzonderlijke sets projecten en afzonderlijke identiteiten wilt behouden. Elk lid van een Data Science-Team kan bijvoorbeeld beide afzonderlijke accounts hebben naast een gedeeld groeps account dat ze gebruiken om hun werk te presen teren aan mensen buiten hun bedrijf. Docenten kunnen ook een account voor hun leer rollen onderhouden die afwijkt van een account dat wordt gebruikt in externe Consulting of open source-werkzaamheden.

## <a name="microsoft-accounts"></a>Micro soft-accounts

Micro soft-accounts worden gebruikt om zich aan te melden bij een wille keurig aantal micro soft-producten en-services, zoals Windows, azure, outlook.com, OneDrive en XBox Live. Als u een van deze services gebruikt, hebt u waarschijnlijk al een micro soft-account dat u kunt gebruiken met Azure Notebooks.

Als u niet zeker weet, selecteert u de opdracht **een maken** in het account prompt. U kunt een nieuw Microsoft-account maken op basis van een e-mail adres van elke provider.

![Opdracht voor het maken van een nieuwe Microsoft-account](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Als u probeert een nieuw account te maken met een e-mail adres waaraan al een account is gekoppeld, wordt het volgende bericht weer gegeven: ' u kunt u niet aanmelden met een werk-of school-e-mail adres. Gebruik een persoonlijk e-mail adres, zoals Gmail of Yahoo!, of ontvang een nieuwe Outlook-e-mail. Probeer in dat geval u aan te melden met het werk-e-mail adres zonder een nieuw account te maken.

Voor onderliggende accounts is de toegang tot Azure Notebooks standaard geblokkeerd. Als u zich aanmeldt met een kinder account, wordt de volgende fout weer gegeven:

![Fout bij het aanmelden met een kinder account: er is iets misgegaan, het bovenliggende item heeft de toegang geblokkeerd](media/accounts/child-account-error.png)

Als u toegang wilt inschakelen, moet een bovenliggend item de volgende stappen uitvoeren:

1. Ga naar `https://account.live.com/mk` en meld u aan met een bovenliggend account.
1. Selecteer in de sectie voor het onderliggende item de optie de **toegang van dit kind beheren voor apps**van derden.
1. Selecteer op de volgende pagina **toegang inschakelen**.
1. Wanneer het onderliggende account wordt gebruikt om u aan te melden bij Azure Notebooks, selecteert u **Ja** in de machtigings prompt die wordt weer gegeven.

> [!Warning]
> Het inschakelen van toegang tot apps van derden voor Azure Notebooks biedt ook toegang tot alle andere apps van derden. Ouders worden geadviseerd om zorgvuldig te gebruiken bij het inschakelen van de toegang en kunnen de activiteiten van hun kinderen nauw keuriger bewaken.

## <a name="work-or-school-accounts"></a>Werk- of schoolaccounts

Een werk-of school account wordt gemaakt door de beheerder van een organisatie om een lid van de organisatie toegang te geven tot Cloud Services van micro soft, zoals Microsoft 365, en ook als een account om zich aan te melden bij Windows op een computer die lid is van een domein. Een werk-of school account gebruikt meestal een organisatie-e-mail adres, zoals any-user@contoso.com .

Als u zich aanmeldt bij Azure Notebooks met een werk-of school account, is het mogelijk dat toestemming van de beheerder nodig is omdat Azure Notebooks de gegevens van het e-mail adres van het account en de browser gegevens van de gebruiker verzamelt of gebruikt. (Browser gegevens worden gebruikt voor het optimaliseren van functies op basis van het populaire gebruik.)

De beheerder van een organisatie account moet toestemming geven namens gebruikers als het niet mogelijk is om de toestemming van gebruikers te beperken. In dit geval zien gebruikers het bericht ' u hebt geen toegang tot deze toepassing ':

![Bericht ' u hebt geen toegang tot deze toepassing ' wanneer u een werk-of school account gebruikt](media/accounts/consent-permissions-denied.png)

Als u toestemming wilt geven als beheerder, gebruikt u de [pagina toestemming van beheerder](https://notebooks.azure.com/account/adminConsent), die u door het proces leidt.

## <a name="next-steps"></a>Volgende stappen  

> [!div class="nextstepaction"]
> [Uw profiel en gebruikers-ID bewerken](azure-notebooks-user-profile.md)
