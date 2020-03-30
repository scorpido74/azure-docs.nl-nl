---
title: Beschikbaarheidsgroep configureren (Azure quickstart-sjabloon)
description: Gebruik Azure quickstart-sjablonen om het Windows-failovercluster te maken, SQL Server VM's toe te voegen aan het cluster, de listener te maken en de interne load balancer in Azure te configureren.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: edf810dfc975eebaf261eac7b89106c9e29c759c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022385"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-an-azure-vm"></a>Azure quickstart-sjablonen gebruiken om een beschikbaarheidsgroep voor SQL Server op een Azure VM te configureren
In dit artikel wordt beschreven hoe u de Azure quickstart-sjablonen gebruiken om de implementatie van een configuratie van de status always on beschikbaarheid voor virtuele SQL Server-machines in Azure gedeeltelijk te automatiseren. In dit proces worden twee Azure-snelstartsjablonen gebruikt: 

   | Template | Beschrijving |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Hiermee maakt u het Windows-failovercluster en worden de SQL Server VM's eraan verbonden. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Hiermee maakt u de listener van de beschikbaarheidsgroep en configureert u de interne load balancer. Deze sjabloon kan alleen worden gebruikt als het Windows-failovercluster is gemaakt met de sjabloon **101-sql-vm-ag-setup.** |
   | &nbsp; | &nbsp; |

Andere delen van de configuratie van de beschikbaarheidsgroep moeten handmatig worden uitgevoerd, zoals het maken van de beschikbaarheidsgroep en het maken van de interne load balancer. Dit artikel bevat de volgorde van geautomatiseerde en handmatige stappen.
 

## <a name="prerequisites"></a>Vereisten 
Als u de installatie van een beschikbaarheidsgroep Always On wilt automatiseren met behulp van quickstartsjablonen, moet u de volgende vereisten hebben: 
- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Een resourcegroep met een domeincontroller. 
- Een of meer met een domein verbonden [VM's in Azure met SQL Server 2016 (of hoger) Enterprise-editie](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) die zich in dezelfde beschikbaarheidsset of beschikbaarheidszone bevinden en die zijn [geregistreerd bij de SQL VM-resourceprovider.](virtual-machines-windows-sql-register-with-resource-provider.md)  
- Twee beschikbare (niet gebruikt door een entiteit) IP-adressen: een voor de interne load balancer en een voor de listener van de beschikbaarheidsgroep binnen hetzelfde subnet als de beschikbaarheidsgroep. Als een bestaande load balancer wordt gebruikt, hebt u slechts één beschikbaar IP-adres nodig.  

## <a name="permissions"></a>Machtigingen
De volgende machtigingen zijn nodig om de groep Beschikbaarheid always on te configureren met Azure quickstart-sjablonen: 

- Een bestaand domeingebruikersaccount met de machtiging **Computerobject maken** in het domein.  Een domeinbeheerdersaccount heeft bijvoorbeeld doorgaans voldoende toestemming account@domain.com(bijvoorbeeld: ). _Dit account moet ook deel uitmaken van de lokale beheerdersgroep op elke vm om het cluster te maken._
- Het domeingebruikersaccount dat de SQL Server-service beheert. 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>Stap 1: Het failovercluster maken en SQL Server VM's aansluiten bij het cluster met behulp van een quickstartsjabloon 
Nadat uw SQL Server VM's zijn geregistreerd bij de SQL VM-resourceprovider, u lid worden van uw SQL Server VM's naar *SqlVirtualMachineGroups.* Deze bron definieert de metagegevens van het Windows-failovercluster. Metagegevens omvatten de versie, editie, volledig gekwalificeerde domeinnaam, Active Directory-accounts om zowel het cluster als de SQL Server-service te beheren en het opslagaccount als cloudgetuige. 

Als u SQL Server VM's toevoegt aan de *sqlVirtualMachineGroups-brongroep,* wordt de Windows Failover Cluster Service overgenomen om het cluster te maken en voegt u deze SQL Server VM's vervolgens aan dat cluster toe. Deze stap wordt geautomatiseerd met de **quickstartsjabloon 101-sql-vm-ag-setup.** U het implementeren met behulp van de volgende stappen:

1. Ga naar de [**quickstartsjabloon 101-sql-vm-ag-setup.**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) Selecteer vervolgens **Implementeren naar Azure** om de snelstartsjabloon in de Azure-portal te openen.
1. Vul de vereiste velden in om de metagegevens voor het Windows-failovercluster te configureren. U de optionele velden leeg laten.

   In de volgende tabel worden de benodigde waarden voor de sjabloon weergegeven: 

   | **Veld** | Waarde |
   | --- | --- |
   | **Abonnement** |  Het abonnement waarbij uw SQL Server VM's bestaan. |
   |**Resourcegroep** | De brongroep waarin uw SQL Server VM's zich bevinden. | 
   |**Failoverclusternaam** | De naam die u wilt voor uw nieuwe Windows-failovercluster. |
   | **Bestaande vmlijst** | De SQL Server VM's die u wilt deelnemen aan de beschikbaarheidsgroep en deel uitmaken van dit nieuwe cluster. Scheid deze waarden met een komma en een spatie (bijvoorbeeld: *SQLVM1, SQLVM2*). |
   | **SQL Server-versie** | De SQL Server-versie van uw SQL Server VM's. Selecteer deze in de vervolgkeuzelijst. Momenteel worden alleen SQL Server 2016- en SQL Server 2017-afbeeldingen ondersteund. |
   | **Bestaande volledig gekwalificeerde domeinnaam** | De bestaande FQDN voor het domein waarin uw SQL Server VM's zich bevinden. |
   | **Bestaand domeinaccount** | Een bestaand domeingebruikersaccount met de machtiging **Computerobject maken** in het domein als de [CNO](/windows-server/failover-clustering/prestage-cluster-adds) wordt gemaakt tijdens de implementatie van sjablonen. Een domeinbeheerdersaccount heeft bijvoorbeeld doorgaans voldoende toestemming account@domain.com(bijvoorbeeld: ). *Dit account moet ook deel uitmaken van de lokale beheerdersgroep op elke vm om het cluster te maken.*| 
   | **Wachtwoord voor domeinaccount** | Het wachtwoord voor het eerder genoemde domeingebruikersaccount. | 
   | **Bestaand Sql-serviceaccount** | Het domeingebruikersaccount dat de [SQL Server-service](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) beheert tijdens account@domain.comde implementatie van de beschikbaarheidsgroep (bijvoorbeeld: ). |
   | **Sql-servicewachtwoord** | Het wachtwoord dat wordt gebruikt door het domeingebruikersaccount dat de SQL Server-service beheert. |
   | **Naam van cloudgetuige** | Een nieuw Azure-opslagaccount dat wordt gemaakt en gebruikt voor de cloudgetuige. U deze naam wijzigen. |
   | **\_artefacten Locatie** | Dit veld is standaard ingesteld en mag niet worden gewijzigd. |
   | **\_artefacten Locatie Sas Token** | Dit veld wordt opzettelijk leeg gelaten. |
   | &nbsp; | &nbsp; |

1. Als u akkoord gaat met de algemene voorwaarden, schakelt u het **selectievakje Akkoord gaan met de bovenstaande voorwaarden** in. Selecteer vervolgens **Kopen** om de implementatie van de snelstartsjabloon te voltooien. 
1. Als u uw implementatie wilt controleren, selecteert u de implementatie in het pictogram **Belnotificatie** in de bovenste navigatiebanner of gaat u naar **Resourcegroep** in de Azure-portal. Selecteer **Implementaties** onder **Instellingen**en kies de implementatie **van Microsoft.Template.** 

>[!NOTE]
> Referenties die tijdens de implementatie van sjablonen worden verstrekt, worden alleen opgeslagen voor de lengte van de implementatie. Nadat de implementatie is voltooid, worden deze wachtwoorden verwijderd. U wordt gevraagd deze opnieuw op te geven als u meer SQL Server VM's aan het cluster toevoegt. 


## <a name="step-2-manually-create-the-availability-group"></a>Stap 2: Handmatig de beschikbaarheidsgroep maken 
Maak handmatig de beschikbaarheidsgroep zoals u dat normaal zou doen, met SQL [Server Management Studio,](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio) [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)of [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> Maak op dit moment *geen* listener, want de quickstartsjabloon **101-sql-vm-aglistener-setup** doet dat automatisch in stap 4. 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>Stap 3: Maak handmatig de interne load balancer
De groeplistener Always On beschikbaarheid vereist een interne instantie van Azure Load Balancer. De interne load balancer biedt een "zwevend" IP-adres voor de authenticiteitsgroep beschikbaarheid dat een snellere failover en heraansluiting mogelijk maakt. Als de SQL Server VM's in een beschikbaarheidsgroep deel uitmaken van dezelfde beschikbaarheidsset, u een Basic load balancer gebruiken. Anders moet u een standaard load balancer gebruiken. 

> [!IMPORTANT]
> De interne load balancer moet zich in hetzelfde virtuele netwerk bevinden als de SQL Server VM-exemplaren. 

Je hoeft alleen maar de interne load balancer te maken. In stap 4 verwerkt de quickstartsjabloon **101-sql-vm-aglistener** de rest van de configuratie (zoals de backendpool, health probe en load-balancing rules). 

1. Open in de Azure-portal de brongroep die de virtuele SQL Server-machines bevat. 
2. Selecteer in de resourcegroep de optie **Toevoegen**.
3. Zoek naar **load balancer**. Selecteer **load balancer**in de zoekresultaten, die door **Microsoft**wordt gepubliceerd.
4. Selecteer op het blad **Load Balancer** de optie **Maken**.
5. Configureer in het dialoogvenster **Load balancer maken** de load balancer als volgt:

   | Instelling | Waarde |
   | --- | --- |
   | **Naam** |Voer een tekstnaam in die de load balancer vertegenwoordigt. Voer bijvoorbeeld **sqlLB**in . |
   | **Type** |**Intern**: De meeste implementaties maken gebruik van een interne load balancer, waarmee toepassingen binnen hetzelfde virtuele netwerk verbinding kunnen maken met de beschikbaarheidsgroep.  </br> **Extern**: Hiermee kunnen toepassingen verbinding maken met de beschikbaarheidsgroep via een openbare internetverbinding. |
   | **Virtueel netwerk** | Selecteer het virtuele netwerk waarin de SQL Server-instanties zich bevinden. |
   | **Subnet** | Selecteer het subnet waarin de SQL Server-instanties zich bevinden. |
   | **Toewijzing ip-adres** |**Statisch** |
   | **Privé IP-adres** | Geef een beschikbaar IP-adres op in het subnet. |
   | **Abonnement** |Als u meerdere abonnementen hebt, wordt dit veld mogelijk weergegeven. Selecteer het abonnement dat u aan deze bron wilt koppelen. Het is normaal gesproken hetzelfde abonnement als alle resources voor de beschikbaarheidsgroep. |
   | **Resourcegroep** |Selecteer de brongroep waarin de SQL Server-instanties zich bevinden. |
   | **Locatie** |Selecteer de Azure-locatie waarin de SQL Server-instanties zich bevinden. |
   | &nbsp; | &nbsp; |

6. Selecteer **Maken**. 


>[!IMPORTANT]
> De openbare IP-bron voor elke SQL Server VM moet een standaard SKU hebben die compatibel is met de standaardloadbalancer. Als u de SKU van de openbare IP-bron van uw VM wilt bepalen, gaat u naar **Resourcegroep,** selecteert u uw bron voor **openbaar IP-adres** voor de SQL Server VM en zoekt u de waarde onder **SKU** in het deelvenster **Overzicht.** 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>Stap 4: De listener van de beschikbaarheidsgroep maken en de interne load balancer configureren met behulp van de sjabloon snel start

Maak de authenticiteitsgroep voor beschikbaarheid en configureer de interne load balancer automatisch met behulp van de quickstartsjabloon **101-sql-vm-aglistener-setup.** De sjabloon voorziet in de Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener-bron. De **quickstartsjabloon 101-sql-vm-aglistener-setup,** via de SQL VM-resourceprovider, doet de volgende acties:

- Hiermee maakt u een nieuwe frontend IP-bron (op basis van de IP-adreswaarde die tijdens de implementatie wordt opgegeven) voor de listener. 
- Hiermee configureert u de netwerkinstellingen voor het cluster en de interne load balancer. 
- Hiermee configureert u de backendpool voor de interne load balancer, de statussonde en de regels voor het balanceren van de lasten.
- Hiermee maakt u de listener van de beschikbaarheidsgroep met het opgegeven IP-adres en de opgegeven naam.
 
>[!NOTE]
> U **101-sql-vm-aglistener-setup** alleen gebruiken als het Windows-failovercluster is gemaakt met de sjabloon **101-sql-vm-ag-setup.**
   
   
Ga als volgt te werk om de interne load balancer te configureren en de listener van de beschikbaarheidsgroep te maken:
1. Ga naar de quickstartsjabloon [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) en selecteer **Implementeren naar Azure** om de snelstartsjabloon in de Azure-portal te starten.
1. Vul de vereiste velden in om de interne load balancer te configureren en de gewenste groeplistener te maken. U de optionele velden leeg laten. 

   In de volgende tabel worden de benodigde waarden voor de sjabloon weergegeven: 

   | **Veld** | Waarde |
   | --- | --- |
   |**Resourcegroep** | De brongroep waarin uw SQL Server VM's en beschikbaarheidsgroep bestaan. | 
   |**Bestaande failoverclusternaam** | De naam van het cluster waarop uw SQL Server VM's zijn verbonden. |
   | **Bestaande Sql Availability Group**| De naam van de beschikbaarheidsgroep waaruw SQL Server VM's deel van uitmaken. |
   | **Bestaande vmlijst** | De namen van de SQL Server VM's die deel uitmaken van de eerder genoemde beschikbaarheidsgroep. Scheid de namen met een komma en een spatie (bijvoorbeeld: *SQLVM1, SQLVM2*). |
   | **Luisteraar** | De DNS-naam die u aan de listener wilt toewijzen. Standaard geeft deze sjabloon de naam 'aglistener' op, maar u deze wijzigen. De naam mag niet meer dan 15 tekens bevatten. |
   | **Listenerpoort** | De poort die de luisteraar moet gebruiken. Normaal gesproken moet deze poort de standaardwaarde van 1433 zijn. Dit is het poortnummer dat de sjabloon opgeeft. Maar als uw standaardpoort is gewijzigd, moet de listenerpoort die waarde gebruiken. | 
   | **Listener-IP** | Het IP-adres dat de luisteraar moet gebruiken. Dit adres wordt gemaakt tijdens de implementatie van sjablonen, dus geef er een op die nog niet in gebruik is.  |
   | **Bestaand subnet** | De naam van het interne subnet van uw SQL Server VM's (bijvoorbeeld: *standaard).* U deze waarde bepalen door naar **Resourcegroep**te gaan, uw virtuele netwerk te selecteren, **Subnetten** te selecteren in het deelvenster **Instellingen** en de waarde onder **Naam**te kopiëren. |
   | **Bestaande interne load balancer** | De naam van de interne load balancer die u in stap 3 hebt gemaakt. |
   | **Sondepoort** | De sondepoort die u wilt dat de interne load balancer gebruikt. De sjabloon gebruikt standaard 59999, maar u deze waarde wijzigen. |
   | &nbsp; | &nbsp; |

1. Als u akkoord gaat met de algemene voorwaarden, schakelt u het **selectievakje Akkoord gaan met de bovenstaande voorwaarden** in. Selecteer **Kopen** om de implementatie van de snelstartsjabloon te voltooien. 
1. Als u uw implementatie wilt controleren, selecteert u de implementatie in het pictogram **Belnotificatie** in de bovenste navigatiebanner of gaat u naar **Resourcegroep** in de Azure-portal. Selecteer **Implementaties** onder **Instellingen**en kies de implementatie **van Microsoft.Template.** 

>[!NOTE]
>Als uw implementatie halverwege mislukt, moet u [de nieuw gemaakte listener](#remove-the-availability-group-listener) handmatig verwijderen met PowerShell voordat u de quickstartsjabloon **101-sql-vm-aglistener opnieuw implementeert.** 

## <a name="remove-the-availability-group-listener"></a>De listener voor beschikbaarheidsgroep verwijderen
Als u later de gewenste beschikbaarheidsgroeplistener moet verwijderen die de sjabloon heeft geconfigureerd, moet u de SQL VM-bronprovider doorlopen. Omdat de listener is geregistreerd via de SQL VM-resourceprovider, is het verwijderen via SQL Server Management Studio onvoldoende. 

De beste methode is om het te verwijderen via de SQL VM-resourceprovider met behulp van het volgende codefragment in PowerShell. Hiermee worden de metagegevens van de beschikbaarheidsgroeplistener verwijderd uit de SQL VM-bronprovider. Het verwijdert ook fysiek de luisteraar uit de beschikbaarheidsgroep. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Algemene fouten
In dit gedeelte worden enkele bekende kwesties en de mogelijke oplossing ervan besproken. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>Beschikbaarheidsgroeplistener voor beschikbaarheidsgroep 'AG-Name\<>' bestaat al
De geselecteerde beschikbaarheidsgroep die wordt gebruikt in de Azure-snelstartsjabloon voor de listener van de beschikbaarheidsgroep, bevat al een listener. Of het is fysiek binnen de beschikbaarheidsgroep, of de metagegevens blijven binnen de SQL VM-resourceprovider. Verwijder de listener met [PowerShell](#remove-the-availability-group-listener) voordat u de quickstartsjabloon **101-sql-vm-aglistener opnieuw implementeert.** 

### <a name="connection-only-works-from-primary-replica"></a>Verbinding werkt alleen vanuit primaire replica
Dit gedrag is waarschijnlijk afkomstig van een mislukte **101-sql-vm-aglistener-setup** template-implementatie die de configuratie van de interne load balancer in een inconsistente status heeft achtergelaten. Controleer of in de backendpool de beschikbaarheidsset wordt weergegeven en of er regels bestaan voor de statussonde en voor de regels voor het balanceren van de regel. Als er iets ontbreekt, is de configuratie van de interne load balancer een inconsistente status. 

Als u dit probleem wilt oplossen, verwijdert u de listener met [PowerShell,](#remove-the-availability-group-listener)verwijdert u de interne load balancer via de Azure-portal en begint u opnieuw bij stap 3. 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest - Alleen SQL virtuele machine lijst kan worden bijgewerkt
Deze fout kan optreden wanneer u de **101-sql-vm-aglistener-setup-sjabloon** implementeert als de listener is verwijderd via SQL Server Management Studio (SSMS), maar niet is verwijderd uit de SQL VM-resourceprovider. Als u de listener via SSMS verwijdert, worden de metagegevens van de listener niet verwijderd uit de SQL VM-resourceprovider. De listener moet via [PowerShell](#remove-the-availability-group-listener)uit de resourceprovider worden verwijderd. 

### <a name="domain-account-does-not-exist"></a>Domeinaccount bestaat niet
Deze fout kan twee oorzaken hebben. Het opgegeven domeinaccount bestaat niet of er ontbreekt de [UPN-gegevens (User Principal Name).](/windows/desktop/ad/naming-properties#userprincipalname) De **sjabloon 101-sql-vm-ag-setup** verwacht een domeinaccount in het *user@domain.com*UPN-formulier (dat wil zeggen), maar sommige domeinaccounts missen het mogelijk. Dit gebeurt meestal wanneer een lokale gebruiker is gemigreerd naar het eerste domeinbeheerdersaccount wanneer de server is gepromoveerd naar een domeincontroller of wanneer een gebruiker is gemaakt via PowerShell. 

Controleer of het account bestaat. Als dat zo is, loopt u misschien in de tweede situatie. Ga als volgt te werk om dit op te lossen:

1. Open op de domeincontroller het venster **Active Directory- gebruikers en computers** in de optie **Extra** in **Serverbeheer**. 
2. Ga naar het account door **Gebruikers** in het linkerdeelvenster te selecteren.
3. Klik met de rechtermuisknop op het account en selecteer **Eigenschappen**.
4. Selecteer het tabblad **Account.** Als het **aanmeldingsvak voor gebruiker** leeg is, is dit de oorzaak van de fout. 

    ![Leeg gebruikersaccount geeft ontbrekende UPN aan](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. Vul het **aanmeldingsvak voor gebruiker** in om de naam van de gebruiker te matchen en selecteer het juiste domein in de vervolgkeuzelijst. 
6. Selecteer **Toepassen** om de wijzigingen op te slaan en sluit het dialoogvenster door **OK**te selecteren. 

Nadat u deze wijzigingen hebt aangebracht, probeert u de Azure quickstart-sjabloon opnieuw te implementeren. 



## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server VM's](virtual-machines-windows-sql-server-iaas-overview.md)
* [Veelgestelde vragen voor SQL Server VM's](virtual-machines-windows-sql-server-iaas-faq.md)
* [Richtlijnen voor prijzen voor SQL Server VM's](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Releasenotes voor SQL Server VM's](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Schakelen tussen licentiemodellen voor een SQL Server VM](virtual-machines-windows-sql-ahb.md)



