---
title: Azure AD-domeinservices migreren vanuit een virtueel netwerk van klassieke bestanden | Microsoft Documenten
description: Meer informatie over het migreren van een bestaand Azure AD Domain Services beheerd domeinexemplaar van het virtuele netwerkmodel Classic naar een virtueel netwerk op basis van Resource Manager.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: 6acf9301367ae2c6947f6935c43f420d3d7cac65
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655019"
---
# <a name="migrate-azure-ad-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>Azure AD Domain Services migreren van het virtuele netwerkmodel Classic naar Resource Manager

Azure Active Directory Domain Services (AD DS) ondersteunt een eenmalige verhuizing voor klanten die momenteel het virtuele netwerkmodel Classic gebruiken naar het virtuele netwerkmodel Voor Resources Manager. Azure AD DS-beheerde domeinen die het implementatiemodel voor Resourcebeheer gebruiken, bieden extra functies, zoals fijnkorrelig wachtwoordbeleid, controlelogboeken en bescherming voor accountuitsluiting.

In dit artikel worden de voordelen en overwegingen voor migratie beschreven en vervolgens de vereiste stappen om een bestaand Azure AD DS-exemplaar te migreren.

> [!NOTE]
> In 2017 werd Azure AD Domain Services beschikbaar voor host in een Azure Resource Manager-netwerk. Sindsdien hebben we een veiligere service kunnen bouwen met behulp van de moderne mogelijkheden van Azure Resource Manager. Omdat Azure Resource Manager-implementaties klassieke implementaties volledig vervangen, worden de klassieke virtuele netwerkimplementaties van Azure AD DS op 1 maart 2023 uitgeschakeld.
>
> Zie voor meer informatie het [officiële afschrijvingsbericht](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="overview-of-the-migration-process"></a>Overzicht van het migratieproces

Het migratieproces neemt een bestaand Azure AD DS-exemplaar dat wordt uitgevoerd in een virtueel netwerk van Classic en verplaatst deze naar een bestaand virtueel netwerk van Resource Manager. De migratie wordt uitgevoerd met PowerShell en heeft twee belangrijke fasen van uitvoering - *voorbereiding* en *migratie*.

![Overzicht van het migratieproces voor Azure AD DS](media/migrate-from-classic-vnet/migration-overview.png)

In de *voorbereidingsfase* maakt Azure AD DS een back-up van het domein om de nieuwste momentopname van gebruikers, groepen en wachtwoorden te synchroniseren met het beheerde domein. Synchronisatie wordt vervolgens uitgeschakeld en de cloudservice die het beheerde Azure AD DS-beheerde domein host, wordt verwijderd. Tijdens de voorbereidingsfase kan het beheerde Azure AD DS-domein gebruikers niet verifiëren.

![Voorbereidingsfase voor het migreren van Azure AD DS](media/migrate-from-classic-vnet/migration-preparation.png)

In de *migratiefase* worden de onderliggende virtuele schijven voor de domeincontrollers uit het door Azure AD DS beheerde domein gekopieerd om de VM's te maken met behulp van het implementatiemodel Resource Manager. Het beheerde Azure AD DS-domein wordt vervolgens opnieuw gemaakt, inclusief de LDAPS- en DNS-configuratie. Synchronisatie met Azure AD wordt opnieuw gestart en LDAP-certificaten worden hersteld. Het is niet nodig om opnieuw lid te worden van machines in een door Azure AD DS beheerd domein: ze worden nog steeds verbonden met het beheerde domein en worden zonder wijzigingen uitgevoerd.

![Migratie van Azure AD DS](media/migrate-from-classic-vnet/migration-process.png)

## <a name="migration-benefits"></a>Migratievoordelen

Wanneer u een door Azure AD DS beheerd domein verplaatst met behulp van dit migratieproces, hoeft u niet opnieuw lid te worden van machines naar het beheerde domein of het Azure AD DS-exemplaar te verwijderen en er een helemaal opnieuw te maken. VM's blijven worden samengevoegd met het door Azure AD DS beheerde domein aan het einde van het migratieproces.

Na de migratie biedt Azure AD DS veel functies die alleen beschikbaar zijn voor domeinen met virtuele netwerken van ResourceManager, zoals:

* Fijnmazige ondersteuning voor wachtwoordbeleid.
* Beveiliging van de uitsluiting van AD-account.
* E-mailmeldingen van waarschuwingen op het door Azure AD DS beheerde domein.
* Controlelogboeken met Azure Monitor.
* Azure-bestanden-integratie
* HD Insights-integratie

Azure AD DS-beheerde domeinen die gebruik maken van een virtueel netwerk van Resource Manager, helpen u up-to-date te blijven met de nieuwste nieuwe functies. Ondersteuning voor Azure AD DS met klassieke virtuele netwerken moet in de toekomst worden afgeschaft.

## <a name="example-scenarios-for-migration"></a>Voorbeeldscenario's voor migratie

Enkele veelvoorkomende scenario's voor het migreren van een door Azure AD DS beheerd domein bevatten de volgende voorbeelden.

> [!NOTE]
> Converteer het virtuele netwerk Van Classic pas nadat u een geslaagde migratie hebt bevestigd. Als u het virtuele netwerk converteert, wordt de optie verwijderd om het beheerde Azure AD DS-beheerde domein terug te draaien of te herstellen als er problemen zijn tijdens de migratie- en verificatiefasen.

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Azure AD DS migreren naar een bestaand virtueel netwerk van Resource Beheer (aanbevolen)

Een veelvoorkomend scenario is de plaats waar u al andere bestaande Classic-bronnen hebt verplaatst naar een implementatiemodel voor Resource Manager en virtueel netwerk. Peering wordt vervolgens gebruikt van het virtuele netwerk Resource Manager naar het virtuele netwerk Classic dat Azure AD DS blijft uitvoeren. Met deze aanpak kunnen de resourcebeheertoepassingen en -services de verificatie- en beheerfunctionaliteit van het door Azure AD DS beheerde domein in het virtuele netwerk Classic gebruiken. Na gemigreerd worden alle resources uitgevoerd met behulp van het implementatiemodel resourcebeheer en het virtuele netwerk.

![Azure AD DS migreren naar een bestaand virtueel netwerk van Resource Manager](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

Stappen op hoog niveau die betrokken zijn bij dit voorbeeldmigratiescenario omvatten de volgende onderdelen:

1. Verwijder bestaande VPN-gateways of virtuele netwerkpeering geconfigureerd op het virtuele Classic-netwerk.
1. Migreer het door Azure AD DS beheerde domein met behulp van de stappen die in dit artikel worden beschreven.
1. Test en bevestig een geslaagde migratie en verwijder vervolgens het virtuele netwerk Classic.

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Meerdere resources migreren, waaronder Azure AD DS

In dit voorbeeldscenario migreert u Azure AD DS en andere bijbehorende resources van het klassieke implementatiemodel naar het implementatiemodel ResourceBeheer. Als sommige resources in het virtuele netwerk Classic naast het beheerde Azure AD DS-domein blijven worden uitgevoerd, kunnen ze allemaal profiteren van de migratie naar het implementatiemodel voor Resource Manager.

![Meerdere resources migreren naar het implementatiemodel resourcebeheer](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

Stappen op hoog niveau die betrokken zijn bij dit voorbeeldmigratiescenario omvatten de volgende onderdelen:

1. Verwijder bestaande VPN-gateways of virtuele netwerkpeering geconfigureerd op het virtuele Classic-netwerk.
1. Migreer het door Azure AD DS beheerde domein met behulp van de stappen die in dit artikel worden beschreven.
1. Stel virtueel netwerkpeeren in tussen het virtuele netwerk Classic en het Resource Manager-netwerk.
1. Test en bevestig een succesvolle migratie.
1. [Extra klassieke bronnen verplaatsen, zoals VM's.][migrate-iaas]

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Azure AD DS migreren, maar andere bronnen in het virtuele netwerk Classic behouden

In dit voorbeeldscenario hebt u de minimale hoeveelheid downtime in één sessie. U migreert alleen Azure AD DS naar een virtueel netwerk van Resource Manager en houdt bestaande bronnen op het klassieke implementatiemodel en het virtuele netwerk. In een volgende onderhoudsperiode u de extra resources migreren van het Klassieke implementatiemodel en het virtuele netwerk naar wens.

![Migreer alleen Azure AD DS naar het implementatiemodel Resource Manager](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

Stappen op hoog niveau die betrokken zijn bij dit voorbeeldmigratiescenario omvatten de volgende onderdelen:

1. Verwijder bestaande VPN-gateways of virtuele netwerkpeering geconfigureerd op het virtuele Classic-netwerk.
1. Migreer het door Azure AD DS beheerde domein met behulp van de stappen die in dit artikel worden beschreven.
1. Stel virtueel netwerkpeeren in tussen het virtuele netwerk Classic en het nieuwe virtuele Netwerk Van Resource Manager.
1. Migreer de [extra resources][migrate-iaas] later van het virtuele netwerk Classic als dat nodig is.

## <a name="before-you-begin"></a>Voordat u begint

Terwijl u een door Azure AD DS beheerd domein voorbereidt en migreert, zijn er enkele overwegingen met betrekking tot de beschikbaarheid van verificatie- en beheerservices. Het beheerde Azure AD DS-domein is gedurende een bepaalde periode tijdens de migratie niet beschikbaar. Toepassingen en services die afhankelijk zijn van Azure AD DS ervaren downtime tijdens de migratie.

> [!IMPORTANT]
> Lees al dit migratieartikel en richtlijnen voordat u het migratieproces start. Het migratieproces is van invloed op de beschikbaarheid van de Azure AD DS-domeincontrollers voor perioden. Gebruikers, services en toepassingen kunnen tijdens het migratieproces niet verifiëren ten opzichte van het beheerde domein.

### <a name="ip-addresses"></a>IP-adressen

De IP-adressen van de domeincontroller voor een door Azure AD DS beheerde domeinwijziging na migratie. Deze wijziging omvat het openbare IP-adres voor het beveiligde LDAP-eindpunt. De nieuwe IP-adressen bevinden zich binnen het adresbereik voor het nieuwe subnet in het virtuele netwerk Van Resource Manager.

In het geval van terugdraaien kunnen de IP-adressen veranderen na het terugdraaien.

Azure AD DS gebruikt doorgaans de eerste twee beschikbare IP-adressen in het adresbereik, maar dit is niet gegarandeerd. U momenteel niet de IP-adressen opgeven die u na de migratie wilt gebruiken.

### <a name="downtime"></a>Downtime

Het migratieproces houdt in dat de domeincontrollers gedurende een bepaalde periode offline zijn. Domeincontrollers zijn niet toegankelijk terwijl Azure AD DS wordt gemigreerd naar het implementatiemodel voor Resource Beheer en het virtuele netwerk. Gemiddeld is de downtime ongeveer 1 tot 3 uur. Deze periode is vanaf het moment dat de domeincontrollers offline worden gehaald tot het moment dat de eerste domeincontroller weer online komt. Dit gemiddelde bevat niet de tijd die nodig is voor de tweede domeincontroller om te repliceren, of de tijd die nodig is om extra resources te migreren naar het implementatiemodel voor Resource Manager.

### <a name="account-lockout"></a>Accountuitsluiting

Azure AD DS-beheerde domeinen die worden uitgevoerd op virtuele klassieke netwerken, hebben geen ad-accountvergrendelingsbeleid. Als VM's worden blootgesteld aan het internet, aanvallers kunnen gebruik maken van wachtwoord-spray methoden om brute-force hun weg naar accounts. Er is geen account lockout beleid om die pogingen te stoppen. Voor azure AD DS-beheerde domeinen die het implementatiemodel voor Resource Beheer en virtuele netwerken gebruiken, beschermt het beleid voor het vergrendelen van accounts van HET AD tegen deze aanvallen met wachtwoordspray.

Standaard, 5 slechte wachtwoord pogingen in 2 minuten vergrendelen van een account voor 30 minuten.

Een vergrendeld account kan niet worden gebruikt om in te loggen, wat de mogelijkheid kan belemmeren om het door Azure AD DS beheerde domein of -toepassingen te beheren dat door het account wordt beheerd. Nadat een door Azure AD DS beheerd domein is gemigreerd, kunnen accounts ervaren wat voelt als een permanente uitsluiting als gevolg van herhaalde mislukte pogingen om u aan te melden. Twee veelvoorkomende scenario's na migratie zijn:

* Een serviceaccount dat een verlopen wachtwoord gebruikt.
    * Het serviceaccount probeert herhaaldelijk in te loggen met een verlopen wachtwoord, waardoor het account wordt geblokkeerd. Als u dit wilt oplossen, zoekt u de toepassing of vm met verlopen referenties en werkt u het wachtwoord bij.
* Een kwaadwillende entiteit gebruikt brute-force pogingen om in te loggen bij accounts.
    * Wanneer VM's worden blootgesteld aan het internet, aanvallers proberen vaak gemeenschappelijke gebruikersnaam en wachtwoord combinaties als ze proberen te ondertekenen. Deze herhaalde mislukte aanmeldingspogingen kunnen de accounts vergrendelen. Het wordt niet aanbevolen om beheerdersaccounts met algemene namen zoals *beheerder* of *beheerder*te gebruiken, bijvoorbeeld om te voorkomen dat beheerdersaccounts worden vergrendeld.
    * Minimaliseer het aantal VM's dat wordt blootgesteld aan het internet. U [Azure Bastion][azure-bastion] gebruiken om veilig verbinding te maken met VM's via de Azure-portal.

Als u vermoedt dat sommige accounts na migratie kunnen worden vergrendeld, wordt in de laatste migratiestappen beschreven hoe u controle inschakelt of de fijnmazige wachtwoordbeleidsinstellingen wijzigen.

### <a name="roll-back-and-restore"></a>Terugdraaien en herstellen

Als de migratie niet succesvol is, is er een proces om een door Azure AD DS beheerd domein terug te draaien of te herstellen. Terugdraaien is een selfserviceoptie om de status van het beheerde domein onmiddellijk terug te geven aan vóór de migratiepoging. Azure-ondersteuningstechnici kunnen een beheerd domein ook als laatste redmiddel herstellen vanuit back-up. Zie voor meer informatie hoe u [een mislukte migratie terugdraaien of herstellen.](#roll-back-and-restore-from-migration)

### <a name="restrictions-on-available-virtual-networks"></a>Beperkingen op beschikbare virtuele netwerken

Er zijn enkele beperkingen op de virtuele netwerken waarnaar een door Azure AD DS beheerd domein kan worden gemigreerd. Het virtuele netwerk van doelresourcebeheer moet aan de volgende vereisten voldoen:

* Het virtuele netwerk ResourceBeheer moet zich in hetzelfde Azure-abonnement bevinden als het virtuele Netwerk Classic waarin Azure AD DS momenteel is geïmplementeerd.
* Het virtuele netwerk Resourcebeheer moet zich in dezelfde regio bevinden als het virtuele netwerk Classic waarin Azure AD DS momenteel is geïmplementeerd.
* Het subnet van het virtuele netwerk van de Resource Manager moet ten minste 3-5 beschikbare IP-adressen hebben.
* Het subnet van het virtuele netwerk resourcebeheer moet een speciaal subnet voor Azure AD DS zijn en mag geen andere workloads hosten.

Zie [Overwegingen voor virtueel netwerkontwerp en configuratieopties][network-considerations]voor meer informatie over vereisten voor virtueel netwerk.

## <a name="migration-steps"></a>Migratiestappen

De migratie naar het implementatiemodel resourcebeheer en het virtuele netwerk is opgesplitst in 5 hoofdstappen:

| Stap    | Uitgevoerd via  | Geschatte tijd  | Downtime  | Terugdraaien/herstellen? |
|---------|--------------------|-----------------|-----------|-------------------|
| [Stap 1 - Update en zoek het nieuwe virtuele netwerk](#update-and-verify-virtual-network-settings) | Azure Portal | 15 minuten | Geen downtime vereist | N.v.t. |
| [Stap 2 - Het beheerde Azure AD DS-domein voorbereiden op migratie](#prepare-the-managed-domain-for-migration) | PowerShell | Gemiddeld 15 – 30 minuten | Downtime van Azure AD DS wordt gestart nadat deze opdracht is voltooid. | Terugdraaien en weervrij maken. |
| [Stap 3 - Het beheerde Azure AD DS-domein verplaatsen naar een bestaand virtueel netwerk](#migrate-the-managed-domain) | PowerShell | Gemiddeld 1 – 3 uur | Eén domeincontroller is beschikbaar zodra deze opdracht is voltooid, de downtime eindigt. | Bij het mislukken zijn zowel terugdraaien (selfservice) als herstel beschikbaar. |
| [Stap 4 - Testen en wachten op de replica domeincontroller](#test-and-verify-connectivity-after-the-migration)| PowerShell- en Azure-portal | 1 uur of meer, afhankelijk van het aantal tests | Beide domeincontrollers zijn beschikbaar en moeten normaal functioneren. | N.v.t. Zodra de eerste vm is gemigreerd, is er geen optie voor terugdraaien of herstellen. |
| [Stap 5 - Optionele configuratiestappen](#optional-post-migration-configuration-steps) | Azure-portal en VM's | N.v.t. | Geen downtime vereist | N.v.t. |

> [!IMPORTANT]
> Lees al dit migratieartikel en deze richtlijnen voordat u het migratieproces start om extra downtime te voorkomen. Het migratieproces is van invloed op de beschikbaarheid van de Azure AD DS-domeincontrollers voor een bepaalde periode. Gebruikers, services en toepassingen kunnen tijdens het migratieproces niet verifiëren ten opzichte van het beheerde domein.

## <a name="update-and-verify-virtual-network-settings"></a>Virtuele netwerkinstellingen bijwerken en verifiëren

Voer de volgende eerste controles en updates uit voordat u met het migratieproces begint. Deze stappen kunnen op elk moment vóór de migratie worden uitgevoerd en hebben geen invloed op de werking van het beheerde Azure AD DS-domein.

1. Werk uw lokale Azure PowerShell-omgeving bij naar de nieuwste versie. Als u de migratiestappen wilt uitvoeren, hebt u ten minste versie *2.3.2*nodig.

    Zie [Azure PowerShell-overzicht][azure-powershell]voor informatie over het controleren en bijwerken van uw PowerShell-versie.

1. Maak of kies een bestaand virtueel netwerk van Resource Manager.

    Zorg ervoor dat netwerkinstellingen de benodigde poorten die nodig zijn voor Azure AD DS niet blokkeren. Poorten moeten open zijn op zowel het virtuele netwerk Classic als het virtuele netwerk Van Resource Manager. Deze instellingen omvatten routetabellen (hoewel het niet wordt aanbevolen om routetabellen te gebruiken) en netwerkbeveiligingsgroepen.

    Zie [Netwerkbeveiligingsgroepen en vereiste poorten][network-ports]om de vereiste poorten te bekijken. Om problemen met netwerkcommunicatie te minimaliseren, wordt aanbevolen om te wachten en een netwerkbeveiligingsgroep of routetabel toe te passen op het virtuele netwerk ResourceManager nadat de migratie is voltooid.

    Noteer deze doelgroep, target virtueel netwerk en target virtueel netwerksubnet. Deze resourcenamen worden gebruikt tijdens het migratieproces.

1. Controleer de door Azure AD DS beheerde domeinstatus in de Azure-portal. Als u waarschuwingen hebt voor het beheerde domein, lost u deze op voordat u het migratieproces start.
1. Als u van plan bent andere resources naar het implementatiemodel voor Resourcebeheer en het virtuele netwerk te verplaatsen, bevestigt u dat deze resources kunnen worden gemigreerd. Zie [Platformondersteunde migratie van IaaS-resources van Classic naar Resource Manager][migrate-iaas]voor meer informatie.

    > [!NOTE]
    > Converteer het virtuele netwerk Classic niet naar een virtueel netwerk van Resource Manager. Als u dit doet, is er geen optie om het beheerde Azure AD DS-beheerde domein terug te draaien of te herstellen.

## <a name="prepare-the-managed-domain-for-migration"></a>Het beheerde domein voorbereiden op migratie

Azure PowerShell wordt gebruikt om het door Azure AD DS beheerde domein voor te bereiden op migratie. Deze stappen omvatten het nemen van een back-up, het onderbreken van synchronisatie en het verwijderen van de cloudservice die Azure AD DS host. Wanneer deze stap is voltooid, wordt Azure AD DS gedurende een bepaalde periode offline gehaald. Als de voorbereidingsstap mislukt, u [terugdraaien naar de vorige status](#roll-back).

Voer de volgende stappen uit om het door Azure AD DS beheerde domein voor te bereiden op migratie:

1. Installeer `Migrate-Aaads` het script in de [PowerShell-galerie][powershell-script]. Dit PowerShell-migratiescript is een digitaal ondertekend door het Azure AD-engineeringteam.

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. Maak een variabele om de referenties voor te houden door het migratiescript met behulp van de cmdlet [Get-Credential.][get-credential]

    Het gebruikersaccount dat u opgeeft, heeft *globale beheerdersrechten* nodig in uw Azure AD-tenant om Azure AD DS en vervolgens *inzenderbevoegdheden* in uw Azure-abonnement in te schakelen om de vereiste Azure AD DS-resources te maken.

    Voer desgevraagd een geschikt gebruikersaccount en wachtwoord in:

    ```powershell
    $creds = Get-Credential
    ```

1. Voer nu `Migrate-Aadds` de cmdlet uit met de parameter *-Voorbereiden.* Geef de *-ManagedDomainFqdn* op voor uw eigen Door Azure AD DS beheerde domein, zoals *aaddscontoso.com:*

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn aaddscontoso.com `
        -Credentials $creds
    ```

## <a name="migrate-the-managed-domain"></a>Het beheerde domein migreren

Als het door Azure AD DS beheerde domein is voorbereid en een back-up is gemaakt, kan het domein worden gemigreerd. Met deze stap worden de VM's van de Azure AD Domain Services-domeincontroller opnieuw gemaakt met behulp van het implementatiemodel Resource Beheer. Deze stap kan 1 tot 3 uur in beslag nemen.

Voer `Migrate-Aadds` de cmdlet uit met de parameter *-Commit.* Geef de *-ManagedDomainFqdn* op voor uw eigen Azure AD DS-beheerde domein dat in de vorige sectie is opgesteld, zoals *aaddscontoso.com:*

Geef de doelgroep op waarnaar het virtuele netwerk is waarnaar u Azure AD DS wilt migreren, zoals *myResourceGroup.* Zorg voor het doelvirtuele netwerk, zoals *myVnet*en het subnet, zoals *DomainServices.*

Nadat deze opdracht is uitgevoerd, u niet terugdraaien:

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn aaddscontoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds
```

Nadat het script is gevalideerd, wordt het beheerde domein voorbereid op migratie, voert u *Y* in om het migratieproces te starten.

> [!IMPORTANT]
> Converteer het virtuele netwerk Classic niet naar een virtueel netwerk van Resource Manager tijdens het migratieproces. Als u het virtuele netwerk converteert, u het beheerde Azure AD DS-beheerde domein niet terugdraaien of herstellen omdat het oorspronkelijke virtuele netwerk niet meer bestaat.

Elke twee minuten tijdens het migratieproces rapporteert een voortgangsindicator de huidige status, zoals wordt weergegeven in de volgende voorbeelduitvoer:

![Voortgangsindicator voor de migratie van Azure AD DS](media/migrate-from-classic-vnet/powershell-migration-status.png)

Het migratieproces wordt voortgezet, zelfs als u het PowerShell-script afsluit. In de Azure-portal wordt de status van het beheerde domein gerapporteerd als *Migreren*.

Wanneer de migratie is voltooid, u het IP-adres van uw eerste domeincontroller bekijken in de Azure-portal of via Azure PowerShell. Er wordt ook een tijdsschatting weergegeven voor de beschikbare tweede domeincontroller.

In dit stadium u optioneel andere bestaande bronnen verplaatsen van het klassieke implementatiemodel en het virtuele netwerk. U de resources in het klassieke implementatiemodel ook behouden en de virtuele netwerken met elkaar peeren nadat de Azure AD DS-migratie is voltooid.

## <a name="test-and-verify-connectivity-after-the-migration"></a>Connectiviteit na de migratie testen en verifiëren

Het kan enige tijd duren voordat de tweede domeincontroller is geïmplementeerd en beschikbaar is voor gebruik in het beheerde Azure AD DS-domein.

Met het implementatiemodel Resource Beheer worden de netwerkbronnen voor het beheerde Azure AD DS-domein weergegeven in de Azure-portal of Azure PowerShell. Zie [Netwerkbronnen die worden gebruikt door Azure AD DS][network-resources]voor meer informatie over wat deze netwerkbronnen zijn en doen.

Wanneer er ten minste één domeincontroller beschikbaar is, voert u de volgende configuratiestappen uit voor netwerkconnectiviteit met VM's:

* **DNS-serverinstellingen bijwerken** Als u andere bronnen in het virtuele netwerk Resource Beheer wilt laten oplossen en het beheerde azure AD DS-domein wilt gebruiken, werkt u de DNS-instellingen bij met de IP-adressen van de nieuwe domeincontrollers. De Azure-portal kan deze instellingen automatisch voor u configureren. Zie [DNS-instellingen voor het virtuele Azure-netwerk][update-dns]bijwerken voor meer informatie over het configureren van het virtuele netwerk Van ResourceBeheer.
* **Vm's met domeinopnieuw opstarten** - Terwijl de IP-adressen van de DNS-server voor de Azure AD DS-domeincontrollers worden gewijzigd, start u alle vm's met domeinverbonden opnieuw, zodat ze vervolgens de nieuwe DNS-serverinstellingen gebruiken. Als toepassingen of VM's handmatig DNS-instellingen hebben geconfigureerd, werkt u deze handmatig bij met de nieuwe IP-adressen van de DNS-server van de domeincontrollers die in de Azure-portal worden weergegeven.

Test nu de virtuele netwerkverbinding en naamomzetting. Probeer de volgende netwerkcommunicatietests op een vm die is verbonden met het virtuele netwerk van Resource Manager of ernaar is gekeken:

1. Controleer of u het IP-adres van een van de domeincontrollers pingen, zoals`ping 10.1.0.4`
    * De IP-adressen van de domeincontrollers worden weergegeven op de pagina **Eigenschappen** voor het beheerde Azure AD DS-domein in de Azure-portal.
1. De naamomzetting van het beheerde domein verifiëren, zoals`nslookup aaddscontoso.com`
    * Geef de DNS-naam op voor uw eigen Azure AD DS-beheerde domein om te controleren of de DNS-instellingen correct zijn en worden opgelost.

De tweede domeincontroller moet 1-2 uur na het einde van de migratiecmdlet beschikbaar zijn. Als u wilt controleren of de tweede domeincontroller beschikbaar is, raadpleegt u de pagina **Eigenschappen** voor het beheerde Azure AD DS-domein in de Azure-portal. Als twee IP-adressen worden weergegeven, is de tweede domeincontroller gereed.

## <a name="optional-post-migration-configuration-steps"></a>Optionele configuratiestappen na migratie

Wanneer het migratieproces is voltooid, zijn enkele optionele configuratiestappen het inschakelen van controlelogboeken of e-mailmeldingen of het bijwerken van het fijnmazige wachtwoordbeleid.

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>U abonneren op controlelogboeken met Azure Monitor

Azure AD DS stelt controlelogboeken bloot om gebeurtenissen op de domeincontrollers op te lossen en te bekijken. Zie [Controlelogboeken inschakelen en gebruiken][security-audits]voor meer informatie .

U sjablonen gebruiken om belangrijke informatie in de logboeken te controleren. De werkmapsjabloon voor controlelogboekkan bijvoorbeeld mogelijke accountuitsluitingen op het beheerde Azure AD DS-domein controleren.

### <a name="configure-azure-ad-domain-services-email-notifications"></a>E-mailmeldingen van Azure AD Domain Services configureren

Als u een melding wilt ontvangen wanneer er een probleem wordt gedetecteerd in het door Azure AD DS beheerde domein, werkt u de instellingen voor e-mailmeldingen in de Azure-portal bij. Zie [Meldingsinstellingen configureren][notifications]voor meer informatie.

### <a name="update-fine-grained-password-policy"></a>Fijnkorrelig wachtwoordbeleid bijwerken

Indien nodig u het fijnmazige wachtwoordbeleid bijwerken om minder beperkend te zijn dan de standaardconfiguratie. U de controlelogboeken gebruiken om te bepalen of een minder beperkende instelling zinvol is en configureert het beleid zo nodig. Gebruik de volgende stappen op hoog niveau om de beleidsinstellingen voor accounts die herhaaldelijk zijn vergrendeld na migratie te controleren en bij te werken:

1. [Configureer wachtwoordbeleid][password-policy] voor minder beperkingen op het door Azure AD DS beheerde domein en observeer de gebeurtenissen in de controlelogboeken.
1. Als serviceaccounts verlopen wachtwoorden gebruiken zoals die zijn geïdentificeerd in de controlelogboeken, werkt u deze accounts bij met het juiste wachtwoord.
1. Als een virtuele machine wordt blootgesteld aan het internet, controleert u op algemene accountnamen zoals *beheerder,* *gebruiker*of *gast* met hoge aanmeldingspogingen. Werk waar mogelijk deze VM's bij om minder algemeen genoemde accounts te gebruiken.
1. Gebruik een netwerktracering op de VM om de bron van de aanvallen te vinden en te voorkomen dat deze IP-adressen zich aanmelden.
1. Wanneer er minimale vergrendelingsproblemen zijn, werkt u het fijnkorrelige wachtwoordbeleid bij om zo beperkend mogelijk te zijn.

### <a name="creating-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Azure AD DS heeft een netwerkbeveiligingsgroep nodig om de poorten te beveiligen die nodig zijn voor het beheerde domein en alle andere binnenkomende verkeer te blokkeren. Deze netwerkbeveiligingsgroep fungeert als een extra beveiligingslaag om de toegang tot het beheerde domein te vergrendelen en wordt niet automatisch gemaakt. Als u de netwerkbeveiligingsgroep wilt maken en de vereiste poorten wilt openen, controleert u de volgende stappen:

1. Selecteer in de Azure-portal uw AD DS-bron in Azure. Op de overzichtspagina wordt een knop weergegeven om een netwerkbeveiligingsgroep te maken als er geen is gekoppeld aan Azure AD Domain Services.
1. Als u beveiligde LDAP gebruikt, voegt u een regel toe aan de netwerkbeveiligingsgroep om binnenkomend verkeer toe te staan voor *TCP-poort* *636.* Zie [Beveiligde LDAP configureren][secure-ldap]voor meer informatie.

## <a name="roll-back-and-restore-from-migration"></a>Terugdraaien en herstellen van migratie

Tot op een bepaald punt in het migratieproces u ervoor kiezen om het beheerde Azure AD DS-beheerde domein terug te draaien of te herstellen.

### <a name="roll-back"></a>Terugdraaien

Als er een fout optreedt wanneer u de PowerShell-cmdlet uitvoert om zich voor te bereiden op migratie in stap 2 of op de migratie zelf in stap 3, kan het beheerde Azure AD DS-domein terugdraaien naar de oorspronkelijke configuratie. Deze terugdraaiing vereist het originele Virtuele Classic-netwerk. Houd er rekening mee dat de IP-adressen na het terugdraaien nog steeds kunnen worden gewijzigd.

Voer `Migrate-Aadds` de cmdlet uit met de parameter *-Afbreken.* Geef de *-ManagedDomainFqdn* op voor uw eigen Azure AD DS-beheerde domein dat in een vorige sectie is opgesteld, zoals *aaddscontoso.com*en de klassieke virtuele netwerknaam, zoals *myClassicVnet:*

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn aaddscontoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds
```

### <a name="restore"></a>Herstellen

Als laatste redmiddel kunnen Azure AD Domain Services worden hersteld vanaf de laatst beschikbare back-up. Een back-up wordt genomen in stap 1 van de migratie om ervoor te zorgen dat de meest recente back-up beschikbaar is. Deze back-up wordt 30 dagen bewaard.

Als u het door Azure AD DS beheerde domein wilt herstellen van back-up, [opent u een ondersteuningsaanvraagticket met behulp van de Azure-portal.][azure-support] Geef uw directory-id, domeinnaam en reden voor herstel op. Het ondersteunings- en herstelproces kan meerdere dagen in beslag nemen.

## <a name="troubleshooting"></a>Problemen oplossen

Als u problemen ondervindt na de migratie naar het implementatiemodel resourcebeheer, controleert u enkele van de volgende veelvoorkomende probleemoplossingsgebieden:

* [Problemen met domeinjoinoplossen][troubleshoot-domain-join]
* [Problemen met accountvergrendeling oplossen][troubleshoot-account-lockout]
* [Problemen met aanmelding bij account oplossen][troubleshoot-sign-in]
* [Problemen met beveiligde LDAP-connectiviteit oplossen][tshoot-ldaps]

## <a name="next-steps"></a>Volgende stappen

Met uw Azure AD DS beheerd domein gemigreerd naar het Resource [Manager-implementatiemodel, maakt en sluit u een Windows-vm aan en][join-windows] installeert u vervolgens [beheerhulpprogramma's.][tutorial-create-management-vm]

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/overview
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Set-AzContext]: /powershell/module/az.accounts/set-azcontext
[Get-AzResource]: /powershell/module/az.resources/get-azresource
[Set-AzResource]: /powershell/module/az.resources/set-azresource
[network-resources]: network-considerations.md#network-resources-used-by-azure-ad-ds
[update-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[security-audits]: security-audit-events.md
[notifications]: notifications.md
[password-policy]: password-policy.md
[secure-ldap]: tutorial-configure-ldaps.md
[migrate-iaas]: ../virtual-machines/windows/migration-classic-resource-manager-overview.md
[join-windows]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[troubleshoot-domain-join]: troubleshoot-domain-join.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[tshoot-ldaps]: tshoot-ldaps.md
[get-credential]: /powershell/module/microsoft.powershell.security/get-credential

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/
