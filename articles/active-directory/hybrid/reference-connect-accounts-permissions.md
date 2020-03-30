---
title: 'Azure AD Connect: accounts en machtigingen | Microsoft Documenten'
description: In dit onderwerp worden de accounts beschreven die zijn gebruikt en gemaakt en de vereiste machtigingen.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 10/03/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6071e6553fb1275fea63a37b4897aef2685bd509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253765"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: accounts en machtigingen

## <a name="accounts-used-for-azure-ad-connect"></a>Accounts die worden gebruikt voor Azure AD Connect

![overzicht van accounts](media/reference-connect-accounts-permissions/account5.png)

Azure AD Connect gebruikt 3 accounts om informatie te synchroniseren van on-premises of Windows Server Active Directory naar Azure Active Directory.  Deze accounts zijn:

- **AD DS-connector-account**: wordt gebruikt om informatie te lezen/schrijven naar Windows Server Active Directory

- **ADSync-serviceaccount:** wordt gebruikt om de synchronisatieservice uit te voeren en toegang te krijgen tot de SQL-database

- **Azure AD Connector-account**: wordt gebruikt om informatie naar Azure AD te schrijven

Naast deze drie accounts die worden gebruikt om Azure AD Connect uit te voeren, hebt u ook de volgende extra accounts nodig om Azure AD Connect te installeren.  Dit zijn:

- **Lokaal beheerdersaccount**: de beheerder die Azure AD Connect installeert en die lokale beheerdersmachtigingen op de machine heeft.

- **AD DS Enterprise Administrator-account**: optioneel gebruikt om het 'AD DS Connector-account' hierboven te maken.

- **Azure AD Global Administrator-account**: wordt gebruikt om het Azure AD Connector-account te maken en Azure AD te configureren.

- **SQL SA-account (optioneel):** wordt gebruikt om de ADSync-database te maken wanneer u de volledige versie van SQL Server gebruikt.  Deze SQL Server kan lokaal of extern zijn voor de Installatie van Azure AD Connect.  Dit account kan hetzelfde account zijn als de Enterprise Administrator.  Het inrichten van de database kan nu buiten de band worden uitgevoerd door de SQL-beheerder en vervolgens worden geïnstalleerd door de Azure AD Connect-beheerder met database-eigenaarrechten.  Zie [Azure AD Connect installeren met SQL-machtigingen voor gedelegeerde beheerders voor](how-to-connect-install-sql-delegation.md) meer informatie hierover.


>[!IMPORTANT]
> Vanaf build 1.4.###.# wordt het niet langer ondersteund om een bedrijfsbeheerder of een domeinbeheerdersaccount te gebruiken als het AD DS Connector-account.  Als u een account probeert in te voeren dat een bedrijfsbeheerder of domeinbeheerder is wanneer u **een bestaand account gebruikt,** ontvangt u een foutmelding.

> [!NOTE]
> Het wordt ondersteund voor het beheren van de beheeraccounts die worden gebruikt in Azure AD Connect vanuit een ESAE-beheerforest (ook wel 'Rood forest' genoemd).
> Speciale beheerforests bieden organisaties de mogelijkheid om administratoraccounts, -werkstations en -groepen te hosten in een omgeving die sterkere beveiligingsmechanismen heeft dan de productieomgeving.
> Voor meer informatie over toegewijde beheerbossen verwijzen wij u naar [ESAE Administrative Forest Design Approach](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#esae-administrative-forest-design-approach).

> [!NOTE]
> De rol Globale beheerder is niet vereist na de eerste installatie en het enige vereiste account is het rolaccount **directorysynchronisatieaccounts.** Dat betekent niet noodzakelijkerwijs dat u het account met de rol Globale beheerder wilt verwijderen. Het is beter om de rol te veranderen in een minder krachtige rol, omdat het volledig verwijderen van het account problemen kan introduceren als u ooit de wizard opnieuw moet uitvoeren. Door de bevoegdheden van de rol te verminderen, u de bevoegdheden altijd opnieuw verhogen als u de wizard Azure AD Connect opnieuw moet gebruiken. 

## <a name="installing-azure-ad-connect"></a>Azure AD Connect installeren
De wizard Installatie van Azure AD Connect biedt twee verschillende paden:

* In Expresinstellingen heeft de wizard meer bevoegdheden nodig.  Dit is zodat het uw configuratie eenvoudig kan instellen, zonder dat u gebruikers hoeft te maken of machtigingen hoeft te configureren.
* In Aangepaste instellingen biedt de wizard u meer keuzes en opties. Er zijn echter enkele situaties waarin u ervoor moet zorgen dat u zelf over de juiste machtigingen beschikt.



## <a name="express-settings-installation"></a>Installatie van express-instellingen
In Express-instellingen vraagt de wizard installatie om het volgende:

  - Referenties ad DS Enterprise Administrator
  - Azure AD Global Administrator-referenties

### <a name="ad-ds-enterprise-admin-credentials"></a>Ad DS Enterprise Admin-referenties
Het AD DS Enterprise Admin-account wordt gebruikt om uw on-premises Active Directory te configureren. Deze referenties worden alleen gebruikt tijdens de installatie en worden niet gebruikt nadat de installatie is voltooid. De ondernemingsbeheerder, niet de domeinbeheerder, moet ervoor zorgen dat de machtigingen in Active Directory in alle domeinen kunnen worden ingesteld.

Als u een upgrade uitvoert van DirSync, worden de AD DS Enterprise Admins-referenties gebruikt om het wachtwoord opnieuw in te stellen voor het account dat door DirSync wordt gebruikt. U hebt ook Azure AD Global Administrator-referenties nodig.

### <a name="azure-ad-global-admin-credentials"></a>Azure AD Global Admin-referenties
Deze referenties worden alleen gebruikt tijdens de installatie en worden niet gebruikt nadat de installatie is voltooid. Het wordt gebruikt om het Azure AD Connector-account te maken dat wordt gebruikt voor het synchroniseren van wijzigingen in Azure AD. Het account maakt synchronisatie ook mogelijk als functie in Azure AD.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>Ad DS-connector-account vereiste machtigingen voor expresinstellingen
Het AD DS-connector-account is gemaakt voor het lezen en schrijven naar Windows Server AD en heeft de volgende machtigingen wanneer deze zijn gemaakt door expresinstellingen:

| Machtiging | Gebruikt voor |
| --- | --- |
| <li>Mapwijzigingen repliceren</li><li>Mapwijzigingen repliceren Alles |Wachtwoordhashsynchronisatie |
| Alle eigenschappen lezen/schrijven Gebruiker |Hybride importeren en ruilen |
| Alle eigenschappen lezen/schrijven iNetOrgPerson |Hybride importeren en ruilen |
| Alle eigenschappen groep lezen/schrijven |Hybride importeren en ruilen |
| Alle eigenschappen lezen/schrijven Contact |Hybride importeren en ruilen |
| Wachtwoord opnieuw instellen |Voorbereiding voor het inschakelen van wachtwoordterugschrijfgegevens |

### <a name="express-installation-wizard-summary"></a>Overzicht van de wizard Express

![Express-installatie](./media/reference-connect-accounts-permissions/express.png)

Het volgende is een samenvatting van de pagina's met de expresinstallatiewizard, de verzamelde referenties en waarvoor ze worden gebruikt.

| Wizard Pagina | Verzamelde referenties | Vereiste machtigingen | Gebruikt voor |
| --- | --- | --- | --- |
| N.v.t. |Gebruiker die de wizard installatie uitvoert |Beheerder van de lokale server |<li>Hiermee maakt u het ADSync-serviceaccount dat wordt gebruikt om de synchronisatieservice uit te voeren. |
| Verbinding maken met Azure AD |Azure AD-directoryreferenties |Wereldwijde beheerdersrol in Azure AD |<li>Synchronisatie inschakelen in de Azure AD-map.</li>  <li>Het maken van het Azure AD Connector-account dat wordt gebruikt voor lopende synchronisatiebewerkingen in Azure AD.</li> |
| Verbinding maken met AD DS |On-premises Active Directory-referenties |Lid van de groep Enterprise Admins (EA) in Active Directory |<li>Hiermee maakt u het AD DS-connectoraccount in Active Directory en worden er machtigingen aan toegekend. Dit aangemaakte account wordt gebruikt om directorygegevens te lezen en te schrijven tijdens synchronisatie.</li> |


## <a name="custom-installation-settings"></a>Aangepaste installatie-instellingen

Met de aangepaste installatie van instellingen biedt de wizard u meer keuzeen en opties. 

### <a name="custom-installation-wizard-summary"></a>Overzicht van de aangepaste installatiewizard

Het volgende is een overzicht van de aangepaste wizardpagina's voor de installatie, de verzamelde referenties en waarvoor ze worden gebruikt.

![Express-installatie](./media/reference-connect-accounts-permissions/customize.png)

| Wizard Pagina | Verzamelde referenties | Vereiste machtigingen | Gebruikt voor |
| --- | --- | --- | --- |
| N.v.t. |Gebruiker die de wizard installatie uitvoert |<li>Beheerder van de lokale server</li><li>Als u een volledige SQL Server gebruikt, moet de gebruiker systeembeheerder (SA) zijn in SQL</li> |Hiermee maakt u standaard het lokale account dat wordt gebruikt als het serviceaccount van de synchronisatieengine. Het account wordt alleen gemaakt als de beheerder geen bepaald account opgeeft. |
| Synchronisatieservices installeren, de optie Service-account |AD- of lokale gebruikersaccountreferenties |Gebruiker, machtigingen worden verleend door de wizard installatie |Als de beheerder een account opgeeft, wordt dit account gebruikt als serviceaccount voor de synchronisatieservice. |
| Verbinding maken met Azure AD |Azure AD-directoryreferenties |Wereldwijde beheerdersrol in Azure AD |<li>Synchronisatie inschakelen in de Azure AD-map.</li>  <li>Het maken van het Azure AD Connector-account dat wordt gebruikt voor lopende synchronisatiebewerkingen in Azure AD.</li> |
| Verbinding maken met uw directory’s |On-premises Active Directory-referenties voor elk forest dat is verbonden met Azure AD |De machtigingen zijn afhankelijk van welke functies u inschakelt en u vinden in het AD DS-connector-account maken |Dit account wordt gebruikt voor het lezen en schrijven van directory-informatie tijdens synchronisatie. |
| AD FS-Servers |Voor elke server in de lijst verzamelt de wizard referenties wanneer de aanmeldingsreferenties van de gebruiker die de wizard uitvoert onvoldoende zijn om verbinding te maken |Domeinbeheerder |Installatie en configuratie van de AD FS-serverrol. |
| Proxyservers voor webtoepassingen |Voor elke server in de lijst verzamelt de wizard referenties wanneer de aanmeldingsreferenties van de gebruiker die de wizard uitvoert onvoldoende zijn om verbinding te maken |Lokale beheerder op de doelmachine |Installatie en configuratie van wapserverrol. |
| Proxyvertrouwensreferenties |Federatieservicevertrouwensreferenties (de referenties die de proxy gebruikt om zich in te schrijven voor een vertrouwenscertificaat van de FS |Domeinaccount dat een lokale beheerder van de AD FS-server is |Eerste inschrijving van fs-WAP-vertrouwenscertificaat. |
| PAGINA AD FS-serviceaccount, 'Optie een domeingebruikersaccount gebruiken' |AD-gebruikersaccountreferenties |Domeingebruiker |Het Azure AD-gebruikersaccount waarvan de referenties worden opgegeven, wordt gebruikt als aanmeldingsaccount van de AD FS-service. |

### <a name="create-the-ad-ds-connector-account"></a>Het AD DS-connectoraccount maken

>[!IMPORTANT]
>Een nieuwe PowerShell-module met de naam ADSyncConfig.psm1 is geïntroduceerd met build **1.1.880.0** (uitgebracht in augustus 2018) met een verzameling cmdlets waarmee u de juiste Active Directory-machtigingen voor het Azure AD DS Connector-account configureren.
>
>Zie Azure [AD Connect: Machtigingen voor AD DS-connectoraccount configureren voor](how-to-connect-configure-ad-ds-connector-account.md) meer informatie

Het account dat u opgeeft op de pagina **Verbinding met uw mappen** moet aanwezig zijn in Active Directory voordat u wordt geïnstalleerd.  Azure AD Connect-versie 1.1.524.0 en hoger heeft de optie om de wizard Azure AD Connect het **AD DS Connector-account** te laten maken dat wordt gebruikt om verbinding te maken met Active Directory.  

Het moet ook de vereiste machtigingen hebben verleend. De wizard Installatie controleert de machtigingen niet en eventuele problemen worden alleen gevonden tijdens synchronisatie.

Welke machtigingen u nodig hebt, is afhankelijk van de optionele functies die u inschakelt. Als u meerdere domeinen hebt, moeten de machtigingen worden verleend voor alle domeinen in het forest. Als u geen van deze functies inschakelt, volstaan de standaardmachtigingen **voor domeingebruikers.**

| Functie | Machtigingen |
| --- | --- |
| ms-DS-ConsistencyGuid-functie |Schrijf machtigingen voor het kenmerk ms-DS-ConsistencyGuid dat is gedocumenteerd in [ontwerpconcepten - Ms-DS-ConsistencyGuid gebruiken als sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Wachtwoordhashsynchronisatie |<li>Mapwijzigingen repliceren</li>  <li>Mapwijzigingen repliceren Alles |
| Hybride implementatie van Exchange |Schrijf machtigingen voor de kenmerken die zijn gedocumenteerd in [hybride terugschrijftekst](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) van Exchange voor gebruikers, groepen en contactpersonen. |
| Openbare map van Exchange Mail |Lees machtigingen voor de kenmerken die zijn gedocumenteerd in [de openbare map van Exchange Mail](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) voor openbare mappen. | 
| Wachtwoord terugschrijven |Schrijf machtigingen voor de kenmerken die zijn gedocumenteerd in [Aan de slag met wachtwoordbeheer](../authentication/howto-sspr-writeback.md) voor gebruikers. |
| Apparaat terugschrijven |Machtigingen die zijn verleend met een PowerShell-script zoals beschreven in [apparaatterugschrijftijd](how-to-connect-device-writeback.md). |
| Groep terugschrijven |Hiermee u **Office 365-groepen** terugschrijven naar een forest waarop Exchange is geïnstalleerd.  Zie [Groepsterugschrijven](how-to-connect-preview.md#group-writeback)voor meer informatie .|

## <a name="upgrade"></a>Upgraden
Wanneer u een upgrade uitvoert van de ene versie van Azure AD Connect naar een nieuwe versie, hebt u de volgende machtigingen nodig:

>[!IMPORTANT]
>Vanaf build 1.1.484 introduceerde Azure AD Connect een regressiebug waarvoor sysadmin-machtigingen nodig zijn om de SQL-database te upgraden.  Deze bug wordt gecorrigeerd in build 1.1.647.  Als u een upgrade uitvoert naar deze build, hebt u sysadmin-machtigingen nodig.  Dbo-machtigingen zijn niet voldoende.  Als u Azure AD Connect probeert te upgraden zonder sysadmin-machtigingen, mislukt de upgrade en werkt Azure AD Connect daarna niet meer correct.  Microsoft is zich hiervan bewust en werkt eraan om dit te corrigeren.


| Belangrijkste | Vereiste machtigingen | Gebruikt voor |
| --- | --- | --- |
| Gebruiker die de wizard installatie uitvoert |Beheerder van de lokale server |Binaire bestanden bijwerken. |
| Gebruiker die de wizard installatie uitvoert |Lid van ADSyncAdmins |Wijzigingen aanbrengen in synchronisatieregels en andere configuratie. |
| Gebruiker die de wizard installatie uitvoert |Als u een volledige SQL-server gebruikt: DBO (of iets dergelijks) van de database met synchronisatieengine |Wijzigingen op databaseniveau aanbrengen, zoals het bijwerken van tabellen met nieuwe kolommen. |

## <a name="more-about-the-created-accounts"></a>Meer informatie over de gemaakte accounts
### <a name="ad-ds-connector-account"></a>AD DS Connector-account
Als u expresinstellingen gebruikt, wordt er een account gemaakt in Active Directory dat wordt gebruikt voor synchronisatie. Het aangemaakte account bevindt zich in het foresthoofddomein in de container Gebruikers en heeft de naam vooraf bevestigd met **MSOL_**. Het account wordt gemaakt met een lang complex wachtwoord dat niet verloopt. Als u een wachtwoordbeleid in uw domein hebt, moet u ervoor zorgen dat lange en complexe wachtwoorden voor dit account zijn toegestaan.

![AD-account](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Als u aangepaste instellingen gebruikt, bent u verantwoordelijk voor het maken van het account voordat u de installatie start.  Zie Het AD DS-connectoraccount maken.

### <a name="adsync-service-account"></a>ADSync-serviceaccount
De synchronisatieservice kan onder verschillende accounts worden uitgevoerd. Het kan worden uitgevoerd onder een **Virtual Service Account** (VSA), een Group Managed Service **Account** (gMSA/sMSA) of een gewoon gebruikersaccount. De ondersteunde opties zijn gewijzigd met de 2017 april release van Connect wanneer u een nieuwe installatie doet. Als u een upgrade uitvoert vanaf een eerdere release van Azure AD Connect, zijn deze extra opties niet beschikbaar.

| Type account | Installatieoptie | Beschrijving |
| --- | --- | --- |
| [Virtuele service-account](#virtual-service-account) | Express en custom, april 2017 en later | Dit is de optie die wordt gebruikt voor alle expresinstallaties, behalve voor installaties op een domeincontroller. Voor custom is dit de standaardoptie, tenzij een andere optie wordt gebruikt. |
| [Door groep beheerd serviceaccount](#group-managed-service-account) | Custom, april 2017 en later | Als u een externe SQL-server gebruikt, raden we u aan een groepsbeheerserviceaccount te gebruiken. |
| [Gebruikersaccount](#user-account) | Express en custom, april 2017 en later | Een gebruikersaccount dat vooraf is bevestigd met AAD_ wordt alleen gemaakt tijdens de installatie wanneer het is geïnstalleerd op Windows Server 2008 en wanneer deze is geïnstalleerd op een domeincontroller. |
| [Gebruikersaccount](#user-account) | Express en op maat, 2017 maart en eerder | Tijdens de installatie wordt een lokaal account gemaakt dat vooraf is bevestigd met AAD_. Bij gebruik van aangepaste installatie kan een ander account worden opgegeven. |

Als u Connect with a build van maart 2017 of eerder gebruikt, moet u het wachtwoord van het serviceaccount niet opnieuw instellen, omdat Windows de versleutelingssleutels om veiligheidsredenen vernietigt. U het account niet wijzigen in een ander account zonder Azure AD Connect opnieuw te installeren. Als u een upgrade uitvoert naar een build vanaf april 2017 of later, wordt het ondersteund om het wachtwoord van het serviceaccount te wijzigen, maar u het gebruikte account niet wijzigen.

> [!Important]
> U het serviceaccount alleen instellen bij de eerste installatie. Het wordt niet ondersteund om het serviceaccount te wijzigen nadat de installatie is voltooid.

Dit is een tabel met de standaardopties, aanbevolen en ondersteunde opties voor het synchronisatieserviceaccount.

Legenda:

- **Vet** geeft de standaardoptie en in de meeste gevallen de aanbevolen optie aan.
- *Cursief* geeft de aanbevolen optie aan wanneer dit niet de standaardoptie is.
- 2008 - Standaardoptie wanneer deze is geïnstalleerd op Windows Server 2008
- Niet-vet - Ondersteunde optie
- Lokaal account - Lokaal gebruikersaccount op de server
- Domeinaccount - Domeingebruikersaccount
- sMSA - [standalone Managed Service-account](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA - [groepsManaged Service-account](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Aangepast telefoonnummer | Extern SQL</br>Aangepast telefoonnummer |
| --- | --- | --- | --- |
| **zelfstandige/werkgroepmachine** | Niet ondersteund | **VSA VSA**</br>Lokale rekening (2008)</br>Lokaal account |  Niet ondersteund |
| **domeinaangesloten machine** | **VSA VSA**</br>Lokale rekening (2008) | **VSA VSA**</br>Lokale rekening (2008)</br>Lokaal account</br>Domeinaccount</br>sMSA, gMSA | **gMSA**</br>Domeinaccount |
| **Domeincontroller** | **Domeinaccount** | *gMSA*</br>**Domeinaccount**</br>sMSA (sMSA)| *gMSA*</br>**Domeinaccount**|

#### <a name="virtual-service-account"></a>Virtuele serviceaccount
Een virtueel serviceaccount is een speciaal type account dat geen wachtwoord heeft en wordt beheerd door Windows.

![VSA VSA](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

De VSA is bedoeld om te worden gebruikt met scenario's waarin de synchronisatie-engine en SQL zich op dezelfde server bevinden. Als u externe SQL gebruikt, raden we u aan om in plaats daarvan een Group Managed Service-account te gebruiken.

Voor deze functie is Windows Server 2008 R2 of hoger vereist. Als u Azure AD Connect installeert op Windows Server 2008, wordt de installatie in plaats daarvan teruggezet naar het gebruik van een [gebruikersaccount.](#user-account)

#### <a name="group-managed-service-account"></a>Groepsbeheeraccount
Als u een externe SQL-server gebruikt, raden we u aan een **groepsbeheerserviceaccount**te gebruiken. Zie Overzicht van [groepsbeheerserviceaccounts](https://technet.microsoft.com/library/hh831782.aspx)voor meer informatie over het voorbereiden van uw Active Directory voor Group Managed Service-account.

Als u deze optie wilt gebruiken, selecteert u op de pagina [Vereiste onderdelen installeren](how-to-connect-install-custom.md#install-required-components) de optie Een bestaand **serviceaccount gebruiken**en selecteert u Managed **Service-account**.  
![VSA VSA](./media/reference-connect-accounts-permissions/serviceaccount.png)  
Het wordt ook ondersteund om een [standalone managed service-account](https://technet.microsoft.com/library/dd548356.aspx)te gebruiken. Deze kunnen echter alleen worden gebruikt op de lokale machine en er is geen voordeel om ze te gebruiken via het standaard virtuele serviceaccount.

Voor deze functie is Windows Server 2012 of hoger vereist. Als u een ouder besturingssysteem moet gebruiken en externe SQL moet gebruiken, moet u een [gebruikersaccount](#user-account)gebruiken.

#### <a name="user-account"></a>Gebruikersaccount
Een lokaal serviceaccount wordt gemaakt door de wizard installatie (tenzij u het account opgeeft dat moet worden gebruikt in aangepaste instellingen). Het account is vooraf vastgesteld **AAD_** en wordt gebruikt voor de werkelijke synchronisatieservice die wordt uitgevoerd als. Als u Azure AD Connect installeert op een domeincontroller, wordt het account in het domein gemaakt. Het **AAD_** serviceaccount moet zich in het domein bevinden als:
   - u een externe server met SQL-server gebruikt
   - u een proxy gebruikt waarvoor verificatie vereist is

![Serviceaccount synchroniseren](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

Het account wordt gemaakt met een lang complex wachtwoord dat niet verloopt.

Dit account wordt gebruikt om de wachtwoorden voor de andere accounts op een veilige manier op te slaan. Deze andere accounts wachtwoorden worden versleuteld opgeslagen in de database. De privésleutels voor de encryptiesleutels worden beveiligd met de geheime sleutelversleuteling van cryptografische diensten met Behulp van Windows Data Protection API (DPAPI).

Als u een volledige SQL Server gebruikt, is het serviceaccount de DBO van de gemaakte database voor de synchronisatieengine. De service werkt niet zoals bedoeld met andere machtigingen. Er wordt ook een SQL-login gemaakt.

Het account krijgt ook machtigingen voor bestanden, registersleutels en andere objecten die verband houden met de Sync Engine.

### <a name="azure-ad-connector-account"></a>Azure AD Connector-account
Er wordt een account in Azure AD gemaakt voor het gebruik van de synchronisatieservice. Dit account kan worden geïdentificeerd aan de andere naam.

![AD-account](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

De naam van de server waarop het account wordt gebruikt, kan worden geïdentificeerd in het tweede deel van de gebruikersnaam. Op de foto is de servernaam DC1. Als u tijdelijke servers hebt, heeft elke server zijn eigen account.

Het account wordt gemaakt met een lang complex wachtwoord dat niet verloopt. Het wordt verleend een speciale rol **Folder Synchronisatie Accounts** die alleen machtigingen heeft om directory synchronisatie taken uit te voeren. Deze speciale ingebouwde rol kan niet worden verleend buiten de wizard Azure AD Connect. De Azure-portal toont dit account met de rol **Gebruiker**.

Er is een limiet van 20 synchronisatieserviceaccounts in Azure AD. Voer de volgende Azure AD PowerShell-cmdlet uit om de lijst met bestaande Azure AD-serviceaccounts in uw Azure AD-cmdlet uit te voeren:`Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Als u ongebruikte Azure AD-serviceaccounts wilt verwijderen, voert u de volgende Azure AD PowerShell-cmdlet uit:`Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

>[!NOTE]
>Voordat u de bovenstaande PowerShell-opdrachten gebruiken, moet u de [Azure Active Directory PowerShell voor Grafiekmodule](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0#installing-the-azure-ad-module) installeren en verbinding maken met uw exemplaar van Azure AD met [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0)

Zie Het [Azure AD Connect-account beheren](how-to-connect-azureadaccount.md) of opnieuw instellen voor meer informatie over het beheren of opnieuw instellen van het wachtwoord voor het Azure AD Connector-account

## <a name="related-documentation"></a>Verwante documentatie
Als u de documentatie over [het integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md)niet hebt gelezen, bevat de volgende tabel koppelingen naar gerelateerde onderwerpen.

|Onderwerp |Koppeling|  
| --- | --- |
|Azure AD Connect downloaden | [Azure AD Connect downloaden](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Installeren met de snelle instellingen | [Snelle installatie van Azure AD Connect](how-to-connect-install-express.md)|
|Installeren met behulp van aangepaste instellingen | [Aangepaste installatie van Azure AD Connect](./how-to-connect-install-custom.md)|
|Upgrade van DirSync | [Upgrade van Azure AD-synchronisatiehulpprogramma (DirSync) (Engelstalig artikel)](how-to-dirsync-upgrade-get-started.md)|
|Na installatie | [Controleer de installatie en wijs licenties toe](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
