---
title: Tenant beperkingen gebruiken voor het beheren van de toegang tot SaaS-apps-Azure AD
description: Tenant beperkingen gebruiken om te beheren welke gebruikers toegang hebben tot apps op basis van hun Azure AD-Tenant.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: kenwith
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd302791aa783f1a95d48f666366aa845fcaadbb
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763020"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Tenant beperkingen gebruiken om de toegang tot SaaS-Cloud toepassingen te beheren

Grote organisaties die de beveiliging benadrukken, willen de overstap naar Cloud Services zoals Office 365, maar moeten weten dat hun gebruikers alleen toegang hebben tot goedgekeurde resources. Traditioneel beperken bedrijven domein namen of IP-adressen wanneer ze de toegang willen beheren. Deze aanpak mislukt in een wereld waar software as a Service (of SaaS)-apps worden gehost in een open bare Cloud, die wordt uitgevoerd op gedeelde domein namen als [Outlook.Office.com](https://outlook.office.com/) en [login.microsoftonline.com](https://login.microsoftonline.com/). Door deze adressen te blok keren, hebben gebruikers geen toegang meer tot Outlook op internet, in plaats van ze alleen te beperken tot goedgekeurde identiteiten en resources.

De oplossing voor de Azure Active Directory (Azure AD) voor deze uitdaging is een functie die Tenant beperkingen heet. Met Tenant beperkingen kunnen organisaties de toegang tot SaaS-Cloud toepassingen beheren op basis van de Azure AD-Tenant die de toepassingen gebruiken voor eenmalige aanmelding. U kunt bijvoorbeeld toegang verlenen tot de Office 365-toepassingen van uw organisatie, terwijl u geen toegang meer hebt tot instanties van deze zelfde toepassingen van andere organisaties.  

Met Tenant beperkingen kunnen organisaties de lijst met tenants opgeven waartoe hun gebruikers toegang mogen hebben. Azure AD verleent vervolgens alleen toegang tot deze toegestane tenants.

Dit artikel richt zich op de beperkingen van de Tenant voor Office 365, maar de functie zou moeten werken met elke SaaS-Cloud-app die moderne verificatie protocollen met Azure AD gebruikt voor eenmalige aanmelding. Als u SaaS-apps gebruikt met een andere Azure AD-Tenant van de Tenant die wordt gebruikt door Office 365, moet u ervoor zorgen dat alle vereiste tenants zijn toegestaan. Zie de [Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory)voor meer informatie over SaaS-Cloud-apps.

## <a name="how-it-works"></a>Hoe werkt het?

De algemene oplossing bestaat uit de volgende onderdelen:

1. **Azure AD**: als de `Restrict-Access-To-Tenants: <permitted tenant list>` is aanwezig, worden door Azure AD alleen beveiligings tokens uitgegeven voor de toegestane tenants.

2. **On-premises proxy server-infra structuur**: deze infra structuur is een proxy-apparaat dat kan Transport Layer Security (TLS)-inspectie. U moet de proxy configureren om de header met de lijst met toegestane tenants in te voegen in verkeer dat is bestemd voor Azure AD.

3. **Client software**: ter ondersteuning van Tenant beperkingen, moet client software tokens rechtstreeks aanvragen bij Azure AD, zodat de proxy infrastructuur verkeer kan onderscheppen. Office 365-toepassingen op basis van een browser ondersteunen momenteel Tenant beperkingen, zoals Office-clients die moderne authenticatie gebruiken (zoals OAuth 2,0).

4. **Moderne authenticatie**: Cloud Services moeten moderne verificatie gebruiken om Tenant beperkingen te gebruiken en de toegang tot alle niet-toegestane tenants te blok keren. U moet Office 365 Cloud Services configureren om standaard moderne verificatie protocollen te gebruiken. Lees de [bijgewerkte office 365 moderne verificatie](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)voor de meest recente informatie over Office 365-ondersteuning voor moderne verificatie.

In het volgende diagram ziet u de verkeers stroom op hoog niveau. Voor Tenant beperkingen is TLS-inspectie alleen vereist voor verkeer naar Azure AD, niet naar de Office 365-Cloud Services. Dit onderscheid is belang rijk, omdat het verkeers volume voor verificatie bij Azure AD doorgaans veel lager is dan het verkeers volume voor SaaS-toepassingen, zoals Exchange Online en share point online.

![Stroom voor het verkeer van Tenant beperkingen-diagram](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Tenant beperkingen instellen

Er zijn twee stappen om aan de slag te gaan met Tenant beperkingen. Zorg er eerst voor dat uw clients verbinding kunnen maken met de juiste adressen. Configureer vervolgens uw proxy-infra structuur.

### <a name="urls-and-ip-addresses"></a>Url's en IP-adressen

Als u Tenant beperkingen wilt gebruiken, moeten uw clients verbinding kunnen maken met de volgende Azure AD-Url's om te verifiëren: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.Microsoft.com](https://login.microsoft.com/)en [login.Windows.net](https://login.windows.net/). Om toegang te krijgen tot Office 365 moeten uw clients ook verbinding kunnen maken met de FQDN-namen (FULLy Qualified Domain names), Url's en IP-adressen die zijn gedefinieerd in [Office 365-url's en IP-](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)adresbereiken. 

### <a name="proxy-configuration-and-requirements"></a>Proxy configuratie en-vereisten

De volgende configuratie is vereist om Tenant beperkingen via uw proxy-infra structuur in te scha kelen. Deze richt lijnen zijn algemeen. Raadpleeg daarom de documentatie van de leverancier van uw proxy voor specifieke implementaties tappen.

#### <a name="prerequisites"></a>Vereisten

- De proxy moet TLS-interceptie, invoeging van HTTP-headers en het filteren van doelen met FQDN-Url's kunnen uitvoeren.

- Clients moeten de certificaat keten vertrouwen die door de proxy voor TLS-communicatie wordt weer gegeven. Als er bijvoorbeeld certificaten van een interne [open bare-sleutel infrastructuur (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) worden gebruikt, moet het interne certificaat van de basis certificerings instantie worden vertrouwd.

- Deze functie is opgenomen in Office 365-abonnementen, maar als u Tenant beperkingen wilt gebruiken om de toegang tot andere SaaS-apps te beheren, zijn Azure AD Premium 1 licenties vereist.

#### <a name="configuration"></a>Configuratie

Plaats voor elke inkomende aanvraag voor login.microsoftonline.com, login.microsoft.com en login.windows.net twee HTTP-headers: *beperken tot tenants* en *beperkende toegang-context*.

De headers moeten de volgende elementen bevatten:

- Gebruik voor *beperkende toegang tot tenants*een waarde van \<permitted tenant list\> , een door komma's gescheiden lijst met tenants waartoe u gebruikers toegang wilt geven. Elk domein dat is geregistreerd bij een Tenant kan worden gebruikt om de Tenant in deze lijst te identificeren. Als u bijvoorbeeld toegang wilt verlenen aan de tenants van Contoso en fabrikam, ziet het naam/waarde-paar er als volgt uit: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- Voor *Restrict-Access-context*gebruikt u een waarde van één map-id en declareert u welke Tenant de Tenant beperkingen instellen. Als u bijvoorbeeld Contoso wilt declareren als de Tenant die het beleid voor Tenant beperkingen instelt, ziet het naam/waarde-paar er als volgt uit: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> U kunt uw directory-ID vinden in de [Azure Active Directory Portal](https://aad.portal.azure.com/). Meld u aan als beheerder, selecteer **Azure Active Directory**en selecteer vervolgens **Eigenschappen**.

Als u wilt voor komen dat gebruikers hun eigen HTTP-header invoegen met niet-goedgekeurde tenants, moet de proxy de header voor het *beperken van toegang tot tenants* vervangen als deze al aanwezig is in de binnenkomende aanvraag.

Clients moeten worden gedwongen de proxy te gebruiken voor alle aanvragen van login.microsoftonline.com, login.microsoft.com en login.windows.net. Als er bijvoorbeeld een PAC-bestand wordt gebruikt om clients te leiden om de proxy te gebruiken, moeten eind gebruikers de PAC-bestanden niet kunnen bewerken of uitschakelen.

## <a name="the-user-experience"></a>De gebruikers ervaring

In deze sectie wordt de ervaring voor zowel eind gebruikers als beheerders beschreven.

### <a name="end-user-experience"></a>De ervaring voor de eindgebruiker

Een voor beeld van een gebruiker is het contoso-netwerk, maar probeert toegang te krijgen tot het fabrikam-exemplaar van een gedeelde SaaS-toepassing, zoals Outlook online. Als Fabrikam een niet-toegestane Tenant is voor de contoso-instantie, ziet de gebruiker een bericht over toegang geweigerd. Dit geeft aan dat u probeert toegang te krijgen tot een resource die tot een organisatie behoort die niet is goedgekeurd door uw IT-afdeling.

### <a name="admin-experience"></a>Beheerervaring

Hoewel de configuratie van Tenant beperkingen wordt uitgevoerd op de bedrijfs proxy-infra structuur, hebben beheerders rechtstreeks toegang tot de rapporten met Tenant beperkingen in de Azure Portal. De rapporten weer geven:

1. Meld u aan bij de [Azure Active Directory Portal](https://aad.portal.azure.com/). Het dash board **Azure Active Directory-beheer centrum** wordt weer gegeven.

2. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster. De pagina overzicht van Azure Active Directory wordt weer gegeven.

3. Selecteer in de kop **andere mogelijkheden** de optie **Tenant beperkingen**.

De beheerder voor de Tenant die is opgegeven als de beperkte-toegangs context Tenant kan dit rapport gebruiken om te zien of aanmeldingen zijn geblokkeerd vanwege het beleid voor Tenant beperkingen, met inbegrip van de identiteit die wordt gebruikt en de doel directory-ID. Aanmeldingen worden opgenomen als de Tenant instelling die de beperking heeft, ofwel de Tenant van de gebruiker of de resource Tenant voor de aanmelding is.

> [!NOTE]
> Het rapport kan beperkte informatie bevatten, zoals de doelmap-ID, wanneer een gebruiker die zich in een andere Tenant bevindt dan de niet-toegankelijke context-aanmelding. In dit geval wordt de door de gebruiker geïdentificeerde informatie, zoals naam en user principal name, gemaskeerd om gebruikers gegevens in andere tenants te beveiligen.

Net als bij andere rapporten in de Azure Portal, kunt u filters gebruiken om het bereik van uw rapport op te geven. U kunt filteren op een bepaald tijds interval, gebruiker, toepassing, client of status. Als u de knop **kolommen** selecteert, kunt u ervoor kiezen om gegevens weer te geven met een combi natie van de volgende velden:

- **Gebruiker**
- **Toepassing**
- **Status**
- **Date**
- **Datum (UTC)** (waarbij UTC Coordinated Universal Time)
- **MFA-authenticatie methode** (multi-factor Authentication-methode)
- **Informatie over MFA-authenticatie** (Details voor multi-factor Authentication)
- **MFA-resultaat**
- **IP-adres**
- **Client**
- **Gebruikersnaam**
- **Locatie**
- **Doel-Tenant-ID**

## <a name="office-365-support"></a>Ondersteuning voor Office 365

Office 365-toepassingen moeten voldoen aan twee criteria om Tenant beperkingen volledig te ondersteunen:

1. De client die wordt gebruikt voor ondersteuning van moderne verificatie.
2. Moderne authenticatie is ingeschakeld als het standaard verificatie protocol voor de Cloud service.

Raadpleeg [bijgewerkte office 365 moderne-verificatie](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) voor de meest recente informatie over welke Office-clients momenteel moderne authenticatie ondersteunen. Deze pagina bevat ook koppelingen naar instructies voor het inschakelen van moderne verificatie op specifieke Exchange Online-en Skype voor bedrijven online-tenants. In share point online is standaard moderne verificatie ingeschakeld.

Office 365-browser toepassingen (de Office-Portal, Yammer, share point-sites, Outlook op internet en meer) ondersteunen momenteel Tenant beperkingen. Op brede clients (Outlook, Skype voor bedrijven, Word, Excel, Power Point en meer) kunnen alleen Tenant beperkingen worden afgedwongen wanneer moderne verificatie wordt gebruikt.  

Outlook en Skype voor bedrijven-clients die ondersteuning bieden voor moderne verificatie kunnen nog steeds verouderde protocollen gebruiken voor tenants waar moderne authenticatie niet is ingeschakeld, waardoor Tenant beperkingen effectief worden omzeild. Met Tenant beperkingen kunnen toepassingen die gebruikmaken van verouderde protocollen, worden geblokkeerd als ze tijdens de verificatie contact opnemen met login.microsoftonline.com, login.microsoft.com of login.windows.net.

Voor Outlook in Windows kunnen klanten ervoor kiezen om beperkingen te implementeren die voor komen dat eind gebruikers niet-goedgekeurde e-mail accounts toevoegen aan hun profielen. Zie bijvoorbeeld de beleids instelling [voor het toevoegen van niet-standaard groep voor Exchange-accounts](https://gpsearch.azurewebsites.net/default.aspx?ref=1) .

## <a name="testing"></a>Testen

Als u Tenant beperkingen wilt proberen voordat u deze voor uw hele organisatie implementeert, hebt u twee opties: een op een host gebaseerde benadering van een hulp programma zoals Fiddler of een gefaseerde implementatie van proxy-instellingen.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler voor een op een host gebaseerde aanpak

Fiddler is een gratis proxy voor webfoutopsporing die kan worden gebruikt om HTTP/HTTPS-verkeer vast te leggen en te wijzigen, inclusief het invoegen van HTTP-headers. Voer de volgende stappen uit om Fiddler te configureren om Tenant beperkingen te testen:

1. [Down load en installeer Fiddler](https://www.telerik.com/fiddler).

2. Configureer Fiddler voor het ontsleutelen van HTTPS-verkeer, per [Help-documentatie van Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Configureer Fiddler voor het invoegen van de *beperkende toegang tot tenants* en *beperkende toegang-context* headers met aangepaste regels:

   1. In het hulp programma Fiddler Web debugger selecteert u het menu **regels** en selecteert u **regels aanpassen...** om het CustomRules-bestand te openen.

   2. Voeg de volgende regels toe aan het begin van de `OnBeforeRequest` functie. Vervang door \<tenant domain\> een domein dat is geregistreerd bij uw Tenant (bijvoorbeeld `contoso.onmicrosoft.com` ). Vervang door \<directory ID\> de Azure AD GUID-id van uw Tenant.

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      Als u meerdere tenants wilt toestaan, gebruikt u een komma om de namen van de tenants van elkaar te scheiden. Bijvoorbeeld:

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Sla het CustomRules-bestand op en sluit het.

Nadat u Fiddler hebt geconfigureerd, kunt u verkeer vastleggen door naar het menu **bestand** te gaan en **verkeer vastleggen**te selecteren.

### <a name="staged-rollout-of-proxy-settings"></a>Gefaseerde implementatie van proxy-instellingen

Afhankelijk van de mogelijkheden van uw proxy-infra structuur kunt u de implementatie van instellingen voor uw gebruikers in de praktijk faseren. Hier volgen een paar opties op hoog niveau voor overweging:

1. Gebruik PAC-bestanden om gebruikers te laten verwijzen naar een test proxy infrastructuur, terwijl normale gebruikers de infra structuur van de productie proxy blijven gebruiken.
2. Sommige proxy servers ondersteunen mogelijk verschillende configuraties met behulp van groepen.

Raadpleeg de documentatie van uw proxy server voor specifieke informatie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [bijgewerkte Office 365 modern-verificatie](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- De [Office 365-url's en IP-](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) adresbereiken controleren
