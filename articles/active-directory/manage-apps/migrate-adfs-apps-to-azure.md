---
title: Toepassingsverificatie verplaatsen van AD FS naar Azure Active Directory
description: Dit artikel is bedoeld om organisaties te helpen begrijpen hoe ze toepassingen naar Azure AD kunnen verplaatsen, met een focus op gefedereerde SaaS-toepassingen.
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/01/2020
ms.author: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcb39606cdbb6488ccdee2828029d3617d689508
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891935"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Toepassingsverificatie verplaatsen van Active Directory Federation Services naar Azure Active Directory

[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) biedt een universeel identiteitsplatform dat uw mensen, partners en klanten één identiteit biedt om toegang te krijgen tot toepassingen en samen te werken vanaf elk platform en apparaat. Azure AD heeft een [volledige suite van mogelijkheden voor identiteitsbeheer.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) Het standaardiseren van uw toepassingsverificatie (app)authenticatie en autorisatie naar Azure AD maakt de voordelen mogelijk die deze mogelijkheden bieden. 

> [!NOTE]
> Dit artikel richt zich op het verplaatsen van toepassingsverificatie van on-premises Active Directory en Active Directory Federation Services naar Azure AD. Raadpleeg de verificatie van de whitepaper [Voor het migreren van toepassingsverificatie naar Azure AD](https://aka.ms/migrateapps/whitepaper) voor een overzicht van het plannen van deze verplaatsing. De whitepaper bespreekt hoe u de migratie, testen en inzichten plannen.

## <a name="introduction"></a>Inleiding

Als u een on-premises directory hebt die gebruikersaccounts bevat, hebt u waarschijnlijk veel toepassingen waarvoor gebruikers zich verifiëren. Elk van deze apps is geconfigureerd voor gebruikers om toegang te krijgen met behulp van hun identiteit. 


Gebruikers kunnen zich ook rechtstreeks verifiëren met uw on-premises Active Directory. Active Directory Federation Services (AD FS) is een standaardgebaseerde identiteitsservice op basis van lokalen. AD FS breidt de mogelijkheid uit om eenmalige aanmeldingsfunctionaliteit (SSO) te gebruiken tussen vertrouwde zakelijke partners zonder dat gebruikers zich afzonderlijk moeten aanmelden bij elke toepassing. Dit staat bekend als Federatie.

Veel organisaties hebben Software as a Service (SaaS) of aangepaste LOB-apps (Line-of-Business) die rechtstreeks naar AD FS worden gefedereerd, naast office 365- en Azure AD-apps. 

![Toepassingen die direct on-premises zijn aangesloten](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**Om de beveiliging van toepassingen te verhogen, is het uw doel om één set toegangscontroles en -beleidsregels in uw on-premises en cloudomgevingen te hebben.** 

![Toepassingen die zijn verbonden via Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>Typen apps die moeten worden gemigreerd

Het migreren van al uw toepassingsverificatie naar Azure AD is optimaal, omdat u één controlevlak krijgt voor identiteits- en toegangsbeheer.

Uw toepassingen kunnen moderne of verouderde protocollen gebruiken voor verificatie. Overweeg eerst migrerende toepassingen die moderne verificatieprotocollen gebruiken (zoals SAML en Open ID Connect). Deze apps kunnen opnieuw worden geconfigureerd om te verifiëren met Azure AD via een ingebouwde connector in onze app-galerie of door de toepassing te registreren in Azure AD. Apps met oudere protocollen kunnen worden geïntegreerd met behulp van [Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy). 

Zie [Welke typen toepassingen kan ik integreren met Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-management)voor meer informatie?

U het [ad FS-toepassingsactiviteitenrapport](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) gebruiken om toepassingen te migreren naar Azure AD als azure [AD Connect-status is ingeschakeld.](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs) 

### <a name="the-migration-process"></a>Het migratieproces

Test uw apps en configuratie tijdens het verplaatsen van uw app-verificatie naar Azure AD. We raden u aan bestaande testomgevingen te blijven gebruiken voor migratietests die naar de productieomgeving worden verplaatst. Als er momenteel geen testomgeving beschikbaar is, u er een instellen met [Azure App Service](https://azure.microsoft.com/services/app-service/) of Azure Virtual [Machines,](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)afhankelijk van de architectuur van de toepassing.

U ervoor kiezen om een aparte test Azure AD-tenant in te stellen die u gebruiken bij het ontwikkelen van uw app-configuraties. 

Uw migratieproces ziet er mogelijk als volgt uit:

**Fase 1 – Huidige status: productie-app authenticeren met AD FS**

![Migratiefase 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

 
**Fase 2 – OPTIONEEL: test instantie van app die aanwijst om Azure-tenant te testen**

Werk de configuratie bij om uw testexemplaar van de app te wijzen op een test-Azure AD-tenant en de vereiste wijzigingen aan te brengen. De app kan worden getest met gebruikers in de test Azure AD-tenant. Tijdens het ontwikkelingsproces u tools zoals [Fiddler](https://www.telerik.com/fiddler) gebruiken om verzoeken en antwoorden te vergelijken en te verifiëren.

Als het niet mogelijk is een afzonderlijke testtenant in te stellen, slaat u deze fase over en houdt u een testexemplaar van een app op en wijst u deze naar uw azure AD-tenant voor productie, zoals beschreven in fase 3 hieronder.

![Migratiefase 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**Fase 3 – Test app die wijst op de Azure-tenant van de productie**

Werk de configuratie bij om uw testexemplaar van de app naar uw productie-exemplaar van Azure te wijzen. U nu testen met gebruikers in uw productie-instantie. Bekijk indien nodig het gedeelte van dit artikel over de overgang van gebruikers.

![Migratiefase 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**Fase 4 – Productie-app die naar productie AD-tenant wijst**

Werk de configuratie van uw productietoepassing bij om naar uw azure-tenant voor productie te wijzen.

![Migratiefase 1 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Apps die zich met AD FS authenticeren, kunnen Active Directory-groepen gebruiken voor machtigingen. Gebruik [Azure AD Connect-synchronisatie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) om identiteitsgegevens te synchroniseren tussen uw on-premises omgeving en Azure AD voordat u met migratie begint. Controleer deze groepen en het lidmaatschap vóór de migratie, zodat u toegang verlenen aan dezelfde gebruikers wanneer de toepassing wordt gemigreerd.

### <a name="line-of-business-lob-apps"></a>Lob-apps (Line of Business)

LOB-apps worden intern ontwikkeld door uw organisatie of beschikbaar als een standaard verpakt product dat in uw datacenter is geïnstalleerd. Voorbeelden hiervan zijn apps die zijn gebouwd op Windows Identity Foundation en SharePoint-apps (niet SharePoint Online). 

LOB-apps die OAuth 2.0, OpenID Connect of WS-Federation gebruiken, kunnen worden geïntegreerd met Azure AD als [app-registraties](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users). Integreer aangepaste apps die SAML 2.0 of WS-Federation gebruiken als [niet-galerietoepassingen](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) op de pagina met bedrijfstoepassingen in de [Azure-portal.](https://portal.azure.com/)

## <a name="saml-based-single-sign-on"></a>SAML-gebaseerde single sign-On

Apps die SAML 2.0 gebruiken voor verificatie, kunnen worden geconfigureerd voor [SAML-gebaseerde eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (SAML-gebaseerde SSO). Met [SAML-gebaseerde SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)u gebruikers toewijzen aan specifieke toepassingsrollen op basis van regels die u definieert in uw SAML-claims. 

Zie [SAML-gebaseerde aanmelding configureren](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications)als u een SaaS-toepassing voor SAML-gebaseerde eenmalige aanmelding wilt configureren. 

![SSO SAML-gebruikersschermafbeeldingen ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

 
Veel SaaS-toepassingen hebben een [toepassingsspecifieke zelfstudie](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) die u door de configuratie voor SAML-gebaseerde eenmalige aanmelding maakt.

![zelfstudie van app](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Sommige apps kunnen eenvoudig worden gemigreerd. Voor apps met complexere vereisten, zoals aangepaste claims, is mogelijk aanvullende configuratie in Azure AD en/of Azure AD Connect vereist. Zie [Claimtoewijzing in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)voor informatie over ondersteunde claimtoewijzingen.

Houd rekening met de volgende beperkingen bij het toewijzen van kenmerken:

* Niet alle kenmerken die kunnen worden uitgegeven in AD FS worden weergegeven in Azure AD als kenmerken die moeten worden uitgevoerd naar SAML-tokens, zelfs niet als deze kenmerken worden gesynchroniseerd. Wanneer u het kenmerk bewerkt, worden in de vervolgkeuzelijst Waarde de verschillende kenmerken weergegeven die beschikbaar zijn in Azure AD. Controleer [de synchronisatieconfiguratie van Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) om ervoor te zorgen dat een vereist kenmerk- bijvoorbeeld samAccountName - wordt gesynchroniseerd met Azure AD. U de extensiekenmerken gebruiken om een claim uit te zenden die geen deel uitmaakt van het standaardgebruikersschema in Azure AD.

* In de meest voorkomende scenario's zijn alleen de NameID-claim en andere algemene gebruikers-id-claims vereist voor een app. Als u wilt bepalen of er aanvullende claims nodig zijn, onderzoekt u welke claims u uitgeeft vanuit AD FS.

* Niet alle claims kunnen problemen zijn omdat sommige claims worden beschermd in Azure AD. 

* De mogelijkheid om versleutelde SAML-tokens te gebruiken is nu in preview. Zie [Hoe: claims aanpassen die zijn uitgegeven in het SAML-token voor bedrijfstoepassingen.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

 

### <a name="software-as-a-service-saas-apps"></a>Software as a service (SaaS) apps

Als de aanmelding van uw gebruiker bij SaaS-apps zoals Salesforce, ServiceNow of Workday is aangemeld en is geïntegreerd met AD FS, gebruikt u federatieve aanmelding voor SaaS-apps. 

De meeste SaaS-toepassingen kunnen al worden geconfigureerd in Azure AD. Microsoft heeft veel vooraf geconfigureerde verbindingen met SaaS-apps in de [azure AD-app-galerie,](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)waardoor uw overgang gemakkelijker wordt. SAML 2.0-toepassingen kunnen worden geïntegreerd met Azure AD via de Azure AD-app-galerie of als [niet-galerietoepassingen.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) 

Apps die gebruikmaken van OAuth 2.0 of OpenID Connect, kunnen op gelijksoortige wijze worden geïntegreerd met Azure AD als [app-registraties](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users). Apps die verouderde protocollen gebruiken, kunnen [Azure AD-toepassingsproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) gebruiken om te verifiëren met Azure AD.

Voor problemen met het onboarding en instappen van uw SaaS-apps u contact opnemen met de [ondersteuningsalias voor SaaS Application Integration.](mailto:SaaSApplicationIntegrations@service.microsoft.com)

**SAML-ondertekeningscertificaten voor SSO**: Ondertekeningscertificaten zijn een belangrijk onderdeel van elke SSO-implementatie. Azure AD maakt de ondertekeningscertificaten om SAML-gebaseerde federatieve SSO voor uw SaaS-toepassingen vast te stellen. Zodra u galerie- of niet-galerietoepassingen hebt toegevoegd, configureert u de toegevoegde toepassing met de optie Federatieve SSO. Zie [Certificaten beheren voor federatieve enkele aanmelding in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on).

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Apps en configuraties die vandaag kunnen worden verplaatst

Apps die u vandaag de dag gemakkelijk verplaatsen, omvatten SAML 2.0-apps die de standaardset configuratie-elementen en claims gebruiken:

* User principal name

* E-mailadres

* Voornaam

* Achternaam

* Alternatief kenmerk als SAML **NameID**, met inbegrip van het Azure AD e-mailkenmerk, e-mailvoorvoegsel, werknemer-id, extensiekenmerken 1-15, of on-premises **SamAccountName**-kenmerk. Zie voor meer informatie [De NameIdentifier-claim bewerken](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

* Aangepaste claims.

De volgende vereisen aanvullende configuratiestappen om te migreren naar Azure AD:

* Aangepaste autorisatie- of MFA-regels (Multi-Factor Authentication) in AD FS. U configureert ze met de functie [Voorwaardelijke toegang voor Azure AD.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

* Apps met meerdere antwoord-URL-eindpunten. U configureert ze in Azure AD met PowerShell of in de Azure-portalinterface.

* WS-Federation-apps, zoals SharePoint-apps waarvoor versie 1.1-SAML-tokens vereist zijn. U ze handmatig configureren met PowerShell. U ook een vooraf geïntegreerde algemene sjabloon voor SharePoint- en SAML 1.1-toepassingen toevoegen vanuit de galerie. Wij ondersteunen het SAML 2.0 protocol.

* Complexe uitgifte van claims transformeert regels. Zie voor informatie over ondersteunde claimstoewijzingen:
   *  [Claimtoewijzing in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping) 
   * [Claims aanpassen die zijn uitgegeven in het SAML-token voor bedrijfstoepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

 

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Apps en configuratie die momenteel niet worden ondersteund in Azure AD

Apps waarvoor de volgende mogelijkheden nodig zijn, kunnen vandaag niet worden gemigreerd.

**Protocolmogelijkheden**

* Ondersteuning voor het WS-Trust ActAs-patroon

* SAML-artefactomzetting

* Verificatie van ondertekende SAML-aanvragen voor handtekeningen  
Houd er rekening mee dat ondertekende aanvragen worden geaccepteerd, maar dat de handtekening niet is geverifieerd.  
Aangezien Azure AD het token alleen terugstuurt naar eindpunten die vooraf zijn geconfigureerd in de toepassing, is verificatie van handtekeningen in de meeste gevallen waarschijnlijk niet vereist.

**Claims in tokenmogelijkheden**

* Claims van andere attribuutopslag dan de Azure AD-map, tenzij die gegevens worden gesynchroniseerd met Azure AD. Zie het overzicht van de [Azure AD-synchronisatie-API](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)voor meer informatie .

* Uitgifte van directory-kenmerken met meerdere waarde. We kunnen bijvoorbeeld op dit moment geen claim met meerdere waarden voor proxyadressen uitgeven.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>App-instellingen toewijzen van AD FS naar Azure AD

Voor de migratie begint, moet eerst worden vastgesteld hoe de app on-premises moet worden geconfigureerd en hoe de configuratie aan Azure AD wordt toegewezen. AD FS en Azure AD werken op dezelfde manier, zodat in beide gevallen de concepten van het configureren van vertrouwens-, aanmeldings- en afmeld- en aanmeldings-URL's en id's van toepassing zijn. Documenteer de AD FS-configuratie-instellingen van uw toepassingen, zodat u ze eenvoudig configureren in Azure AD.

### <a name="map-app-configuration-settings"></a>Configuratie-instellingen voor app-apps

The following table describes some of the most common mapping of settings between an AD FS Relying Party Trust to Azure AD Enterprise Application:

* AD FS – Vind de instelling in de AD FS Relying Party Trust voor de app. Klik met de rechtermuisknop op de relying party en selecteer Eigenschappen. 

* Azure AD: De instelling is geconfigureerd in [azure portal](https://portal.azure.com/) in de single sign-on eigenschappen van elke toepassing.

| Configuratie-instelling| AD FS| Configureren in Azure AD| SAML-token |
| - | - | - | - |
| **Aanmeldings-URL van app** <p>De URL voor de gebruiker om zich aan te melden bij de app in een door de Serviceprovider (SP)geïnitieerde SAML-stroom.| N.v.t.| BasisSAML-configuratie openen vanaf SAML-gebaseerde aanmelding| N.v.t. |
| **Antwoord-URL van app** <p>De URL van de app vanuit het perspectief van de identiteitsprovider (IdP). De IdP stuurt de gebruiker en token hier nadat de gebruiker zich heeft aangemeld bij de IdP.  Dit is ook bekend als **SAML bewering consument eindpunt**.| Het tabblad **Eindpunten** selecteren| BasisSAML-configuratie openen vanaf SAML-gebaseerde aanmelding| Doelelement in het SAML-token. Voorbeeldwaarde:[https://contoso.my.salesforce.com](https://contoso.my.salesforce.com/) |
| **App-URL voor afmelden** <p>Dit is de URL waarnaar "afmeldopruiming" verzoeken worden verzonden wanneer een gebruiker zich afmeldt bij een app. De IdP stuurt het verzoek om de gebruiker ook uit te loggen van alle andere apps.| Het tabblad **Eindpunten** selecteren| BasisSAML-configuratie openen vanaf SAML-gebaseerde aanmelding| N.v.t. |
| **App-id** <p>Dit is de app-id vanuit het perspectief van de IdP. De url-waarde van de aanmelding wordt vaak gebruikt voor de id (maar niet altijd).  Soms noemt de app dit de 'entiteits-ID'.| Het tabblad **Id's** selecteren|BasisSAML-configuratie openen vanaf SAML-gebaseerde aanmelding| Kaarten aan het **element Publiek** in het SAML-token. |
| **Federatieve metagegevens van app** <p>Dit is de locatie van de federatiemetagegevens van de app. De IdP gebruikt deze om bepaalde configuratie-instellingen automatisch bij te werken, zoals eindpunten of versleutelingscertificaten.| Het tabblad **Controle** selecteren| N.v.t. Azure AD ondersteunt niet direct het gebruik van metagegevens van toepassingsfederatie. U de federatiemetagegevens handmatig importeren.| N.v.t. |
| **Gebruikersnaam/ naam-id** <p>Kenmerk dat wordt gebruikt om voor de app op unieke wijze de gebruikers-id aan te geven vanuit Azure AD of AD FS.  Dit kenmerk is meestal het UPN of het e-mailadres van de gebruiker.| Claimregels. In de meeste gevallen geeft de claimregel een claim uit met een type dat eindigt met de NameIdentifier.| U de id vinden onder de **kopgebruikerskenmerken en -claims**. Standaard wordt de UPN gebruikt| Kaarten aan het **NameID-element** in het SAML-token. |
| **Overige claims** <p>Voorbeelden van andere claimgegevens die gewoonlijk van de IdP naar de app worden verzonden, zijn voornaam, achternaam, e-mailadres en groepslidmaatschap.| In AD FS kunt u deze gegevens vinden als andere claimregels op de Relying Party.| U de id vinden onder de header **User Attributes & Claims**. Selecteer **Beeld** en bewerk alle andere gebruikerskenmerken.| N.v.t. |


### <a name="map-identity-provider-idp-settings"></a>Instellingen voor kaartidentiteitprovider (IdP)

Configureer uw toepassingen om te wijzen op Azure AD versus AD FS voor SSO. Hier richten we ons op SaaS-apps die het SAML-protocol gebruiken. Dit concept strekt zich echter ook uit tot aangepaste LOB-apps.

> [!NOTE]
> De configuratiewaarden voor Azure AD volgen het patroon waarin uw Azure Tenant ID {tenant-id} vervangt en de toepassings-id {application-id} vervangt. U vindt deze informatie in de [Azure-portal](https://portal.azure.com/) onder Azure Active Directory > Eigenschappen: 

* Selecteer Directory-id om uw tenant-id te bekijken. 

* Selecteer Toepassings-id om de toepassings-id te zien.

 Breng op hoog niveau de volgende configuratie-elementen van SaaS-apps in kaart aan Azure AD. 

 

| Element| Configuratiewaarde |
| - | - |
| Uitgever van identiteitsprovider| [https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| Url van het inloggen van identiteitsprovider| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Uitlog-URL van identiteitsprovider| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Locatie van federatiemetagegevens| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |


### <a name="map-sso-settings-for-saas-apps"></a>SSO-instellingen voor SaaS-instellingen toewijzen

SaaS-apps moeten weten waar ze verificatieverzoeken moeten verzenden en hoe ze de ontvangen tokens kunnen valideren. In de volgende tabel worden de elementen beschreven die sso-instellingen in de app moeten configureren en hun waarden of locaties binnen AD FS en Azure AD

| Configuratie-instelling| AD FS| Configureren in Azure AD |
| - | - | - |
| **IDP-aanmeldings-URL** <p>Aanmeldings-URL van de IdP vanuit het perspectief van de app (waarbij de gebruiker wordt doorgestuurd om in te loggen).| De URL van ad FS-aanmelding is de servicenaam van de AD FS-federatie, gevolgd door '/adfs/ls/'. <p>Bijvoorbeeld:[https://fs.contoso.com/adfs/ls/](https://fs.contoso.com/adfs/ls/)| Vervang {tenant-id} door uw tenant-id. <p> Voor apps die het SAML-P-protocol gebruiken:[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>Voor apps die het WS-Federation-protocol gebruiken:[https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **IDP-afmeldings-URL**<p>De URL van de IdP uitloggen vanuit het perspectief van de app (waarbij de gebruiker wordt doorgestuurd wanneer hij zich afmeldt bij de app).| De aanmeldings-URL is hetzelfde als de aanmeldings-URL, of dezelfde URL met "wa=wsignout1.0" toegevoegd. Bijvoorbeeld:[https://fs.contoso.com/adfs/ls/?wa=wsignout1.0](https://fs.contoso.com/adfs/ls/?wa=wsignout1.0)| Vervang {tenant-id} door uw tenant-id.<p>Voor apps die het SAML-P-protocol gebruiken:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> Voor apps die het WS-Federation-protocol gebruiken:[https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Certificaatondertekeningscertificaat**<p>De IdP gebruikt de privésleutel van het certificaat om uitgegeven tokens te ondertekenen. Er wordt gecontroleerd of het token afkomstig is van de dezelfde IdP die de app is geconfigureerd om te vertrouwen.| U vindt het AD FS-certificaat voor token-ondertekening in AD FS-beheer onder **Certificaten**.| Zoek het in de Azure-portal in de **afzonderlijke aanmeldingseigenschappen** van de toepassing onder het **SAML-ondertekeningscertificaat**voor koptekst . Daar kunt u het certificaat downloaden om het te uploaden naar de app.  <p>Als de toepassing meer dan één certificaat heeft, u alle certificaten vinden in het XML-bestand met federatiemetagegevens. |
| **Identificatie/ "emittent"**<p>Id van idp vanuit het perspectief van de app (ook wel de 'emittent-ID').<p>In het SAML-token wordt de waarde weergegeven als het element Uitgever.| De id voor AD FS is meestal de federatieservice-id in AD FS-beheer onder **Service > Federation Service Properties bewerken.** Bijvoorbeeld:[http://fs.contoso.com/adfs/services/trust](http://fs.contoso.com/adfs/services/trust)| Vervang {tenant-id} door uw tenant-id.<p>[https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| **Metagegevens idp-federatie**<p>Locatie van de openbaar beschikbare federatiemetadata van de IdP. (Sommige apps gebruiken federatiemetagegevens als alternatief voor het afzonderlijk door de beheerder configureren van URL's, id en tokenhandtekeningcertificaat.)| Zoek de URL van de AD FS-federatie metagegevens in AD FS-beheer onder **Service > Eindpunten > metagegevens > Type: Federatie**. Bijvoorbeeld:[https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml](https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml)| De overeenkomstige waarde voor [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml)Azure AD volgt het patroon . Vervang {TenantDomainName} door de naam van uw tenant in de indeling 'contoso.onmicrosoft.com'.   <p>Zie voor meer informatie [Federatiemetagegevens](https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-federation-metadata). |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>AD FS-beveiligingsbeleid weergeven in Azure AD

Wanneer u uw app-verificatie verplaatst naar Azure AD, maakt u toewijzingen van bestaand beveiligingsbeleid naar hun gelijkwaardige of alternatieve varianten die beschikbaar zijn in Azure AD. Als u ervoor zorgt dat deze toewijzingen kunnen worden uitgevoerd terwijl aan de door uw app-eigenaren vereiste beveiligingsnormen worden voldaan, wordt de migratie van de app aanzienlijk eenvoudiger.

Voor elk regeltype en de bijbehorende voorbeelden stellen we hier voor hoe de regel eruit ziet in AD FS, de ad fs-regeltaal-equivalente code en hoe deze kaart in Azure AD.

### <a name="map-authorization-rules"></a>Regels voor kaartautorisatie

Hieronder volgen voorbeelden van typen autorisatieregels in AD FS en hoe u deze toewijzen aan Azure AD:

#### <a name="example-1-permit-access-to-all-users"></a>Voorbeeld 1: Toegang verlenen aan alle gebruikers

Toegang tot alle gebruikers toestaan, zoals in AD FS: 

![Migratiefase 1 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


Hiermee wordt op een van de volgende manieren een reeks toegewezen aan Azure AD:

In de [Azure-portal:](https://portal.azure.com/)
* Optie 1: Gebruikerstoewijzing instellen die vereist is op Nr. ![toegangscontrolebeleid voor SaaS-apps bewerken ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    Houd er rekening mee dat het instellen van de vereiste overschakeling voor gebruikerstoewijzing vereist moet worden ingesteld, waarbij gebruikers aan de toepassing moeten worden toegewezen om toegang te krijgen. Wanneer ingesteld op Nee, hebben alle gebruikers toegang. Deze schakelaar bepaalt niet wat wordt weergegeven voor gebruikers in de Ervaring Mijn apps. 

 
* Optie 2: Wijs uw toepassing toe aan de automatische groep 'Alle gebruikers' in het tabblad Gebruikers en groepen. <p>
U moet [Dynamische groepen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) in uw Azure AD-tenant inschakelen om de standaardgroep 'Alle gebruikers' beschikbaar te maken.

   ![Mijn SaaS-apps in Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>Voorbeeld 2: Een groep expliciet toestaan

Expliciete groepsmachtiging in AD FS:


![Uitgifteautorisatieregels ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


Zo wordt de regel toegewezen aan Azure AD:

In de [Azure-portal](https://portal.azure.com/)maakt u eerst [een gebruikersgroep](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) die overeenkomt met de groep gebruikers van AD FS en wijst u vervolgens app-machtigingen toe aan die groep:

![Toewijzing toevoegen ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>Voorbeeld 3: Een specifieke gebruiker autoriseren

Expliciete gebruikersautorisatie in AD FS:

![Uitgifteautorisatieregels ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

Zo wordt de regel toegewezen aan Azure AD:

Voeg in de [Azure-portal](https://portal.azure.com/)een gebruiker toe aan de app via het tabblad Toewijzing toevoegen van de app zoals hieronder wordt weergegeven:

![Mijn SaaS-apps in Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

 
### <a name="map-multi-factor-authentication-rules"></a>Regels voor meervoudige verificatie toewijzen 

Een on-premise implementatie van [Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication) en AD FS werkt nog steeds na de migratie omdat u gefedeerd bent met AD FS. Overweeg echter te migreren naar de ingebouwde MFA-mogelijkheden van Azure die zijn gekoppeld aan de voorwaardelijke toegangswerkstromen van Azure AD. 

Hieronder volgen voorbeelden van typen MFA-regels in AD FS en hoe u deze toewijzen aan Azure AD op basis van verschillende voorwaarden:

MFA-regelinstellingen in AD FS:

![Azure AD MFA-instellingen](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Voorbeeld 1: MFA afdwingen op basis van gebruikers/groepen

De selectie voor gebruiker/groepen is een regel waarmee u MFA afdwingen op basis van een groep (Sid) of per gebruiker (Primary SID). Afgezien van de toewijzingen gebruiker/groepen, fungeren alle extra selectievakjes in de gebruikersinterface van de AD FS MFA-configuratie als aanvullende regels die worden geëvalueerd nadat de regel Gebruiker/Groepen is afgedwongen. 


Mfa-regels opgeven voor een gebruiker of een groep in Azure AD:

1. Een [nieuw beleid voor voorwaardelijke toegang maken](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

2. Selecteer **Toewijzingen**. Voeg de gebruiker(s) of groep toe waarop u MFA wilt afdwingen.

3. Configureer de opties **voor toegangsbesturingselementen** zoals hieronder weergegeven:  
‎

![AAD MFA-instellingen](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)

 
 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Voorbeeld 2: MFA afdwingen voor niet-geregistreerde apparaten

Mfa-regels opgeven voor niet-geregistreerde apparaten in Azure AD:

1. Een [nieuw beleid voor voorwaardelijke toegang maken](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

2. Stel de **toewijzingen in op** **alle gebruikers.**

3. Configureer de opties **voor toegangsbesturingselementen** zoals hieronder weergegeven:  
‎

![AAD MFA-instellingen](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

 
Wanneer u de optie Voor meerdere besturingselementen instelt op Vereisen een van de geselecteerde besturingselementen, betekent dit dat als aan een van de door het selectievakje opgegeven voorwaarden door de gebruiker is voldaan, deze toegang krijgen tot uw app.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Voorbeeld 3: MFA afdwingen op basis van locatie

Mfa-regels opgeven op basis van de locatie van een gebruiker in Azure AD:

1. Een [nieuw beleid voor voorwaardelijke toegang maken](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

1. Stel de **toewijzingen in op** **alle gebruikers.**

1. [Benoemde locaties configureren in Azure AD,](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) anders wordt federatie vanuit uw bedrijfsnetwerk vertrouwd. 

1. Configureer de **regels voorwaarden** om de locaties op te geven waarvoor u MFA wilt afdwingen.

![Azure AD MFA-instellingen](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. Configureer de opties **voor toegangsbesturingselementen** zoals hieronder weergegeven:


![Beleid voor kaarttoegangsbeheer](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

 
### <a name="map-emit-attributes-as-claims-rule"></a>Kenmerken van Emit toewijzen als claimregel

Hier vindt u een voorbeeld van hoe kenmerken worden toegewezen in AD FS:

![Azure AD MFA-instellingen](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


Zo wordt de regel toegewezen aan Azure AD:

Selecteer in de [Azure-portal](https://portal.azure.com/) **Enterprise-toepassingen**, **Enkele aanmelding**en voeg **SAML-tokenkenmerken** toe zoals hieronder wordt weergegeven:

![Azure AD MFA-instellingen](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>Ingebouwde toegangscontrolebeleid toewijzen

AD FS 2016 heeft verschillende ingebouwde toegangscontrolebeleidsregels waaruit u kiezen:

![Azure AD ingebouwd in toegangscontrole](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)

 
Als u ingebouwde beleidsregels wilt implementeren in Azure AD, u een [nieuw beleid voor voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json) gebruiken en de toegangsbesturingselementen configureren, of u de aangepaste beleidsontwerper in AD FS 2016 gebruiken om toegangsbeheerbeleid te configureren. De Rule Editor heeft een uitputtende lijst van Permit en Except opties die u kunnen helpen om allerlei permutaties.

![Azure AD-toegangsbeheerbeleid](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



In deze tabel hebben we een aantal handige opties voor toestaan en behalve vermeld en hoe deze worden toegewezen aan Azure AD. 


| | De optie Vergunning configureren in Azure AD?| De optie Except configureren in Azure AD? |
| - | - | - |
| Van specifieke netwerk| Kaarten [naar benoemde locatie](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) in Azure AD| De optie **Uitsluiten** gebruiken voor [vertrouwde locaties](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) |
| Van specifieke groepen| [Een toewijzing voor gebruiker/groepen instellen](https://docs.microsoft.com/azure/active-directory/manage-apps/assign-user-or-group-access-portal)| De optie **Uitsluiten gebruiken** in Gebruikers en groepen |
| Van apparaten met specifiek vertrouwensniveau| Stel dit in vanuit het besturingselement 'Apparaatstatus' onder Toewijzingen > Voorwaarden| Gebruik de optie **Uitsluiten** onder apparaatstatus voorwaarde en **Alle apparaten opnemen** |
| Met specifieke claims in de aanvraag| Deze instelling kan niet worden gemigreerd| Deze instelling kan niet worden gemigreerd |


Een voorbeeld van het configureren van de optie Uitsluiten voor vertrouwde locaties in de Azure Portal:

![Schermafbeelding van het beheerbeleid voor toegangsbeheer toewijzen](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Gebruikers overschakelen van AD FS naar Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>AD FS-groepen synchroniseren in Azure AD

Wanneer u autorisatieregels in kaart brengt, kunnen apps die zich verifiëren met AD FS Active Directory-groepen gebruiken voor machtigingen. Gebruik in dat geval [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) om deze groepen te synchroniseren met Azure AD voordat u de toepassingen migreert. Zorg ervoor dat u deze groepen en het lidmaatschap vóór de migratie verifieert, zodat u dezelfde gebruikers toegang verlenen wanneer de toepassing wordt gemigreerd.

Zie [Vereisten voor het gebruik van groepskenmerken die zijn gesynchroniseerd vanuit Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims)voor meer informatie.

### <a name="setup-user-self-provisioning"></a>Zelfvoorziening van de gebruiker instellen 

Sommige SaaS-toepassingen ondersteunen de mogelijkheid om gebruikers zelf in te richten wanneer ze zich voor het eerst aanmelden bij de toepassing. In Azure Active Directory (Azure AD) verwijst de term app-inrichting naar het automatisch maken van gebruikersidentiteiten en -rollen in de cloud[(SaaS)](https://azure.microsoft.com/overview/what-is-saas/)waartoe gebruikers toegang nodig hebben. Gebruikers die zijn gemigreerd, hebben al een account in de SaaS-toepassing. Alle nieuwe gebruikers die na de migratie zijn toegevoegd, moeten worden ingericht. Test [saas-app-inrichting](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) zodra de toepassing is gemigreerd.

### <a name="sync-external-users-in-azure-ad"></a>Externe gebruikers synchroniseren in Azure AD

Uw bestaande externe gebruikers kunnen op twee belangrijke manieren binnen AD FS worden ingesteld:

#### <a name="external-users-with-a-local-account-within-your-organization"></a>Externe gebruikers met een lokaal account binnen uw organisatie

U deze accounts nog steeds gebruiken op dezelfde manier als uw interne gebruikersaccounts. Deze externe gebruikersaccounts hebben een principenaam binnen uw organisatie, hoewel de e-mail van het account extern kan wijzen. Naarmate u verder komt met uw migratie, u profiteren van de voordelen die [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) biedt door deze gebruikers te migreren om hun eigen huisstijl te gebruiken wanneer een dergelijke identiteit beschikbaar is. Dit stroomlijnt het aanmeldingsproces voor die gebruikers, omdat ze vaak zijn aangemeld met hun eigen zakelijke aanmelding. Het beheer van uw organisatie wordt ook afgenomen door dat u geen accounts meer hoeft te beheren voor externe gebruikers.

#### <a name="federated-external-identities"></a>Gefedereerde externe identiteiten

Als u momenteel actief bent met een externe organisatie, moet u een aantal benaderingen volgen:

* [Azure Active Directory B2B-samenwerkingsgebruikers toevoegen aan de Azure-portal.](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator) U proactief B2B-uitnodigingen van de Azure AD-beheerportal naar de partnerorganisatie sturen voor individuele leden om de apps en assets te blijven gebruiken die ze gewend zijn. 

* [Maak een selfservice B2B-aanmeldingsworkflow](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal) die een aanvraag genereert voor individuele gebruikers bij uw partnerorganisatie met behulp van de B2B-uitnodigingsAPI.

Het maakt niet uit hoe uw bestaande externe gebruikers zijn geconfigureerd, ze hebben waarschijnlijk machtigingen die zijn gekoppeld aan hun account, hetzij in groepslidmaatschap of specifieke machtigingen. Evalueren of deze machtigingen moeten worden gemigreerd of opgeschoond. Accounts binnen uw organisatie die een externe gebruiker vertegenwoordigen, moeten worden uitgeschakeld zodra de gebruiker is gemigreerd naar een externe identiteit. Het migratieproces moet worden besproken met uw zakelijke partners, omdat er een onderbreking kan zijn in hun vermogen om verbinding te maken met uw resources.

## <a name="migrate-and-test-your-apps"></a>Uw apps migreren en testen

Volg het migratieproces in dit artikel.

Ga vervolgens naar de [Azure-portal](https://aad.portal.azure.com/) om te testen of de migratie een succes was. Volg de onderstaande instructies:
1. Selecteer **Enterprise Applications** > **All-toepassingen** en zoek uw app in de lijst.

1. Selecteer**Gebruikers en groepen** **beheren** > om ten minste één gebruiker of groep aan de app toe te wijzen.

1. Selecteer**Voorwaardelijke toegang** **beheren** > . Bekijk uw lijst met beleidsregels en zorg ervoor dat u de toegang tot de toepassing niet blokkeert met een [beleid voor voorwaardelijke toegang.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

Afhankelijk van hoe u uw app configureert, controleert u of SSO naar behoren werkt. 

| Verificatietype| Testen |
| - | - |
| OAuth / OpenID Connect| Selecteer **Enterprise-toepassingen > machtigingen** en zorg ervoor dat u hebt ingestemd met de toepassing die in uw organisatie moet worden gebruikt in de gebruikersinstellingen voor uw app.  
‎ |
| Op SAML gebaseerde SSO| Gebruik de knop [SAML-instellingen testen](https://docs.microsoft.com/azure/active-directory/develop/howto-v1-debug-saml-sso-issues) onder **Aanmelding voor één melding**.  
‎ |
| SSO op basis van wachtwoord| Download en installeer de [MyApps Secure Sign](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[-](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[In Extension](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction). Met deze extensie u een van de cloud-apps van uw organisatie starten waarvoor u een SSO-proces moet gebruiken.  
‎ |
| Toepassingsproxy| Controleer of uw connector wordt uitgevoerd en toegewezen aan uw toepassing. Ga naar de handleiding[ ](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot)voor het oplossen van problemen bij [toepassingen](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot) voor verdere hulp.  
‎ |

> [!NOTE]
> Cookies uit de oude AD FS-omgeving blijven bestaan door de machines van de gebruiker. Deze cookies kunnen problemen veroorzaken met de migratie omdat gebruikers kunnen worden doorverwezen naar de oude AD FS-inlogomgeving ten opzichte van de nieuwe Azure AD-aanmelding. Mogelijk moet u de browsercookies van de gebruiker handmatig wissen of een script gebruiken. U ook het Configuratiebeheer van het Systeemcentrum of een vergelijkbaar platform gebruiken.

### <a name="troubleshoot"></a>Problemen oplossen

Als er fouten zijn uit de test van de gemigreerde toepassingen, kan het oplossen van problemen de eerste stap zijn voordat u teruggaat naar de bestaande AD FS-relying-partijen. Zie [SamL-gebaseerde aanmelding voor toepassingen in Azure Active Directory debuggen.](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-v1-debug-saml-sso-issues)

### <a name="rollback-migration"></a>Terugdraaien migratie

Als de migratie mislukt, raden we u aan de bestaande relying parties op de AD FS-servers te laten staan en de toegang tot de relying parties te verwijderen. Dit zorgt voor een snelle terugval indien nodig tijdens de implementatie.

### <a name="end-user-communication"></a>Communicatie van eindgebruikers

Hoewel het geplande uitvalvenster zelf minimaal kan zijn, moet u nog steeds van plan zijn deze termijnen proactief aan werknemers te communiceren terwijl u de cut-over maakt van AD FS naar Azure AD. Zorg ervoor dat uw app-ervaring een feedbackknop heeft of een aanwijzer naar uw helpdesk voor problemen.

Zodra de implementatie is voltooid, u de gebruikers op de hoogte stellen van de succesvolle implementatie en hen herinneren aan eventuele nieuwe stappen die ze moeten nemen.

* Gebruikers instrueren om het [toegangspaneel](https://myapps.microsoft.com.com/) te gebruiken om toegang te krijgen tot alle gemigreerde toepassingen. 

* Herinner gebruikers eraan dat ze mogelijk hun MFA-instellingen moeten bijwerken. 

* Als Self-Service Password Reset is geïmplementeerd, moeten gebruikers mogelijk hun verificatiemethoden bijwerken of verifiëren. Zie [MFA-](https://aka.ms/mfatemplates) en [SSPR-communicatiesjablonen](https://aka.ms/ssprtemplates) voor eindgebruikers.

Communicatie naar externe gebruikers: Deze groep gebruikers wordt meestal het meest kritisch beïnvloed in geval van problemen. Dit geldt vooral als uw beveiligingshouding een andere set regels voor voorwaardelijke toegang of risicoprofielen voor externe partners dicteert. Zorg ervoor dat externe partners op de hoogte zijn van het cloudmigratieschema en een tijdschema hebben waarin ze worden aangemoedigd om deel te nemen aan een pilot-implementatie die alle stromen test die uniek zijn voor externe samenwerking. Zorg er tot slot voor dat ze toegang hebben tot uw helpdesk in geval van problemen.

## <a name="next-steps"></a>Volgende stappen
Toepassingsverificatie [migreren naar Azure AD](https://aka.ms/migrateapps/whitepaper) lezen<p>
Voorwaardelijke [toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) en [MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) instellen
