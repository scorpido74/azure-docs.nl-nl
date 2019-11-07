---
title: Just-in-time-toegang tot virtuele machines in Azure Security Center | Microsoft Docs
description: In dit document wordt gedemonstreerd hoe just-in-time-VM-toegang in Azure Security Center u de toegang tot uw virtuele Azure-machines kunt beheren.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: fb092a3c6b473680480c3bba0ad6f437176833de
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73576406"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Toegang tot virtuele machines beheren met Just-in-time

Just-in-time-toegang (VM) voor virtuele machines kan worden gebruikt om inkomend verkeer naar uw Azure-Vm's te vergren delen, waardoor de bloot stelling aan aanvallen wordt verkleind en zo snel mogelijk toegang tot virtuele machines kan worden gemaakt.

> [!NOTE]
> De just-in-time-functie is beschikbaar in de laag standaard van Security Center. Bekijk de pagina [Prijzen](security-center-pricing.md) voor meer informatie over de tariefopties van Security Center.


> [!NOTE]
> Security Center just-in-time-VM-toegang ondersteunt momenteel alleen Vm's die via Azure Resource Manager zijn geïmplementeerd. Zie [Azure Resource Manager vs. klassieke implementatie](../azure-resource-manager/resource-manager-deployment-model.md)voor meer informatie over het klassieke en Resource Manager-implementatie model.

## <a name="attack-scenario"></a>Aanvals scenario

Brute force-aanvallen zijn vaak gericht op beheer poorten als middel om toegang te krijgen tot een virtuele machine. Als dit lukt, kan een aanvaller de controle over de virtuele machine overnemen en een aanvaller binnen in uw omgeving tot stand brengen.

Een manier om de bloot stelling aan een beveiligings aanval te verminderen is het beperken van de hoeveelheid tijd die een poort is geopend. Beheer poorten hoeven niet te allen tijde open zijn. Ze hoeven alleen te zijn geopend terwijl u verbonden bent met de virtuele machine, bijvoorbeeld om beheer-of onderhouds taken uit te voeren. Wanneer just-in-time is ingeschakeld, maakt Security Center gebruik van [netwerk beveiligings groep](../virtual-network/security-overview.md#security-rules) (NSG) en Azure firewall regels, waarmee de toegang tot beheer poorten wordt beperkt zodat deze niet kunnen worden benaderd door aanvallers.

![Just-in-time-scenario](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Hoe werkt JIT-toegang?

Wanneer just-in-time is ingeschakeld, wordt binnenkomend verkeer naar uw Azure-Vm's door Security Center vergrendeld door een NSG-regel te maken. U selecteert de poorten op de VM waarop het inkomende verkeer wordt vergrendeld. Deze poorten worden bepaald door de just-in-time-oplossing.

Wanneer een gebruiker toegang tot een virtuele machine vraagt, controleert Security Center of de gebruiker [op rollen gebaseerde Access Control (RBAC)-](../role-based-access-control/role-assignments-portal.md) machtigingen voor die VM heeft. Als de aanvraag is goedgekeurd, Security Center automatisch de netwerk beveiligings groepen (Nsg's) en Azure Firewall zodanig configureren dat inkomend verkeer naar de geselecteerde poorten en aangevraagde bron-IP-adressen of-bereiken worden toegestaan voor de opgegeven hoeveelheid tijd. Nadat de tijd is verstreken, wordt de Nsg's door Security Center teruggezet naar de vorige status. Deze verbindingen die al tot stand zijn gebracht, worden echter niet onderbroken.

 > [!NOTE]
 > Als een JIT-toegangs aanvraag wordt goedgekeurd voor een virtuele machine achter een Azure Firewall, wijzigt Security Center automatisch de beleids regels NSG en firewall. Voor de hoeveelheid tijd die is opgegeven, staan de regels binnenkomend verkeer naar de geselecteerde poorten toe en aangevraagde IP-adressen of bereiken van de bron. Nadat de tijd is overschreden Security Center, worden de firewall-en NSG-regels teruggezet naar de vorige status.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Benodigde machtigingen voor het configureren en gebruiken van JIT

| Een gebruiker in staat stellen: | Machtigingen om in te stellen|
| --- | --- |
| Een JIT-beleid voor een virtuele machine configureren of bewerken | *Wijs deze acties toe aan de rol:*  <ul><li>Binnen het bereik van een abonnement of resource groep die is gekoppeld aan de virtuele machine:<br/> ```Microsoft.Security/locations/jitNetworkAccessPolicies/write``` </li><li> Binnen het bereik van een abonnement of resource groep of VM: <br/>```Microsoft.Compute/virtualMachines/write```</li></ul> | 
| ||
|JIT-toegang aanvragen voor een virtuele machine | *Deze acties toewijzen aan de gebruiker:*  <ul><li>Binnen het bereik van een abonnement of resource groep die is gekoppeld aan de virtuele machine:<br/>  ```Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action``` </li><li>  Binnen het bereik van een abonnement of resource groep of VM:<br/> ```Microsoft.Compute/virtualMachines/read``` </li></ul>|


## <a name="configure-jit-on-a-vm"></a>JIT configureren op een virtuele machine

Er zijn drie manieren om een JIT-beleid te configureren op een virtuele machine:

- [JIT-toegang configureren in Azure Security Center](#jit-asc)
- [JIT-toegang configureren in een Azure VM-Blade](#jit-vm)
- [Een JIT-beleid op een virtuele machine via een programma configureren](#jit-program)

## <a name="configure-jit-in-security-center"></a>JIT configureren in Security Center

Vanuit Security Center kunt u een JIT-beleid configureren en toegang aanvragen tot een virtuele machine met behulp van een JIT-beleid


### JIT-toegang configureren op een virtuele machine in Security Center<a name="jit-asc"></a>

1. Open het dashboard van **Security Center**.

2. Selecteer in het linkerdeel venster **just-in-time-VM-toegang**.

    ![Tegel just-in-time-VM-toegang](./media/security-center-just-in-time/just-in-time.png)

    Het venster **just-in-time-VM-toegang** wordt geopend.

      ![Just-in-time-toegang inschakelen](./media/security-center-just-in-time/enable-just-in-time.png)

    **Just-in-time-VM-toegang** biedt informatie over de status van uw vm's:

    - **Geconfigureerd** : vm's die zijn geconfigureerd voor ondersteuning van just-in-time-VM-toegang. De gegevens die worden weer gegeven, zijn de afgelopen week en bevatten voor elke VM het aantal goedgekeurde aanvragen, de datum en tijd van laatste toegang en de laatste gebruiker.
    - **Aanbevolen** : vm's die just-in-time-VM-toegang kunnen ondersteunen, maar die niet zijn geconfigureerd voor. U wordt aangeraden just-in-time-VM-toegangs beheer in te scha kelen voor deze Vm's.
    - **Geen aanbeveling**: redenen waarom een VM mogelijk niet wordt aanbevolen zijn:
      - Ontbrekende NSG: voor de just-in-time-oplossing moet een NSG aanwezig zijn.
      - Klassieke VM-Security Center just-in-time-VM-toegang ondersteunt momenteel alleen Vm's die via Azure Resource Manager zijn geïmplementeerd. Een klassieke implementatie wordt niet ondersteund door de just-in-time-oplossing. 
      - Andere-een VM bevindt zich in deze categorie als de just-in-time-oplossing is uitgeschakeld in het beveiligings beleid van het abonnement of de resource groep, of als op de virtuele machine een openbaar IP-adres ontbreekt en er geen NSG aanwezig is.

3. Selecteer het tabblad **Aanbevolen** .

4. Onder **virtuele machine**klikt u op de virtuele machines die u wilt inschakelen. Hiermee wordt een vinkje bij een virtuele machine geplaatst.

5. Klik op **JIT inschakelen op vm's**.
   -. Deze Blade bevat de standaard poorten die worden aanbevolen door Azure Security Center:
      - 22-SSH
      - 3389-RDP
      - 5985-WinRM 
      - 5986-WinRM
6. U kunt ook aangepaste poorten configureren:

      1. Klik op **Toevoegen**. Het venster **poort configuratie toevoegen** wordt geopend.
      2. Voor elke poort die u wilt configureren, zowel standaard als aangepast, kunt u de volgende instellingen aanpassen:

    - **Protocol type**: het protocol dat is toegestaan op deze poort wanneer een aanvraag wordt goedgekeurd.
    - **Toegestane IP-adressen van bron**-de IP-bereiken die op deze poort zijn toegestaan wanneer een aanvraag wordt goedgekeurd.
    - **Maximale aanvraag tijd**: de maximale tijd venster gedurende welke een specifieke poort kan worden geopend.

     3. Klik op **OK**.

1. Klik op **Opslaan**.

> [!NOTE]
>Wanneer JIT-VM-toegang is ingeschakeld voor een virtuele machine, maakt Azure Security Center de regels ' alle binnenkomend verkeer weigeren ' voor de geselecteerde poorten in de netwerk beveiligings groepen die zijn gekoppeld en Azure Firewall. Als er andere regels zijn gemaakt voor de geselecteerde poorten, hebben de bestaande regels voor rang op de nieuwe regels ' alle inkomende verkeer weigeren '. Als er geen bestaande regels zijn op de geselecteerde poorten, hebben de nieuwe regels voor het weigeren van binnenkomend verkeer de hoogste prioriteit in de netwerk beveiligings groepen en de Azure Firewall.


## <a name="request-jit-access-via-security-center"></a>JIT-toegang aanvragen via Security Center

Om toegang te vragen tot een virtuele machine via Security Center:

1. Onder **just-in-time-VM-toegang**selecteert u het tabblad **geconfigureerd** .

2. Onder **virtuele machine**klikt u op de virtuele machines waarvoor u toegang wilt aanvragen. Hiermee wordt een vinkje naast de virtuele machine geplaatst.


    - Het pictogram in de kolom **verbindings Details** geeft aan of JIT is ingeschakeld op de NSG of FW. Als de functie is ingeschakeld op beide, wordt alleen het pictogram Firewall weer gegeven.

    - In de kolom **verbindings Details** vindt u de informatie die nodig is om verbinding te maken met de virtuele machine en de bijbehorende open poorten.

      ![Just-in-time-toegang aanvragen](./media/security-center-just-in-time/request-just-in-time-access.png)

3. Klik op **toegang aanvragen**. Het venster **toegang tot aanvragen** wordt geopend.

      ![JIT-Details](./media/security-center-just-in-time/just-in-time-details.png)

4. Configureer onder **toegang aanvragen**voor elke virtuele machine de poorten die u wilt openen en de bron-IP-adressen waarop de poort is geopend en het tijd venster waarvoor de poort wordt geopend. Het is alleen mogelijk om toegang aan te vragen voor de poorten die zijn geconfigureerd in het just-in-time-beleid. Elke poort heeft een Maxi maal toegestane tijd die is afgeleid van het just-in-time-beleid.

5. Klik op **poorten openen**.

> [!NOTE]
> Als een gebruiker die toegang aanvraagt zich achter een proxy bevindt, werkt de optie **Mijn IP** mogelijk niet. Mogelijk moet u het volledige IP-adres bereik van de organisatie definiëren.

## <a name="edit-a-jit-access-policy-via-security-center"></a>Een JIT-toegangs beleid bewerken via Security Center

U kunt het bestaande just-in-time-beleid van een virtuele machine wijzigen door een nieuwe poort toe te voegen en te configureren voor de beveiliging van die virtuele machine of door een andere instelling te wijzigen die betrekking heeft op een reeds beveiligde poort.

Een bestaande just-in-time-beleid van een virtuele machine bewerken:
1. Op het tabblad **geconfigureerd** , onder **vm's**, selecteert u een virtuele machine waaraan u een poort wilt toevoegen door te klikken op de drie puntjes in de rij voor die virtuele machine. 

1. Selecteer **Bewerken**.
1. Onder **JIT VM-toegangs configuratie**kunt u de bestaande instellingen van een reeds beveiligde poort bewerken of een nieuwe aangepaste poort toevoegen. 
  ![JIT-VM-toegang](./media/security-center-just-in-time/edit-policy.png)

## <a name="audit-jit-access-activity-in-security-center"></a>JIT-toegangs activiteit controleren in Security Center

U kunt inzicht krijgen in VM-activiteiten met zoeken in Logboeken. Logboeken weer geven:

1. Onder **just-in-time-VM-toegang**selecteert u het tabblad **geconfigureerd** .
2. Selecteer onder **virtuele machines**een virtuele machine om informatie weer te geven over door te klikken op de drie puntjes in de rij voor die VM en selecteer **activiteiten logboek** in het menu. Het **activiteiten logboek** wordt geopend.

   ![Activiteiten logboek selecteren](./media/security-center-just-in-time/select-activity-log.png)

   Het **activiteiten logboek** bevat een gefilterde weer gave van eerdere bewerkingen voor die virtuele machine, samen met tijd, datum en abonnement.

U kunt de logboek gegevens downloaden door **hier te klikken om alle items te downloaden als CSV**-bestand.

Wijzig de filters en klik op **Toep assen** om een zoek opdracht en logboek te maken.



## JIT-toegang vanaf een Azure VM-pagina configureren<a name="jit-vm"></a>

Voor uw gemak kunt u vanuit de pagina van de virtuele machine in Security Center verbinding maken met een virtuele machine met behulp van JIT.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>JIT-toegang op een virtuele machine configureren via de Azure VM-pagina

Als u de just-in-time-toegang op uw Vm's eenvoudig wilt implementeren, kunt u een virtuele machine zo instellen dat alleen just-in-time-toegang rechtstreeks vanuit de virtuele machine wordt toegestaan.

1. Selecteer **virtuele machines**In het Azure Portal.
2. Klik op de virtuele machine die u wilt beperken tot just-in-time-toegang.
3. Klik in het menu op **configuratie**.
4. Onder **just-in-time-Access** klikt u op **just-in-time-beleid inschakelen**. 

Dit maakt just-in-time-toegang voor de virtuele machine mogelijk met de volgende instellingen:

- Windows-servers:
    - RDP-poort 3389
    - Drie uur voor Maxi maal toegestane toegang
    - Toegestane IP-adressen van bron worden ingesteld op een
- Linux-servers:
    - SSH-poort 22
    - Drie uur voor Maxi maal toegestane toegang
    - Toegestane IP-adressen van bron worden ingesteld op een
     
Als een virtuele machine al just-in-time is ingeschakeld, kunt u, wanneer u naar de pagina configuratie gaat, zien dat just-in-time is ingeschakeld en kunt u de koppeling gebruiken om het beleid in Azure Security Center te openen om de instellingen weer te geven en te wijzigen.

![JIT-configuratie in VM](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-the-azure-vm-blade"></a>JIT-toegang aanvragen tot een virtuele machine via de Blade Azure VM

Wanneer u in de Azure Portal probeert verbinding te maken met een virtuele machine, controleert Azure of u een just-in-time-toegangs beleid op die VM hebt geconfigureerd.

- Als u een JIT-beleid op de virtuele machine hebt geconfigureerd, kunt u op **toegang aanvragen** klikken om toegang te krijgen tot de set JIT-beleids regels voor de virtuele machine. 

  >![JIT-aanvraag](./media/security-center-just-in-time/jit-request.png)

  De toegang wordt aangevraagd met de volgende standaard parameters:

  - **bron-IP**: ' any ' (*) (kan niet worden gewijzigd)
  - **tijds bereik**: drie uur (kan niet worden gewijzigd) <!--Isn't this set in the policy-->
  - **poort nummer** RDP-poort 3389 voor Windows/poort 22 voor Linux (kan worden gewijzigd)

    > [!NOTE]
    > Nadat een aanvraag is goedgekeurd voor een virtuele machine die wordt beveiligd door Azure Firewall, geeft Security Center de gebruiker de juiste verbindings gegevens (de poort toewijzing van de tabel DNAT) die moet worden gebruikt om verbinding te maken met de virtuele machine.

- Als u geen JIT hebt geconfigureerd op een virtuele machine, wordt u gevraagd om een JIT-beleid te configureren.

  ![JIT-prompt](./media/security-center-just-in-time/jit-prompt.png)

## Een JIT-beleid op een virtuele machine via een programma configureren<a name="jit-program"></a>

U kunt just-in-time instellen en gebruiken via REST Api's en via Power shell.

## <a name="jit-vm-access-via-rest-apis"></a>JIT-VM-toegang via REST-Api's

De just-in-time VM-toegangs functie kan worden gebruikt via de Azure Security Center-API. U kunt informatie krijgen over geconfigureerde Vm's, nieuwe toevoegen, toegang aanvragen tot een virtuele machine en meer via deze API. Zie [JIT-netwerk toegangs beleid](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)voor meer informatie over de just-in-time-rest API.

## <a name="jit-vm-access-via-powershell"></a>JIT-VM-toegang via Power shell

Als u de just-in-time-toegang tot een VM wilt gebruiken via Power shell, gebruikt u de officiële Azure Security Center Power shell-cmdlets en specifiek `Set-AzJitNetworkAccessPolicy`.

In het volgende voor beeld wordt een just-in-time-VM-toegangs beleid ingesteld op een specifieke virtuele machine en worden de volgende opties ingesteld:

1.  Sluit poort 22 en 3389.

2.  Stel een maximum tijd venster van drie uur in voor elke periode zodat deze kan worden geopend per goedgekeurde aanvraag.
3.  Hiermee staat u toe dat de gebruiker die toegang aanvraagt om de bron-IP-adressen te beheren en de gebruiker in staat stelt een geslaagde sessie te maken op een goedgekeurde just-in-time-toegangs aanvraag.

Voer de volgende handelingen uit in Power shell om dit te bewerkstelligen:

1.  Wijs een variabele toe die het just-in-time-VM-toegangs beleid voor een virtuele machine bevat:

        $JitPolicy = (@{
         id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})

2.  Voeg het just-in-time-VM-toegangs beleid in op een matrix:
    
        $JitPolicyArr=@($JitPolicy)

3.  Configureer het just-in-time-toegangs beleid voor de virtuele machine op de geselecteerde VM:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="request-access-to-a-vm-via-powershell"></a>Toegang aanvragen tot een virtuele machine via Power shell

In het volgende voor beeld ziet u een just-in-time-toegangs aanvraag voor een virtuele machine naar een specifieke virtuele machine waarvoor poort 22 is aangevraagd voor het openen van een specifiek IP-adres en voor een specifieke hoeveelheid tijd:

Voer het volgende uit in Power shell:
1.  De toegangs eigenschappen van de VM-aanvraag configureren

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  De para meters voor de VM-toegangs aanvraag invoegen in een matrix:

        $JitPolicyArr=@($JitPolicyVm1)
3.  De aanvraag toegang verzenden (gebruik de resource-ID die u in stap 1 hebt ontvangen)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Zie de Power shell-cmdlet-documentatie voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u met Just-in-time-VM-toegang in Security Center de toegang tot uw virtuele Azure-machines kunt beheren.

Zie de volgende onderwerpen voor meer informatie over Security Center:

- [Beveiligings beleid instellen](tutorial-security-policy.md) : informatie over het configureren van beveiligings beleid voor uw Azure-abonnementen en-resource groepen.
- [Aanbevelingen voor beveiliging beheren](security-center-recommendations.md) : Ontdek hoe u met aanbevelingen uw Azure-resources kunt beveiligen.
- [Bewaking van beveiligings status](security-center-monitoring.md) : Leer hoe u de status van uw Azure-resources kunt bewaken.
- [Beveiligings waarschuwingen beheren en](security-center-managing-and-responding-alerts.md) erop reageren: informatie over het beheren en reageren op beveiligings waarschuwingen.
- [Partner oplossingen](security-center-partner-solutions.md) bewaken: Leer hoe u de integriteits status van uw partner oplossingen kunt bewaken.
- [Security Center FAQ](security-center-faq.md) : vind Veelgestelde vragen over het gebruik van de service.
- [Azure-beveiligingsblog](https://blogs.msdn.microsoft.com/azuresecurity/): lees blogberichten over de beveiliging en naleving van Azure.

