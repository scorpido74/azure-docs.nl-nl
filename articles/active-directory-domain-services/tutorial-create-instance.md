---
title: Zelfstudie - Een Azure Active Directory Domain Services-exemplaar maken | Microsoft Documenten
description: In deze zelfstudie leert u hoe u een Azure Active Directory Domain Services-exemplaar maakt en configureert met behulp van de Azure-portal.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 7652bacdebec19f8a5d55874cfb903e8748cef4d
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639707"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance"></a>Zelfstudie: Een Azure Active Directory Domain Services-exemplaar maken en configureren

Azure Active Directory Domain Services (Azure AD DS) biedt beheerde domeinservices zoals domeinjoin, groepsbeleid, LDAP, Kerberos/NTLM-verificatie die volledig compatibel is met Windows Server Active Directory. U gebruikt deze domeinservices zonder zelf domeincontrollers te implementeren, te beheren en te patchen. Azure AD DS integreert met uw bestaande Azure AD-tenant. Met deze integratie kunnen gebruikers zich aanmelden met hun bedrijfsreferenties en u bestaande groepen en gebruikersaccounts gebruiken om de toegang tot bronnen te beveiligen.

U een beheerd domein maken met standaardconfiguratieopties voor netwerken en synchronisatie, of [deze instellingen handmatig definiëren.][tutorial-create-instance-advanced] In deze zelfstudie ziet u hoe u standaardopties gebruikt om een Azure AD DS-exemplaar te maken en te configureren met behulp van de Azure-portal.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Informatie over DNS-vereisten voor een beheerd domein
> * Een Azure AD DS-exemplaar maken
> * Wachtwoord-hashsynchronisatie inschakelen

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* U hebt *algemene beheerdersrechten* nodig in uw Azure AD-tenant om Azure AD DS in te schakelen.
* U hebt *inzenderbevoegdheden* in uw Azure-abonnement nodig om de vereiste Azure AD DS-resources te maken.

Hoewel dit niet vereist is voor Azure AD DS, wordt het aanbevolen om [zelfservicewachtwoordreset (SSPR)][configure-sspr] te configureren voor de Azure AD-tenant. Gebruikers kunnen hun wachtwoord wijzigen zonder SSPR, maar SSPR helpt als ze hun wachtwoord vergeten en het opnieuw moeten instellen.

> [!IMPORTANT]
> Nadat u een door Azure AD DS beheerd domein hebt gemaakt, u de instantie niet verplaatsen naar een andere brongroep, virtueel netwerk, abonnement, enz. Zorg ervoor dat u het meest geschikte abonnement, resourcegroep, regio en virtueel netwerk selecteert wanneer u het Azure AD DS-exemplaar implementeert.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In deze zelfstudie maakt en configureert u het Azure AD DS-exemplaar met behulp van de Azure-portal. Meld u eerst aan bij de [Azure-portal](https://portal.azure.com)om aan de slag te gaan.

## <a name="create-an-instance"></a>Een instantie maken

Voer de volgende stappen uit om de wizard **Azure AD Domain Services inschakelen** te starten:

1. Selecteer in het menu van Azure Portal of op de **Startpagina** de optie **Een resource maken**.
1. Voer *Domeinservices* in op de zoekbalk en kies *Azure AD Domain Services* in de zoeksuggesties.
1. Selecteer op de pagina Azure AD Domain Services de optie **Maken**. De wizard **Azure AD Domain Services inschakelen** wordt gestart.
1. Selecteer het **Azure-abonnement** waarin u het beheerde domein wilt maken.
1. Selecteer de **resourcegroep** waartoe het beheerde domein moet behoren. Kies om **nieuw te maken** of selecteer een bestaande resourcegroep.

Wanneer u een Azure AD DS-exemplaar maakt, geeft u een DNS-naam op. Er zijn enkele overwegingen wanneer u deze DNS-naam kiest:

* **Ingebouwde domeinnaam:** Standaard wordt de ingebouwde domeinnaam van de map gebruikt (een *.onmicrosoft.com* achtervoegsel). Als u via internet beveiligde LDAP-toegang tot het beheerde domein wilt inschakelen, u geen digitaal certificaat maken om de verbinding met dit standaarddomein te beveiligen. Microsoft is eigenaar van het *domein .onmicrosoft.com,* zodat een Certificaatautoriteit (CA) geen certificaat afgeeft.
* **Aangepaste domeinnamen:** De meest voorkomende aanpak is het opgeven van een aangepaste domeinnaam, meestal een die u al bezit en is routetabel. Wanneer u een routeerbaar, aangepast domein gebruikt, kan het verkeer correct stromen als dat nodig is om uw toepassingen te ondersteunen.
* **Niet-routeerbare domeinachtervoegsels:** We raden u over het algemeen aan een niet-routeerbaar domeinnaamachtervoegsel te vermijden, zoals *contoso.local.* Het *.local* achtervoegsel is niet routeerbaar en kan problemen veroorzaken met dns-resolutie.

> [!TIP]
> Als u een aangepaste domeinnaam maakt, moet u rekening houden met bestaande DNS-naamruimten. Het wordt aanbevolen om een domeinnaam te gebruiken die los staat van bestaande Azure- of on-premises DNS-naamruimte.
>
> Als u bijvoorbeeld een bestaande DNS-naamruimte van *contoso.com*hebt, maakt u een door Azure AD DS beheerd domein met de aangepaste domeinnaam van *aaddscontoso.com*. Als u veilige LDAP moet gebruiken, moet u deze aangepaste domeinnaam registreren en bezitten om de vereiste certificaten te genereren.
>
> Mogelijk moet u een aantal extra DNS-records maken voor andere services in uw omgeving of voorwaardelijke DNS-expediteurs tussen bestaande DNS-naamruimten in uw omgeving. Als u bijvoorbeeld een webserver uitvoert die een site host met de hoofd-DNS-naam, kunnen er naamgevingsconflicten zijn waarvoor extra DNS-vermeldingen nodig zijn.
>
> In deze tutorials en how-to artikelen wordt het aangepaste domein van *aaddscontoso.com* als kort voorbeeld gebruikt. Geef in alle opdrachten uw eigen domeinnaam op.

De volgende DNS-naambeperkingen zijn ook van toepassing:

* **Beperkingen voor domeinvoorvoegsel:** U geen beheerd domein maken met een voorvoegsel dat langer is dan 15 tekens. Het voorvoegsel van uw opgegeven domeinnaam (zoals *aaddscontoso* in de *aaddscontoso.com* domeinnaam) moet 15 of minder tekens bevatten.
* **Conflicten met netwerknamen:** De DNS-domeinnaam voor uw beheerde domein zou nog niet in het virtuele netwerk moeten bestaan. Controleer specifiek op de volgende scenario's die tot een naamconflict zouden leiden:
    * Als u al een Active Directory-domein hebt met dezelfde DNS-domeinnaam in het virtuele Azure-netwerk.
    * Als het virtuele netwerk waar u het beheerde domein wilt inschakelen, een VPN-verbinding heeft met uw on-premises netwerk. Zorg er in dit scenario voor dat u geen domein hebt met dezelfde DNS-domeinnaam op uw on-premises netwerk.
    * Als u een bestaande Azure-cloudservice met die naam in het virtuele Azure-netwerk hebt.

Voltooi de velden in het venster *Basisbeginselen* van de Azure-portal om een Azure AD DS-exemplaar te maken:

1. Voer een **DNS-domeinnaam** in voor uw beheerde domein, rekening houdend met de vorige punten.
1. Kies de **Azure-locatie** waarin het beheerde domein moet worden gemaakt. Als u een regio kiest die beschikbaarheidszones ondersteunt, worden de Azure AD DS-resources verdeeld over zones voor extra redundantie.

    Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Tolerantie wordt gegarandeerd door aanwezigheid van minimaal drie afzonderlijke zones in alle actieve regio's.

    U er niets voor configureren dat Azure AD DS over zones wordt verdeeld. Het Azure-platform verwerkt automatisch de zonedistributie van resources. Zie [Beschikbaarheidszones in Azure voor][availability-zones] meer informatie en voor de beschikbaarheid van de regio?

1. De **SKU** bepaalt de prestaties, de back-upfrequentie en het maximum aantal forestvertrouwensrelaties dat u maken. U de SKU wijzigen nadat het beheerde domein is gemaakt als uw bedrijfsvereisten of vereisten veranderen. Zie [Azure AD DS SKU-concepten voor][concepts-sku]meer informatie.

    Selecteer voor deze zelfstudie de *Standaard* SKU.
1. Een *forest* is een logische constructie die wordt gebruikt door Active Directory Domain Services om een of meer domeinen te groeperen. Standaard wordt een door Azure AD DS beheerd domein gemaakt als *gebruikersforest.* Dit type forest synchroniseert alle objecten uit Azure AD, inclusief gebruikersaccounts die zijn gemaakt in een on-premises AD DS-omgeving. Een *resourceforest* synchroniseert alleen gebruikers en groepen die rechtstreeks in Azure AD zijn gemaakt. Resourceforests zijn momenteel in preview. Zie overzicht van [Azure AD DS-bronforests][resource-forests]voor meer informatie over *resourceforests,* waaronder waarom u er een gebruiken en hoe u forestvertrouwensrelaties maken met on-premises AD DS-domeinen.

    Kies voor deze zelfstudie om een *gebruikersforest* te maken.

    ![Basisinstellingen configureren voor een Azure AD Domain Services-exemplaar](./media/tutorial-create-instance/basics-window.png)

Als u snel een door Azure AD DS beheerd domein wilt maken, u **Controleren + maken** selecteren om extra standaardconfiguratieopties te accepteren. De volgende standaardinstellingen worden geconfigureerd wanneer u deze optie voor het maken kiest:

* Hiermee maakt u een virtueel netwerk met de naam *aadds-vnet* dat gebruikmaakt van het IP-adresbereik van *10.0.2.0/24*.
* Hiermee maakt u een subnet met de naam *aadds-subnet* met het IP-adresbereik van *10.0.2.0/24*.
* Synchroniseert *alle* gebruikers van Azure AD naar het beheerde Azure AD DS-domein.

Selecteer **Controleren + maken** om deze standaardconfiguratieopties te accepteren.

## <a name="deploy-the-managed-domain"></a>Het beheerde domein implementeren

Controleer op de **pagina Overzicht** van de wizard de configuratie-instellingen voor het beheerde domein. U teruggaan naar elke stap van de wizard om wijzigingen aan te brengen. Als u een door Azure AD DS beheerd domein op een consistente manier wilt implementeren in een andere Azure AD-tenant met behulp van deze configuratieopties, u ook **een sjabloon voor automatisering downloaden.**

1. Als u het beheerde domein wilt maken, selecteert **u Maken**. Er wordt een notitie weergegeven dat bepaalde configuratieopties zoals DNS-naam of virtueel netwerk niet kunnen worden gewijzigd nadat het beheer de AD DS van Azure is gemaakt. Als u wilt doorgaan, selecteert u **OK**.
1. Het inrichten van uw beheerde domein kan tot een uur duren. Er wordt een melding weergegeven in de portal die de voortgang van uw Azure AD DS-implementatie weergeeft. Selecteer de melding om de gedetailleerde voortgang voor de implementatie te bekijken.

    ![Melding in de Azure-portal van de lopende implementatie](./media/tutorial-create-instance/deployment-in-progress.png)

1. De pagina wordt geladen met updates over het implementatieproces, waaronder het maken van nieuwe bronnen in uw directory.
1. Selecteer uw resourcegroep, zoals *myResourceGroup,* en kies vervolgens uw Azure AD DS-exemplaar in de lijst met Azure-bronnen, zoals *aaddscontoso.com*. Op het tabblad **Overzicht** ziet u dat het beheerde domein momenteel *wordt geïmplementeerd.* U het beheerde domein pas configureren als het volledig is ingericht.

    ![Status domeinservices tijdens de indpositie](./media/tutorial-create-instance/provisioning-in-progress.png)

1. Wanneer het beheerde domein volledig is ingericht, wordt op het tabblad **Overzicht** de domeinstatus weergegeven als *Actief*.

    ![Status domeinservices eenmaal ingericht](./media/tutorial-create-instance/successfully-provisioned.png)

Het beheerde domein is gekoppeld aan uw Azure AD-tenant. Tijdens het inrichtingsproces maakt Azure AD DS twee Enterprise-toepassingen met de naam *Domain Controller Services* en *AzureActiveDirectoryDomainControllerServices* in de Azure AD-tenant. Deze Enterprise-toepassingen zijn nodig om uw beheerde domein te onderhouden. Verwijder deze toepassingen niet.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>DNS-instellingen bijwerken voor het virtuele Azure-netwerk

Nu Azure AD DS is geïmplementeerd, configureert u het virtuele netwerk zodat andere verbonden VM's en toepassingen het beheerde domein kunnen gebruiken. Als u deze connectiviteit wilt bieden, werkt u de DNS-serverinstellingen voor uw virtuele netwerk bij om te wijzen op de twee IP-adressen waar Azure AD DS is geïmplementeerd.

1. Op het tabblad **Overzicht** voor uw beheerde domein worden enkele **vereiste configuratiestappen**weergegeven. De eerste configuratiestap is het bijwerken van DNS-serverinstellingen voor uw virtuele netwerk. Zodra de DNS-instellingen correct zijn geconfigureerd, wordt deze stap niet meer weergegeven.

    De vermelde adressen zijn de domeincontrollers voor gebruik in het virtuele netwerk. In dit voorbeeld zijn deze adressen *10.0.2.4* en *10.0.2.5*. U deze IP-adressen later vinden op het tabblad **Eigenschappen.**

    ![DNS-instellingen voor uw virtuele netwerk configureren met de IP-adressen van Azure AD Domain Services](./media/tutorial-create-instance/configure-dns.png)

1. Als u de DNS-serverinstellingen voor het virtuele netwerk wilt bijwerken, selecteert u de knop **Configureren.** De DNS-instellingen worden automatisch geconfigureerd voor uw virtuele netwerk.

> [!TIP]
> Als u in de vorige stappen een bestaand virtueel netwerk hebt geselecteerd, krijgen alle VM's die met het netwerk zijn verbonden, alleen de nieuwe DNS-instellingen na een herstart. U VM's opnieuw starten met de Azure-portal, Azure PowerShell of Azure CLI.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Gebruikersaccounts voor Azure AD DS inschakelen

Azure AD DS heeft wachtwoordhashes nodig in een indeling die geschikt is voor NT LAN Manager (NTLM) en Kerberos-verificatie om gebruikers op het beheerde domein te verifiëren. Azure AD genereert of slaat geen wachtwoordhashes op in de indeling die vereist is voor NTLM- of Kerberos-verificatie totdat u Azure AD DS inschakelt voor uw tenant. Om veiligheidsredenen slaat Azure AD ook geen wachtwoordreferenties op in formulier clear-text. Daarom kan Azure AD deze NTLM- of Kerberos-wachtwoordhashes niet automatisch genereren op basis van de bestaande referenties van gebruikers.

> [!NOTE]
> Zodra de juiste gegevens zijn geconfigureerd, worden de bruikbare wachtwoordhashes opgeslagen in het beheerde Azure AD DS-domein. Als u het door Azure AD DS beheerde domein verwijdert, worden ook alle wachtwoordhashes die op dat moment zijn opgeslagen, verwijderd. Gesynchroniseerde referentiegegevens in Azure AD kunnen niet opnieuw worden gebruikt als u later een door Azure AD DS beheerd domein maakt- u moet de wachtwoordhashsynchronisatie opnieuw configureren om de wachtwoordhashes opnieuw op te slaan. Eerder met domeinen verbonden VM's of gebruikers kunnen niet onmiddellijk verifiëren - Azure AD moet de wachtwoordhashes genereren en opslaan in het nieuwe door Azure AD DS beheerde domein. Zie [Wachtwoordhashsynchronisatieproces voor Azure AD DS en Azure AD Connect voor][password-hash-sync-process]meer informatie.

De stappen voor het genereren en opslaan van deze wachtwoordhashes zijn verschillend voor cloudgebruikersaccounts die zijn gemaakt in Azure AD versus gebruikersaccounts die zijn gesynchroniseerd vanuit uw on-premises directory met Azure AD Connect. Een cloudgebruikersaccount is een account dat is gemaakt in uw Azure AD-directory via de Azure portal of Azure AD PowerShell-cmdlets. Deze gebruikersaccounts worden niet gesynchroniseerd vanuit een on-premises directory. Laten we in deze zelfstudie werken met een basisgebruikersaccount voor alleen cloud. Zie [Wachtwoordhashes synchroniseren voor gebruikersaccounts die zijn gesynchroniseerd van uw on-premises AD naar uw beheerde domein voor][on-prem-sync]meer informatie over de aanvullende stappen die nodig zijn om Azure AD Connect te gebruiken.

> [!TIP]
> Als uw Azure AD-tenant een combinatie heeft van alleen cloudgebruikers en gebruikers van uw on-premises AD, moet u beide stappen uitvoeren.

Voor gebruikersaccounts die alleen in de cloud zijn, moeten gebruikers hun wachtwoorden wijzigen voordat ze Azure AD DS kunnen gebruiken. Dit wachtwoordwijzigingsproces zorgt ervoor dat de wachtwoordhashes voor Kerberos- en NTLM-verificatie worden gegenereerd en opgeslagen in Azure AD. Het account wordt niet gesynchroniseerd van Azure AD naar Azure AD DS totdat het wachtwoord is gewijzigd. Ververloopt de wachtwoorden voor alle cloudgebruikers in de tenant die Azure AD DS moeten gebruiken, waardoor een wachtwoordwijziging bij de volgende aanmelding wordt afgewend, of worden cloudgebruikers geïnstrueerd om hun wachtwoorden handmatig te wijzigen. Laten we voor deze zelfstudie handmatig een gebruikerswachtwoord wijzigen.

Voordat een gebruiker zijn wachtwoord opnieuw kan instellen, moet de Azure [AD-tenant zijn geconfigureerd voor het opnieuw instellen van het zelfservicewachtwoord.][configure-sspr]

Als u het wachtwoord voor een gebruiker met alleen de cloud wilt wijzigen, moet de gebruiker de volgende stappen uitvoeren:

1. Ga naar de pagina Azure [https://myapps.microsoft.com](https://myapps.microsoft.com)AD Access-deelvenster op .
1. Selecteer in de rechterbovenhoek uw naam en kies **Profiel** in het vervolgkeuzemenu.

    ![Selecteer het profiel](./media/tutorial-create-instance/select-profile.png)

1. Selecteer **op** de profielpagina **Wachtwoord wijzigen**.
1. Voer op de pagina **Wachtwoord wijzigen** uw bestaande (oude) wachtwoord in en voer een nieuw wachtwoord in en bevestig deze.
1. Selecteer **Indienen**.

Het duurt enkele minuten nadat u uw wachtwoord hebt gewijzigd voordat het nieuwe wachtwoord bruikbaar is in Azure AD DS en u met succes hebt aangemeld bij computers die zijn verbonden met het beheerde domein.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Informatie over DNS-vereisten voor een beheerd domein
> * Een Azure AD DS-exemplaar maken
> * Beheerdersgebruikers toevoegen aan domeinbeheer
> * Gebruikersaccounts voor Azure AD DS inschakelen en wachtwoordhashes genereren

Configureer een Virtueel Azure-netwerk voor toepassingsworkloads voordat u vm's voor het domein sluit en toepassingen implementeert die het beheerde Azure AD DS-domein gebruiken.

> [!div class="nextstepaction"]
> [Configureer het virtuele Azure-netwerk voor toepassingsworkloads om uw beheerde domein te gebruiken](tutorial-configure-networking.md)

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
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[skus]: overview.md
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain#selecting-a-prefix
