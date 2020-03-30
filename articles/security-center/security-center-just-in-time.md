---
title: Just-in-time toegang tot virtuele machines in Azure Security Center | Microsoft Documenten
description: Dit document laat zien hoe just-in-time VM-toegang in Azure Security Center u helpt de toegang tot uw virtuele Azure-machines te beheren.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 4b2b388fb736997010a6cbbdf93b23b77c7ef3a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603983"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Beveilig uw beheerpoorten met just-in-time toegang

Als u de standaardprijslaag van het Beveiligingscentrum (zie [prijzen)](/azure/security-center/security-center-pricing)bevindt, u binnenkomend verkeer naar uw Azure VM's vergrendelen met vm-toegang (Just-in-time). Dit vermindert de blootstelling aan aanvallen en biedt tegelijkertijd gemakkelijke toegang tot vm's wanneer dat nodig is.

> [!NOTE]
> Just-in-time VM-toegang voor beveiligingscentrum ondersteunt momenteel alleen VM's die zijn geïmplementeerd via Azure Resource Manager. Zie Azure Resource Manager versus klassieke [implementatie](../azure-resource-manager/management/deployment-models.md)voor meer informatie over de klassieke en Resource Manager-implementatiemodellen.

[!INCLUDE [security-center-jit-description](../../includes/security-center-jit-description.md)]

## <a name="configure-jit-on-a-vm"></a>JIT configureren op een vm

Er zijn drie manieren om een JIT-beleid op een VM te configureren:

- [JIT-toegang configureren in Azure Security Center](#jit-asc)
- [JIT-toegang configureren op een Azure VM-pagina](#jit-vm)
- [Een JIT-beleid configureren op een VM-programmatisch](#jit-program)

## <a name="configure-jit-in-azure-security-center"></a>JIT configureren in Azure Security Center

Vanuit Security Center u een JIT-beleid configureren en toegang tot een VM aanvragen met behulp van een JIT-beleid

### <a name="configure-jit-access-on-a-vm-in-security-center"></a>JIT-toegang configureren op een vm in beveiligingscentrum<a name="jit-asc"></a>

1. Open het dashboard van **Security Center**.

1. Selecteer in het linkerdeelvenster **just-in-time VM-toegang**.

    ![Just-in-time VM-toegangstegel](./media/security-center-just-in-time/just-in-time.png)

    Het **just-in-time VM-toegangsvenster** wordt geopend en toont informatie over de status van uw VM's:

    - **Geconfigureerd** - VM's die zijn geconfigureerd om just-in-time VM-toegang te ondersteunen. De gepresenteerde gegevens zijn voor de laatste week en bevatten voor elke VM het aantal goedgekeurde aanvragen, de laatste toegangsdatum en -tijd en de laatste gebruiker.
    - **Aanbevolen** - VM's die just-in-time VM-toegang kunnen ondersteunen, maar niet zijn geconfigureerd. We raden u aan just-in-time VM-toegangscontrole in te schakelen voor deze VM's.
    - **Geen aanbeveling**: redenen waarom een VM mogelijk niet wordt aanbevolen zijn:
      - Ontbrekende NSG - Voor de just-in-time-oplossing moet een NSG aanwezig zijn.
      - De just-in-time VM-toegang voor het klassieke VM - Beveiligingscentrum ondersteunt momenteel alleen VM's die zijn geïmplementeerd via Azure Resource Manager. Een klassieke implementatie wordt niet ondersteund door de just-in-time oplossing. 
      - Andere - Een VM bevindt zich in deze categorie als de just-in-time-oplossing is uitgeschakeld in het beveiligingsbeleid van het abonnement of de resourcegroep, of als de VM een openbaar IP-adres mist en geen NSG heeft.

1. Selecteer het **tabblad Aanbevolen.**

1. Klik **onder VIRTUELE MACHINE**op de VM's die u wilt inschakelen. Hiermee wordt een vinkje naast een VM plaatst.

      ![Just-in-time toegang inschakelen](./media/security-center-just-in-time/enable-just-in-time.png)

1. Klik **op JIT inschakelen op VM's**. Er wordt een deelvenster geopend met de standaardpoorten die worden aanbevolen door Azure Security Center:
    - 22 - SSH
    - 3389 - RDP
    - 5985 - WinRM 
    - 5986 - WinRM
1. Optioneel u aangepaste poorten aan de lijst toevoegen:

      1. Klik op**toevoegen**. Het venster **Poortconfiguratie toevoegen** wordt geopend.
      1. Voor elke poort die u wilt configureren, zowel standaard als aangepast, u de volgende instellingen aanpassen:
            - **Protocoltype**- Het protocol dat is toegestaan op deze poort wanneer een aanvraag wordt goedgekeurd.
            - **Toegestane bron-IP-adressen**- De IP-bereiken die zijn toegestaan op deze poort wanneer een aanvraag wordt goedgekeurd.
            - **Maximale aanvraagtijd**- Het maximale tijdvenster waarin een specifieke poort kan worden geopend.

     1. Klik op **OK**.

1. Klik op **Opslaan**.

> [!NOTE]
>Wanneer JIT VM Access is ingeschakeld voor een VM, maakt Azure Security Center 'alle binnenkomende verkeersregels weigeren' regels voor de geselecteerde poorten in de netwerkbeveiligingsgroepen die zijn gekoppeld en Azure Firewall ermee. Als er andere regels zijn gemaakt voor de geselecteerde poorten, hebben de bestaande regels voorrang op de nieuwe regels 'alle binnenkomende verkeer weigeren'. Als er geen bestaande regels zijn voor de geselecteerde poorten, hebben de nieuwe regels 'alle binnenkomende verkeer weigeren' de hoogste prioriteit in de netwerkbeveiligingsgroepen en Azure Firewall.


## <a name="request-jit-access-via-security-center"></a>JIT-toegang aanvragen via Security Center

Ga als lid van het Beveiligde Centrum om toegang tot een vm aan te vragen:

1. Selecteer onder **Just-in-time VM-toegang**het tabblad **Geconfigureerd.**

1. Klik **onder Virtuele machine**op de VM's waarvoor u toegang wilt aanvragen. Hiermee wordt een vinkje naast de VM plaatst.

    - Het pictogram in de kolom **Verbindingsdetails** geeft aan of JIT is ingeschakeld op de NSG of FW. Als deze op beide is ingeschakeld, wordt alleen het firewallpictogram weergegeven.

    - De kolom **Verbindingsdetails** bevat de informatie die nodig is om de VM en de geopende poorten met elkaar te verbinden.

      ![Just-in-time-toegang aanvragen](./media/security-center-just-in-time/request-just-in-time-access.png)

1. Klik **op Toegang tot aanvragen**. Het **toegangsvenster voor aanvragen** wordt geopend.

      ![JIT-details](./media/security-center-just-in-time/just-in-time-details.png)

1. Configureer onder **Toegang tot aanvragen**voor elke virtuele machine de poorten die u wilt openen en de bron-IP-adressen waarop de poort wordt geopend en het tijdvenster waarvoor de poort wordt geopend. Het is alleen mogelijk om toegang te vragen tot de poorten die zijn geconfigureerd in het just-in-time beleid. Elke poort heeft een maximaal toegestane tijd die is afgeleid van het just-in-time-beleid.

1. Klik **op Poorten openen**.

> [!NOTE]
> Als een gebruiker die om toegang vraagt achter een proxy zit, werkt de optie **Mijn IP** mogelijk niet. Mogelijk moet u het volledige IP-adresbereik van de organisatie definiëren.



## <a name="edit-a-jit-access-policy-via-security-center"></a>Een JIT-toegangsbeleid bewerken via Security Center

U het bestaande just-in-time-beleid van een vm wijzigen door een nieuwe poort toe te voegen en te configureren om voor die vm te beschermen, of door een andere instelling met betrekking tot een reeds beveiligde poort te wijzigen.

Ga als lid van het werk om een bestaand just-in-time beleid van een virtuele machine te bewerken:

1. Selecteer op het tabblad **Geconfigureerd** onder **VM's**een vm waaraan u een poort wilt toevoegen door op de drie puntjes in de rij voor die vm te klikken. 

1. Selecteer **Bewerken**.

1. Onder **jit-vm-toegangsconfiguratie**u de bestaande instellingen van een reeds beveiligde poort bewerken of een nieuwe aangepaste poort toevoegen. 
  ![jit vm-toegang](./media/security-center-just-in-time/edit-policy.png)



## <a name="audit-jit-access-activity-in-security-center"></a>JIT-toegangsactiviteit controleren in Security Center

U inzicht krijgen in VM-activiteiten met behulp van logboekzoekopdrachten. Logboeken weergeven:

1. Selecteer onder **Just-in-time VM-toegang**het tabblad **Geconfigureerd.**
2. Selecteer **onder VM's**een vm om informatie over weer te geven door op de drie puntjes in de rij voor die vm te klikken en **selecteer Activiteitslogboek** in het menu. Het **logboek Activiteit** wordt geopend.

   ![Activiteitslogboek selecteren](./media/security-center-just-in-time/select-activity-log.png)

   **Activiteitenlogboek** biedt een gefilterde weergave van eerdere bewerkingen voor die vm, samen met tijd, datum en abonnement.

U de loginformatie downloaden door **Klik hier om alle items als CSV te downloaden.**

Wijzig de filters en klik op **Toepassen** om een zoekopdracht en logboek te maken.



## <a name="configure-jit-access-from-an-azure-vms-page"></a>JIT-toegang configureren vanaf de pagina van een Azure VM<a name="jit-vm"></a>

Voor uw gemak u rechtstreeks verbinding maken met een VM via JIT vanuit de pagina van de VM in Security Center.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>JIT-toegang op een vm configureren via de Azure VM-pagina

Om het eenvoudig te maken om just-in-time toegang uit te rollen in uw VM's, u een VM instellen om alleen just-in-time toegang rechtstreeks vanuit de VM toe te staan.

1. Zoek in de [Azure-portal](https://ms.portal.azure.com)naar virtuele **machines**en selecteer deze. 
2. Selecteer de virtuele machine die u wilt beperken tot just-in-time toegang.
3. Selecteer **Configuratie**in het menu .
4. Selecteer **Just-in-time toegang onder Just-in-time-toegang**de optie **Just-in-time inschakelen**. 

Dit maakt just-in-time toegang voor de VM mogelijk met behulp van de volgende instellingen:

- Windows-servers:
    - RDP-poort 3389
    - Drie uur maximaal toegestane toegang
    - Toegestane bron-IP-adressen is ingesteld op
- Linux servers:
    - SSH-poort 22
    - Drie uur maximaal toegestane toegang
    - Toegestane bron-IP-adressen is ingesteld op
     
Als een vm al just-in-time is ingeschakeld, u bij het openen van de configuratiepagina zien dat just-in-time is ingeschakeld en u de koppeling gebruiken om het beleid in Azure Security Center te openen om de instellingen weer te geven en te wijzigen.

![jit config in vm](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-an-azure-vms-page"></a>JIT-toegang tot een VM aanvragen via de pagina van een Azure VM

In de Azure-portal controleert Azure wanneer u verbinding probeert te maken met een vm of u een just-in-time toegangsbeleid hebt geconfigureerd op die VM.

- Als u een JIT-beleid hebt geconfigureerd op de VM, u op **Toegang aanvragen** om toegang te verlenen in overeenstemming met het JIT-beleid voor de VM klikken. 

  >![jit-verzoek](./media/security-center-just-in-time/jit-request.png)

  Toegang wordt aangevraagd met de volgende standaardparameters:

  - **bron-IP**: 'Any' (*) (kan niet worden gewijzigd)
  - **tijdsbereik**: Drie uur (kan niet worden gewijzigd) <!--Isn't this set in the policy-->
  - **poortnummer** RDP-poort 3389 voor Windows / poort 22 voor Linux (kan worden gewijzigd)

    > [!NOTE]
    > Nadat een aanvraag is goedgekeurd voor een VM die is beveiligd door Azure Firewall, biedt Security Center de gebruiker de juiste verbindingsgegevens (de poorttoewijzing uit de DNAT-tabel) om verbinding te maken met de VM.

- Als u geen JIT-geconfigureerd hebt op een virtuele machine, wordt u gevraagd er een JIT-beleid op te configureren.

  ![jit prompt](./media/security-center-just-in-time/jit-prompt.png)

## <a name="configure-a-jit-policy-on-a-vm-programmatically"></a>Een JIT-beleid configureren op een VM-programmatisch<a name="jit-program"></a>

U just-in-time instellen en gebruiken via REST API's en via PowerShell.

### <a name="jit-vm-access-via-rest-apis"></a>JIT VM toegang via REST API's

De just-in-time VM-toegangsfunctie kan worden gebruikt via de Azure Security Center API. U informatie krijgen over geconfigureerde VM's, nieuwe toevoegen, toegang tot een VM aanvragen en meer via deze API. Zie [Jit Network Access Policies](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)voor meer informatie over de just-in-time REST API.

### <a name="jit-vm-access-via-powershell"></a>JIT VM-toegang via PowerShell

Als u de just-in-time VM-toegangsoplossing via PowerShell wilt gebruiken, gebruikt `Set-AzJitNetworkAccessPolicy`u de officiële PowerShell-cmdlets van azure security center, en specifiek.

In het volgende voorbeeld wordt een just-in-time VM-toegangsbeleid ingesteld op een specifieke virtuele machine en wordt het volgende ingesteld:

1.  Sluit poorten 22 en 3389.

2.  Stel een maximumtijdvenster van 3 uur in voor elk, zodat ze per goedgekeurde aanvraag kunnen worden geopend.
3.  Hiermee kan de gebruiker die toegang vraagt om de bron-IP-adressen te beheren en kan de gebruiker een succesvolle sessie instellen op een goedgekeurd just-in-time toegangsverzoek.

Voer het volgende uit in PowerShell om dit te bereiken:

1.  Wijs een variabele toe die het just-in-time VM-toegangsbeleid voor een vm bevat:

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

2.  Voeg het vm-just-in-time VM-toegangsbeleid in een array in:
    
        $JitPolicyArr=@($JitPolicy)

3.  Configureer het just-in-time VM-toegangsbeleid op de geselecteerde VM:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

### <a name="request-access-to-a-vm-via-powershell"></a>Toegang tot een VM aanvragen via PowerShell

In het volgende voorbeeld ziet u een just-in-time VM-toegangsverzoek naar een specifieke vm waarin poort 22 wordt gevraagd om te worden geopend voor een specifiek IP-adres en voor een bepaalde hoeveelheid tijd:

Voer het volgende uit in PowerShell:
1.  De eigenschappen voor toegang tot vm-aanvragen configureren

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  De parameters voor vm-toegangsaanvragen in een array invoegen:

        $JitPolicyArr=@($JitPolicyVm1)
3.  De aanvraagtoegang verzenden (gebruik de bron-id die u hebt in stap 1)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Zie voor meer informatie de [PowerShell-cmdlet-documentatie](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview).


## <a name="automatic-cleanup-of-redundant-jit-rules"></a>Automatisch opruimen van overbodige JIT-regels 

Wanneer u een JIT-beleid bijwerkt, wordt er automatisch een opschoningstool uitgevoerd om de geldigheid van uw hele regelset te controleren. De tool zoekt naar discrepanties tussen regels in uw beleid en regels in de NSG. Als het opruimgereedschap een mismatch vindt, bepaalt het de oorzaak en verwijdert het, wanneer het veilig is om dit te doen, ingebouwde regels weg die niet meer nodig zijn. De reiniger verwijdert nooit regels die u hebt gemaakt.

Voorbeelden van scenario's waarin de reiniger een ingebouwde regel kan verwijderen:

- Wanneer er twee regels met identieke definities bestaan en de ene een hogere prioriteit heeft dan de andere (wat betekent dat de regel met een lagere prioriteit nooit zal worden gebruikt)
- Wanneer een regelbeschrijving de naam van een VM bevat die niet overeenkomt met het doel-IP in de regel 


## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u hoe just-in-time VM-toegang in beveiligingscentrum u helpt de toegang tot uw virtuele Azure-machines te beheren.

Zie de volgende onderwerpen voor meer informatie over Security Center:

- [Beveiligingsbeleid instellen](tutorial-security-policy.md) : meer informatie over het configureren van beveiligingsbeleid voor uw Azure-abonnementen en resourcegroepen.
- [Beveiligingsaanbevelingen beheren](security-center-recommendations.md) : lees hoe aanbevelingen u helpen uw Azure-bronnen te beschermen.
- [Bewaking van de beveiligingsstatus](security-center-monitoring.md) : meer informatie over het bewaken van de status van uw Azure-bronnen.