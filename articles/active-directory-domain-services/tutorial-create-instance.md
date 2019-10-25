---
title: Zelf studie-een Azure Active Directory Domain Services-exemplaar maken | Microsoft Docs
description: In deze zelf studie leert u hoe u een Azure Active Directory Domain Services-exemplaar kunt maken en configureren met behulp van de Azure Portal.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: iainfou
ms.openlocfilehash: b99eafeae60e81fd7d902289a47190a2cbe1daa3
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786985"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance"></a>Zelf studie: een Azure Active Directory Domain Services-exemplaar maken en configureren

Azure Active Directory Domain Services (Azure AD DS) biedt beheerde domein Services, zoals domein deelname, groeps beleid, LDAP, Kerberos/NTLM-verificatie die volledig compatibel is met Windows Server Active Directory. U gebruikt deze domein Services zonder zelf domein controllers te implementeren, beheren en repareren. Azure AD DS integreert met uw bestaande Azure AD-Tenant. Met deze integratie kunnen gebruikers zich aanmelden met hun bedrijfs referenties, en kunt u bestaande groepen en gebruikers accounts gebruiken om de toegang tot bronnen te beveiligen.

U kunt een beheerd domein maken met behulp van standaard configuratie opties voor netwerken en synchronisatie, of [deze instellingen hand matig definiëren][tutorial-create-instance-advanced]. In deze zelf studie ziet u hoe u standaard opties kunt gebruiken om een Azure AD DS-exemplaar te maken en te configureren met behulp van de Azure Portal.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Informatie over DNS-vereisten voor een beheerd domein
> * Een Azure AD DS-exemplaar maken
> * Wachtwoord-hashsynchronisatie inschakelen

Als u nog geen abonnement op Azure hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

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

## <a name="create-an-instance"></a>Een instantie maken

Voer de volgende stappen uit om de wizard **Azure AD Domain Services inschakelen** te starten:

1. Selecteer in de linkerbovenhoek van de Azure Portal **+ een resource maken**.
1. Voer *domein Services* in de zoek balk in en kies *Azure AD Domain Services* in de zoek suggesties.
1. Selecteer op de pagina Azure AD Domain Services **maken**. De wizard **Azure AD Domain Services inschakelen** wordt gestart.
1. Selecteer het Azure- **abonnement** waarin u het beheerde domein wilt maken.
1. Selecteer de **resource groep** waarvan het beheerde domein deel moet uitmaken. Maak een **nieuwe** resource groep of selecteer een bestaande.

Wanneer u een exemplaar van Azure AD DS maakt, geeft u een DNS-naam op. Er zijn enkele aandachtspunten bij het kiezen van deze DNS-naam:

* **Ingebouwde domein naam:** De ingebouwde domein naam van de Directory wordt standaard gebruikt (een *. onmicrosoft.com* -achtervoegsel). Als u beveiligde LDAP-toegang tot het beheerde domein via internet wilt inschakelen, kunt u geen digitaal certificaat maken om de verbinding met dit standaard domein te beveiligen. Micro soft is eigenaar van het *onmicrosoft.com* -domein, waardoor een certificerings instantie (CA) geen certificaat kan uitgeven.
* **Aangepaste domein namen:** De meest voorkomende benadering is het opgeven van een aangepaste domein naam, meestal een die u al bezit en routeerbaar is. Wanneer u een routeerbaar, aangepast domein gebruikt, kan het verkeer op de juiste wijze worden uitgevoerd om uw toepassingen te ondersteunen.
* **Niet-routeerbaar domein achtervoegsels:** U wordt geadviseerd om een niet-routeerbaar domein naam achtervoegsel, zoals *contoso. local*, te voor komen. Het achtervoegsel *. local* is niet routeerbaar en kan problemen met de DNS-omzetting veroorzaken.

> [!TIP]
> Als u een aangepaste domein naam maakt, moet u rekening houden met bestaande DNS-naam ruimten. U kunt het beste een uniek voor voegsel voor de domein naam toevoegen. Als uw naam van de DNS-basis bijvoorbeeld *contoso.com*is, maakt u een Azure AD DS beheerd domein met de aangepaste domein naam *Corp.contoso.com* of *DS.contoso.com*. In een hybride omgeving met een on-premises AD DS omgeving zijn deze voor voegsels mogelijk al in gebruik. Gebruik een uniek voor voegsel voor Azure AD DS.
>
> U kunt de DNS-basis naam voor uw Azure AD DS beheerde domein gebruiken, maar u moet mogelijk enkele extra DNS-records maken voor andere services in uw omgeving. Als u bijvoorbeeld een webserver uitvoert die als host fungeert voor een-site met behulp van de DNS-naam van de basis, kan er sprake zijn van naam conflicten waarvoor extra DNS-vermeldingen zijn vereist.
>
> In deze zelf studies en artikelen met procedures wordt het aangepaste domein *contoso.com* als een kort voor beeld gebruikt. Geef in alle opdrachten uw eigen domein naam op. Dit kan ook een uniek voor voegsel bevatten.
>
> Zie [een naam voorvoegsel voor het domein selecteren][naming-prefix]voor meer informatie.

De volgende DNS-naam beperkingen zijn ook van toepassing:

* **Beperkingen voor domein voorvoegsels:** U kunt geen beheerd domein met een voor voegsel maken dat langer is dan 15 tekens. Het voor voegsel van de opgegeven domein naam (bijvoorbeeld *Contoso* in de domein naam *contoso.com* ) mag Maxi maal 15 tekens bevatten.
* **Conflicten met netwerk naam:** De DNS-domein naam voor uw beheerde domein mag niet al bestaan in het virtuele netwerk. Controleer met name op de volgende scenario's die leiden tot een naam conflict:
    * Als u al een Active Directory domein met dezelfde DNS-domein naam hebt in het virtuele Azure-netwerk.
    * Als het virtuele netwerk waar u het beheerde domein wilt inschakelen, een VPN-verbinding heeft met uw on-premises netwerk. In dit scenario zorgt u ervoor dat u geen domein hebt met dezelfde DNS-domein naam in uw on-premises netwerk.
    * Als u een bestaande Azure-Cloud service hebt met die naam in het virtuele Azure-netwerk.

Vul de velden in het venster *basis beginselen* van het Azure Portal in om een Azure AD DS-exemplaar te maken:

1. Voer een **DNS-domein naam** in voor uw beheerde domein, waarbij rekening wordt gehouden met de vorige punten.
1. Kies de Azure- **locatie** waar het beheerde domein moet worden gemaakt.

    ![Basis instellingen voor een Azure AD Domain Services-exemplaar configureren](./media/tutorial-create-instance/basics-window.png)

Als u snel een door Azure AD DS beheerd domein wilt maken, kunt u op **revisie + maken** klikken om aanvullende standaard configuratie opties te accepteren. De volgende standaard waarden worden geconfigureerd wanneer u deze Maak optie kiest:

* Hiermee maakt u een virtueel netwerk met de naam *aadds-vnet* dat gebruikmaakt van het IP-adres bereik van *10.0.1.0/24*.
* Hiermee maakt u een subnet met de naam *aadds-subnet* met het IP-adres bereik *10.0.1.0/24*.
* Synchroniseert *alle* gebruikers van Azure AD in het door Azure AD DS beheerde domein.

1. Selecteer **controleren + maken** om deze standaard configuratie opties te accepteren.

## <a name="deploy-the-managed-domain"></a>Het beheerde domein implementeren

Controleer op de pagina **samen vatting** van de wizard de configuratie-instellingen voor het beheerde domein. U kunt terugkeren naar elke stap van de wizard om wijzigingen aan te brengen. Als u een Azure AD DS beheerd domein op een consistente manier opnieuw wilt implementeren in een andere Azure AD-Tenant met behulp van deze configuratie opties, kunt u ook **een sjabloon voor Automation downloaden**.

1. Selecteer **maken**om het beheerde domein te maken. Er wordt een opmerking weer gegeven dat bepaalde configuratie opties, zoals de DNS-naam of het virtuele netwerk, niet kunnen worden gewijzigd nadat de beheerde Azure-AD DS is gemaakt. Selecteer **OK**om door te gaan.
1. Het proces van het inrichten van uw beheerde domein kan tot een uur duren. Er wordt een melding weer gegeven in de portal die de voortgang van de implementatie van uw Azure-AD DS weergeeft. Selecteer de melding om de gedetailleerde voortgang van de implementatie te bekijken.

    ![Melding in de Azure Portal van de implementatie die wordt uitgevoerd](./media/tutorial-create-instance/deployment-in-progress.png)

1. De pagina wordt geladen met updates voor het implementatie proces, met inbegrip van het maken van nieuwe resources in uw Directory.
1. Selecteer uw resource groep, zoals *myResourceGroup*, en kies vervolgens uw Azure AD DS-exemplaar in de lijst met Azure-resources, zoals *contoso.com*. Op het tabblad **overzicht** ziet u dat het beheerde domein momenteel wordt *geïmplementeerd*. U kunt het beheerde domein pas configureren als het volledig is ingericht.

    ![Status van domein Services tijdens de inrichtings status](./media/tutorial-create-instance/provisioning-in-progress.png)

1. Wanneer het beheerde domein volledig is ingericht, wordt in het tabblad **overzicht** de domein status weer gegeven als *actief*.

    ![Domain Services-status nadat deze is ingericht](./media/tutorial-create-instance/successfully-provisioned.png)

We richten Azure AD Domain Services op de Azure Active Directory-Tenant en de Azure AD Domain Services resource voor de service worden gemaakt binnen het bijbehorende Azure-abonnement. Tijdens het inrichtings proces maakt Azure AD DS twee zakelijke toepassingen met de naam *Domain Controller Services* en *AzureActiveDirectoryDomainControllerServices* in uw Azure Active Directory-exemplaar waarin u Azure hebt ingeschakeld AD Domain Services. Deze zakelijke toepassingen zijn nodig voor de service van uw beheerde domein.  Het is essentieel dat deze toepassingen op elk gewenst moment niet worden verwijderd.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>DNS-instellingen bijwerken voor het virtuele Azure-netwerk

Als Azure AD DS geïmplementeerd, configureert u nu het virtuele netwerk zodat andere verbonden Vm's en toepassingen het beheerde domein kunnen gebruiken. Werk de DNS-server instellingen voor het virtuele netwerk bij om deze connectiviteit te laten verwijzen naar de twee IP-adressen waarop Azure AD DS wordt geïmplementeerd.

1. Op het tabblad **overzicht** voor uw beheerde domein worden enkele **vereiste configuratie stappen**weer gegeven. De eerste configuratie stap is het bijwerken van de DNS-server instellingen voor het virtuele netwerk. Zodra de DNS-instellingen correct zijn geconfigureerd, wordt deze stap niet meer weer gegeven.

    De vermelde adressen zijn de domein controllers voor gebruik in het virtuele netwerk. In dit voor beeld zijn deze adressen *10.1.0.4* en *10.1.0.5*. U kunt deze IP-adressen later vinden op het tabblad **Eigenschappen** .

    ![Configureer DNS-instellingen voor het virtuele netwerk met de Azure AD Domain Services IP-adressen](./media/tutorial-create-instance/configure-dns.png)

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

Voor Cloud gebruikers accounts moeten gebruikers hun wacht woord wijzigen voordat ze Azure AD DS kunnen gebruiken. Door deze wachtwoord wijziging worden de wacht woord-hashes voor Kerberos-en NTLM-verificatie gegenereerd en opgeslagen in azure AD. U kunt de wacht woorden laten verlopen voor alle gebruikers in de Tenant die Azure AD DS moeten gebruiken, waarbij een wachtwoord wijziging wordt afgedwongen bij de volgende aanmelding of als u de wacht woorden hand matig wilt wijzigen. Voor deze zelf studie kunt u een gebruikers wachtwoord hand matig wijzigen.

Voordat een gebruiker het wacht woord opnieuw kan instellen, moet de Azure AD-Tenant worden [geconfigureerd voor selfservice voor het opnieuw instellen van een wacht woord][configure-sspr].

Als u het wacht woord voor een alleen-Cloud gebruiker wilt wijzigen, moet de gebruiker de volgende stappen uitvoeren:

1. Ga naar de pagina Azure AD-toegangs venster op [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. Selecteer in de rechter bovenhoek uw naam en kies vervolgens **profiel** in de vervolg keuzelijst.

    ![Selecteer het profiel](./media/tutorial-create-instance/select-profile.png)

1. Selecteer op de pagina **profiel** de optie **wacht woord wijzigen**.
1. Voer uw bestaande (oude) wacht woord in op de pagina **wacht woord wijzigen** , voer een nieuw wacht woord in en bevestig dit.
1. Selecteer **Indienen**.

Het duurt enkele minuten voordat u uw wacht woord voor het nieuwe wacht woord hebt gewijzigd zodat het kan worden gebruikt in azure AD DS en u kunt zich aanmelden bij computers die zijn gekoppeld aan het beheerde domein.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Informatie over DNS-vereisten voor een beheerd domein
> * Een Azure AD DS-exemplaar maken
> * Gebruikers met beheerders rechten toevoegen aan domein beheer
> * Gebruikers accounts inschakelen voor Azure AD DS en wacht woord-hashes genereren

Voordat u een domein toevoegt aan Vm's en toepassingen implementeert die gebruikmaken van het door Azure AD DS beheerde domein, configureert u een virtueel Azure-netwerk voor werk belastingen van toepassingen.

> [!div class="nextstepaction"]
> [Virtuele Azure-netwerken configureren voor werk belastingen van toepassingen om uw beheerde domein te gebruiken](tutorial-configure-networking.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services

<!-- EXTERNAL LINKS -->
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain#selecting-a-prefix
