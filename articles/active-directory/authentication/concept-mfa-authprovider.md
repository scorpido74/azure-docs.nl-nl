---
title: Azure Multi-Factor Auth-providers - Azure Active Directory
description: Wanneer moet u een Auth-provider gebruiken met Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf50a8f58978a010fe3d8228ace8579fcf52eb38
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309906"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Wanneer een Azure Multi-Factor Authentication Provider gebruiken

> [!IMPORTANT]
> Vanaf 1 september 2018 kunnen nieuwe auth providers niet meer worden gemaakt. Bestaande auth-providers kunnen nog steeds worden gebruikt en bijgewerkt, maar migratie is niet langer mogelijk. Meervoudige verificatie blijft beschikbaar als functie in Azure AD Premium-licenties.

Verificatie in twee stappen is standaard beschikbaar voor globale beheerders van Azure Active Directory- en Office 365-gebruikers. Als u echter wilt profiteren van [geavanceerde functies](howto-mfa-mfasettings.md), moet u de volledige versie van Azure Multi-Factor Authentication (MFA) aanschaffen.

Een Azure Multi-Factor Auth-provider wordt gebruikt om te profiteren van functies die worden geleverd door Azure Multi-Factor Authentication voor gebruikers die **geen licenties hebben.**

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Kanttekeningen met betrekking tot de Azure MFA SDK

Let op: de SDK is afgeschaft en blijft alleen werken tot 14 november 2018. Na deze datum kan de SDK niet meer worden aangeroepen.

## <a name="what-is-an-mfa-provider"></a>Wat is een MFA-provider?

Er zijn twee soorten Auth-providers en het onderscheid is ongeveer hoe uw Azure-abonnement in rekening wordt gebracht. De optie Per verificatie berekent het aantal verificaties die in een maand op uw tenant worden uitgevoerd. Deze optie wordt aanbevolen als u een aantal gebruikers hebt die slechts sporadisch verifiëren. De optie Per gebruiker berekent het aantal personen in uw tenant die in een maand verificatie in twee stappen uitvoeren. Deze optie wordt aanbevolen als u een aantal gebruikers met licenties hebt, maar MFA wilt uitbreiden naar meer gebruikers buiten de restricties van uw licentie.

## <a name="manage-your-mfa-provider"></a>Uw MFA-provider beheren

U kunt het gebruiksmodel (per ingeschakelde gebruiker of per verificatie) niet wijzigen nadat een MFA-provider is gemaakt.

Als u voldoende licenties hebt aangeschaft om alle gebruikers te dekken die zijn ingeschakeld voor MFA, u de MFA-provider helemaal verwijderen.

Als de MFA-provider niet is gekoppeld aan een Azure AD-tenant, of als u de nieuwe MFA-provider koppelt aan een andere Azure AD-tenant, worden de gebruikersinstellingen en configuratieopties niet overgedragen. Bestaande Azure MFA-servers moeten ook opnieuw worden geactiveerd met behulp van activeringsreferenties die via de MFA-provider worden gegenereerd.

### <a name="removing-an-authentication-provider"></a>Een verificatieprovider verwijderen

> [!CAUTION]
> Er is geen bevestiging bij het verwijderen van een verificatieprovider. **Verwijderen** selecteren is een permanent proces.

Verificatieproviders zijn te vinden in de **Azure portal** > **Azure Active Directory** > **Security** > **MFA** > **Providers.** Klik op vermelde providers om details en configuraties te bekijken die aan die provider zijn gekoppeld.

Voordat u een verificatieprovider verwijdert, moet u rekening houden met de aangepaste instellingen die zijn geconfigureerd in uw provider. Bepaal welke instellingen moeten worden gemigreerd naar algemene MFA-instellingen van uw provider en voltooi de migratie van deze instellingen. 

Azure MFA-servers die aan providers zijn gekoppeld, moeten opnieuw worden geactiveerd met behulp van referenties die zijn gegenereerd onder **Azure Portal** > **Azure Active Directory** > **Security** > **MFA** > **Server-instellingen.** Voordat u het opnieuw activeert, moeten `\Program Files\Multi-Factor Authentication Server\Data\` de volgende bestanden worden verwijderd uit de map op Azure MFA-servers in uw omgeving:

- caCert
- Cert
- groepCACert
- groupKey
- Groupname
- licentieSleutel
- pkey pkey pkey pkey

![Een auth-provider verwijderen uit de Azure-portal](./media/concept-mfa-authprovider/authentication-provider-removal.png)

Wanneer u hebt bevestigd dat alle instellingen zijn gemigreerd, u naar de **Azure Portal** > **Azure Active Directory** > **Security** > **MFA-providers** **MFA** > bladeren en de ellipsen selecteren **...** en **verwijderen**selecteren.

> [!WARNING]
> Als u een verificatieprovider verwijdert, worden alle rapportagegegevens die aan die provider zijn gekoppeld, verwijderd. U activiteitenrapporten opslaan voordat u uw provider verwijderd.

> [!NOTE]
> Gebruikers met oudere versies van de Microsoft Authenticator-app en Azure MFA Server moeten hun app mogelijk opnieuw registreren.

## <a name="next-steps"></a>Volgende stappen

[Multi-Factor Authentication-instellingen configureren](howto-mfa-mfasettings.md)
