---
title: Het diagnoseprogramma voor Windows Virtual Desktop implementeren - Azure
description: Het implementeren van het UX-hulpprogramma voor diagnostische gegevens voor Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4eb63fe4bd8f8a8b0961aa6a7fccb8de9b7c2f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123406"
---
# <a name="deploy-the-diagnostics-tool"></a>Het diagnosehulpprogramma implementeren

>[!IMPORTANT]
>Vanaf 16 maart 2020 hebben we diagnostische query's die van invloed zijn op de gebruikerservaring tijdelijk uitgeschakeld vanwege de toegenomen vraag naar de service. Dit zorgt ervoor dat het hulpprogramma niet meer werkt omdat het afhankelijk is van deze query's om te functioneren. We werken dit artikel bij wanneer diagnostische query's weer beschikbaar zijn.
>
>Tot die tijd raden we u ten zeerste aan [Log Analytics te gebruiken](diagnostics-log-analytics.md) voor voortdurende monitoring.

Dit is wat het diagnoseprogramma voor Windows Virtual Desktop voor u kan doen:

- Diagnostische activiteiten (beheer, verbinding of feed) voor één gebruiker opzoeken gedurende een periode van één week.
- Verzamel sessiehostgegevens voor verbindingsactiviteiten vanuit uw Log Analytics-werkruimte.
- Bekijk de prestatiegegevens van virtuele machines voor een bepaalde host.
- Bekijk welke gebruikers zijn aangemeld bij de sessiehost.
- Stuur bericht naar actieve gebruikers op een specifieke sessiehost.
- Meld gebruikers af bij een sessiehost.

## <a name="prerequisites"></a>Vereisten

U moet een Azure Active Directory App-registratie en een logboekanalysewerkruimte maken voordat u de sjabloon Azure Resource Manager voor het hulpprogramma implementeren. U of de beheerder hebt deze machtigingen nodig om dat te doen:

- Eigenaar van het Azure-abonnement
- Toestemming om resources te maken in uw Azure-abonnement
- Toestemming om een Azure AD-app te maken
- RDS-eigenaar of inzenderrechten

U moet deze twee PowerShell-modules ook installeren voordat u aan de slag gaat:

- [Azure PowerShell-module](/powershell/azure/install-az-ps?view=azps-2.4.0/)
- [Azure AD-module](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0/)

Zorg ervoor dat je je abonnements-id bij de hand hebt staan voor wanneer je je aanmeldt.

Nadat u alles op orde hebt, u de azure AD-appregistratie maken.

## <a name="create-an-azure-active-directory-app-registration"></a>Een Azure Active Directory-app-registratie maken

In deze sectie ziet u hoe u PowerShell gebruiken om de Azure Active Directory-app met een serviceprincipal te maken en api-machtigingen ervoor te krijgen.

>[!NOTE]
>De API-machtigingen zijn Windows Virtual Desktop, Log Analytics en Microsoft Graph API-machtigingen worden toegevoegd aan de Azure Active Directory-toepassing.

1. Open PowerShell als administrator.
2. Meld u aan bij Azure met een account met machtigingen voor eigenaar of inzender voor het Azure-abonnement dat u wilt gebruiken voor het diagnoseprogramma:
   ```powershell
   Login-AzAccount
   ```
3. Meld u aan bij Azure AD met hetzelfde account:
   ```powershell
   Connect-AzureAD
   ```
4. Ga naar de [RdS-Templates GitHub repo](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) en voer het **Script CreateADAppRegistrationforDiagnostics.ps1** uit in PowerShell.
5.  Wanneer het script u vraagt om uw app een naam te geven, voert u een unieke app-naam in.


Nadat het script is uitgevoerd, moet het de volgende dingen in de uitvoer weergeven:

-  Een bericht dat bevestigt dat uw app nu een opdracht voor een serviceprincipal-rol heeft.
-  Uw client-id en clientgeheime sleutel die u nodig hebt wanneer u de diagnosetool implementeert.

Nu u uw app hebt geregistreerd, is het tijd om uw Log Analytics-werkruimte te configureren.

## <a name="configure-your-log-analytics-workspace"></a>Uw werkruimte Log Analytics configureren

Voor de best mogelijke ervaring raden we u aan uw Log Analytics-werkruimte te configureren met de volgende prestatiemeteritems waarmee u verklaringen van de gebruikerservaring afleiden in een externe sessie. Zie Prestatiemeterdrempels voor Windows voor een lijst met aanbevolen tellers met voorgestelde [drempelwaarden.](deploy-diagnostics.md#windows-performance-counter-thresholds)

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>Een Azure Log Analytics-werkruimte maken met PowerShell

U een PowerShell-script uitvoeren om een Log Analytics-werkruimte te maken en de aanbevolen Windows-prestatiemeteritems configureren om de gebruikerservaring en de prestaties van apps te controleren.

>[!NOTE]
>Als u al een bestaande Log Analytics-werkruimte hebt die u hebt gemaakt zonder het PowerShell-script dat u wilt gebruiken, gaat u verder met [Het valideren van de scriptresultaten in de Azure-portal.](#validate-the-script-results-in-the-azure-portal)

Ga als u het PowerShell-script uitvoert:

1.  Open PowerShell als beheerder.
2.  Ga naar de [GitHub-repo voor RDS-sjablonen](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) en voer het **Script CreateLogAnalyticsWorkspaceforDiagnostics.ps1** uit in PowerShell.
3. Voer de volgende waarden voor de parameters in:

    - Voer **voor ResourceGroupName**de naam voor de resourcegroep in.
    - Voer voor **LogAnalyticsWorkspaceName**een unieke naam in voor uw Log Analytics-werkruimte.
    - Voer **voor Locatie**het Azure-gebied in dat u gebruikt.
    - Voer de **Azure Subscription ID**in die u vinden in de Azure-portal onder **Abonnementen**.

4. Voer de referenties in van een gebruiker met gedelegeerde beheerderstoegang.
5. Meld u aan bij de Azure-portal met de referenties van dezelfde gebruiker.
6. Schrijf de LogAnalyticsWorkspace ID op of onthoud de LogAnalyticsWorkspace ID voor later.
7. Als u de werkruimte Log Analytics instelt met het PowerShell-script, moeten uw prestatiemeteritems al zijn geconfigureerd en u doorgaan met [Het scriptvalideren in de Azure-portal.](#validate-the-script-results-in-the-azure-portal) Ga anders verder naar de volgende sectie.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>Windows-prestatiemeteritems configureren in uw bestaande Werkruimte Log Analytics

Deze sectie is bedoeld voor gebruikers die een bestaande Azure Log Analytics-werkruimte willen gebruiken die is gemaakt zonder het PowerShell-script in de vorige sectie. Als u het script niet hebt gebruikt, moet u de aanbevolen Windows-prestatiemeteritems handmatig configureren.

U de aanbevolen prestatiemeteritems als u de aanbevolen prestatiemeteritems handmatig configureren:

1. Open uw internetbrowser en meld u aan bij de [Azure-portal](https://portal.azure.com/) met uw beheerdersaccount.
2. Ga vervolgens naar **Log Analytics-werkruimten** om de geconfigureerde Windows-prestatiemeteritems te bekijken.
3. Selecteer **in** de sectie Instellingen de optie **Geavanceerde instellingen**.
4. Ga daarna naar De**prestatiemeteritems** **voor gegevens** > windows en voeg de volgende tellers toe:

    -   Logische\*Schijf(\\) %Vrije ruimte
    -   LogicalDisk(C:)\\Avg. Schijfwachtrijlengte
    -   Geheugen(\*\\) Beschikbare Mbytes
    -   Processorinformatie(\*\\) Processortijd
    -   Vertraging van de\*invoer\\van gebruikers per sessie( ) Maximale invoervertraging

Meer informatie over de prestatiemeteritems bij [Windows- en Linux-prestatiegegevensbronnen in Azure Monitor](/azure/azure-monitor/platform/data-sources-performance-counters).

>[!NOTE]
>Eventuele extra tellers die u configureert, worden niet weergegeven in het diagnoseprogramma zelf. Als u het wilt laten verschijnen in het diagnoseprogramma, moet u het config-bestand van het gereedschap configureren. Instructies voor hoe dit te doen met geavanceerd beheer zullen op een later tijdstip beschikbaar zijn in GitHub.

## <a name="validate-the-script-results-in-the-azure-portal"></a>De scriptresultaten valideren in de Azure-portal

Voordat u doorgaat met het implementeren van het diagnoseprogramma, raden we u aan te controleren of uw Azure Active Directory-toepassing API-machtigingen heeft en dat uw Log Analytics-werkruimte de vooraf geconfigureerde Windows-prestatiemeteritems heeft.

### <a name="review-your-app-registration"></a>Uw app-registratie controleren

Ga als u ervoor zorgen dat uw app-registratie API-machtigingen heeft:

1. Open een browser en maak verbinding met de [Azure-portal](https://portal.azure.com/) met uw beheerdersaccount.
2. Ga naar **Azure Active Directory**.
3. Ga naar **App-registraties** en selecteer **Alle toepassingen**.
4. Zoek naar uw Azure AD-appregistratie met dezelfde app-naam die u hebt ingevoerd in stap 5 van [Een Azure Active Directory-app-registratie maken.](deploy-diagnostics.md#create-an-azure-active-directory-app-registration)

### <a name="review-your-log-analytics-workspace"></a>Uw logboekanalysewerkruimte controleren

Ga als u ervoor zorgen dat uw log analytics-werkruimte de vooraf geconfigureerde Windows-prestatiemeteritems heeft:

1. Ga in de [Azure-portal](https://portal.azure.com/)naar **Log Analytics-werkruimten** om de geconfigureerde Windows-prestatiemeteritems te bekijken.
2. Selecteer **Onder Instellingen**de optie Geavanceerde **instellingen**.
3. Ga daarna naar > **Gegevenswindows-prestatiemeteritems**. **Data**
4. Controleer of de volgende tellers vooraf zijn geconfigureerd:

   - Logische\*schijf(\\) %Vrije ruimte: geeft de hoeveelheid vrije ruimte van de totale bruikbare ruimte op de schijf als percentage weer.
   - LogicalDisk(C:)\\Avg. Schijfwachtrijlengte: de lengte van de aanvraag voor schijfoverdracht voor uw C-station. De waarde mag niet meer dan 2 voor meer dan een korte periode van tijd.
   - Geheugen(\*\\) Beschikbare Mbytes: Het beschikbare geheugen voor het systeem in megabytes.
   - Processorinformatie(\*\\) Processortijd: het percentage verstreken tijd dat de processor besteedt aan het uitvoeren van een niet-Idle thread.
   - Vertraging van de\*invoer\\van gebruikers per sessie( ) Maximale invoervertraging

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Verbinding maken met VM's in uw Log Analytics-werkruimte

Als u de status van VM's wilt bekijken, moet u de Log Analytics-verbinding inschakelen. Volg de volgende stappen om uw VM's met elkaar te verbinden:

1. Open een browser en meld u aan bij de [Azure-portal](https://portal.azure.com/) met uw beheerdersaccount.
2. Ga naar uw Log Analytics Workspace.
3. Selecteer **virtuele machines**in het linkerdeelvenster onder Werkruimtegegevensbronnen .
4. Selecteer de naam van de VM waarmee u verbinding wilt maken.
5. Selecteer **Verbinden**.

## <a name="deploy-the-diagnostics-tool"></a>Het diagnosehulpprogramma implementeren

Ga als u de sjabloon Azure Resource Management implementeren voor het diagnosehulpprogramma:

1.  Ga naar de [pagina GitHub Azure RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy).
2.  Implementeer de sjabloon naar Azure en volg de instructies in de sjabloon. Zorg ervoor dat u over de volgende informatie beschikt:

    -   Client-id
    -   Client-Secret
    -   Log Analytics-werkruimte-id

3.  Zodra de invoerparameters zijn opgegeven, accepteert u de algemene voorwaarden en selecteert u **Kopen**.

De implementatie duurt 2-3 minuten. Ga na een succesvolle implementatie naar de brongroep en zorg ervoor dat de bronnen voor web-app- en app-serviceplannen aanwezig zijn.

Daarna moet u de OMLEIDING URI instellen.

### <a name="set-the-redirect-uri"></a>De URI omleiden instellen

Ga als u de URI omleiden:

1.  Ga in de [Azure-portal](https://portal.azure.com/)naar **App Services** en zoek de toepassing die u hebt gemaakt.
2.  Ga naar de overzichtspagina en kopieer de URL die je daar vindt.
3.  Navigeer naar **app-registraties** en selecteer de app die u wilt implementeren.
4.  Selecteer **verificatie**in het linkerdeelvenster onder Sectie Beheren .
5.  Voer de gewenste URI-omleiding in het tekstvak **URI omleiden** en selecteer **Opslaan** in de linkerbovenhoek van het menu.
6. Selecteer **Web** in het vervolgkeuzemenu onder Type.
7. Voer de URL in op de pagina met app-overzicht en voeg **/security/signin-callback toe** aan het einde ervan. Bijvoorbeeld: `https://<yourappname>.azurewebsites.net/security/signin-callback`.

   ![De uri-pagina omleiden](media/redirect-uri-page.png)

8. Ga nu naar uw Azure-bronnen, selecteer de Azure App Services-bron met de naam die u in de sjabloon hebt opgegeven en navigeer naar de URL die eraan is gekoppeld. (Als de app-naam die u in `contosoapp45`de sjabloon hebt <https://contosoapp45.azurewebsites.net>gebruikt, bijvoorbeeld is, dan is de bijbehorende URL ).
9. Meld u aan met het juiste Azure Active Directory-gebruikersaccount.
10.   Selecteer **Accepteren**.

## <a name="distribute-the-diagnostics-tool"></a>Het diagnosegereedschap distribueren

Voordat u het diagnoseprogramma beschikbaar maakt voor uw gebruikers, controleert u of ze de volgende machtigingen hebben:

- Gebruikers hebben leestoegang nodig voor log-analyses. Zie [Aan de slag met rollen, machtigingen en beveiliging met Azure Monitor](/azure/azure-monitor/platform/roles-permissions-security)voor meer informatie.
-  Gebruikers hebben ook leestoegang nodig voor de Windows Virtual Desktop-tenant (RDS Reader-rol). Zie [Gedelegeerde toegang in Windows Virtual Desktop](delegated-access-virtual-desktop.md)voor meer informatie.

U moet uw gebruikers ook de volgende informatie geven:

- De URL van de app
- De namen van de individuele tenant van de tenantgroep die ze kunnen openen.

## <a name="use-the-diagnostics-tool"></a>Het diagnosegereedschap gebruiken

Nadat u zich bij uw account hebt aangemeld met de informatie die u van uw organisatie hebt ontvangen, moet u de UPN klaarhebben voor de gebruiker waarvoor u activiteiten wilt opvragen. Een zoekopdracht geeft u alle activiteiten onder het opgegeven activiteitstype dat in de afgelopen week is gebeurd.

### <a name="how-to-read-activity-search-results"></a>Zoekresultaten voor activiteiten lezen

Activiteiten worden gesorteerd op tijdstempel, met de nieuwste activiteit eerst. Als de resultaten een fout retourneren, controleert u eerst of het een servicefout is. Maak voor servicefouten een ondersteuningsticket met de activiteitsgegevens om ons te helpen het probleem te debuggen. Alle andere fouttypen kunnen meestal worden opgelost door de gebruiker of beheerder. Zie [Problemen identificeren en diagnosticeren](diagnostics-role-service.md#common-error-scenarios)voor een lijst met de meest voorkomende foutscenario's en hoe deze op te lossen.

>[!NOTE]
>Servicefouten worden in de gekoppelde documentatie "externe fouten" genoemd. Dit wordt gewijzigd wanneer we de PowerShell-referentie bijwerken.

Verbindingsactiviteiten kunnen meer dan één fout bevatten. U het activiteitstype uitbreiden om andere fouten te zien die de gebruiker is tegengekomen. Selecteer de naam van de foutcode om een dialoogvenster te openen om meer informatie over de fout code te bekijken.

### <a name="investigate-the-session-host"></a>De sessiehost onderzoeken 

Zoek en selecteer in de zoekresultaten de sessiehost waarover u informatie wilt.

U de status van sessiehost analyseren:

- Op basis van een vooraf gedefinieerde drempelwaarde u de statusgegevens van de sessiehost opvragen met Log Analytics-query's ophalen.
- Als er geen activiteit is of de sessiehost niet is verbonden met Log Analytics, is de informatie niet beschikbaar.

U ook communiceren met gebruikers op de sessiehost:

- U zich afmelden of een bericht sturen naar aangemelde gebruikers.
- De gebruiker waar naar u oorspronkelijk hebt gezocht, is standaard geselecteerd, maar u ook extra gebruikers selecteren om berichten te verzenden of meerdere gebruikers tegelijk af te melden.

### <a name="windows-performance-counter-thresholds"></a>Windows prestatiemeterdrempels

- LogicalDisk(\*\\) %Vrije ruimte:

    - Hiermee geeft u het percentage van de totale bruikbare ruimte weer op de logische schijf die vrij is.
    - Drempel: Minder dan 20% is gemarkeerd als ongezond.

- LogicalDisk(C:)\\Avg. Schijfwachtrijlengte:

    - Staat voor de omstandigheden van het opslagsysteem.
    - Drempelwaarde: Hoger dan 5 is gemarkeerd als ongezond.

- Geheugen(\*\\) Beschikbare Mbytes:

    - Het beschikbare geheugen voor het systeem.
    - Drempel: Minder dan 500 megabyte gemarkeerd als ongezond.

- Processorinformatie(\*\\) Processortijd:

    - Drempelwaarde: Hoger dan 80% is gemarkeerd als ongezond.

- [Vertraging van de\*invoer\\van gebruikers per sessie( ) Maximale invoervertraging:](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)

    - Drempelwaarde: Hoger dan 2000 ms is gemarkeerd als ongezond.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het monitoren van activiteitenlogboeken bij [Diagnostische gegevens gebruiken met Log Analytics](diagnostics-log-analytics.md).
- Lees meer over veelvoorkomende foutscenario's en hoe u deze oplossen bij [Problemen identificeren en diagnosticeren.](diagnostics-role-service.md)
