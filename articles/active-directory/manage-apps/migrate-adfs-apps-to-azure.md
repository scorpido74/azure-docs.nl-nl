---
title: Apps van AD FS verplaatsen naar Azure AD. | Microsoft Docs
description: In dit artikel is bedoeld ter ondersteuning van organisaties bij het verplaatsen van toepassingen naar Azure AD, met een focus op federatieve SaaS-toepassingen.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/02/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3c3eb715c3e371d7e2985f233df584fb83a9870
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063452"
---
# <a name="move-applications-from-ad-fs-to-azure-ad"></a>Toepassingen van AD FS verplaatsen naar Azure AD 

Dit artikel helpt u begrijpen hoe u toepassingen verplaatsen van AD FS met Azure Active Directory (Azure AD). De focus ligt hierbij op federatieve SaaS-toepassingen.

In dit document wordt geen stapsgewijze begeleiding gegeven. Het biedt wel theoretische richtlijnen voor de migratie door de vertaling van on-premises configuraties naar Azure AD inzichtelijk te maken. Er worden ook enkele veelvoorkomende scenario's behandeld.

## <a name="introduction"></a>Inleiding

Als u een on-premises map met gebruikersaccounts hebt, dan is de kans groot dat u minimaal een of twee apps hebt. En deze apps worden geconfigureerd voor gebruikers zodat ze ze kunnen openen. Dat doen ze door zich aan te melden met die identiteiten.

En net als vele andere organisatie bent u waarschijnlijk al bezig over te stappen op cloudtoepassingen en -identiteiten. Mogelijk maakt u al gebruik van Office 365 en Azure AD Connect. Misschien hebt u SaaS-cloudtoepassingen ingesteld voor enkele belangrijke werkbelastingen, maar nog niet voor alle.  

Veel organisaties beschikken over SaaS- of line-of-business (LOB)-apps die rechtstreeks zijn gefedereerd met een on-premises aanmeldingsservice, zoals Active Directory Federation Services (AD FS), in combinatie met Office 365- en Azure AD-apps. Deze handleiding wordt beschreven waarom en hoe u uw toepassingen naar Azure AD.

> [!NOTE]
> De handleiding biedt uitgebreide informatie over de configuratie en migratie van SaaS-apps, met belangrijke informatie over aangepaste LOB-apps. Meer gedetailleerde richtlijnen voor LOB-apps staan voor later gepland.

![Toepassingen die rechtstreeks on-premises zijn verbonden](media/migrate-adfs-apps-to-azure/migrate1.png)

![Toepassingen die worden federatief via Azure AD](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="reasons-for-moving-apps-to-azure-ad"></a>Redenen voor het verplaatsen van apps naar Azure AD

Voor een organisatie die al gebruikmaakt van AD FS, Ping of een andere on-premises verificatieprovider, kunt apps verplaatsen naar Azure AD de volgende voordelen:

- **Veiliger toegang**

  - Gedetailleerde besturings elementen voor toegang per toepassing, met inbegrip van Azure Multi-Factor Authentication, met behulp van [voorwaardelijke toegang van Azure AD](../active-directory-conditional-access-azure-portal.md)configureren. Het beleid kan worden toegepast op SaaS- en aangepaste apps op dezelfde manier als u voor Office 365 zou doen.
  - Voor de detectie van bedreigingen en het helpen beveiligen van de aanmelding op basis van machine learning en heuristieken waarmee risicovol verkeer wordt geïdentificeerd, kunt u profiteren van [Azure AD Identity Protection](../active-directory-identityprotection.md).

- **B2B-samenwerking van Azure AD**

  Nadat het aanmelden bij SaaS-apps op Azure AD is gebaseerd, kunt u uw partners toegang geven aan cloudresources met [B2B-samenwerking van Azure AD](../b2b/what-is-b2b.md).

- **Eenvoudiger beheer met en aanvullende mogelijkheden van Azure AD**
  
  Als id-provider voor SaaS-apps ondersteunt Azure AD aanvullende mogelijkheden zoals:
  - Certificaten voor tokenondertekening per toepassing.
  - [Configureerbare certificaatvervaldatums](manage-certificates-for-federated-single-sign-on.md).
  - [Automatisch inrichten](../app-provisioning/user-provisioning.md) van gebruikersaccounts (in de belangrijkste Azure Marketplace-apps) op basis van Azure AD-identiteiten.

- **De voordelen van een on-premises id-provider behouden**
  
  Terwijl u de voordelen van Azure AD krijgt, kunt u uw on-premises-oplossing voor verificatie blijven gebruiken. Op die manier blijven voordelen zoals on-premises oplossingen voor meervoudige verificatie, logging en controle intact.

- **Hulp bij buiten gebruik stellen van de on-premises id-provider**
  
  Voor organisaties die gebruik wilt stellen van de on-premises verificatie-product, kan apps verplaatsen naar Azure AD een eenvoudigere overgang door deel van het werk uit de weg.

## <a name="mapping-types-of-apps-on-premises-to-types-of-apps-in-azure-ad"></a>App-typen on-premises toewijzen aan app-typen in Azure AD

De meeste apps kunnen in enkele categorieën worden ondergebracht op basis van het type aanmelding dat ze gebruiken. Deze categorieën bepalen hoe de app in Azure AD wordt gerepresenteerd.

Kortweg geldt dat SAML 2.0-toepassingen kunnen worden geïntegreerd met Azure AD via de Azure AD-toepassingsgalerie in de Marketplace of als niet-Marketplace-toepassingen. Apps die gebruikmaken van OAuth 2.0 of OpenID Connect, kunnen op gelijksoortige wijze worden geïntegreerd met Azure AD als *app-registraties*. Lees verder voor meer informatie.

### <a name="federated-saas-apps-vs-custom-lob-apps"></a>Federatieve SaaS-apps versus aangepaste LOB-apps

Federatieve apps omvatten apps in de volgende categorieën:

- SaaS-apps 
    - Als uw gebruikers zich aanmelden bij SaaS-apps als Salesforce, ServiceNow of Workday en u een integratie uitvoert met een on-premises id-provider als AD FS of Ping, maakt u gebruik van federatieve aanmelding voor SaaS-apps.
    - Over het algemeen maken apps gebruik van het SAML 2.0-protocol voor federatieve aanmelding.
    - Apps in deze categorie kunnen worden geïntegreerd met Azure AD als bedrijfstoepassingen, vanuit de Marketplace of als niet-Marketplace-toepassingen.
- Aangepaste LOB-apps
    - Dit verwijst naar niet-SaaS-apps, die intern zijn ontwikkeld door uw organisatie of beschikbaar zijn als een standaard verpakt product dat is geïnstalleerd in uw datacentrum. Dit omvat SharePoint-apps en apps die zijn gebouwd onder Windows Identity Foundation.
    - Apps kunnen gebruikmaken van SAML 2.0, WS-Federation, OAuth of OpenID Connect voor federatieve aanmelding.
    - Aangepaste apps die gebruikmaken van OAuth 2.0, OpenID Connect of WS-Federation, kunnen met Azure AD worden geïntegreerd als app-registraties. Aangepaste apps die gebruikmaken van SAML 2.0 of WS-Federation kunnen worden geïntegreerd als niet-Marketplace toepassingen binnen bedrijfstoepassingen.

### <a name="non-federated-apps"></a>Niet-federatieve apps

U kunt niet-federatieve apps integreren met Azure AD met behulp van de Azure Active Directory-toepassingsproxy en aanverwante mogelijkheden. Niet-federatieve apps zijn:

- Apps die rechtstreeks gebruikmaken van geïntegreerde Windows-verificatie bij Active Directory. U kunt deze apps integreren met Azure AD via [Azure Active Directory-toepassingsproxy](application-proxy-add-on-premises-application.md).
- Apps die via een agent worden geïntegreerd met uw provider voor eenmalige aanmelding en die headers gebruiken voor verificatie. On-premises apps die bij aanmelden en autorisatie op basis van headers gebruikmaken van een geïnstalleerde agent, kunnen worden geconfigureerd voor Azure AD-aanmelding via de toepassingsproxy van Azure AD met [Ping-toegang voor Azure AD](https://blogs.technet.microsoft.com/enterprisemobility/2017/06/15/ping-access-for-azure-ad-is-now-generally-available-ga/).

## <a name="translating-on-premises-federated-apps-to-azure-ad"></a>Federatieve, on-premises apps vertalen naar Azure AD

AD FS en Azure AD werken op soortgelijke wijze, dus de concepten betreffende het configureren van vertrouwensrelaties, URL's voor aan- en afmelden, en id's zijn op beide producten van toepassing. Er zijn echter wel een paar kleine verschillen die u moet kennen wanneer u de overgang maakt.

In de volgende tabellen worden belangrijke ideeën in kaart gebracht die gelden voor zowel AD FS-, Azure AD- als SaaS-apps om u te helpen de vertaalslag te maken.

### <a name="representing-the-app-in-azure-ad-or-ad-fs"></a>De app representeren in Azure AD of AD FS

Voor de migratie begint, moet eerst worden vastgesteld hoe de app on-premises moet worden geconfigureerd en hoe de configuratie aan Azure AD wordt toegewezen. In de volgende tabel ziet u configuratie-elementen van de Relying Party van AD FS en de overeenkomstige elementen in Azure AD.

- AD FS-term: Relying Party of vertrouwensrelatie van Relying Party.
- Azure AD-term: bedrijfstoepassing of app-registratie (afhankelijk van het type app).

|Configuratie-element van app|Beschrijving|Locatie in AD FS-configuratie|Overeenkomstige locatie in Azure AD-configuratie|SAML-tokenelement|
|-----|-----|-----|-----|-----|
|Aanmeldings-URL van app|URL van de aanmeldingspagina van deze toepassing. Hier gaat de gebruiker naartoe om zich aan te melden bij de app in een door SP geïnitieerde SAML-stroom.|N.v.t.|In Azure AD wordt binnen Azure Portal de aanmeldings-URL als de aanmeldings-URL geconfigureerd in de eigenschappen voor **eenmalige aanmelding** van de toepassing.</br></br>(Mogelijk moet u **Geavanceerde URL-instellingen weergeven** selecteren om de aanmeldings-URL te zien.)|N.v.t.|
|Antwoord-URL van app|URL van de app vanuit het perspectief van de id-provider (IdP). Hier worden de gebruiker en het token naartoe gestuurd nadat de gebruiker zich bij de IdP heeft aangemeld.</br></br> Dit wordt ook wel het ‘consumenteneindpunt van de SAML-bewerking’ genoemd.|Te vinden in de vertrouwensrelatie van de Relying Party van AD FS voor de app. Klik met de rechtermuisknop op de Relying Party, selecteer **Eigenschappen** en selecteer het tabblad **Eindpunten**.|In Azure AD wordt de antwoord-URL binnen de Azure-portal geconfigureerd in de eigenschappen voor **eenmalige aanmelding** van de toepassing.</br></br>(Mogelijk moet u **Geavanceerde URL-instellingen weergeven** selecteren om de antwoord-URL te zien.)|Komt overeen met het **Doel**-element in het SAML-token.</br></br> Voorbeeldwaarde: `https://contoso.my.salesforce.com`|
|App-URL voor afmelden|URL waarnaar aanvragen voor 'sign-out cleanup' worden verzonden als een gebruiker zich bij een app afmeldt, waarna wordt afgemeld bij alle overige apps waar de IdP de gebruiker heeft aangemeld.|Te vinden in AD FS-beheer onder **Vertrouwensrelaties van Relying Party**. Klik met de rechtermuisknop op de Relying Party, selecteer **Eigenschappen** en selecteer het tabblad **Eindpunten**.|N.v.t. Azure AD biedt geen ondersteuning voor ‘eenmalig afmelden’, dat wil zeggen afmelden bij alle apps. De gebruiker wordt gewoon afgemeld bij Azure AD zelf.|N.v.t.|
|App-id|Id van de app vanuit het perspectief van de IdP. De waarde van de aanmeldings-URL wordt vaak (maar niet altijd) gebruikt als de id.</br></br> In de app wordt deze id soms de ‘entiteits-id’ genoemd.|In AD FS is dit de Relying Party-id. Klik met de rechtermuisknop op de vertrouwensrelatie van de Relying Party, selecteer **Eigenschappen** en selecteer het tabblad **Id’s**.|In Azure AD wordt de id in Azure Portal in de eigenschappen voor **eenmalige aanmelding** van de toepassing als id onder **Domein en URL's** geconfigureerd. (Mogelijk moet u het selectievakje **Geavanceerde URL-instellingen weergeven** inschakelen.)|Komt overeen met het element **Doelgroep** in het SAML-token.|
|Federatieve metagegevens van app|Locatie van de federatieve metagegevens van de app. De IdP gebruikt deze om bepaalde configuratie-instellingen automatisch bij te werken, zoals eindpunten of versleutelingscertificaten.|De URL voor de federatieve metagegevens van de app is te vinden in de vertrouwensrelatie van de Relying Party van AD FS voor de app. Klik met de rechtermuisknop op de vertrouwensrelatie, selecteer **Eigenschappen** en selecteer het tabblad **Controle**.|N.v.t. Azure biedt geen rechtstreekse ondersteuning voor het gebruik van federatieve metagegevens van toepassingen.|N.v.t.|
|Gebruikers-id/**NameID**|Kenmerk dat wordt gebruikt om voor de app op unieke wijze de gebruikers-id aan te geven vanuit Azure AD of AD FS.</br></br> Dit kenmerk is gewoonlijk de UPN of het e-mailadres van de gebruiker.|In AD FS kunt u dit vinden als een claimregel op de Relying Party. In de meeste gevallen geeft de claimregel een claim uit met een type dat eindigt op 'nameidentifier'.|In Azure AD is de gebruikers-id te vinden in Azure Portal in de eigenschappen voor **eenmalige aanmelding** van de toepassing, onder de kop **Gebruikerskenmerken**.</br></br>Standaard wordt de user principal name gebruikt.|Wordt van de IdP naar de app gecommuniceerd als het **NameID**-element in het SAML-token.|
|Andere claims die naar de app worden verzonden|Naast de gebruikers-id/**NameID** worden er vaak andere claimgegevens van de IdP naar de app verzonden. Voorbeelden zijn voornaam, achternaam, e-mailadres en groepen waarvan de gebruiker lid is.|In AD FS kunt u deze gegevens vinden als andere claimregels op de Relying Party.|In Azure AD zijn deze gegevens te vinden in Azure Portal in de eigenschappen voor **eenmalige aanmelding** van de toepassing, onder de kop **Gebruikerskenmerken**. Selecteer **Beeld** en bewerk alle andere gebruikerskenmerken.|N.v.t.|

### <a name="representing-azure-ad-as-an-identity-provider-in-an-saas-app"></a>Azure AD representeren als een id-provider in een SaaS-app
Als onderdeel van de migratie moet u de app zo configureren dat deze wijst naar Azure AD (versus de on-premises id-provider). Deze sectie richt zich op SaaS-apps die het SAML-protocol gebruiken, en niet op aangepaste LOB-apps. De concepten gelden echter ook voor aangepaste LOB-apps.

Op hoog niveau zijn er enkele belangrijke dingen waarmee een SaaS-app naar Azure AD wijst:

- URL van id-provider: https&#58;//sts.windows.net/{tenant-id}/
- Aanmeldings-URL van de id-provider: https&#58;//login.microsoftonline.com/{tenant-id}/saml2
- Afmeldings-URL van de id-provider: https&#58;//login.microsoftonline.com/{tenant-id}/saml2 
- Locatie van bestand met federatieve metagegevens: https&#58;//login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id} 

Vervang {Tenant-id} door uw tenant-id, te vinden in de Azure-portal onder **Azure Active Directory** > **Eigenschappen** als **Map-ID**. Vervang {application-id} door uw toepassings-id, te vinden in de eigenschappen van de toepassing als **Toepassings-id**.

De volgende tabel beschrijft de belangrijkste IdP-configuratie-elementen voor het configureren van SSO-instellingen in de app en hun waarden of locaties binnen AD FS en Azure AD. Het referentiekader van de tabel is de SaaS-app, die moet weten waarnaar verificatieaanvragen moeten worden verzonden en hoe de ontvangen tokens moeten worden gevalideerd.

|Configuratie-element|Beschrijving|AD FS|Azure AD|
|---|---|---|---|
|IdP </br>aanmelden </br>URL|Aanmeldings-URL van de IdP vanuit het perspectief van de app (waarnaar de gebruiker wordt omgeleid voor aanmelding).|De AD FS-aanmeldings-URL is de AD FS-federation-servicenaam gevolgd door ‘/adfs/ls/’. Bijvoorbeeld: https&#58;//fs.contoso.com/adfs/ls/|De overeenkomstige waarde voor Azure AD volgt het patroon waarbij {tenant-id} wordt vervangen door uw tenant-id. Deze waarde is te vinden in de Azure-portal onder **Azure Active Directory** > **Eigenschappen** als **Map-ID**.</br></br>Voor apps die gebruikmaken van het SAML-P-protocol: https&#58;//login.microsoftonline.com/{tenant-id}/saml2 </br></br>Voor apps die gebruikmaken van het WS-Federation-protocol: https&#58;//login.microsoftonline.com/{tenant-id}/wsfed|
|IdP </br>afmelden </br>URL|URL voor afmelden van de IdP vanuit het perspectief van de app (waarnaar de gebruiker wordt omgeleid wanneer deze zich afmeldt bij de app).|Voor AD FS is URL voor afmelden gelijk aan de aanmeldings-URL, of dezelfde URL met het achtervoegsel "wa=wsignout1.0". Bijvoorbeeld: https&#58;//fs.contoso.com/adfs/ls/?wa=wsignout1.0|De overeenkomstige waarde voor Azure AD hangt ervan af of de app SAML 2.0-afmelding ondersteunt.</br></br>Als de app SAML-afmelding ondersteunt, heeft de waarde het patroon waarbij de waarde voor {tenant-id} word vervangen door de tenant-id. Deze is te vinden in de Azure-portal onder **Azure Active Directory** > **Eigenschappen** als **Map-ID**: https&#58;//login.microsoftonline.com/{tenant-id}/saml2</br></br>Als de app geen ondersteuning biedt voor SAML afmelding: https&#58;//login.microsoftonline.com/common/wsfederation?wa=wsignout1.0|
|Token </br>ondertekening </br>certificaat|Het certificaat waarvan de IdP de persoonlijke sleutel gebruikt voor het ondertekenen van uitgegeven tokens. Er wordt gecontroleerd of het token afkomstig is van de dezelfde IdP die de app is geconfigureerd om te vertrouwen.|U vindt het AD FS-certificaat voor token-ondertekening in AD FS-beheer onder **Certificaten**.|In Azure AD kunt u het certificaat voor token-ondertekening vinden in de Azure-portal in de eigenschappen voor **eenmalige aanmelding** onder de kop **SAML-handtekeningcertificaat**. Daar kunt u het certificaat downloaden om het te uploaden naar de app.</br></br> Als de toepassing meer dan één certificaat heeft, kunt u alle certificaten vinden in het metagegevens-XML-bestand van de federatie.|
|Id/</br>'verlener'|Id van de IdP vanuit het perspectief van de app (ook wel 'uitgever-ID' genoemd).</br></br>In het SAML-token wordt de waarde weergegeven als het element **Verlener**.|De id voor AD FS is meestal de federatieservice-id in AD FS-beheer onder **Service** > **Eigenschappen van de Federation Service bewerken**. Bijvoorbeeld: http&#58;//fs.contoso.com/adfs/services/trust|De overeenkomstige waarde voor Azure AD volgt het patroon waarbij de waarde voor {tenant-id} wordt vervangen door de tenant-id. Te vinden in de Azure-portal onder **Azure Active Directory** > **Eigenschappen** als **Map-ID**: https&#58;//sts.windows.net/{tenant-id}/|
|IdP </br>federatie- </br>metagegevens|Locatie van de openbaar beschikbare federatiemetagegevens van de IdP. (Sommige apps gebruiken federatiemetagegevens als alternatief voor het afzonderlijk door de beheerder configureren van URL's, id en tokenhandtekeningcertificaat.)|U vindt de URL van de AD FS-federatiemetagegevens in AD FS-beheer onder **Service** > **Eindpunten** > **Metagegevens** >  **Type: Federatiemetagegevens**. Bijvoorbeeld: https&#58;//fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml|De overeenkomstige waarde voor Azure AD volgt het patroon https&#58;//login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml. De waarde voor {TenantDomainName} wordt vervangen door de naam van uw tenant in de notatie "contoso.onmicrosoft.com". </br></br>Zie voor meer informatie [Federatiemetagegevens](../develop/azure-ad-federation-metadata.md).

## <a name="moving-saas-apps"></a>SaaS-apps verplaatsen

Vandaag is een handmatig proces verplaatsen SaaS-apps van AD FS of een andere id-provider naar Azure AD. Zie de [lijst met zelfstudies voor het integreren van SaaS-apps in de Marketplace](../saas-apps/tutorial-list.md) voor app-specifieke richtlijnen.

Bij de integratiezelfstudies wordt ervan uitgegaan dat u een geheel nieuwe integratie uitvoert. Bij het plannen, beoordelen, configureren en overzetten van uw apps moet u rekening houden met een paar belangrijke concepten die specifiek zijn voor migratie:  

- Sommige apps kunnen eenvoudig worden gemigreerd. Voor apps met complexere vereisten, zoals aangepaste claims, is mogelijk aanvullende configuratie in Azure AD en/of Azure AD Connect vereist.
- In de meest voorkomende scenario's zijn alleen de **NameID**-claim en andere algemene gebruikers-id-claims vereist voor een app. Om te bepalen of er aanvullende claims nodig zijn, onderzoekt u welke claims u uitgeeft vanuit AD FS of uw id-provider van derden.
- Nadat u hebt vastgesteld dat er extra claims vereist zijn, moet u ervoor zorgen dat ze beschikbaar zijn in Azure AD. Controleer de synchronisatieconfiguratie van Azure AD Connect om u ervan te verzekeren dat een vereist attribuut - bijvoorbeeld **samAccountName**- gesynchroniseerd wordt met Azure AD.
- Nadat de kenmerken beschikbaar zijn in Azure AD, kunt u claimuitgifteregels toevoegen in Azure AD om die kenmerken op te nemen als claims in uitgegeven tokens. U voegt deze regels toe in de eigenschappen voor **eenmalige aanmelding** van de app in Azure AD.

### <a name="assess-what-can-be-moved"></a>Beoordelen wat kan worden verplaatst

SAML 2.0-toepassingen kunnen worden geïntegreerd met Azure AD via de Azure AD-toepassingsgalerie in de Marketplace of als niet-Marketplace-toepassingen.  

Voor sommige configuraties zijn extra stappen vereist om te configureren in Azure AD, en sommige configuraties worden vandaag de dag niet ondersteund. Om te bepalen wat u kunt verplaatsen, bekijkt u de huidige configuratie van elk van uw apps. Kijk in het bijzonder naar:

- Geconfigureerde claimregels (uitgiftetransformatieregels).
- SAML **NameID**-indeling en kenmerk.
- Uitgegeven SAML-tokenversies.
- Andere configuraties, zoals regels voor uitgifteautorisatie of het beleid voor toegangsbeheer en regels voor meervoudige verificatie (aanvullende verificatie).

#### <a name="what-can-be-moved-today"></a>Wat vandaag nog kunnen worden verplaatst

Apps die u vandaag gemakkelijk kunt verplaatsen zijn SAML 2.0-apps die gebruikmaken van de standaardset van configuratie-elementen en claims. Deze apps kunnen bestaan uit:

- User principal name.
- E-mailadres.
- Voornaam.
- Achternaam.
- Alternatief kenmerk als SAML **NameID**, met inbegrip van het Azure AD e-mailkenmerk, e-mailvoorvoegsel, werknemer-id, extensiekenmerken 1-15, of on-premises **SamAccountName**-kenmerk. Zie voor meer informatie [De NameIdentifier-claim bewerken](../develop/active-directory-saml-claims-customization.md).
- Aangepaste claims. Zie voor informatie over het toewijzen van ondersteunde claims [Claims toewijzen in Azure Active Directory](../develop/active-directory-claims-mapping.md) en [Uitgegeven claims in het SAML-token voor bedrijfstoepassingen in Azure Active Directory aanpassen](../develop/active-directory-saml-claims-customization.md).

Naast aangepaste claims en **NameID**-elementen, zijn configuraties waarvoor extra configuratiestappen in Azure AD nodig zijn als onderdeel van de migratie:

- Aangepaste autorisatie-of Multi-Factor Authentication-regels in AD FS. U kunt deze configureren met behulp van de functie [voorwaardelijke toegang van Azure AD](../active-directory-conditional-access-azure-portal.md) .
- Apps met meerdere SAML-eindpunten. U configureert deze in Azure AD met behulp van PowerShell. (Deze mogelijkheid is niet beschikbaar in de portal.)
- WS-Federation-apps, zoals SharePoint-apps waarvoor versie 1.1-SAML-tokens vereist zijn. U moet deze handmatig configureren met behulp van PowerShell.

#### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Apps en configuratie die momenteel niet worden ondersteund in Azure AD

Apps waarvoor de volgende mogelijkheden zijn vereist, kunnen momenteel niet worden gemigreerd. Als u apps hebt waarvoor deze functies vereist zijn, geef dan a.u.b. feedback in de commentaarsectie.

- Protocolmogelijkheden:
    - Ondersteuning voor eenmalige SAML-afmelding (SLO) van alle apps waarbij de gebruiker is aangemeld.
    - Ondersteuning voor het WS-Trust ActAs-patroon.
    - SAML-artefacten omzetten.
    - Handtekeningverificatie van ondertekende SAML-aanvragen. Houd er rekening mee dat ondertekende aanvragen worden geaccepteerd, maar de handtekening niet wordt geverifieerd.
- Tokenmogelijkheden:
    - Versleuteling van SAML-tokens.
    - SAML-versie 1.1-tokens binnen SAML-protocolreacties.
- Claims in tokenmogelijkheden:
    - Uitgeven van on-premises groepsnamen als claims.
    - Claims van andere stores dan Azure AD.
    - Complexe transformatieregel voor claimuitgifte. Zie voor informatie over het toewijzen van ondersteunde claims [Claims toewijzen in Azure Active Directory](../develop/active-directory-claims-mapping.md) en [Uitgegeven claims in het SAML-token voor bedrijfstoepassingen in Azure Active Directory aanpassen](../develop/active-directory-saml-claims-customization.md).
    - Uitgifte van mapextensies als claims.
    - Aangepaste specificatie van de **NameID**-indeling.
    - Uitgifte van meerwaardige kenmerken.

> [!NOTE]
> Azure AD is voortdurend in ontwikkeling om mogelijkheden op dit gebied toe te voegen. Dit artikel wordt regelmatig bijgewerkt.

### <a name="configure-azure-ad"></a>Azure AD configureren

#### <a name="configure-single-sign-on-sso-settings-for-the-saas-app"></a>Instellingen voor eenmalige aanmelding (SSO) configureren voor de SaaS-app

In Azure AD configureert u eenmalige SAML-aanmelding (zoals vereist door uw app) binnen de eigenschappen voor **eenmalige aanmelding** van de app onder **Gebruikerskenmerken**.

![Eigenschappen voor eenmalige aanmelding met de sectie 'Gebruikerskenmerken'](media/migrate-adfs-apps-to-azure/migrate3.png)

Selecteer **Alle gebruikerskenmerken weergeven en bewerken** om de kenmerken te zien die als claims in het beveiligingstoken moeten worden verzonden.

![Hiermee wordt de lijst met kenmerken weer gegeven die u als claims kunt verzenden](media/migrate-adfs-apps-to-azure/migrate4.png)

Selecteer een bepaalde kenmerkrij om deze te bewerken, of selecteer **Kenmerk toevoegen** om een nieuw kenmerk toe te voegen.

![Hiermee wordt het deel venster kenmerk bewerken weer gegeven](media/migrate-adfs-apps-to-azure/migrate5.png)

#### <a name="assign-users-to-the-app"></a>Gebruikers aan de app toewijzen

Om ervoor te zorgen dat uw gebruikers in Azure AD zich kunnen aanmelden bij een SaaS-app, moet u ze toegang geven.

Als u gebruikers in de Azure AD-portal wilt toewijzen, gaat u naar het scherm van de SaaS-app en selecteert u **Gebruikers en groepen** in de zijbalk. Als u een gebruiker of groep wilt toevoegen, selecteert u bovenaan het deelvenster **Gebruiker toevoegen**.

![Knop 'Gebruiker toevoegen' in 'Gebruikers en groepen'](media/migrate-adfs-apps-to-azure/migrate6.png)

![Hiermee wordt het deel venster toewijzing toevoegen weer gegeven](media/migrate-adfs-apps-to-azure/migrate7.png)

Om hun toegang te verifiëren, moeten gebruikers de SaaS-app in hun [toegangsvenster](../user-help/active-directory-saas-access-panel-introduction.md) zien wanneer ze zich aanmelden. Zij kunnen het toegangsvenster vinden op https://myapps.microsoft.com. In dit voorbeeld is aan een gebruiker toegang verleend tot zowel Salesforce als ServiceNow.

![Voorbeeld van toegangspaneel met de apps Salesforce en ServiceNow](media/migrate-adfs-apps-to-azure/migrate8.png)

### <a name="configure-the-saas-app"></a>De Saas-app configureren

Het cutover-proces van on-premises federatie naar Azure AD hangt ervan af of de SaaS-app waarmee u werkt meerdere id-providers ondersteunt. Hier volgen enkele veelgestelde vragen over ondersteuning voor meerdere IdP’s:

   **V: Wat betekent het dat een app meerdere id-providers ondersteunt?**

   A: In SaaS-apps die ondersteuning bieden voor meerdere id-providers, kunt u alle gegevens invoeren over de nieuwe id-provider (in dit geval Azure AD) voordat u de nieuwe aanmeldingsmethode daadwerkelijk wijzigt. Nadat de configuratie gereed is, kunt u de authenticatieconfiguratie van de app naar Azure AD laten wijzen.

   **V: Waarom maakt het uit of de SaaS-app meerder IdP’s ondersteunt?**

   A: Als er geen ondersteuning is voor meerdere id-providers, moet de beheerder een korte service- of onderhoudsperiode reserveren waarin Azure AD wordt geconfigureerd als de nieuwe IdP van de app. Gebruikers moeten ervan op de hoogte worden gesteld dat ze zich gedurende deze onderbreking niet bij hun account kunnen aanmelden.

   Als een app ondersteuning biedt voor meerdere IdP” s, kan de extra IdP vooraf worden geconfigureerd. De beheerder kan vervolgens de IdP verwisselen bij de cutover naar Azure.

   Als de app meerdere id-providers ondersteunt en u ervoor kiest meerdere IdP’s tegelijkertijd verificatie bij aanmelden te laten afhandelen, kan de gebruiker op de aanmeldingspagina een IdP kiezen voor verificatie.

#### <a name="example-support-for-multiple-identity-providers"></a>Voor beeld: ondersteuning voor meerdere id-providers

In Salesforce kunt u de IdP-configuratie bijvoorbeeld vinden onder **Instellingen** > **Bedrijfsinstellingen** > **Mijn domein** > **Verificatieconfiguratie**.

![De sectie 'Verificatieconfiguratie' in de Salesforce-app](media/migrate-adfs-apps-to-azure/migrate9.png)

Vanwege de configuratie die eerder is gemaakt onder **Identiteit** > **Instellingen voor eenmalige aanmelding**, moet u uw IdP voor vertificatieconfiguratie kunnen wijzigen. U kunt deze bijvoorbeeld wijzigen van AD FS in Azure AD.

![Azure AD selecteren als verificatieservice](media/migrate-adfs-apps-to-azure/migrate10.png)

### <a name="optional-configure-user-provisioning-in-azure-ad"></a>Optioneel: inrichten van gebruikers configureren in Azure AD

Als u wilt dat Azure AD de inrichting van gebruikers voor een SaaS-app direct afhandelt, raadpleegt u [ Inrichting en ongedaan maken van inrichting voor gebruikers voor SaaS-toepassingen automatiseren met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="next-steps"></a>Volgende stappen

- [Toepassingen beheren met Azure Active Directory](what-is-application-management.md)
- [Toegang tot apps beheren](what-is-access-management.md)
- [Azure AD Connect-federatie](../hybrid/how-to-connect-fed-whatis.md)
