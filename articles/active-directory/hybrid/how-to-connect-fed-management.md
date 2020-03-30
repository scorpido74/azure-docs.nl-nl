---
title: Azure AD Connect - AD FS-beheer en -aanpassing | Microsoft Documenten
description: AD FS-beheer met Azure AD Connect en aanpassing van de inlogervaring van de gebruiker AD FS met Azure AD Connect en PowerShell.
keywords: AD FS, ADFS, AD FS-beheer, AAD Connect, Connect, sign-in, AD FS-aanpassing, reparatievertrouwensrelatie, O365, federatie, relying party
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcbeedddc65a916f869a778616779917a9571181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331985"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Active Directory Federation Services beheren en aanpassen met Azure AD Connect
In dit artikel wordt beschreven hoe u Active Directory Federation Services (AD FS) beheert en aanpast met Azure AD Connect (Azure AD). Het bevat ook andere veelvoorkomende AD FS-taken die u mogelijk moet uitvoeren voor een volledige configuratie van een AD FS-farm.

| Onderwerp | Wat het dekt |
|:--- |:--- |
| **AD FS beheren** | |
| [Herstel het vertrouwen](#repairthetrust) |De federatievertrouwensrelatie herstellen met Office 365. |
| [Federate met Azure AD met alternatieve inlog-id](#alternateid) | Federatie configureren met alternatieve aanmeldings-id  |
| [Een AD FS-server toevoegen](#addadfsserver) |Een AD FS-farm uitbreiden met een extra AD FS-server. |
| [Een proxyserver van AD FS-webtoepassing toevoegen](#addwapserver) |Een AD FS-farm uitbreiden met een extra WAP-server (Web Application Proxy). |
| [Een federatief domein toevoegen](#addfeddomain) |Hoe voeg je een federatief domein toe. |
| [Het TLS/SSL-certificaat bijwerken](how-to-connect-fed-ssl-update.md)| Het TLS/SSL-certificaat bijwerken voor een AD FS-farm. |
| **AD FS aanpassen** | |
| [Een aangepast bedrijfslogo of -illustratie toevoegen](#customlogo) |Een AD FS-aanmeldingspagina aanpassen met een bedrijfslogo en illustratie. |
| [Een aanmeldingsbeschrijving toevoegen](#addsignindescription) |Een aanmeldingspaginabeschrijving toevoegen. |
| [Regels voor AD FS-claimwijzigen](#modclaims) |Ad FS-claims wijzigen voor verschillende federatiescenario's. |

## <a name="manage-ad-fs"></a>AD FS beheren
U verschillende AD FS-gerelateerde taken uitvoeren in Azure AD Connect met minimale tussenkomst van de gebruiker met behulp van de wizard Azure AD Connect. Nadat u Azure AD Connect hebt geïnstalleerd door de wizard uit te voeren, u de wizard opnieuw uitvoeren om extra taken uit te voeren.

## <a name="repair-the-trust"></a><a name="repairthetrust"></a>Herstel het vertrouwen 
U Azure AD Connect gebruiken om de huidige status van de AD FS- en Azure AD-vertrouwensrelatie te controleren en passende acties te ondernemen om de vertrouwensrelatie te herstellen. Volg deze stappen om uw Azure AD- en AD FS-vertrouwensrelatie te herstellen.

1. Selecteer **AAD en ADFS-vertrouwensrelatie herstellen** in de lijst met extra taken.
   ![Aad- en ADFS-vertrouwensrelatie herstellen](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. Geef op de pagina **Verbinding maken met Azure AD** uw globale beheerdersreferenties op voor Azure AD en klik op **Volgende**.
   ![Verbinding maken met Azure AD](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. Voer op de pagina **Referenties voor externe toegang** de referenties voor de domeinbeheerder in.

   ![Rastoegangsreferenties](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    Nadat u op **Volgende**hebt geklikt, controleert Azure AD Connect op de certificaatstatus en worden eventuele problemen weergegeven.

    ![Staat van de certificaten](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    Op de pagina **Klaar om te configureren** wordt de lijst weergegeven met acties die worden uitgevoerd om de vertrouwensrelatie te herstellen.

    ![Gereed voor configuratie](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. Klik **op Installeren** om de vertrouwensrelatie te herstellen.

> [!NOTE]
> Azure AD Connect kan alleen certificaten herstellen of erop reageren die zelf zijn ondertekend. Azure AD Connect kan certificaten van derden niet herstellen.

## <a name="federate-with-azure-ad-using-alternateid"></a><a name="alternateid"></a>Federate met Azure AD met AlternateID 
Het wordt aanbevolen dat de on-premises user principal name (UPN) en de cloud User Principal Name hetzelfde blijven. Als de lokale UPN gebruikmaakt van een niet-routeerbare domein (ex. Contoso.local) of kan niet worden gewijzigd vanwege lokale toepassingsafhankelijkheden, raden we aan om alternatieve inlog-id in te stellen. Met alternatieve inlog-id u een aanmeldingservaring configureren waarbij gebruikers zich kunnen aanmelden met een ander kenmerk dan hun UPN, zoals e-mail. De keuze voor gebruikersnaam in Azure AD Connect is standaard voor het kenmerk UserPrincipalName in Active Directory. Als u een ander kenmerk voor gebruikersnaam kiest en federt met AD FS, configureert Azure AD Connect AD FS voor alternatieve aanmeldings-id. Hieronder wordt een voorbeeld gegeven van het kiezen van een ander kenmerk voor gebruikersnaam:

![Selectie van alternatieve id-kenmerken](./media/how-to-connect-fed-management/attributeselection.png)

Het configureren van alternatieve inlog-id voor AD FS bestaat uit twee hoofdstappen:
1. **De juiste set uitgifteclaims configureren:** de uitgifteclaimregels in de vertrouwensrelatie tussen Azure AD en relying party worden gewijzigd om het geselecteerde kenmerk UserPrincipalName te gebruiken als de alternatieve id van de gebruiker.
2. **Alternatieve aanmeldings-id inschakelen in de AD FS-configuratie**: de AD FS-configuratie wordt bijgewerkt, zodat AD FS gebruikers in de juiste forests kan opzoeken met behulp van de alternatieve id. Deze configuratie wordt ondersteund voor AD FS op Windows Server 2012 R2 (met KB2919355) of hoger. Als de AD FS-servers 2012 R2 zijn, controleert Azure AD Connect op de aanwezigheid van de vereiste KB. Als de KB niet wordt gedetecteerd, wordt een waarschuwing weergegeven nadat de configuratie is voltooid, zoals hieronder wordt weergegeven:

    ![Waarschuwing voor ontbrekende KB op 2012R2](./media/how-to-connect-fed-management/kbwarning.png)

    Als u de configuratie wilt herstellen in geval van ontbrekende KB, installeert u de vereiste [KB2919355](https://go.microsoft.com/fwlink/?LinkID=396590) en herstelt u de vertrouwensrelatie met [Reparatie AAD en AD FS Trust.](#repairthetrust)

> [!NOTE]
> Lees [Alternatieve aanmeldings-id](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id) configureren voor meer informatie over alternatieve ID en stappen die handmatig moeten worden geconfigureerd

## <a name="add-an-ad-fs-server"></a><a name="addadfsserver"></a>Een AD FS-server toevoegen 

> [!NOTE]
> Azure AD Connect heeft het PFX-certificaat nodig om een AD FS-server toe te voegen. Daarom u deze bewerking alleen uitvoeren als u de AD FS-farm hebt geconfigureerd met Azure AD Connect.

1. Selecteer **Een extra federatieserver implementeren**en klik op **Volgende**.

   ![Extra federatieserver](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. Voer op de pagina **Verbinding maken met Azure AD** uw globale beheerdersreferenties voor Azure AD in en klik op **Volgende**.

   ![Verbinding maken met Azure AD](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Geef de referenties van de domeinbeheerder op.

   ![Referenties voor domeinbeheerder](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. Azure AD Connect vraagt om het wachtwoord van het PFX-bestand dat u hebt opgegeven tijdens het configureren van uw nieuwe AD FS-farm met Azure AD Connect. Klik **op Wachtwoord invoeren** om het wachtwoord voor het PFX-bestand op te geven.

   ![Certificaatwachtwoord](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![TLS/SSL-certificaat opgeven](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. Voer op de pagina **AD FS-servers** de servernaam of het IP-adres in dat moet worden toegevoegd aan de AD FS-farm.

   ![AD FS-servers](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. Klik **op Volgende**en ga door de laatste pagina **Configureren.** Nadat Azure AD Connect klaar is met het toevoegen van de servers aan de AD FS-farm, krijgt u de optie om de connectiviteit te verifiëren.

   ![Gereed voor configuratie](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![Installatie voltooid](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="add-an-ad-fs-wap-server"></a><a name="addwapserver"></a>Een WAP-server van AD FS toevoegen 

> [!NOTE]
> Azure AD Connect heeft het PFX-certificaat nodig om een WAP-server toe te voegen. Daarom u deze bewerking alleen uitvoeren als u de AD FS-farm hebt geconfigureerd met Azure AD Connect.

1. Selecteer **Proxy van webtoepassing implementeren** in de lijst met beschikbare taken.

   ![Proxy van webtoepassing implementeren](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Geef de referenties van de globale beheerder van Azure op.

   ![Verbinding maken met Azure AD](./media/how-to-connect-fed-management/wapserver2.PNG)

3. Geef op de pagina **SSL-certificaat opgeven** het wachtwoord op voor het PFX-bestand dat u hebt opgegeven toen u de AD FS-farm configureerde met Azure AD Connect.
   ![Certificaatwachtwoord](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![TLS/SSL-certificaat opgeven](./media/how-to-connect-fed-management/WapServer4.PNG)

4. Voeg de server toe die als WAP-server moet worden toegevoegd. Omdat de WAP-server mogelijk niet aan het domein is verbonden, vraagt de wizard om beheerdersreferenties voor de server die wordt toegevoegd.

   ![Referenties van de beheerserver](./media/how-to-connect-fed-management/WapServer5.PNG)

5. Geef op de pagina **Proxy-vertrouwensreferenties** beheerdersreferenties op om de proxyvertrouwensrelatie te configureren en toegang te krijgen tot de primaire server in de AD FS-farm.

   ![Proxyvertrouwensreferenties](./media/how-to-connect-fed-management/WapServer6.PNG)

6. Op de pagina **Klaar om te configureren** toont de wizard de lijst met acties die worden uitgevoerd.

   ![Gereed voor configuratie](./media/how-to-connect-fed-management/WapServer7.PNG)

7. Klik **op Installeren** om de configuratie te voltooien. Nadat de configuratie is voltooid, geeft de wizard u de mogelijkheid om de verbinding met de servers te verifiëren. Klik **op Verifiëren** om de verbinding te controleren.

   ![Installatie voltooid](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="add-a-federated-domain"></a><a name="addfeddomain"></a>Een federatief domein toevoegen 

Het is eenvoudig om een domein toe te voegen dat met Azure AD kan worden gefedereerd met Azure AD. Azure AD Connect voegt het domein voor federatie toe en wijzigt de claimregels om de uitgever correct weer te geven wanneer u meerdere domeinen hebt die zijn gefedereerd met Azure AD.

1. Als u een federatief domein wilt toevoegen, selecteert u de taak **Een extra Azure AD-domein toevoegen**.

   ![Extra Azure AD-domein](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. Geef op de volgende pagina van de wizard de globale beheerdersreferenties voor Azure AD op.

   ![Verbinding maken met Azure AD](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. Geef op de pagina **Referenties voor externe toegang** de referenties van de domeinbeheerder op.

   ![Rastoegangsreferenties](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. Op de volgende pagina biedt de wizard een lijst met Azure AD-domeinen waarmee u uw on-premises directory federatieren. Kies het domein in de lijst.

   ![Azure AD-domein](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    Nadat u het domein hebt gekozen, geeft de wizard u de juiste informatie over verdere acties die de wizard zal uitvoeren en de impact van de configuratie. Als u in sommige gevallen een domein selecteert dat nog niet is geverifieerd in Azure AD, biedt de wizard u informatie waarmee u het domein verifiëren. Zie [Uw aangepaste domeinnaam toevoegen aan Azure Active Directory](../active-directory-domains-add-azure-portal.md) voor meer informatie.

5. Klik op **Volgende**. Op de pagina **Klaar om te configureren** wordt de lijst weergegeven met acties die Azure AD Connect uitvoert. Klik **op Installeren** om de configuratie te voltooien.

   ![Gereed voor configuratie](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> Gebruikers van het toegevoegde federatieve domein moeten worden gesynchroniseerd voordat ze kunnen inloggen op Azure AD.

## <a name="ad-fs-customization"></a>AD FS-aanpassing
In de volgende secties vindt u meer informatie over enkele veelvoorkomende taken die u mogelijk moet uitvoeren wanneer u uw AD FS-aanmeldingspagina aanpast.

## <a name="add-a-custom-company-logo-or-illustration"></a><a name="customlogo"></a>Een aangepast bedrijfslogo of -illustratie toevoegen 
Als u het logo wilt wijzigen van het bedrijf dat wordt weergegeven op de **aanmeldingspagina,** gebruikt u de volgende Windows PowerShell-cmdlet en -syntaxis.

> [!NOTE]
> De aanbevolen afmetingen voor het logo zijn 260 x 35 \@ 96 dpi met een bestandsgrootte van niet groter dan 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> De parameter *TargetName* is vereist. Het standaardthema dat is uitgebracht met AD FS, krijgt de naam Standaard.

## <a name="add-a-sign-in-description"></a><a name="addsignindescription"></a>Een aanmeldingsbeschrijving toevoegen 
Als u een aanmeldingspaginabeschrijving wilt toevoegen aan de **aanmeldingspagina,** gebruikt u de volgende Windows PowerShell-cmdlet en -syntaxis.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modify-ad-fs-claim-rules"></a><a name="modclaims"></a>Regels voor AD FS-claimwijzigen 
AD FS ondersteunt een uitgebreide claimtaal die u gebruiken om aangepaste claimregels te maken. Zie [De rol van de taal van de claimregel voor](https://technet.microsoft.com/library/dd807118.aspx)meer informatie .

In de volgende secties wordt beschreven hoe u aangepaste regels schrijven voor bepaalde scenario's die betrekking hebben op Azure AD en AD FS-federatie.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Onveranderlijke ID afhankelijk van een waarde die aanwezig is in het kenmerk
Met Azure AD Connect u een kenmerk opgeven dat moet worden gebruikt als bronanker wanneer objecten worden gesynchroniseerd met Azure AD. Als de waarde in het aangepaste kenmerk niet leeg is, u een onveranderlijke id-claim indienen.

U bijvoorbeeld **ms-ds-consistencyguid** selecteren als het kenmerk voor het bronanker en **ImmutableID** als **ms-ds-consistencyguid** uitgeven voor het geval het kenmerk er een waarde tegenover heeft. Als er geen waarde is voor het kenmerk, geeft u **objectGuid** als de onveranderlijke id. U de set aangepaste claimregels samenstellen zoals beschreven in de volgende sectie.

**Regel 1: Querykenmerken**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

In deze regel bevraagt u de waarden van **ms-ds-consistencyguid** en **objectGuid** voor de gebruiker uit Active Directory. Wijzig de winkelnaam in een geschikte winkelnaam in uw AD FS-implementatie. Wijzig ook het type claims in een juist claimtype voor uw federatie, zoals gedefinieerd voor **objectGuid** en **ms-ds-consistencyguid.**

U vermijdt ook het toevoegen **en** niet **uitgeven**van een uitgaande kwestie voor de entiteit en u de waarden als tussenliggende waarden gebruiken. U geeft de claim in een latere regel uit nadat u hebt vastgesteld welke waarde u als onveranderlijke id moet gebruiken.

**Regel 2: Controleer of ms-ds-consistencyguid bestaat voor de gebruiker**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Deze regel definieert een tijdelijke vlag genaamd **idflag** die is ingesteld op **useguid** als er geen **ms-ds-consistencyguid voor** de gebruiker. De logica hierachter is het feit dat AD FS geen lege claims toestaat. Dus wanneer u `http://contoso.com/ws/2016/02/identity/claims/objectguid` `http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid` claims toevoegt en in regel 1, komt u alleen in een **msdsconsistencyguid-claim** terecht als de waarde voor de gebruiker wordt ingevuld. Als deze niet wordt ingevuld, ziet AD FS dat deze een lege waarde heeft en deze onmiddellijk daalt. Alle objecten hebben **bezwaarGuid,** zodat claim zal er altijd zijn nadat regel 1 is uitgevoerd.

**Regel 3: Geef ms-ds-consistencyguid af als onveranderlijke ID als deze aanwezig is**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c.Value);

Dit is een impliciete **Exist** check. Als de waarde voor de claim bestaat, geef dat dan af als de onveranderlijke ID. In het vorige voorbeeld wordt de **claim voor naam-id's** gebruikt. U moet dit wijzigen in het juiste claimtype voor de onveranderlijke id in uw omgeving.

**Regel 4: Probleem objectGuid als onveranderlijke ID als ms-ds-consistencyGuid niet aanwezig is**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c2.Value);

In deze regel controleert u gewoon de tijdelijke **vlag-idflag.** U beslist of u de claim wilt indienen op basis van de waarde ervan.

> [!NOTE]
> De volgorde van deze regels is belangrijk.

### <a name="sso-with-a-subdomain-upn"></a>SSO met subdomein UPN

U meer dan één domein toevoegen dat moet worden gefedereerd met Azure AD Connect, zoals beschreven in [Een nieuw federatief domein toevoegen.](how-to-connect-fed-management.md#addfeddomain) Met Azure AD Connect-versie 1.1.553.0 en uiterlijk wordt automatisch de juiste claimregel voor issuerID gemaakt. Als u geen gebruik maken van Azure AD Connect-versie 1.1.553.0 of uiterlijk, wordt aanbevolen dat het hulpprogramma [voor Azure AD RPT-claimregels](https://aka.ms/aadrptclaimrules) wordt gebruikt om de juiste claimregels voor de vertrouwensrelatie van azure AD-relying party te genereren en in te stellen.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [aanmeldingsopties voor gebruikers](plan-connect-user-signin.md).
