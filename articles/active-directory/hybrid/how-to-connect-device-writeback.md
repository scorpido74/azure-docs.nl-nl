---
title: 'Azure AD Connect: terugschrijven van apparaten inschakelen | Microsoft Documenten'
description: In dit document wordt beschreven hoe u apparaatterugschrijfinformatie inschakelt met Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 632f6f80184c6ba3409bd30ae070cbaefc77f036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109506"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: terugschrijven van apparaten inschakelen
> [!NOTE]
> Een abonnement op Azure AD Premium is vereist voor het terugschrijven van apparaten.
> 
> 

In de volgende documentatie vindt u informatie over het inschakelen van de functie voor het terugschrijven van het apparaat in Azure AD Connect. Apparaatafschrijving wordt gebruikt in de volgende scenario's:

* Windows Hello voor Bedrijven inschakelen [met behulp van de implementatie van hybride certificaatvertrouwensrelaties](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust-prereqs#device-registration)
* Voorwaardelijke toegang inschakelen op basis van apparaten naar ADFS-toepassingen (2012 R2 of hoger) (vertrouwensrelaties van relying party).

Dit biedt extra zekerheid en zekerheid dat toegang tot toepassingen alleen wordt verleend aan vertrouwde apparaten. Zie Risico beheren met [voorwaardelijke toegang](../active-directory-conditional-access-azure-portal.md) en [On-premises voorwaardelijke toegang instellen met Azure Active Directory-apparaatregistratie](../../active-directory/active-directory-device-registration-on-premises-setup.md)voor meer informatie over voorwaardelijke toegang.

> [!IMPORTANT]
> <li>Apparaten moeten zich in hetzelfde forest bevinden als de gebruikers. Aangezien apparaten naar één forest moeten worden teruggeschreven, ondersteunt deze functie momenteel geen implementatie met meerdere gebruikersforests.</li>
> <li>Er kan slechts één configuratieobject voor apparaatregistratie worden toegevoegd aan het on-premises Active Directory-forest. Deze functie is niet compatibel met een topologie waarbij de on-premises Active Directory is gesynchroniseerd met meerdere Azure AD-mappen.</li>

## <a name="part-1-install-azure-ad-connect"></a>Deel 1: Azure AD Connect installeren
Installeer Azure AD Connect met aangepaste of Expresinstellingen. Microsoft raadt aan om te beginnen met alle gebruikers en groepen die zijn gesynchroniseerd voordat u apparaatterugschrijfformulieren inschakelt.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>Deel 2: Apparaatterugschrijven inschakelen in Azure AD Connect
1. Voer de wizard installatie opnieuw uit. Selecteer **Apparaatopties configureren** op de pagina Extra taken en klik op **Volgende**. 

    ![Apparaatopties configureren](./media/how-to-connect-device-writeback/deviceoptions.png)

    >[!NOTE]
    > De nieuwe apparaatopties configureren zijn alleen beschikbaar in versie 1.1.819.0 en nieuwer.

2. Selecteer op de pagina Apparaatopties de optie **Apparaatterugschrijven configureren**. Optie om **apparaatterugschrijfinformatie uit** te schakelen is pas beschikbaar als het terugschrijven van het apparaat is ingeschakeld. Klik op **Volgende** om naar de volgende pagina in de wizard te gaan.
    ![Apparaatbewerking kiezen](./media/how-to-connect-device-writeback/configuredevicewriteback1.png)

3. Op de terugschrijfpagina ziet u het meegeleverde domein als het standaard schrijfbos van het apparaat.
   ![Doelforest voor aangepaste installatieapparaten](./media/how-to-connect-device-writeback/writebackforest.png)

4. **De containerpagina** van het apparaat biedt de optie om de active directory voor te bereiden met behulp van een van de twee beschikbare opties:

    a. **Referenties voor bedrijfsbeheerders verstrekken:** als de referenties van de bedrijfsbeheerder worden verstrekt voor het forest waarin apparaten moeten worden teruggeschreven, bereidt Azure AD Connect het forest automatisch voor tijdens de configuratie van het terugschrijven van het apparaat.

    b. **PowerShell-script downloaden:** Azure AD Connect genereert automatisch een PowerShell-script waarmee de active directory kan worden voorbereid op het terugschrijven van apparaten. Als de referenties van de bedrijfsbeheerder niet kunnen worden verstrekt in Azure AD Connect, wordt voorgesteld om het PowerShell-script te downloaden. Geef het gedownloade PowerShell-script **CreateDeviceContainer.psq** aan de bedrijfsbeheerder van het forest waar naar apparaten wordt teruggeschreven.
    ![Active Directory Forest voorbereiden](./media/how-to-connect-device-writeback/devicecontainercreds.png)
    
    De volgende bewerkingen worden uitgevoerd voor het voorbereiden van het active directory forest:
    * Als ze nog niet bestaan, maakt en configureert u nieuwe containers en objecten onder CN=Device Registration Configuration,CN=Services,CN=Configuration,[forest-dn].
    * Als ze nog niet bestaan, maakt en configureert u nieuwe containers en objecten onder CN=RegisteredDevices,[domain-dn]. Apparaatobjecten worden in deze container gemaakt.
    * Hiermee stelt u de benodigde machtigingen in voor het Azure AD Connector-account om apparaten in uw Active Directory te beheren.
    * Alleen hoeft te worden uitgevoerd op een forest, zelfs als Azure AD Connect wordt geïnstalleerd op meerdere forests.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Controleren of apparaten zijn gesynchroniseerd met Active Directory
Apparaat terugschrijven moet nu goed werken. Houd er rekening mee dat het tot 3 uur kan duren voordat apparaatobjecten naar AD worden geschreven.  Ga als volgt te werk om te controleren of uw apparaten correct worden gesynchroniseerd:

1. Start het Active Directory-beheercentrum.
2. Breid RegisteredDevices uit, binnen het domein dat wordt gefedereerd.

   ![Geregistreerde apparaten van het Active Directory-beheercentrum](./media/how-to-connect-device-writeback/devicewriteback5.png)

3. Huidige geregistreerde apparaten worden daar vermeld.

   ![Lijst met geregistreerde apparaten van active directory-beheercentrum](./media/how-to-connect-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Voorwaardelijke toegang inschakelen
Gedetailleerde instructies om dit scenario in te schakelen zijn beschikbaar binnen [Het instellen van on-premises voorwaardelijke toegang met Azure Active Directory Device Registration](../../active-directory/active-directory-device-registration-on-premises-setup.md).

## <a name="troubleshooting"></a>Problemen oplossen
### <a name="the-writeback-checkbox-is-still-disabled"></a>Het selectievakje terugschrijven is nog steeds uitgeschakeld
Als het selectievakje voor het terugschrijven van apparaten niet is ingeschakeld, ook al hebt u de bovenstaande stappen gevolgd, worden u in de volgende stappen doorgeleid naar wat de installatiewizard controleert voordat het selectievakje is ingeschakeld.

First things first:

* Het forest waar de apparaten aanwezig zijn, moet het forestschema hebben geüpgraded naar Windows 2012 R2-niveau, zodat het apparaatobject en de bijbehorende kenmerken aanwezig zijn.
* Als de wizard Installatie al actief is, worden eventuele wijzigingen niet gedetecteerd. Vul in dit geval de wizard installatie in en voer deze opnieuw uit.
* Zorg ervoor dat het account dat u opgeeft in het initialisatiescript daadwerkelijk de juiste gebruiker is die wordt gebruikt door de Active Directory Connector. Voer de volgende stappen uit om dit te verifiëren:
  * Open **Synchronisatieservice**in het startmenu .
  * Open het tabblad **Connectors.**
  * Zoek de connector met type Active Directory Domain Services en selecteer deze.
  * Selecteer **Eigenschappen**onder **Acties**.
  * Ga naar **Verbinding maken met Active Directory Forest**. Controleer of het domein en de gebruikersnaam die op dit scherm zijn opgegeven, overeenkomen met het account dat aan het script is opgegeven.
    ![Connectoraccount in Sync Service Manager](./media/how-to-connect-device-writeback/connectoraccount.png)

Configuratie controleren in Active Directory:

* Controleer of de service voor apparaatregistratie zich op de onderstaande locatie bevindt (CN=DeviceRegistrationService,CN=Device Registration Services,CN=Device Registration Configuration,CN=Services,CN=Configuration) onder configuratienaamgevingscontext.

![Problemen oplossen, DeviceRegistrationService in configuratienaamruimte](./media/how-to-connect-device-writeback/troubleshoot1.png)

* Controleer of er slechts één configuratieobject is door in de configuratienaamruimte te zoeken. Als er meer dan één is, verwijdert u het duplicaat.

![Problemen oplossen, zoeken naar de dubbele objecten](./media/how-to-connect-device-writeback/troubleshoot2.png)

* Controleer in het object Apparaatregistratieservice of het kenmerk msDS-DeviceLocation aanwezig is en een waarde heeft. Zoek deze locatie op en zorg ervoor dat deze aanwezig is met de objectType msDS-DeviceContainer.

![Problemen oplossen, msDS-DeviceLocation](./media/how-to-connect-device-writeback/troubleshoot3.png)

![Problemen oplossen, objectklasse RegisteredDevices](./media/how-to-connect-device-writeback/troubleshoot4.png)

* Controleer of het account dat door de Active Directory Connector wordt gebruikt, machtigingen vereist voor de container Geregistreerde apparaten die in de vorige stap zijn gevonden. Dit zijn de verwachte machtigingen voor deze container:

![Problemen oplossen, machtigingen voor container verifiëren](./media/how-to-connect-device-writeback/troubleshoot5.png)

* Controleer of het Active Directory-account machtigingen heeft voor het object CN=Device Registration Configuration,CN=Services,CN=Configuration.

![Problemen oplossen, machtigingen voor apparaatregistratieconfiguratie verifiëren](./media/how-to-connect-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Aanvullende informatie
* [Risico beheren met voorwaardelijke toegang](../active-directory-conditional-access-azure-portal.md)
* [On-premises voorwaardelijke toegang instellen met Azure Active Directory-apparaatregistratie](../../active-directory/active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).

