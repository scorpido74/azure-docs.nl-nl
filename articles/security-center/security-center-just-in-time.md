---
title: Just-in-time-toegang tot virtuele machines in Azure Security Center | Microsoft Docs
description: In dit document wordt gedemonstreerd hoe just-in-time-VM-toegang in Azure Security Center u de toegang tot uw virtuele Azure-machines kunt beheren.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: b24e0487aef73ed7852cb4a64766a1f8d92aff94
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84677425"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Beveilig uw beheer poorten met Just-in-time-toegang

Als u zich in de Standard-prijs categorie van Security Center bevindt (Zie [prijzen](/azure/security-center/security-center-pricing)), kunt u inkomend verkeer naar uw Azure-vm's vergren delen met Just-in-time-toegang (VM) voor virtuele machines. Dit vermindert de bloot stelling aan aanvallen en biedt zo eenvoudig toegang om verbinding te maken met Vm's wanneer dat nodig is.

> [!NOTE]
> Security Center just-in-time-VM-toegang ondersteunt momenteel alleen Vm's die via Azure Resource Manager zijn geïmplementeerd. Zie [Azure Resource Manager vs. klassieke implementatie](../azure-resource-manager/management/deployment-models.md)voor meer informatie over het klassieke en Resource Manager-implementatie model.

[!INCLUDE [security-center-jit-description](../../includes/security-center-jit-description.md)]

## <a name="configure-jit-on-a-vm"></a>JIT configureren op een virtuele machine

Er zijn drie manieren om een JIT-beleid te configureren op een virtuele machine:

- [JIT-toegang configureren in Azure Security Center](#jit-asc)
- [JIT-toegang configureren op een Azure VM-pagina](#jit-vm)
- [Een JIT-beleid op een virtuele machine via een programma configureren](#jit-program)

## <a name="configure-jit-in-azure-security-center"></a>JIT configureren in Azure Security Center

Vanuit Security Center kunt u een JIT-beleid configureren en toegang aanvragen tot een virtuele machine met behulp van een JIT-beleid

### <a name="configure-jit-access-on-a-vm-in-security-center"></a>JIT-toegang configureren op een virtuele machine in Security Center<a name="jit-asc"></a>

1. Open het dashboard van **Security Center**.

1. Selecteer in het linkerdeel venster **just-in-time-VM-toegang**.

    ![Tegel just-in-time-VM-toegang](./media/security-center-just-in-time/just-in-time.png)

    Het venster **just-in-time-VM-toegang** wordt geopend en toont informatie over de status van uw vm's:

    - **Geconfigureerd** : vm's die zijn geconfigureerd voor ondersteuning van just-in-time-VM-toegang. De gegevens die worden weer gegeven, zijn de afgelopen week en bevatten voor elke VM het aantal goedgekeurde aanvragen, de datum en tijd van laatste toegang en de laatste gebruiker.
    - **Aanbevolen** : vm's die just-in-time-VM-toegang kunnen ondersteunen, maar die niet zijn geconfigureerd voor. U wordt aangeraden just-in-time-VM-toegangs beheer in te scha kelen voor deze Vm's.
    - **Geen aanbeveling**: redenen waarom een VM mogelijk niet wordt aanbevolen zijn:
      - Ontbrekende NSG: voor de just-in-time-oplossing moet een NSG aanwezig zijn.
      - Klassieke VM-Security Center just-in-time-VM-toegang ondersteunt momenteel alleen Vm's die via Azure Resource Manager zijn geïmplementeerd. Een klassieke implementatie wordt niet ondersteund door de just-in-time-oplossing. 
      - Andere-een VM bevindt zich in deze categorie als de just-in-time-oplossing is uitgeschakeld in het beveiligings beleid van het abonnement of de resource groep, of als op de virtuele machine een openbaar IP-adres ontbreekt en er geen NSG aanwezig is.

1. Selecteer het tabblad **Aanbevolen** .

1. Onder **virtuele machine**klikt u op de virtuele machines die u wilt inschakelen. Hiermee wordt een vinkje bij een virtuele machine geplaatst.

      ![Just-in-time-toegang inschakelen](./media/security-center-just-in-time/enable-just-in-time.png)

1. Klik op **JIT inschakelen op vm's**. Er wordt een deel venster geopend met de standaard poorten die worden aanbevolen door Azure Security Center:
    - 22-SSH
    - 3389-RDP
    - 5985-WinRM 
    - 5986-WinRM
1. U kunt desgewenst aangepaste poorten toevoegen aan de lijst:

      1. Klik op **Add**. Het venster **poort configuratie toevoegen** wordt geopend.
      1. Voor elke poort die u wilt configureren, zowel standaard als aangepast, kunt u de volgende instellingen aanpassen:
            - **Protocol type**: het protocol dat is toegestaan op deze poort wanneer een aanvraag wordt goedgekeurd.
            - **Toegestane IP-adressen van bron**-de IP-bereiken die op deze poort zijn toegestaan wanneer een aanvraag wordt goedgekeurd.
            - **Maximale aanvraag tijd**: de maximale tijd venster gedurende welke een specifieke poort kan worden geopend.

     1. Klik op **OK**.

1. Klik op **Opslaan**.

> [!NOTE]
>Wanneer JIT-VM-toegang is ingeschakeld voor een virtuele machine, maakt Azure Security Center de regels ' alle binnenkomend verkeer weigeren ' voor de geselecteerde poorten in de netwerk beveiligings groepen die zijn gekoppeld en Azure Firewall. Als er andere regels zijn gemaakt voor de geselecteerde poorten, hebben de bestaande regels voor rang op de nieuwe regels ' alle inkomende verkeer weigeren '. Als er geen bestaande regels zijn op de geselecteerde poorten, hebben de nieuwe regels voor het weigeren van binnenkomend verkeer de hoogste prioriteit in de netwerk beveiligings groepen en de Azure Firewall.


## <a name="request-jit-access-via-security-center"></a>JIT-toegang aanvragen via Security Center

Om toegang te vragen tot een virtuele machine via Security Center:

1. Onder **just-in-time-VM-toegang**selecteert u het tabblad **geconfigureerd** .

1. Onder **virtuele machine**klikt u op de virtuele machines waarvoor u toegang wilt aanvragen. Hiermee wordt een vinkje naast de virtuele machine geplaatst.

    - Het pictogram in de kolom **verbindings Details** geeft aan of JIT is ingeschakeld op de NSG of FW. Als de functie is ingeschakeld op beide, wordt alleen het pictogram Firewall weer gegeven.

    - In de kolom **verbindings Details** vindt u de informatie die nodig is om verbinding te maken met de virtuele machine en de bijbehorende open poorten.

      ![Just-in-time-toegang aanvragen](./media/security-center-just-in-time/request-just-in-time-access.png)

1. Klik op **toegang aanvragen**. Het venster **toegang tot aanvragen** wordt geopend.

      ![JIT-Details](./media/security-center-just-in-time/just-in-time-details.png)

1. Configureer onder **toegang aanvragen**voor elke virtuele machine de poorten die u wilt openen en de bron-IP-adressen waarop de poort is geopend en het tijd venster waarvoor de poort wordt geopend. Het is alleen mogelijk om toegang aan te vragen voor de poorten die zijn geconfigureerd in het just-in-time-beleid. Elke poort heeft een Maxi maal toegestane tijd die is afgeleid van het just-in-time-beleid.

1. Klik op **poorten openen**.

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
2. Onder **vm's**selecteert u een virtuele machine om informatie weer te geven over door te klikken op de drie puntjes in de rij voor die VM en selecteert u **activiteiten logboek** in het menu. Het **activiteiten logboek** wordt geopend.

   ![Activiteiten logboek selecteren](./media/security-center-just-in-time/select-activity-log.png)

   Het **activiteiten logboek** bevat een gefilterde weer gave van eerdere bewerkingen voor die virtuele machine, samen met tijd, datum en abonnement.

U kunt de logboek gegevens downloaden door **hier te klikken om alle items te downloaden als CSV**-bestand.

Wijzig de filters en klik op **Toep assen** om een zoek opdracht en logboek te maken.



## <a name="configure-jit-access-from-an-azure-vms-page"></a>JIT-toegang vanaf een Azure VM-pagina configureren<a name="jit-vm"></a>

Voor uw gemak kunt u vanuit de pagina van de virtuele machine in Security Center verbinding maken met een virtuele machine met behulp van JIT.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>JIT-toegang op een virtuele machine configureren via de Azure VM-pagina

Als u de just-in-time-toegang op uw Vm's eenvoudig wilt implementeren, kunt u een virtuele machine zo instellen dat alleen just-in-time-toegang rechtstreeks vanuit de virtuele machine wordt toegestaan.

1. Zoek in het [Azure Portal](https://ms.portal.azure.com)naar **virtuele machines**en selecteer deze. 
2. Selecteer de virtuele machine die u wilt beperken tot just-in-time-toegang.
3. Selecteer in het menu **configuratie**.
4. Onder **just-in-time-toegang**selecteert u **just-in-time inschakelen**. 

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

### <a name="request-jit-access-to-a-vm-via-an-azure-vms-page"></a>JIT-toegang aanvragen tot een virtuele machine via de pagina van een Azure-VM

Wanneer u in de Azure Portal probeert verbinding te maken met een virtuele machine, controleert Azure of u een just-in-time-toegangs beleid op die VM hebt geconfigureerd.

- Als u een JIT-beleid op de virtuele machine hebt geconfigureerd, kunt u op **toegang aanvragen** klikken om toegang te verlenen volgens het JIT-beleid dat is ingesteld voor de virtuele machine. 

  >![JIT-aanvraag](./media/security-center-just-in-time/jit-request.png)

  Toegang is aangevraagd met de volgende standaard parameters:

  - **bron-IP**: ' any ' (*) (kan niet worden gewijzigd)
  - **tijds bereik**: drie uur (kan niet worden gewijzigd) <!--Isn't this set in the policy-->
  - **poort nummer** RDP-poort 3389 voor Windows/poort 22 voor Linux (kan worden gewijzigd)

    > [!NOTE]
    > Nadat een aanvraag is goedgekeurd voor een virtuele machine die wordt beveiligd door Azure Firewall, geeft Security Center de gebruiker de juiste verbindings gegevens (de poort toewijzing van de tabel DNAT) die moet worden gebruikt om verbinding te maken met de virtuele machine.

- Als u geen JIT hebt geconfigureerd op een virtuele machine, wordt u gevraagd om een JIT-beleid te configureren.

  ![JIT-prompt](./media/security-center-just-in-time/jit-prompt.png)

## <a name="configure-a-jit-policy-on-a-vm-programmatically"></a>Een JIT-beleid op een virtuele machine via een programma configureren<a name="jit-program"></a>

U kunt just-in-time instellen en gebruiken via REST Api's en via Power shell.

### <a name="jit-vm-access-via-rest-apis"></a>JIT-VM-toegang via REST-Api's

De just-in-time VM-toegangs functie kan worden gebruikt via de Azure Security Center-API. U kunt informatie krijgen over geconfigureerde Vm's, nieuwe toevoegen, toegang aanvragen tot een virtuele machine en meer via deze API. Zie [JIT-netwerk toegangs beleid](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)voor meer informatie over de just-in-time-rest API.

### <a name="jit-vm-access-via-powershell"></a>JIT-VM-toegang via Power shell

Als u de just-in-time-toegang tot een VM wilt gebruiken via Power shell, gebruikt u de officiële Azure Security Center Power shell-cmdlets en specifiek `Set-AzJitNetworkAccessPolicy` .

In het volgende voor beeld wordt een just-in-time-VM-toegangs beleid ingesteld op een specifieke virtuele machine en worden de volgende opties ingesteld:

1.    Sluit poort 22 en 3389.

2.    Stel een maximum tijd venster van drie uur in voor elke periode zodat deze kan worden geopend per goedgekeurde aanvraag.
3.    Hiermee staat u toe dat de gebruiker die toegang aanvraagt om de bron-IP-adressen te beheren en de gebruiker in staat stelt een geslaagde sessie te maken op een goedgekeurde just-in-time-toegangs aanvraag.

Voer de volgende handelingen uit in Power shell om dit te bewerkstelligen:

1.    Wijs een variabele toe die het just-in-time-VM-toegangs beleid voor een virtuele machine bevat:

        $JitPolicy = (@ {id = "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";   poorten = (@ {Number = 22;        Protocol = " \* ";        allowedSourceAddressPrefix = @ (" \* ");        maxRequestAccessDuration = "PT3H"}, @ {Number = 3389;        Protocol = " \* ";        allowedSourceAddressPrefix = @ (" \* ");        maxRequestAccessDuration = "PT3H"})})

2.    Voeg het just-in-time-VM-toegangs beleid in op een matrix:
    
        $JitPolicyArr = @ ($JitPolicy)

3.    Configureer het just-in-time-toegangs beleid voor de virtuele machine op de geselecteerde VM:
    
        Set-AzJitNetworkAccessPolicy-type "Basic"-Location "locatie"-name ""-ResourceGroupName "RESOURCEGROUP"-VirtualMachine $JitPolicyArr 

### <a name="request-access-to-a-vm-via-powershell"></a>Toegang aanvragen tot een virtuele machine via Power shell

In het volgende voor beeld ziet u een just-in-time-toegangs aanvraag voor een virtuele machine naar een specifieke virtuele machine waarvoor poort 22 is aangevraagd voor het openen van een specifiek IP-adres en voor een specifieke hoeveelheid tijd:

Voer het volgende uit in Power shell:
1.    De toegangs eigenschappen van de VM-aanvraag configureren

        $JitPolicyVm 1 = (@ {id = "/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";   poorten = (@ {Number = 22;      endTimeUtc = "2018-09-17T17:00:00.3658798 Z";      allowedSourceAddressPrefix = @ ("IPV4ADDRESS")})})
2.    De para meters voor de VM-toegangs aanvraag invoegen in een matrix:

        $JitPolicyArr = @ ($JitPolicyVm 1)
3.    De aanvraag toegang verzenden (gebruik de resource-ID die u in stap 1 hebt ontvangen)

        Start-AzJitNetworkAccessPolicy-ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default"-VirtualMachine $JitPolicyArr

Zie de [Power shell-cmdlet-documentatie](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview)voor meer informatie.


## <a name="automatic-cleanup-of-redundant-jit-rules"></a>Automatisch opschonen van redundante JIT-regels 

Telkens wanneer u een JIT-beleid bijwerkt, wordt automatisch een Cleanup-hulp programma uitgevoerd om de geldigheid van de volledige ruleset te controleren. Het hulp programma zoekt naar verschillen tussen regels in uw beleid en regels in het NSG. Als het hulp programma voor opschonen een niet-overeenkomend item detecteert, wordt de oorzaak bepaald en, wanneer het veilig is om dit te doen, verwijdert u ingebouwde regels die niet meer nodig zijn. De verruiming verwijdert nooit regels die u hebt gemaakt.

Voor beelden van scenario's waarin de Removal een ingebouwde regel kan verwijderen:

- Wanneer er twee regels met identieke definities bestaan en een hogere prioriteit heeft dan de andere, (wat betekent dat de regel met een lagere prioriteit nooit wordt gebruikt)
- Wanneer een regel beschrijving de naam bevat van een virtuele machine die niet overeenkomt met de doel-IP in de regel 


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u met Just-in-time-VM-toegang in Security Center de toegang tot uw virtuele Azure-machines kunt beheren.

Zie de volgende onderwerpen voor meer informatie over Security Center:

- Met de module Microsoft Learn [worden uw servers en virtuele machines beschermd tegen brute-force-aanvallen en schadelijke software met Azure Security Center](https://docs.microsoft.com/learn/modules/secure-vms-with-azure-security-center/)
- [Beveiligings beleid instellen](tutorial-security-policy.md) : informatie over het configureren van beveiligings beleid voor uw Azure-abonnementen en-resource groepen.
- [Aanbevelingen voor beveiliging beheren](security-center-recommendations.md) : Ontdek hoe u met aanbevelingen uw Azure-resources kunt beveiligen.
- [Bewaking van beveiligings status](security-center-monitoring.md) : Leer hoe u de status van uw Azure-resources kunt bewaken.
