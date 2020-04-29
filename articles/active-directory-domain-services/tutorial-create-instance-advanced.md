---
title: Zelf studie-een Azure Active Directory Domain Services-exemplaar maken | Microsoft Docs
description: In deze zelf studie leert u hoe u een Azure Active Directory Domain Services-exemplaar kunt maken en configureren en geavanceerde configuratie opties kunt opgeven met behulp van de Azure Portal.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: f2d7f1725623dcc031f3c2b36bacd6dbc9ad339d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81639956"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance-with-advanced-configuration-options"></a>Zelf studie: een Azure Active Directory Domain Services-exemplaar maken en configureren met geavanceerde configuratie opties

Azure Active Directory Domain Services (Azure AD DS) biedt beheerde domein Services, zoals domein deelname, groeps beleid, LDAP, Kerberos/NTLM-verificatie die volledig compatibel is met Windows Server Active Directory. U gebruikt deze domein Services zonder zelf domein controllers te implementeren, beheren en repareren. Azure AD DS integreert met uw bestaande Azure AD-Tenant. Met deze integratie kunnen gebruikers zich aanmelden met hun bedrijfs referenties, en kunt u bestaande groepen en gebruikers accounts gebruiken om de toegang tot bronnen te beveiligen.

U kunt [een beheerd domein maken met behulp van standaard configuratie opties][tutorial-create-instance] voor netwerken en synchronisatie, of deze instellingen hand matig definiëren. In deze zelf studie leert u hoe u deze geavanceerde configuratie opties definieert voor het maken en configureren van een Azure AD DS-exemplaar met behulp van de Azure Portal.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * DNS-en virtuele netwerk instellingen configureren voor een beheerd domein
> * Een Azure AD DS-exemplaar maken
> * Gebruikers met beheerders rechten toevoegen aan domein beheer
> * Wachtwoord-hashsynchronisatie inschakelen

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources en bevoegdheden nodig om deze zelf studie te volt ooien:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory Tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises Directory of een alleen-Cloud Directory.
    * Als dat nodig is, [maakt u een Azure Active Directory-Tenant][create-azure-ad-tenant] of [koppelt u een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* U hebt *globale beheerders* bevoegdheden nodig in uw Azure AD-Tenant om Azure AD DS in te scha kelen.
* U hebt *Inzender* bevoegdheden nodig in uw Azure-abonnement om de vereiste Azure AD DS-resources te maken.

Hoewel het niet vereist is voor Azure AD DS, is het raadzaam om [selfservice voor wachtwoord herstel (SSPR)][configure-sspr] in te stellen voor de Azure AD-Tenant. Gebruikers kunnen hun wacht woord wijzigen zonder SSPR, maar SSPR helpt als ze hun wacht woord verg eten en het opnieuw moeten instellen.

> [!IMPORTANT]
> Nadat u een door Azure AD DS beheerd domein hebt gemaakt, kunt u het exemplaar niet verplaatsen naar een andere resource groep, een virtueel netwerk, een abonnement, enzovoort. Zorg ervoor dat u het meest geschikte abonnement, de resource groep, de regio en het virtuele netwerk selecteert wanneer u het Azure AD DS-exemplaar implementeert.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In deze zelf studie maakt en configureert u het Azure AD DS-exemplaar met behulp van de Azure Portal. Meld u eerst aan bij de [Azure Portal](https://portal.azure.com)om aan de slag te gaan.

## <a name="create-an-instance-and-configure-basic-settings"></a>Een exemplaar maken en basis instellingen configureren

Voer de volgende stappen uit om de wizard **Azure AD Domain Services inschakelen** te starten:

1. Selecteer in het menu van Azure Portal of op de **Startpagina** de optie **Een resource maken**.
1. Voer *domein Services* in de zoek balk in en kies *Azure AD Domain Services* in de zoek suggesties.
1. Selecteer op de pagina Azure AD Domain Services **maken**. De wizard **Azure AD Domain Services inschakelen** wordt gestart.
1. Selecteer het Azure- **abonnement** waarin u het beheerde domein wilt maken.
1. Selecteer de **resource groep** waarvan het beheerde domein deel moet uitmaken. Maak een **nieuwe** resource groep of selecteer een bestaande.

Wanneer u een exemplaar van Azure AD DS maakt, geeft u een DNS-naam op. Er zijn enkele aandachtspunten bij het kiezen van deze DNS-naam:

* **Ingebouwde domein naam:** De ingebouwde domein naam van de Directory wordt standaard gebruikt (een *. onmicrosoft.com* -achtervoegsel). Als u beveiligde LDAP-toegang tot het beheerde domein via internet wilt inschakelen, kunt u geen digitaal certificaat maken om de verbinding met dit standaard domein te beveiligen. Micro soft is eigenaar van het *onmicrosoft.com* -domein, waardoor een certificerings instantie (CA) geen certificaat kan uitgeven.
* **Aangepaste domein namen:** De meest voorkomende benadering is het opgeven van een aangepaste domein naam, meestal een die u al bezit en routeerbaar is. Wanneer u een routeerbaar, aangepast domein gebruikt, kan het verkeer op de juiste wijze worden uitgevoerd om uw toepassingen te ondersteunen.
* **Niet-routeerbaar domein achtervoegsels:** U wordt geadviseerd om een niet-routeerbaar domein naam achtervoegsel, zoals *contoso. local*, te voor komen. Het achtervoegsel *. local* is niet routeerbaar en kan problemen met de DNS-omzetting veroorzaken.

> [!TIP]
> Als u een aangepaste domein naam maakt, moet u rekening houden met bestaande DNS-naam ruimten. Het is raadzaam een domein naam te gebruiken die losstaat van een bestaande Azure-of on-premises DNS-naam ruimte.
>
> Als u bijvoorbeeld een bestaande DNS-naam ruimte van *contoso.com*hebt, maakt u een door Azure AD DS beheerd domein met de aangepaste domein naam *aaddscontoso.com*. Als u beveiligde LDAP wilt gebruiken, moet u deze aangepaste domein naam registreren en de vereiste certificaten genereren.
>
> Mogelijk moet u enkele extra DNS-records maken voor andere services in uw omgeving, of voorwaardelijke DNS-doorstuur servers tussen bestaande DNS-naam ruimten in uw omgeving. Als u bijvoorbeeld een webserver uitvoert die als host fungeert voor een-site met behulp van de DNS-naam van de basis, kan er sprake zijn van naam conflicten waarvoor extra DNS-vermeldingen zijn vereist.
>
> In deze zelf studies en artikelen met procedures wordt het aangepaste domein *aaddscontoso.com* als een kort voor beeld gebruikt. Geef in alle opdrachten uw eigen domein naam op.

De volgende DNS-naam beperkingen zijn ook van toepassing:

* **Beperkingen voor domein voorvoegsels:** U kunt geen beheerd domein met een voor voegsel maken dat langer is dan 15 tekens. Het voor voegsel van uw opgegeven domein naam (zoals *aaddscontoso* in de domein naam *aaddscontoso.com* ) mag Maxi maal 15 tekens bevatten.
* **Conflicten met netwerk naam:** De DNS-domein naam voor uw beheerde domein mag niet al bestaan in het virtuele netwerk. Controleer met name op de volgende scenario's die leiden tot een naam conflict:
    * Als u al een Active Directory domein met dezelfde DNS-domein naam hebt in het virtuele Azure-netwerk.
    * Als het virtuele netwerk waar u het beheerde domein wilt inschakelen, een VPN-verbinding heeft met uw on-premises netwerk. In dit scenario zorgt u ervoor dat u geen domein hebt met dezelfde DNS-domein naam in uw on-premises netwerk.
    * Als u een bestaande Azure-Cloud service hebt met die naam in het virtuele Azure-netwerk.

Vul de velden in het venster *basis beginselen* van het Azure Portal in om een Azure AD DS-exemplaar te maken:

1. Voer een **DNS-domein naam** in voor uw beheerde domein, waarbij rekening wordt gehouden met de vorige punten.
1. Kies de Azure- **locatie** waar het beheerde domein moet worden gemaakt. Als u een regio kiest die Beschikbaarheidszones ondersteunt, worden de Azure AD DS-resources verdeeld over zones voor extra redundantie.

    Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Tolerantie wordt gegarandeerd door aanwezigheid van minimaal drie afzonderlijke zones in alle actieve regio's.

    Er is niets waarmee u kunt configureren voor Azure AD DS worden gedistribueerd over meerdere zones. Het Azure-platform verwerkt automatisch de zone distributie van resources. Zie [Wat zijn Beschikbaarheidszones in azure?][availability-zones] voor meer informatie en om de beschik baarheid van regio's te bekijken.

1. De **SKU** bepaalt de prestaties, de back-upfrequentie en het maximum aantal forest-vertrouwens relaties die u kunt maken. Als uw bedrijfs vereisten of-vereisten veranderen, kunt u de SKU wijzigen nadat het beheerde domein is gemaakt. Zie [Azure AD DS SKU-concepten][concepts-sku]voor meer informatie.

    Voor deze zelf studie selecteert u de *standaard* -SKU.
1. Een *forest* is een logische constructie die door Active Directory Domain Services wordt gebruikt om een of meer domeinen te groeperen. Standaard wordt een door Azure AD DS beheerd domein gemaakt als een *gebruikers* forest. Dit type forest synchroniseert alle objecten van Azure AD, met inbegrip van gebruikers accounts die zijn gemaakt in een on-premises AD DS omgeving. Een *resource* -forest synchroniseert alleen gebruikers en groepen die rechtstreeks in azure AD zijn gemaakt. Bron-forests zijn momenteel beschikbaar als preview-versie. Zie [overzicht van Azure AD DS-resource forests][resource-forests]voor meer informatie over *bron* -forests, waaronder waarom u er één kunt gebruiken en hoe u forest-vertrouwens relaties maakt met on-premises AD DS domeinen.

    Voor deze zelf studie kiest u ervoor om een *gebruikers* forest te maken.

    ![Basis instellingen voor een Azure AD Domain Services-exemplaar configureren](./media/tutorial-create-instance-advanced/basics-window.png)

1. Als u extra opties hand matig wilt configureren, kiest u **volgende netwerk**. Als dat niet het geval is, selecteert u **controleren + maken** om de standaard configuratie opties te accepteren en gaat u naar de sectie voor het [implementeren van uw beheerde domein](#deploy-the-managed-domain). De volgende standaard waarden worden geconfigureerd wanneer u deze Maak optie kiest:

    * Hiermee maakt u een virtueel netwerk met de naam *aadds-vnet* dat gebruikmaakt van het IP-adres bereik van *10.0.1.0/24*.
    * Hiermee maakt u een subnet met de naam *aadds-subnet* met het IP-adres bereik *10.0.1.0/24*.
    * Synchroniseert *alle* gebruikers van Azure AD in het door Azure AD DS beheerde domein.

## <a name="create-and-configure-the-virtual-network"></a>Het virtuele netwerk maken en configureren

Als u verbinding wilt maken, hebt u een virtueel Azure-netwerk en een toegewezen subnet nodig. Azure AD DS is ingeschakeld in dit subnet van het virtuele netwerk. In deze zelf studie maakt u een virtueel netwerk, maar u kunt er in plaats daarvan voor kiezen om een bestaand virtueel netwerk te gebruiken. Bij beide benaderingen moet u een toegewezen subnet maken voor gebruik door Azure AD DS.

Enkele aandachtspunten voor dit toegewezen subnet van het virtuele netwerk bevatten de volgende gebieden:

* Het adres bereik van het subnet moet ten minste 3-5 beschik bare IP-adressen bevatten ter ondersteuning van de Azure AD DS-resources.
* Selecteer niet het subnet van de *Gateway* voor het implementeren van Azure AD DS. Het wordt niet ondersteund voor het implementeren van Azure-AD DS in een *Gateway* -subnet.
* Implementeer geen andere virtuele machines in het subnet. Toepassingen en Vm's gebruiken vaak netwerk beveiligings groepen om de connectiviteit te beveiligen. Door deze werk belastingen uit te voeren in een afzonderlijk subnet kunt u deze netwerk beveiligings groepen Toep assen zonder de verbinding met uw beheerde domein te onderbreken.
* U kunt uw beheerde domein niet naar een ander virtueel netwerk verplaatsen nadat u Azure AD DS hebt ingeschakeld.

Zie [netwerk overwegingen voor Azure Active Directory Domain Services][network-considerations]voor meer informatie over het plannen en configureren van het virtuele netwerk.

Vul de velden in het venster *netwerk* als volgt in:

1. Kies op de pagina **netwerk** een virtueel netwerk waarop u Azure AD DS wilt implementeren in de vervolg keuzelijst of selecteer **nieuwe maken**.
    1. Als u ervoor kiest om een virtueel netwerk te maken, voert u een naam in voor het virtuele netwerk, zoals *myVnet*, en geeft u een adres bereik op, zoals *10.0.1.0/24*.
    1. Maak een toegewezen subnet met een duidelijke naam, zoals *DomainServices*. Geef een adres bereik op, bijvoorbeeld *10.0.1.0/24*.

    [![](./media/tutorial-create-instance-advanced/create-vnet.png "Create a virtual network and subnet for use with Azure AD Domain Services")](./media/tutorial-create-instance-advanced/create-vnet-expanded.png#lightbox)

    Zorg ervoor dat u een adres bereik kiest dat binnen uw privé-IP-adres bereik valt. IP-adresbereiken die zich in de open bare adres ruimte bevinden, veroorzaken fouten in azure AD DS.

1. Selecteer een subnet van een virtueel netwerk, zoals *DomainServices*.
1. Als u klaar bent, kiest u **volgende beheer**.

## <a name="configure-an-administrative-group"></a>Een beheer groep configureren

Een speciale beheer groep met de naam *Aad DC-Administrators* wordt gebruikt voor het beheer van het Azure AD DS-domein. Aan leden van deze groep worden beheerders machtigingen verleend op Vm's die zijn gekoppeld aan het beheerde domein. Op Vm's die lid zijn van een domein, wordt deze groep toegevoegd aan de lokale groep Administrators. Leden van deze groep kunnen ook Extern bureaublad gebruiken om extern verbinding te maken met virtuele machines die lid zijn van een domein.

U hebt geen machtigingen voor *domein Administrator* of *ondernemings Administrator* voor een beheerd domein met Azure AD DS. Deze machtigingen zijn gereserveerd door de service en worden niet beschikbaar gesteld voor gebruikers binnen de Tenant. In plaats daarvan kunt u met de groep *Aad DC-Administrators* bepaalde geprivilegieerde bewerkingen uitvoeren. Deze bewerkingen behoren tot de beheer groep op Vm's die lid zijn van een domein en het configureren van groepsbeleid.

De wizard maakt automatisch de groep *Aad DC-Administrators* in uw Azure AD-adres lijst. Als u een bestaande groep met deze naam in uw Azure AD-adres lijst hebt, wordt deze groep door de wizard geselecteerd. U kunt ervoor kiezen om tijdens het implementatie proces extra gebruikers toe te voegen aan deze groep *Aad DC-Administrators* . Deze stappen kunnen later worden uitgevoerd.

1. Om extra gebruikers toe te voegen aan deze groep *Aad DC-Administrators* , selecteert u **groepslid maatschap beheren**.

    ![Groepslid maatschap van de groep AAD DC-Administrators configureren](./media/tutorial-create-instance-advanced/admin-group.png)

1. Selecteer de knop **leden toevoegen** , zoek naar en selecteer gebruikers in uw Azure AD-adres lijst. Zoek bijvoorbeeld naar uw eigen account en voeg deze toe aan de groep *Aad DC-Administrators* .
1. Indien gewenst, wijzigt of voegt u extra ontvangers voor meldingen toe wanneer er waarschuwingen zijn in het door Azure AD DS beheerde domein waarvoor aandacht is vereist.
1. Wanneer u klaar bent, kiest u **volgende synchronisatie**.

## <a name="configure-synchronization"></a>Synchronisatie configureren

Met Azure AD DS kunt u *alle* gebruikers en groepen synchroniseren die beschikbaar zijn in azure AD of een synchronisatie met een *bereik* van alleen specifieke groepen. Als u ervoor kiest om *alle* gebruikers en groepen te synchroniseren, kunt u er later niet voor kiezen om alleen een synchronisatie met een bereik uit te voeren. Zie [Azure AD Domain Services scoped Synchronization][scoped-sync](Engelstalig) voor meer informatie over het bereik van synchronisatie.

1. Voor deze zelf studie kiest u voor het synchroniseren van **alle** gebruikers en groepen. Deze synchronisatie keuze is de standaard optie.

    ![Een volledige synchronisatie uitvoeren van gebruikers en groepen vanuit Azure AD](./media/tutorial-create-instance-advanced/sync-all.png)

1. Selecteer **controleren + maken**.

## <a name="deploy-the-managed-domain"></a>Het beheerde domein implementeren

Controleer op de pagina **samen vatting** van de wizard de configuratie-instellingen voor het beheerde domein. U kunt terugkeren naar elke stap van de wizard om wijzigingen aan te brengen. Als u een Azure AD DS beheerd domein op een consistente manier opnieuw wilt implementeren in een andere Azure AD-Tenant met behulp van deze configuratie opties, kunt u ook **een sjabloon voor Automation downloaden**.

1. Selecteer **maken**om het beheerde domein te maken. Er wordt een opmerking weer gegeven dat bepaalde configuratie opties, zoals de DNS-naam of het virtuele netwerk, niet kunnen worden gewijzigd nadat de beheerde Azure-AD DS is gemaakt. Selecteer **OK**om door te gaan.
1. Het proces van het inrichten van uw beheerde domein kan tot een uur duren. Er wordt een melding weer gegeven in de portal die de voortgang van de implementatie van uw Azure-AD DS weergeeft. Selecteer de melding om de gedetailleerde voortgang van de implementatie te bekijken.

    ![Melding in de Azure Portal van de implementatie die wordt uitgevoerd](./media/tutorial-create-instance-advanced/deployment-in-progress.png)

1. Selecteer uw resource groep, zoals *myResourceGroup*, en kies vervolgens uw Azure AD DS-exemplaar in de lijst met Azure-resources, zoals *aaddscontoso.com*. Op het tabblad **overzicht** ziet u dat het beheerde domein momenteel wordt *geïmplementeerd*. U kunt het beheerde domein pas configureren als het volledig is ingericht.

    ![Status van domein Services tijdens de inrichtings status](./media/tutorial-create-instance-advanced/provisioning-in-progress.png)

1. Wanneer het beheerde domein volledig is ingericht, wordt in het tabblad **overzicht** de domein status weer gegeven als *actief*.

    ![Domain Services-status nadat deze is ingericht](./media/tutorial-create-instance-advanced/successfully-provisioned.png)

Het beheerde domein is gekoppeld aan uw Azure AD-Tenant. Tijdens het inrichtings proces maakt Azure AD DS twee zakelijke toepassingen met de naam *Domain Controller Services* en *AzureActiveDirectoryDomainControllerServices* in de Azure AD-Tenant. Deze zakelijke toepassingen zijn nodig voor de service van uw beheerde domein. Verwijder deze toepassingen niet.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>DNS-instellingen bijwerken voor het virtuele Azure-netwerk

Als Azure AD DS geïmplementeerd, configureert u nu het virtuele netwerk zodat andere verbonden Vm's en toepassingen het beheerde domein kunnen gebruiken. Werk de DNS-server instellingen voor het virtuele netwerk bij om deze connectiviteit te laten verwijzen naar de twee IP-adressen waarop Azure AD DS wordt geïmplementeerd.

1. Op het tabblad **overzicht** voor uw beheerde domein worden enkele **vereiste configuratie stappen**weer gegeven. De eerste configuratie stap is het bijwerken van de DNS-server instellingen voor het virtuele netwerk. Zodra de DNS-instellingen correct zijn geconfigureerd, wordt deze stap niet meer weer gegeven.

    De vermelde adressen zijn de domein controllers voor gebruik in het virtuele netwerk. In dit voor beeld zijn deze adressen *10.1.0.4* en *10.1.0.5*. U kunt deze IP-adressen later vinden op het tabblad **Eigenschappen** .

    ![Configureer DNS-instellingen voor het virtuele netwerk met de Azure AD Domain Services IP-adressen](./media/tutorial-create-instance-advanced/configure-dns.png)

1. Selecteer de knop **configureren** om de DNS-server instellingen voor het virtuele netwerk bij te werken. De DNS-instellingen worden automatisch geconfigureerd voor het virtuele netwerk.

> [!TIP]
> Als u in de vorige stappen een bestaand virtueel netwerk hebt geselecteerd, krijgen de virtuele machines die zijn verbonden met het netwerk alleen de nieuwe DNS-instellingen nadat de computer opnieuw is opgestart. U kunt Vm's opnieuw opstarten met behulp van de Azure Portal, Azure PowerShell of de Azure CLI.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Gebruikers accounts inschakelen voor Azure AD DS

Voor het verifiëren van gebruikers in het beheerde domein heeft Azure AD DS wacht woord-hashes nodig in een indeling die geschikt is voor NT LAN Manager (NTLM) en Kerberos-verificatie. Azure AD genereert of slaat geen wacht woord-hashes in de vereiste indeling voor NTLM-of Kerberos-authenticatie totdat u Azure AD DS voor uw Tenant inschakelt. Uit veiligheids overwegingen slaat Azure AD ook geen wachtwoord referenties op in een normale tekst vorm. Daarom kan Azure AD deze NTLM-of Kerberos-wachtwoord hashes niet automatisch genereren op basis van de bestaande referenties van gebruikers.

> [!NOTE]
> Na de juiste configuratie worden de bruikbare wachtwoord hashes opgeslagen in het beheerde domein van Azure AD DS. Als u het door Azure AD DS beheerde domein verwijdert, worden ook de wacht woord-hashes verwijderd die op dat punt zijn opgeslagen. Gesynchroniseerde referentie gegevens in azure AD kunnen niet opnieuw worden gebruikt als u later een door Azure AD DS beheerd domein maakt, moet u de wachtwoord hash-synchronisatie opnieuw configureren om de wacht woord-hashes opnieuw op te slaan. Eerder aan een domein gekoppelde Vm's of gebruikers kunnen zich niet onmiddellijk verifiëren: Azure AD moet de wacht woord-hashes genereren en opslaan in het nieuwe Azure AD DS beheerde domein. Zie [wacht woord-hash synchronisatie proces voor Azure AD DS en Azure AD Connect][password-hash-sync-process]voor meer informatie.

De stappen voor het genereren en opslaan van deze wacht woord-hashes zijn anders voor Cloud gebruikers accounts die zijn gemaakt in azure AD versus gebruikers accounts die zijn gesynchroniseerd vanuit uw on-premises Directory met Azure AD Connect. Een cloudgebruikersaccount is een account dat is gemaakt in uw Azure AD-directory via de Azure portal of Azure AD PowerShell-cmdlets. Deze gebruikers accounts zijn niet gesynchroniseerd vanuit een on-premises Directory. In deze zelf studie werken we met een basis gebruikers account in de Cloud. Zie voor meer informatie over de extra stappen die vereist zijn voor het gebruik van Azure AD Connect [wacht woord-hashes synchroniseren voor gebruikers accounts die zijn gesynchroniseerd vanuit uw on-premises AD met uw beheerde domein][on-prem-sync].

> [!TIP]
> Als uw Azure AD-Tenant een combi natie heeft van Cloud gebruikers en gebruikers van uw on-premises AD, moet u beide sets met stappen volt ooien.

Voor Cloud gebruikers accounts moeten gebruikers hun wacht woord wijzigen voordat ze Azure AD DS kunnen gebruiken. Door deze wachtwoord wijziging worden de wacht woord-hashes voor Kerberos-en NTLM-verificatie gegenereerd en opgeslagen in azure AD. Het account wordt niet gesynchroniseerd van Azure AD naar Azure AD DS totdat het wacht woord is gewijzigd. Verloopt de wacht woorden voor alle Cloud gebruikers in de Tenant die Azure AD DS moeten gebruiken, waardoor een wachtwoord wijziging wordt afgedwongen bij de volgende aanmelding of om Cloud gebruikers te instrueren hun wacht woord hand matig te wijzigen. Voor deze zelf studie kunt u een gebruikers wachtwoord hand matig wijzigen.

Voordat een gebruiker het wacht woord opnieuw kan instellen, moet de Azure AD-Tenant worden [geconfigureerd voor selfservice voor het opnieuw instellen van een wacht woord][configure-sspr].

Als u het wacht woord voor een alleen-Cloud gebruiker wilt wijzigen, moet de gebruiker de volgende stappen uitvoeren:

1. Ga naar de pagina Azure AD-toegangs venster [https://myapps.microsoft.com](https://myapps.microsoft.com)op.
1. Selecteer in de rechter bovenhoek uw naam en kies vervolgens **profiel** in de vervolg keuzelijst.

    ![Selecteer het profiel](./media/tutorial-create-instance-advanced/select-profile.png)

1. Selecteer op de pagina **profiel** de optie **wacht woord wijzigen**.
1. Voer uw bestaande (oude) wacht woord in op de pagina **wacht woord wijzigen** , voer een nieuw wacht woord in en bevestig dit.
1. Selecteer **Indienen**.

Het duurt enkele minuten voordat u uw wacht woord voor het nieuwe wacht woord hebt gewijzigd zodat het kan worden gebruikt in azure AD DS en u kunt zich aanmelden bij computers die zijn gekoppeld aan het beheerde domein.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * DNS-en virtuele netwerk instellingen configureren voor een beheerd domein
> * Een Azure AD DS-exemplaar maken
> * Gebruikers met beheerders rechten toevoegen aan domein beheer
> * Gebruikers accounts inschakelen voor Azure AD DS en wacht woord-hashes genereren

Als u dit beheerde domein in actie wilt zien, maakt u een virtuele machine en voegt u deze toe aan het domein.

> [!div class="nextstepaction"]
> [Een virtuele machine met Windows Server toevoegen aan uw beheerde domein](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
