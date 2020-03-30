---
title: Verificatie op basis van kopteksten met PingAccess voor Azure AD-toepassingsproxy | Microsoft Documenten
description: Publiceer toepassingen met PingAccess en App Proxy om verificatie op basis van kopteksten te ondersteunen.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3fb94629262519f8cfa5da72ee343726aa7d1c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367978"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Verificatie op basis van kopteksten voor één aanmelding met toepassingsproxy en PingAccess

Azure Active Directory (Azure AD) Application Proxy is een samenwerking aangegaan met PingAccess, zodat uw Azure AD-klanten toegang hebben tot meer van uw toepassingen. PingAccess breidt de [bestaande application proxy-aanbiedingen](application-proxy.md) uit met één aanmeldingstoegang tot toepassingen die kopteksten gebruiken voor verificatie.

## <a name="whats-pingaccess-for-azure-ad"></a>Wat is PingAccess voor Azure AD?

Met PingAccess voor Azure AD u gebruikers toegang en eenmalige aanmelding (SSO) geven voor toepassingen die kopteksten gebruiken voor verificatie. Application Proxy behandelt deze toepassingen als alle andere, met behulp van Azure AD om toegang te verifiëren en vervolgens het passeren van verkeer via de connectorservice. PingAccess zit voor de toepassingen en vertaalt het toegangstoken van Azure AD naar een koptekst. De toepassing ontvangt dan de verificatie in de indeling die kan worden gelezen.

Uw gebruikers zullen niets anders merken wanneer ze zich aanmelden om uw bedrijfstoepassingen te gebruiken. Ze kunnen nog steeds overal op elk apparaat werken. De Connectors voor toepassingsproxy leiden extern verkeer naar alle apps zonder rekening te houden met hun verificatietype, zodat ze nog steeds automatisch ladingen in evenwicht brengen.

## <a name="how-do-i-get-access"></a>Hoe krijg ik toegang?

Aangezien dit scenario afkomstig is van een partnerschap tussen Azure Active Directory en PingAccess, hebt u licenties nodig voor beide services. Azure Active Directory Premium-abonnementen bevatten echter een basisPingAccess-licentie die maximaal 20 toepassingen dekt. Als u meer dan 20 op kopteksten gebaseerde toepassingen wilt publiceren, u een extra licentie kopen bij PingAccess.

Zie [Azure Active Directory-edities](../fundamentals/active-directory-whatis.md)voor meer informatie.

## <a name="publish-your-application-in-azure"></a>Uw toepassing publiceren in Azure

Dit artikel is voor mensen om een toepassing met dit scenario voor de eerste keer te publiceren. Naast het detailleren van de publicatiestappen, begeleidt het u bij het aan de slag gaan met zowel Application Proxy als PingAccess. Als u beide services al hebt geconfigureerd, maar een opfriscursus wilt voor de publicatiestappen, gaat u naar de sectie [Uw toepassing toevoegen aan Azure AD met toepassingsproxy.](#add-your-application-to-azure-ad-with-application-proxy)

> [!NOTE]
> Aangezien dit scenario een partnerschap is tussen Azure AD en PingAccess, bestaan sommige instructies op de Ping-identiteitsite.

### <a name="install-an-application-proxy-connector"></a>Een toepassingsproxyconnector installeren

Als u Application Proxy hebt ingeschakeld en al een connector hebt geïnstalleerd, u deze sectie overslaan en [naar Uw toepassing toevoegen aan Azure AD met Application Proxy](#add-your-application-to-azure-ad-with-application-proxy).

De Application Proxy-connector is een Windows Server-service die het verkeer van uw externe werknemers naar uw gepubliceerde toepassingen leidt. Zie [Zelfstudie: Een on-premises toepassing voor externe toegang toevoegen via Application Proxy in Azure Active Directory](application-proxy-add-on-premises-application.md)voor meer gedetailleerde installatie-instructies.

1. Meld u aan bij de [Azure Active Directory-portal](https://aad.portal.azure.com/) als toepassingsbeheerder. De pagina **Azure Active Directory-beheercentrum** wordt weergegeven.
1. Selecteer **azure Active Directory** > **Application proxy** > **Download connector service**. De pagina **Download van de toepassingsproxyconnector** wordt weergegeven.

   ![Downloaden van toepassingsproxyconnector](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Volg de installatie-instructies.

Als u de connector downloadt, moet de proxy van de toepassing automatisch worden ingeschakeld voor uw map, maar zo niet, u **Toepassingsproxy inschakelen**selecteren.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Uw toepassing toevoegen aan Azure AD met toepassingsproxy

Er zijn twee acties die u moet uitvoeren in de Azure-portal. Eerst moet u uw toepassing publiceren met Application Proxy. Vervolgens moet u informatie verzamelen over de toepassing die u gebruiken tijdens de stappen PingAccess.

#### <a name="publish-your-application"></a>Uw toepassing publiceren

U moet eerst uw aanvraag publiceren. Deze actie omvat:

- Uw on-premises toepassing toevoegen aan Azure AD
- Een gebruiker toewijzen voor het testen van de toepassing en het kiezen van op kopteksten gebaseerde SSO
- De omleidings-URL van de toepassing instellen
- Machtigingen verlenen aan gebruikers en andere toepassingen om uw on-premises toepassing te gebruiken

Ga als het gaat om het publiceren van uw eigen on-premises applicatie:

1. Als u dat in de laatste sectie niet hebt gedaan, meldt u zich aan bij de [Azure Active Directory-portal](https://aad.portal.azure.com/) als toepassingsbeheerder.
1. Selecteer **Enterprise-toepassingen** > **Nieuwe toepassing** > Voeg een**on-premises toepassing toe.** De **pagina Uw eigen on-premises toepassings** toevoegen wordt weergegeven.

   ![Uw eigen on-premises toepassing toevoegen](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Vul de vereiste velden in met informatie over uw nieuwe toepassing. Gebruik de onderstaande richtlijnen voor de instellingen.

   > [!NOTE]
   > Zie [Een on-premises app toevoegen aan Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)voor een meer gedetailleerde walkthrough van deze stap.

   1. **Interne URL**: Normaal gesproken geeft u de URL op die u naar de aanmeldingspagina van de app brengt wanneer u zich in het bedrijfsnetwerk bevindt. Voor dit scenario moet de connector de PingAccess-proxy behandelen als de voorpagina van de toepassing. Gebruik deze `https://<host name of your PingAccess server>:<port>`indeling: . De poort is standaard 3000, maar u deze configureren in PingAccess.

      > [!WARNING]
      > Voor dit type eenmalige aanmelding moet `https` de interne URL `http`.

   1. **Verificatiemethode voor vooraf verificatie:** Kies **Azure Active Directory**.
   1. **URL vertalen in kopteksten:** Kies **Nee**.

   > [!NOTE]
   > Als dit uw eerste toepassing is, gebruikt u poort 3000 om deze instelling te starten en terug te komen als u uw PingAccess-configuratie wijzigt. Voor volgende toepassingen moet de poort overeenkomen met de listener die u hebt geconfigureerd in PingAccess. Meer informatie over [luisteraars in PingAccess](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Selecteer **Toevoegen**. De overzichtspagina voor de nieuwe toepassing wordt weergegeven.

Wijs nu een gebruiker toe voor het testen van toepassingen en kies een enkele aanmelding op basis van kopteksten:

1. Selecteer **gebruikers en groepen** > **Gebruikersgebruikers** > **en -groepen\<toevoegen op**de zijbalk van de toepassing ( Nummer> geselecteerd). Er verschijnt een lijst met gebruikers en groepen waaruit u kiezen.

   ![Toont de lijst met gebruikers en groepen](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Selecteer een gebruiker voor het testen van toepassingen en selecteer **Selecteren**. Zorg ervoor dat dit testaccount toegang heeft tot de on-premises toepassing.
1. Selecteer **Toewijzen**.
1. Selecteer op de zijbalk van de toepassing De optie **Op een malige** > **koptekst .**

   > [!TIP]
   > Als dit de eerste keer is dat u een enkele aanmelding op basis van kopteksten gebruikt, moet u PingAccess installeren. Gebruik de koppeling op deze enkele aanmeldingspagina om PingAccess te downloaden om ervoor te zorgen dat uw Azure-abonnement automatisch is gekoppeld aan uw PingAccess-installatie. U de downloadsite nu openen of later terugkomen op deze pagina.

   ![Toont aanbordvideo's op basis van kopteksten en PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Selecteer **Opslaan**.

Zorg er vervolgens voor dat de URL van de omleiding is ingesteld op uw externe URL:

1. Selecteer azure Active**Directory-app-registraties**op de zijbalk van het **Azure Active Directory-beheercentrum** **Azure Active Directory** > . Er verschijnt een lijst met toepassingen.
1. Selecteer uw toepassing.
1. Selecteer de koppeling naast **URI's omleiden**, met het aantal omleidings-URI's dat is ingesteld voor web- en openbare clients. De ** \<toepassingsnaam> - Verificatiepagina** wordt weergegeven.
1. Controleer of de externe URL die u eerder aan uw toepassing hebt toegewezen, in de lijst **URI's omleiden** staat. Als dit niet het is, voegt u nu de externe URL toe met behulp van een omleidings-URI-type **web**en selecteert u **Opslaan**.

Tot slot, het opzetten van uw on-premises applicatie, zodat gebruikers leestoegang hebben en andere toepassingen hebben lees / schrijf toegang:

1. Selecteer **api-machtigingen** > In de zijbalk **app-registraties** voor uw toepassing de optie API-machtigingen Voeg**microsoft-API's** > **Microsoft Graph****toe.** >  De pagina **API-machtigingen aanvragen** voor **Microsoft Graph** wordt weergegeven, die de API's voor Windows Azure Active Directory bevat.

   ![Toont de pagina API-machtigingen aanvragen](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Selecteer **Gedelegeerde machtigingen** > **Gebruiker** > **Gebruiker.Lees**.
1. Selecteer **Toepassingsmachtigingen** > **Toepassing.ReadWrite.All****Application** > .
1. Selecteer **Machtigingen toevoegen**.
1. Selecteer op de pagina **API-machtigingen** de **optie Toestemming voor beheerders verlenen \<voor uw adreslijstnaam>**.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Informatie verzamelen voor de stappen PingAccess

U moet deze drie gegevens (alle GUID's) verzamelen om uw toepassing in te stellen met PingAccess:

| Naam van het agbal AD | Naam van pingaccess,veld | Gegevensindeling |
| --- | --- | --- |
| **(Client-)id van de app** | **Client-id** | GUID |
| **(Tenant-)id van de map** | **Uitgevende instelling** | GUID |
| `PingAccess key` | **Client Secret** | Willekeurige tekenreeks |

Ga als volgt te werk om deze informatie te verzamelen:

1. Selecteer azure Active**Directory-app-registraties**op de zijbalk van het **Azure Active Directory-beheercentrum** **Azure Active Directory** > . Er verschijnt een lijst met toepassingen.
1. Selecteer uw toepassing. De pagina **App-registraties** voor uw toepassing wordt weergegeven.

   ![Registratieoverzicht voor een aanvraag](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Selecteer naast de **id-waarde van toepassing (client)** het pictogram **Kopiëren naar klembord** en kopieer t/m het. U geeft deze waarde later op als de client-id van PingAccess.
1. Selecteer vervolgens de **id-waarde directory (tenant)** ook **Kopiëren naar klembord**en kopieer en sla deze op. U geeft deze waarde later op als de uitgever van PingAccess.
1. Selecteer **certificaten en geheimen** > Op de zijbalk van de **app-registraties** voor uw toepassing**nieuwe clientgeheim.** De geheime pagina **Een client toevoegen** wordt weergegeven.

   ![Toont de pagina Een clientgeheim toevoegen](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. Typ **in** `PingAccess key`Beschrijving .
1. Kies **onder Verloopt**de optie hoe u de PingAccess-sleutel instelt: In 1 **jaar**, In **2 jaar**of **Nooit**.
1. Selecteer **Toevoegen**. De PingAccess-sleutel wordt weergegeven in de tabel met clientgeheimen, met een willekeurige tekenreeks die automatisch wordt gevuld in het veld **WAARDE.**
1. Selecteer naast het veld **WAARDE van** de PingAccess-sleutel het pictogram **Kopiëren naar klembord** en kopieer en sla het op. U geeft deze waarde later op als het clientgeheim van PingAccess.

**Werk `acceptMappedClaims` het veld bij:**

1. Meld u aan bij de [Azure Active Directory-portal](https://aad.portal.azure.com/) als toepassingsbeheerder.
1. Selecteer **Azure Active Directory** > **App-registraties**. Er verschijnt een lijst met toepassingen.
1. Selecteer uw toepassing.
1. Selecteer **Manifest**op de zijbalk van de pagina **App-registraties** voor uw toepassing . De manifestJSON-code voor de registratie van uw aanvraag wordt weergegeven.
1. Zoek naar `acceptMappedClaims` het veld en `True`wijzig de waarde in .
1. Selecteer **Opslaan**.

### <a name="use-of-optional-claims-optional"></a>Gebruik van optionele claims (optioneel)

Met optionele claims u standaard-maar-niet-standaardclaims toevoegen die elke gebruiker en tenant heeft. U optionele claims voor uw toepassing configureren door het toepassingsmanifest te wijzigen. Zie Het artikel [Het manifest van het Azure AD-toepassingsmanifest begrijpen](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

Voorbeeld om e-mailadres op te nemen in de access_token die PingAccess verbruikt:
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Gebruik van beleid voor het in kaart brengen van claims (optioneel)

[Claims Mapping Policy (preview)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties) voor kenmerken die niet bestaan in AzureAD. Met claimtoewijzing u oude on-prem-apps migreren naar de cloud door aanvullende aangepaste claims toe te voegen die worden ondersteund door uw ADFS- of gebruikersobjecten

Als u wilt dat uw toepassing een aangepaste claim gebruikt en extra velden opneemt, moet u er zeker van zijn dat u ook een aangepast beleid voor het toewijzen van claims hebt [gemaakt en aan de toepassing wilt toewijzen.](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)

> [!NOTE]
> Als u een aangepaste claim wilt gebruiken, moet u ook een aangepast beleid hebben dat is gedefinieerd en toegewezen aan de toepassing. Dit beleid moet alle vereiste aangepaste kenmerken bevatten.
>
> U beleidsdefinitie en toewijzing uitvoeren via PowerShell of Microsoft Graph. Als u ze in PowerShell doet, moet `New-AzureADPolicy` u deze mogelijk eerst `Add-AzureADServicePrincipalPolicy`gebruiken en vervolgens toewijzen aan de toepassing met . Zie [Beleidstoewijzing claimen voor](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)meer informatie .

Voorbeeld:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>PingAccess inschakelen om aangepaste claims te gebruiken

PingAccess inschakelen om aangepaste claims te gebruiken is optioneel, maar vereist als u verwacht dat de toepassing extra claims gebruikt.

Wanneer u PingAccess in de volgende stap configureert, moet de websessie die u maakt (Instellingen->Access->Websessies) **het aanvraagprofiel** hebben uitgeschakeld en **gebruikerskenmerken vernieuwen** ingesteld op **Geen**

## <a name="download-pingaccess-and-configure-your-application"></a>PingAccess downloaden en uw toepassing configureren

Nu u alle installatiestappen van Azure Active Directory hebt voltooid, u verder gaan met het configureren van PingAccess.

De gedetailleerde stappen voor het PingAccess-gedeelte van dit scenario worden voortgezet in de documentatie Ping-identiteit. Volg de instructies in [PingAccess configureren voor Azure AD om toepassingen te beschermen die zijn gepubliceerd met Microsoft Azure AD-toepassingsproxy](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) op de website Ping Identity.

Met deze stappen u PingAccess installeren en een PingAccess-account instellen (als u er nog geen hebt). Als u vervolgens een OIDC-verbinding (Azure AD OpenID Connect) wilt maken, stelt u een tokenprovider in met de **waarde directory (tenant)** die u hebt gekopieerd van de Azure AD-portal. Als u vervolgens een websessie op PingAccess wilt maken, `PingAccess key` gebruikt u de id en waarden **van toepassing (client).** Daarna u identiteitstoewijzing instellen en een virtuele host, site en toepassing maken.

### <a name="test-your-application"></a>Uw toepassing testen

Wanneer u al deze stappen hebt voltooid, moet uw toepassing operationeel zijn. Als u deze wilt testen, opent u een browser en navigeert u naar de externe URL die u hebt gemaakt toen u de toepassing in Azure hebt gepubliceerd. Meld u aan met het testaccount dat u aan de toepassing hebt toegewezen.

## <a name="next-steps"></a>Volgende stappen

- [PingAccess voor Azure AD configureren om toepassingen te beschermen die zijn gepubliceerd met Microsoft Azure AD-toepassingsproxy](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)
- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](what-is-single-sign-on.md)
- [Problemen en foutmeldingen van toepassingsproxy oplossen](application-proxy-troubleshoot.md)
