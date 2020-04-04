---
title: De Azure MFA NPS-extensie configureren - Azure Active Directory
description: Nadat u de NPS-extensie hebt geïnstalleerd, gebruikt u deze stappen voor geavanceerde configuratie zoals IP-whitelisting en UPN-vervanging.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34d92af88106151e7efba679c53c5b5bd1c07dcd
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653790"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Geavanceerde configuratieopties voor de NPS-extensie voor Multi-Factor Authentication

De NPS-extensie (Network Policy Server) breidt uw cloudgebaseerde Azure Multi-Factor Authentication-functies uit naar uw on-premises infrastructuur. In dit artikel wordt ervan uitgegaan dat u de extensie al hebt geïnstalleerd en nu wilt weten hoe u de extensie aanpassen voor uw behoeften. 

## <a name="alternate-login-id"></a>Alternatieve aanmeldings-id

Aangezien de NPS-extensie verbinding maakt met zowel uw on-premises als cloudmappen, u een probleem tegenkomen waarbij uw on-premises gebruikershoofdnamen (UPN's) niet overeenkomen met de namen in de cloud. Om dit probleem op te lossen, gebruikt u alternatieve aanmeldings-id's. 

Binnen de NPS-extensie u een Active Directory-kenmerk aanwijzen dat moet worden gebruikt in plaats van de UPN voor Azure Multi-Factor Authentication. Dit stelt u in staat om uw on-premises resources te beschermen met verificatie in twee stappen zonder uw on-premises UPN's te wijzigen. 

Als u alternatieve aanmeldings-namen wilt configureren, gaat u naar `HKLM\SOFTWARE\Microsoft\AzureMfa` en bewerkt u de volgende registerwaarden:

| Name | Type | Standaardwaarde | Beschrijving |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | tekenreeks | Leeg | Wijs de naam van het kenmerk Active Directory aan dat u wilt gebruiken in plaats van het UPN. Dit kenmerk wordt gebruikt als kenmerk AlternateLoginId. Als deze registerwaarde is ingesteld op een [geldig Active Directory-kenmerk](https://msdn.microsoft.com/library/ms675090.aspx) (bijvoorbeeld e-mail of displayName), wordt de waarde van het kenmerk gebruikt in plaats van de UPN van de gebruiker voor verificatie. Als deze registerwaarde leeg is of niet is geconfigureerd, wordt AlternateLoginId uitgeschakeld en wordt de UPN van de gebruiker gebruikt voor verificatie. |
| LDAP_FORCE_GLOBAL_CATALOG | booleaans | False | Gebruik deze vlag om het gebruik van globale catalogus voor LDAP-zoekopdrachten te forceren wanneer u AlternateLoginId opzoekt. Configureer een domeincontroller als globale catalogus, voeg het kenmerk AlternateLoginId toe aan de globale catalogus en schakel deze vlag in. <br><br> Als LDAP_LOOKUP_FORESTS is geconfigureerd (niet leeg), **wordt deze vlag afgedwongen als waar,** ongeacht de waarde van de registerinstelling. In dit geval vereist de NPS-extensie dat de globale catalogus wordt geconfigureerd met het kenmerk AlternateLoginId voor elk forest. |
| LDAP_LOOKUP_FORESTS | tekenreeks | Leeg | Geef een semi-dikke darm gescheiden lijst van bossen om te zoeken. Bijvoorbeeld, *contoso.com;foobar.com*. Als deze registerwaarde is geconfigureerd, zoekt de NPS-extensie iteratief alle forests in de volgorde waarin ze zijn vermeld en retourneert de eerste succesvolle AlternateLoginId-waarde. Als deze registerwaarde niet is geconfigureerd, is de alternateloginid-opzoekfunctie beperkt tot het huidige domein.|

Als u problemen met alternatieve aanmeldings-id's wilt oplossen, gebruikt u de aanbevolen stappen voor [Alternatieve inlog-id-fouten](howto-mfa-nps-extension-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>IP-uitzonderingen

Als u de beschikbaarheid van de server moet controleren, bijvoorbeeld als load balancers controleren welke servers worden uitgevoerd voordat workloads worden verzonden, wilt u niet dat deze controles worden geblokkeerd door verificatieverzoeken. Maak in plaats daarvan een lijst met IP-adressen waarvan u weet dat ze worden gebruikt door serviceaccounts en schakel vereisten voor meervoudige verificatie voor die lijst uit.

Als u een lijst met `HKLM\SOFTWARE\Microsoft\AzureMfa` IP-instellingen wilt configureren, gaat u naar en configureert u de volgende registerwaarde:

| Name | Type | Standaardwaarde | Beschrijving |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | tekenreeks | Leeg | Geef een lijst met gescheiden IP-adressen met dubbele dubbeldarmen op. Neem de IP-adressen op van machines waar serviceaanvragen vandaan komen, zoals de NAS/VPN-server. IP-bereiken en subnetten worden niet ondersteund. <br><br> Bijvoorbeeld *10.0.0.0.1;10.0.0.2;10.0.0.3*.

> [!NOTE]
> Deze registersleutel wordt niet standaard gemaakt door de installateur en er verschijnt een fout in het AuthZOptCh-logboek wanneer de service opnieuw wordt opgestart. Deze fout in het logboek kan worden genegeerd, maar als deze registersleutel wordt gemaakt en leeg wordt gelaten als dit niet nodig is, wordt het foutbericht niet weergegeven.

Wanneer een aanvraag binnenkomt van een `IP_WHITELIST`IP-adres dat bestaat in de verificatie in twee stappen, wordt deze overgeslagen. De IP-lijst wordt vergeleken met het IP-adres dat is opgegeven in het *kenmerk ratNASIPAddress* van de RADIUS-aanvraag. Als er een RADIUS-aanvraag binnenkomt zonder het kenmerk ratNASIPAddress, wordt de volgende waarschuwing geregistreerd: "P_WHITE_LIST_WARNING::IP Whitelist wordt genegeerd omdat bron-IP ontbreekt in RADIUS-aanvraag in het kenmerk NasIpAddress."

## <a name="next-steps"></a>Volgende stappen

[Resolve error messages from the NPS extension for Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md) (Foutberichten van de NPS-extensie voor Azure Multi-Factor Authentication oplossen)
