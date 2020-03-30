---
title: Tenantbeperkingen gebruiken om toegang tot SaaS-apps te beheren - Azure AD
description: Hoe tenantbeperkingen te gebruiken om te beheren welke gebruikers toegang hebben tot apps op basis van hun Azure AD-tenant.
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
ms.date: 03/28/2019
ms.author: mimart
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecd49b340810f92727f0fc98f84031c8cbf68179
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481174"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Tenantbeperkingen gebruiken om de toegang tot SaaS-cloudtoepassingen te beheren

Grote organisaties die de nadruk leggen op beveiliging willen overstappen naar cloudservices zoals Office 365, maar moeten weten dat hun gebruikers alleen toegang hebben tot goedgekeurde bronnen. Traditioneel beperken bedrijven domeinnamen of IP-adressen wanneer ze toegang willen beheren. Deze aanpak mislukt in een wereld waar software as a service (of SaaS) apps worden gehost in een openbare cloud, draait op gedeelde domeinnamen zoals [outlook.office.com](https://outlook.office.com/) en [login.microsoftonline.com](https://login.microsoftonline.com/). Als u deze adressen blokkeert, kunnen gebruikers niet volledig toegang krijgen tot de webversie van Outlook, in plaats van ze alleen te beperken tot goedgekeurde identiteiten en bronnen.

De Azure Active Directory-oplossing (Azure AD) voor deze uitdaging is een functie die tenantbeperkingen wordt genoemd. Met tenantbeperkingen kunnen organisaties de toegang tot SaaS-cloudtoepassingen beheren op basis van de Azure AD-tenant die de toepassingen gebruiken voor eenmalige aanmelding. U bijvoorbeeld toegang tot de Office 365-toepassingen van uw organisatie toestaan en tegelijkertijd de toegang tot de exemplaren van dezelfde toepassingen van andere organisaties verhinderen.  

Met tenantbeperkingen kunnen organisaties de lijst met tenants opgeven waartoe hun gebruikers toegang hebben. Azure AD verleent dan alleen toegang tot deze toegestane tenants.

Dit artikel richt zich op tenantbeperkingen voor Office 365, maar de functie moet werken met elke SaaS-cloud-app die moderne verificatieprotocollen met Azure AD gebruikt voor eenmalige aanmelding. Als u SaaS-apps gebruikt met een andere Azure AD-tenant dan de tenant die wordt gebruikt door Office 365, controleert u of alle vereiste tenants zijn toegestaan. Zie de [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/)voor meer informatie over SaaS-cloud-apps.

## <a name="how-it-works"></a>Hoe werkt het?

De algemene oplossing bestaat uit de volgende componenten:

1. **Azure AD**: `Restrict-Access-To-Tenants: <permitted tenant list>` Als het aanwezig is, geeft Azure AD alleen beveiligingstokens uit voor de toegestane tenants.

2. **On-premises proxyserverinfrastructuur:** deze infrastructuur is een proxy-apparaat dat geschikt is voor TLS-inspectie (Transport Layer Security). U moet de proxy configureren om de koptekst met de lijst met toegestane tenants in te voegen in verkeer dat is bestemd voor Azure AD.

3. **Clientsoftware:** Om tenantbeperkingen te ondersteunen, moet clientsoftware tokens rechtstreeks aanvragen bij Azure AD, zodat de proxy-infrastructuur verkeer kan onderscheppen. Browsergebaseerde Office 365-toepassingen ondersteunen momenteel tenantbeperkingen, evenals Office-clients die moderne verificatie gebruiken (zoals OAuth 2.0).

4. **Moderne verificatie:** cloudservices moeten moderne verificatie gebruiken om tenantbeperkingen te gebruiken en de toegang tot alle niet-toegestane tenants te blokkeren. U moet Office 365-cloudservices standaard configureren om moderne verificatieprotocollen te gebruiken. Lees [Updated Office 365 modern authentication](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)voor de meest recente informatie over Office 365-ondersteuning voor moderne verificatie.

Het volgende diagram illustreert de verkeersstroom op hoog niveau. Tenantbeperkingen vereisen TLS-inspectie alleen bij verkeer naar Azure AD, niet naar de Office 365-cloudservices. Dit onderscheid is belangrijk, omdat het verkeersvolume voor verificatie naar Azure AD doorgaans veel lager is dan het verkeersvolume naar SaaS-toepassingen zoals Exchange Online en SharePoint Online.

![Huurder beperkingen verkeersstroom - diagram](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Tenantbeperkingen instellen

Er zijn twee stappen om aan de slag te gaan met tenantbeperkingen. Zorg er eerst voor dat uw klanten verbinding kunnen maken met de juiste adressen. Ten tweede configureert u uw proxy-infrastructuur.

### <a name="urls-and-ip-addresses"></a>URL's en IP-adressen

Als u tenantbeperkingen wilt gebruiken, moeten uw clients verbinding kunnen maken met de volgende Azure AD-URL's om te verifiëren: [login.microsoftonline.com,](https://login.microsoftonline.com/) [login.microsoft.com](https://login.microsoft.com/)en [login.windows.net](https://login.windows.net/). Bovendien moeten uw clients, om toegang te krijgen tot Office 365, ook verbinding kunnen maken met de volledig gekwalificeerde domeinnamen (FQDN's), URL's en IP-adressen die zijn gedefinieerd in [Office 365-URL's en IP-adresbereiken.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) 

### <a name="proxy-configuration-and-requirements"></a>Proxyconfiguratie en vereisten

De volgende configuratie is vereist om tenantbeperkingen in te schakelen via uw proxy-infrastructuur. Deze richtlijnen zijn algemeen, dus u moet verwijzen naar de documentatie van uw proxyleverancier voor specifieke implementatiestappen.

#### <a name="prerequisites"></a>Vereisten

- De proxy moet TLS-interceptie, HTTP-header-invoeging en filterbestemmingen kunnen uitvoeren met FQDN/URL's.

- Clients moeten vertrouwen op de certificaatketen die door de proxy wordt gepresenteerd voor TLS-communicatie. Als bijvoorbeeld certificaten van een interne [openbare sleutelinfrastructuur (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) worden gebruikt, moet het certificaat van de interne instantie van de instantie van de basiscertificaat worden vertrouwd.

- Deze functie is opgenomen in Office 365-abonnementen, maar als u tenantbeperkingen wilt gebruiken om de toegang tot andere SaaS-apps te beheren, zijn Azure AD Premium 1-licenties vereist.

#### <a name="configuration"></a>Configuratie

Voeg voor elke binnenkomende aanvraag om login.microsoftonline.com, login.microsoft.com en login.windows.net twee HTTP-kopteksten in te voegen: *Beperken tot tenants en* Beperkte *toegang-context*.

De kopteksten moeten de volgende elementen bevatten:

- Voor *Beperken tot toegang tot tenants*, \<gebruik een\>waarde van de toegestane tenantlijst , een door komma's gescheiden lijst met tenants die gebruikers toegang wilt geven. Elk domein dat is geregistreerd bij een tenant kan worden gebruikt om de tenant in deze lijst te identificeren. Om bijvoorbeeld toegang te verlenen aan zowel Contoso- als Fabrikam-huurders, ziet het naam/waardepaar eruit als volgt: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- Gebruik *voor Restrict-Access-Context*een waarde van één map-id, waarbij u aangeeft welke tenant de tenantbeperkingen instelt. Als u Contoso bijvoorbeeld wilt declareren als de tenant die het beleid voor tenantbeperkingen instelt, ziet het naam-/waardepaar eruit als volgt: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> U uw directory-id vinden in de [Azure Active Directory-portal.](https://aad.portal.azure.com/) Meld u aan als beheerder, selecteert **Azure Active Directory**en selecteer **Eigenschappen**.

Om te voorkomen dat gebruikers hun eigen HTTP-header invoegen bij niet-goedgekeurde tenants, moet de proxy de header *Restrict-Access-To-Tenants* vervangen als deze al aanwezig is in de binnenkomende aanvraag.

Clients moeten worden gedwongen om de proxy te gebruiken voor alle verzoeken om te login.microsoftonline.com, login.microsoft.com en login.windows.net. Als PAC-bestanden bijvoorbeeld worden gebruikt om clients te verwijzen naar het gebruik van de proxy, moeten eindgebruikers de PAC-bestanden niet kunnen bewerken of uitschakelen.

## <a name="the-user-experience"></a>De gebruikerservaring

In deze sectie wordt de ervaring beschreven voor zowel eindgebruikers als beheerders.

### <a name="end-user-experience"></a>De ervaring voor de eindgebruiker

Een voorbeeldgebruiker bevindt zich in het Contoso-netwerk, maar probeert toegang te krijgen tot het Fabrikam-exemplaar van een gedeelde SaaS-toepassing zoals Outlook online. Als Fabrikam een niet-toegestane tenant is voor het Contoso-exemplaar, ziet de gebruiker een toegangsweigeringsbericht waarin staat dat u toegang probeert te krijgen tot een bron die behoort tot een organisatie die niet is goedgekeurd door uw IT-afdeling.

### <a name="admin-experience"></a>Beheerervaring

Hoewel de configuratie van tenantbeperkingen wordt uitgevoerd op de bedrijfsproxy-infrastructuur, hebben beheerders rechtstreeks toegang tot de rapporten over tenantbeperkingen in de Azure-portal. Ga als het gaat om het bekijken van de rapporten:

1. Meld u aan bij de [Azure Active Directory-portal](https://aad.portal.azure.com/). Het dashboard **van het Azure Active Directory-beheercentrum** wordt weergegeven.

2. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster. De overzichtspagina van Azure Active Directory wordt weergegeven.

3. Selecteer **Tenantbeperkingen**in de kop **Andere mogelijkheden** .

De beheerder voor de tenant die is opgegeven als de tenant Restricted-Access-Context kan dit rapport gebruiken om aanmeldingen geblokkeerd te zien vanwege het beleid voor tenantbeperkingen, inclusief de gebruikte identiteit en de doelmap-ID. Aanmeldingen zijn inbegrepen als de tenant die de beperking instelt de gebruikerstenant of resourcetenant voor de aanmelding is.

> [!NOTE]
> Het rapport kan beperkte informatie bevatten, zoals doelmap-id, wanneer een gebruiker die zich in een andere tenant bevindt dan de tenant Restricted-Access-Context zich aanmeldt. In dit geval wordt gebruikersidentificeerbare informatie, zoals naam en gebruikersnaam, gemaskeerd om gebruikersgegevens in andere tenants te beschermen.

Net als andere rapporten in de Azure-portal u filters gebruiken om het bereik van uw rapport op te geven. U filteren op een specifiek tijdsinterval, gebruiker, toepassing, client of status. Als u de knop **Kolommen** selecteert, u ervoor kiezen om gegevens weer te geven met een combinatie van de volgende velden:

- **Gebruiker**
- **Toepassing**
- **Status**
- **Datum**
- **Datum (UTC)** (waar UTC is Gecoördineerd Universele Tijd)
- **MFA Auth-methode** (methode voor meervoudige verificatie)
- **MFA Auth-detail** (detail van multifactorauthenticatie)
- **MFA-resultaat**
- **IP-adres**
- **Client**
- **Gebruikersnaam**
- **Locatie**
- **Doeltenant-id**

## <a name="office-365-support"></a>Ondersteuning voor Office 365

Office 365-toepassingen moeten aan twee criteria voldoen om tenantbeperkingen volledig te ondersteunen:

1. De gebruikte client ondersteunt moderne authenticatie.
2. Moderne verificatie is ingeschakeld als het standaardverificatieprotocol voor de cloudservice.

Raadpleeg [de moderne verificatie van Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) voor de meest recente informatie over welke Office-clients momenteel moderne verificatie ondersteunen. Die pagina bevat ook koppelingen naar instructies voor het inschakelen van moderne verificatie op specifieke Exchange Online- en Skype voor Bedrijven Online-tenants. SharePoint Online maakt moderne verificatie al standaard mogelijk.

Office 365-browsertoepassingen (office portal, Yammer, SharePoint-sites, Outlook on the Web en meer) ondersteunen momenteel tenantbeperkingen. Dikke clients (Outlook, Skype voor Bedrijven, Word, Excel, PowerPoint en meer) kunnen alleen tenantbeperkingen afdwingen wanneer u moderne verificatie gebruikt.  

Outlook- en Skype voor Bedrijven-clients die moderne verificatie ondersteunen, kunnen mogelijk nog steeds oudere protocollen gebruiken tegen tenants waar moderne verificatie niet is ingeschakeld, waardoor tenantbeperkingen effectief worden omzeild. Tenantbeperkingen kunnen toepassingen blokkeren die verouderde protocollen gebruiken als ze contact opnemen met login.microsoftonline.com, login.microsoft.com of login.windows.net tijdens verificatie.

Voor Outlook op Windows kunnen klanten ervoor kiezen beperkingen te implementeren die voorkomen dat eindgebruikers niet-goedgekeurde e-mailaccounts aan hun profielen toevoegen. Zie bijvoorbeeld de groepsbeleidsinstelling Voor [het toevoegen van niet-standaard Exchange-accounts.](https://gpsearch.azurewebsites.net/default.aspx?ref=1)

## <a name="testing"></a>Testen

Als u tenantbeperkingen wilt uitproberen voordat u deze voor uw hele organisatie implementeert, hebt u twee opties: een hostbenadering met behulp van een tool zoals Fiddler of een gefaseerde implementatie van proxy-instellingen.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler voor een host-based aanpak

Fiddler is een gratis webdebugging proxy die kan worden gebruikt om HTTP/HTTPS-verkeer vast te leggen en te wijzigen, inclusief het invoegen van HTTP-headers. Voer de volgende stappen uit om Fiddler te configureren om tenantbeperkingen te testen:

1. [Fiddler downloaden en installeren.](https://www.telerik.com/fiddler)

2. Configureer Fiddler om HTTPS-verkeer te decoderen, volgens [de help-documentatie van Fiddler.](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)

3. Configureer Fiddler om de koppen *Beperken tot tenants en* Toegang tot toegang tot *context* in te voegen met aangepaste regels:

   1. Selecteer in het gereedschap Fiddler Web debugger het menu **Regels** en selecteer **Regels aanpassen...** om het bestand CustomRules te openen.

   2. Voeg de volgende regels toe `OnBeforeRequest` aan het begin van de functie. Vervang \<tenantdomein\> door een domein dat is `contoso.onmicrosoft.com`geregistreerd bij uw tenant (bijvoorbeeld). Vervang \<directory-id\> door de Azure AD GUID-id van uw tenant.

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

      Als u meerdere tenants moet toestaan, gebruikt u een komma om de tenantnamen te scheiden. Bijvoorbeeld:

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Sla het bestand CustomRules op en sluit deze.

Nadat u Fiddler hebt geconfigureerd, u verkeer vastleggen door naar het menu **Bestand** te gaan en **Verkeer vastleggen te**selecteren.

### <a name="staged-rollout-of-proxy-settings"></a>Gefaseerde implementatie van proxy-instellingen

Afhankelijk van de mogelijkheden van uw proxy-infrastructuur u mogelijk de implementatie van instellingen naar uw gebruikers faseren. Hier zijn een paar opties op hoog niveau voor overweging:

1. Gebruik PAC-bestanden om testgebruikers naar een testproxy-infrastructuur te wijzen, terwijl normale gebruikers de infrastructuur voor productieproxy blijven gebruiken.
2. Sommige proxyservers ondersteunen mogelijk verschillende configuraties met behulp van groepen.

Voor specifieke details verwijzen wij u naar de documentatie van uw proxyserver.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [bijgewerkte moderne verificatie van Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- De [URL's en IP-adresbereiken van Office 365 controleren](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
