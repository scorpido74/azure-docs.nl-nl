---
title: 'Azure AD Connect: ondersteunde topologieën | Microsoft Documenten'
description: In dit onderwerp worden ondersteunde en niet-ondersteunde topologieën voor Azure AD Connect beschreven
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: conceptual
ms.date: 11/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9618e02f54fbb2a3b92771761c5fcf700d126b5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253830"
---
# <a name="topologies-for-azure-ad-connect"></a>Topologieën voor Azure AD Connect
In dit artikel worden verschillende on-premises en Azure Active Directory -topologieën (Azure AD) beschreven die Azure AD Connect-synchronisatie gebruiken als de oplossing voor de belangrijkste integratie. Dit artikel bevat zowel ondersteunde als niet-ondersteunde configuraties.


Hier is de legende voor foto's in het artikel:

| Beschrijving | Symbool |
| --- | --- |
| On-premises Active Directory-forest |![On-premises Active Directory-forest](./media/plan-connect-topologies/LegendAD1.png) |
| On-premises Active Directory met gefilterde import |![Active Directory met gefilterde import](./media/plan-connect-topologies/LegendAD2.png) |
| Synchronisatieserver van Azure AD Connect |![Synchronisatieserver van Azure AD Connect](./media/plan-connect-topologies/LegendSync1.png) |
| Synchronisatiemodus voor Azure AD Connect |![Synchronisatiemodus voor Azure AD Connect](./media/plan-connect-topologies/LegendSync2.png) |
| GALSync met Forefront Identity Manager (FIM) 2010 of Microsoft Identity Manager (MIM) 2016 |![GALSync met FIM 2010 of MIM 2016](./media/plan-connect-topologies/LegendSync3.png) |
| Azure AD Connect-synchronisatieserver, gedetailleerd |![Azure AD Connect-synchronisatieserver, gedetailleerd](./media/plan-connect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/plan-connect-topologies/LegendAAD.png) |
| Niet-ondersteund scenario |![Niet-ondersteund scenario](./media/plan-connect-topologies/LegendUnsupported.png) |


> [!IMPORTANT]
> Microsoft biedt geen ondersteuning voor het wijzigen of uitvoeren van Azure AD Connect-synchronisatie buiten de configuraties of acties die formeel zijn gedocumenteerd. Een van deze configuraties of acties kan resulteren in een inconsistente of niet-ondersteunde status van Azure AD Connect-synchronisatie. Als gevolg hiervan kan Microsoft geen technische ondersteuning bieden voor dergelijke implementaties.


## <a name="single-forest-single-azure-ad-tenant"></a>Eén forest, één Azure AD-tenant
![Topologie voor één forest en één tenant](./media/plan-connect-topologies/SingleForestSingleDirectory.png)

De meest voorkomende topologie is één on-premises forest, met één of meerdere domeinen en één Azure AD-tenant. Voor Azure AD-verificatie wordt wachtwoordhashsynchronisatie gebruikt. De expresinstallatie van Azure AD Connect ondersteunt alleen deze topologie.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Eén forest, meerdere synchronisatieservers naar één Azure AD-tenant
![Niet-ondersteunde, gefilterde topologie voor één forest](./media/plan-connect-topologies/SingleForestFilteredUnsupported.png)

Meerdere Azure AD Connect-synchronisatieservers die zijn verbonden met dezelfde Azure AD-tenant worden niet ondersteund, behalve een [faseringsserver.](#staging-server) Het wordt niet ondersteund, zelfs als deze servers zijn geconfigureerd om te synchroniseren met een wederzijds exclusieve set objecten. Mogelijk hebt u deze topologie overwogen als u niet alle domeinen in het forest vanaf één server bereiken of als u de belasting over meerdere servers wilt verdelen.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Meerdere forests, één Azure AD-tenant
![Topologie voor meerdere forests en één tenant](./media/plan-connect-topologies/MultiForestSingleDirectory.png)

Veel organisaties hebben omgevingen met meerdere on-premises Active Directory-forests. Er zijn verschillende redenen voor het hebben van meer dan één on-premises Active Directory-forest. Typische voorbeelden zijn ontwerpen met accountresources bossen en het resultaat van een fusie of overname.

Wanneer u meerdere forests hebt, moeten alle forests bereikbaar zijn via één Azure AD Connect-synchronisatieserver. De server moet worden samengevoegd met een domein. Indien nodig om alle forests te bereiken, u de server in een perimeternetwerk plaatsen (ook bekend als DMZ, gedemilitariseerde zone en gescreend subnet).

De wizard Installatie van Azure AD Connect biedt verschillende opties om gebruikers te consolideren die in meerdere forests zijn vertegenwoordigd. Het doel is dat een gebruiker slechts één keer wordt vertegenwoordigd in Azure AD. Er zijn een aantal veelvoorkomende topologieën die u configureren in het aangepaste installatiepad in de wizard installatie. Selecteer **op** de pagina Uw gebruikers uniek identificeren de bijbehorende optie die uw topologie vertegenwoordigt. De consolidatie is alleen geconfigureerd voor gebruikers. Dubbele groepen worden niet geconsolideerd met de standaardconfiguratie.

Gemeenschappelijke topologieën worden besproken in de secties over afzonderlijke topologieën, [volledige mesh](#multiple-forests-full-mesh-with-optional-galsync)en [de account-resource topologie](#multiple-forests-account-resource-forest).

De standaardconfiguratie in Azure AD Connect-synchronisatie gaat ervan uit:

* Elke gebruiker heeft slechts één account ingeschakeld en het forest waarin dit account zich bevindt, wordt gebruikt om de gebruiker te verifiëren. Deze veronderstelling is voor wachtwoord hash sync, pass-through authenticatie en federatie. UserPrincipalName en sourceAnchor/immutableID komen uit dit forest.
* Elke gebruiker heeft slechts één postvak.
* Het forest dat het postvak host voor een gebruiker, heeft de beste gegevenskwaliteit voor kenmerken die zichtbaar zijn in de Exchange Global Address List (GAL). Als er geen postvak voor de gebruiker is, kan elk forest worden gebruikt om deze kenmerkwaarden bij te dragen.
* Als u een gekoppeld postvak hebt, is er ook een account in een ander forest dat wordt gebruikt voor aanmelding.

Als uw omgeving niet overeenkomt met deze veronderstellingen, gebeuren de volgende dingen:

* Als u meer dan één actief account of meer dan één postvak hebt, kiest de synchronisatieengine het en wordt het andere genegeerd.
* Een gekoppeld postvak met geen ander actief account wordt niet geëxporteerd naar Azure AD. Het gebruikersaccount is in geen enkele groep vertegenwoordigd als lid. Een gekoppeld postvak in DirSync wordt altijd weergegeven als een normaal postvak. Deze wijziging is opzettelijk een ander gedrag om scenario's met meerdere forest's beter te ondersteunen.

Meer details vindt u in [Het begrijpen van de standaardconfiguratie.](concept-azure-ad-connect-sync-default-configuration.md)

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Meerdere forests, meerdere synchronisatieservers naar één Azure AD-tenant
![Niet-ondersteunde topologie voor meerdere forests en meerdere synchronisatieservers](./media/plan-connect-topologies/MultiForestMultiSyncUnsupported.png)

Als meer dan één Azure AD Connect-synchronisatieserver is verbonden met één Azure AD-tenant, wordt deze niet ondersteund. De uitzondering is het gebruik van een [staging server](#staging-server).

Deze topologie verschilt van de onderstaande in dat **meerdere synchronisatieservers** die zijn verbonden met één Azure AD-tenant niet worden ondersteund.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>Meerdere forests, single sync server, gebruikers zijn vertegenwoordigd in slechts één map
![Optie voor het vertegenwoordigen van gebruikers slechts één keer in alle mappen](./media/plan-connect-topologies/MultiForestUsersOnce.png)

![Afbeelding van meerdere bossen en afzonderlijke topologieën](./media/plan-connect-topologies/MultiForestSeparateTopologies.png)

In deze omgeving worden alle on-premises bossen behandeld als afzonderlijke entiteiten. Er is geen gebruiker aanwezig in een ander forest. Elk bos heeft zijn eigen Exchange-organisatie en er is geen GALSync tussen de bossen. Deze topologie kan de situatie zijn na een fusie/overname of in een organisatie waar elke business unit onafhankelijk opereert. Deze forests bevinden zich in dezelfde organisatie in Azure AD en worden weergegeven met een uniforme GAL. In de vorige afbeelding wordt elk object in elk forest eenmaal weergegeven in de metaverse en samengevoegd in de doel-Azure AD-tenant.

### <a name="multiple-forests-match-users"></a>Meerdere forests: match gebruikers
Gemeenschappelijk voor al deze scenario's is dat distributie- en beveiligingsgroepen een mix van gebruikers, contactpersonen en FSP's (Foreign Security Principals) kunnen bevatten. FSP's worden gebruikt in Active Directory Domain Services (AD DS) om leden uit andere forests in een beveiligingsgroep te vertegenwoordigen. Alle FSP's zijn opgelost om het echte object in Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Meerdere forests: volledig gaas met optionele GALSync
![Optie voor het gebruik van het e-mailkenmerk voor het afstemmen wanneer gebruikersidentiteiten bestaan in meerdere mappen](./media/plan-connect-topologies/MultiForestUsersMail.png)

![Volledige mesh-topologie voor meerdere forests](./media/plan-connect-topologies/MultiForestFullMesh.png)

Een volledige mesh-topologie stelt gebruikers en bronnen in staat om zich in elk forest te bevinden. Algemeen zijn er twee-weg trusts tussen de bossen.

Als Exchange in meer dan één forest aanwezig is, is er mogelijk (optioneel) een on-premises GALSync-oplossing. Elke gebruiker wordt dan vertegenwoordigd als contactpersoon in alle andere bossen. GALSync wordt vaak geïmplementeerd via FIM 2010 of MIM 2016. Azure AD Connect kan niet worden gebruikt voor on-premises GALSync.

In dit scenario worden identiteitsobjecten samengevoegd via het e-mailkenmerk. Een gebruiker die een postvak in het ene forest heeft, wordt samengevoegd met de contactpersonen in de andere forests.

### <a name="multiple-forests-account-resource-forest"></a>Meerdere forests: accountresourceforest
![Optie voor het gebruik van de kenmerken ObjectSID en msExchMasterAccountSID voor matching wanneer identiteiten bestaan in meerdere mappen](./media/plan-connect-topologies/MultiForestUsersObjectSID.png)

![Account-resource foresttopologie voor meerdere forests](./media/plan-connect-topologies/MultiForestAccountResource.png)

In een foresttopologie van accountresources hebt u een of meer *accountforests* met actieve gebruikersaccounts. U hebt ook een of meer *resourceforests* met uitgeschakelde accounts.

In dit scenario vertrouwt één (of meer) resourceforest alle accountforests. Het resourceforest heeft doorgaans een uitgebreid Active Directory-schema met Exchange en Lync. Alle Exchange- en Lync-services bevinden zich in dit forest, samen met andere gedeelde services. Gebruikers hebben een uitgeschakeld gebruikersaccount in dit forest en het postvak is gekoppeld aan het accountforest.

## <a name="office-365-and-topology-considerations"></a>Overwegingen voor Office 365 en topologie
Sommige Office 365-workloads hebben bepaalde beperkingen op ondersteunde topologieën:

| Workload | Beperkingen |
| --------- | --------- |
| Exchange Online | Zie [Hybride implementaties met meerdere Active Directory-forests](https://technet.microsoft.com/library/jj873754.aspx)voor meer informatie over hybride topologieën die worden ondersteund door Exchange Online. |
| Skype voor Bedrijven | Wanneer u meerdere on-premises forests gebruikt, wordt alleen de foresttopologie van de accountbron ondersteund. Zie [Milieuvereisten voor Skype voor Bedrijven Server 2015 voor](https://technet.microsoft.com/library/dn933910.aspx)meer informatie. |

Als u een grotere organisatie bent, u overwegen de functie [Office 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) te gebruiken. Hiermee u bepalen in welk datacentergebied de resources van de gebruiker zich bevinden.

## <a name="staging-server"></a>Faseringsserver
![Staging server in een topologie](./media/plan-connect-topologies/MultiForestStaging.png)

Azure AD Connect ondersteunt het installeren van een tweede server in *de faseringsmodus.* Een server in deze modus leest gegevens van alle verbonden mappen, maar schrijft niets naar verbonden mappen. Het maakt gebruik van de normale synchronisatiecyclus en heeft daarom een bijgewerkte kopie van de identiteitsgegevens.

In een ramp waarbij de primaire server uitvalt, u uitvallen naar de staging-server. U doet dit in de wizard Azure AD Connect. Deze tweede server kan zich in een ander datacenter bevinden omdat er geen infrastructuur wordt gedeeld met de primaire server. U moet elke configuratiewijziging die op de primaire server is aangebracht handmatig kopiëren naar de tweede server.

U een faseringsserver gebruiken om een nieuwe aangepaste configuratie en het effect dat deze heeft op uw gegevens te testen. U een voorbeeld van de wijzigingen bekijken en de configuratie aanpassen. Wanneer u tevreden bent met de nieuwe configuratie, u van de stagingserver de actieve server maken en de oude actieve server instellen op de faseringsmodus.

U deze methode ook gebruiken om de actieve synchronisatieserver te vervangen. Bereid de nieuwe server voor en stel deze in op de faseringsmodus. Zorg ervoor dat het in een goede staat is, schakel de faseringsmodus uit (waardoor deze actief is) en sluit de momenteel actieve server af.

Het is mogelijk om meer dan één staging-server te hebben wanneer u meerdere back-ups in verschillende datacenters wilt hebben.

## <a name="multiple-azure-ad-tenants"></a>Meerdere Azure AD-tenants
We raden u aan één tenant in Azure AD te hebben voor een organisatie.
Voordat u meerdere Azure AD-tenants wilt gebruiken, raadpleegt u het beheer van het artikel [Beheer van de eenheden in Azure AD](../users-groups-roles/directory-administrative-units.md). Het omvat veelvoorkomende scenario's waarin u één tenant gebruiken.

![Topologie voor meerdere forests en meerdere tenants](./media/plan-connect-topologies/MultiForestMultiDirectory.png)

Er is een 1:1-relatie tussen een Azure AD Connect-synchronisatieserver en een Azure AD-tenant. Voor elke Azure AD-tenant hebt u één Azure AD Connect-synchronisatieserverinstallatie nodig. De azure AD-tenantexemplaren worden geïsoleerd door het ontwerp. Dat wil zeggen dat gebruikers in de ene tenant geen gebruikers in de andere tenant kunnen zien. Als u deze scheiding wilt, is dit een ondersteunde configuratie. Anders moet u het enige Azure AD-tenantmodel gebruiken.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Elk object slechts één keer in een Azure AD-tenant
![Gefilterde topologie voor één forest](./media/plan-connect-topologies/SingleForestFiltered.png)

In deze topologie is één Azure AD Connect-synchronisatieserver verbonden met elke Azure AD-tenant. De Azure AD Connect-synchronisatieservers moeten zijn geconfigureerd voor filtering, zodat elk een wederzijds exclusieve set objecten heeft waarop ze moeten werken. U bijvoorbeeld elke server naar een bepaald domein of organisatie-eenheid toepassingsgebied.

Een DNS-domein kan slechts in één Azure AD-tenant worden geregistreerd. De UPN's van de gebruikers in de on-premises Active Directory-instantie moeten ook afzonderlijke naamruimten gebruiken. In de vorige afbeelding worden bijvoorbeeld drie afzonderlijke UPN-achtervoegsels geregistreerd in de on-premises Active Directory-instantie: contoso.com, fabrikam.com en wingtiptoys.com. De gebruikers in elk on-premises Active Directory-domein gebruiken een andere naamruimte.

>[!NOTE]
>Global Address List Synchronization (GalSync) wordt niet automatisch uitgevoerd in deze topologie en vereist een extra aangepaste MIM-implementatie om ervoor te zorgen dat elke tenant een volledige algemene adreslijst (GAL) heeft in Exchange Online en Skype voor Bedrijven Online.


Deze topologie heeft de volgende beperkingen voor anders ondersteunde scenario's:

* Slechts één van de Azure AD-tenants kan een Exchange-hybride inschakelen met het on-premises Active Directory-exemplaar.
* Windows 10-apparaten kunnen worden gekoppeld aan slechts één Azure AD-tenant.
* De optie Single Sign-on (SSO) voor wachtwoordhashsynchronisatie en pass-through-verificatie kan worden gebruikt met slechts één Azure AD-tenant.

De eis voor een wederzijds exclusieve set objecten geldt ook voor terugschrijven. Sommige writeback-functies worden niet ondersteund met deze topologie omdat ze uitgaan van één on-premises configuratie. Deze functies omvatten onder andere:

* Terugschrijven groeperen met standaardconfiguratie.
* Apparaat terugschrijven.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Elk object meerdere keren in een Azure AD-tenant
![Niet-ondersteunde topologie voor één forest en meerdere tenants](./media/plan-connect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Niet-ondersteunde topologie voor één forest en meerdere connectors](./media/plan-connect-topologies/SingleForestMultiConnectorsUnsupported.png)

Deze taken worden niet ondersteund:

* Synchroniseer dezelfde gebruiker met meerdere Azure AD-tenants.
* Breng een configuratiewijziging aan zodat gebruikers in een Azure AD-tenant worden weergegeven als contactpersonen in een andere Azure AD-tenant.
* Wijzig azure AD Connect-synchronisatie om verbinding te maken met meerdere Azure AD-tenants.

### <a name="galsync-by-using-writeback"></a>GALSync met behulp van writeback
![Niet-ondersteunde topologie voor meerdere forests en meerdere mappen, waarbij GALSync zich richt op Azure AD](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Niet-ondersteunde topologie voor meerdere forests en meerdere mappen, waarbij GALSync zich richt op on-premises Active Directory](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Azure AD-tenants worden geïsoleerd door het ontwerp. Deze taken worden niet ondersteund:

* Wijzig de configuratie van Azure AD Connect-synchronisatie om gegevens uit een andere Azure AD-tenant te lezen.
* Exporteer gebruikers als contactpersonen naar een ander on-premises Active Directory-exemplaar met Azure AD Connect-synchronisatie.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync met on-premises synchronisatieserver
![GALSync in een topologie voor meerdere forests en meerdere mappen](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync.png)

U FIM 2010 of MIM 2016 on-premises gebruiken om gebruikers (via GALSync) te synchroniseren tussen twee Exchange-organisaties. De gebruikers in de ene organisatie worden weergegeven als buitenlandse gebruikers/contactpersonen in de andere organisatie. Deze verschillende on-premises Active Directory-exemplaren kunnen vervolgens worden gesynchroniseerd met hun eigen Azure AD-tenants.

## <a name="next-steps"></a>Volgende stappen
Zie [Aangepaste installatie van Azure AD Connect](how-to-connect-install-custom.md)voor meer informatie over het installeren van Azure AD Connect voor deze scenario's.

Meer informatie over de [synchronisatieconfiguratie van Azure AD Connect.](how-to-connect-sync-whatis.md)

Meer informatie over [het integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
