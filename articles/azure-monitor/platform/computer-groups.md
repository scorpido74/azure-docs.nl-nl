---
title: Computergroepen in Azure Monitor-logboekquery's | Microsoft Documenten
description: Met computergroepen in Azure Monitor u logboekquery's naar een bepaalde set computers richten.  In dit artikel worden de verschillende methoden beschreven die u gebruiken om computergroepen te maken en hoe u deze gebruiken in een logboekquery.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/05/2019
ms.openlocfilehash: a005b6cec811b8a584123dc4c8abab77766961e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274773"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Computergroepen in Azure Monitor-logboekquery's
Met computergroepen in Azure Monitor u [logboekquery's](../log-query/log-query-overview.md) naar een bepaalde set computers richten.  Elke groep wordt gevuld met computers met behulp van een query die u definieert of door groepen uit verschillende bronnen te importeren.  Wanneer de groep is opgenomen in een logboekquery, zijn de resultaten beperkt tot records die overeenkomen met de computers in de groep.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Een computergroep maken
U een computergroep maken in Azure Monitor met behulp van een van de methoden in de volgende tabel.  Details over elke methode zijn opgenomen in de onderstaande secties. 

| Methode | Beschrijving |
|:--- |:--- |
| Logboekquery |Maak een logboekquery die een lijst met computers retourneert. |
| API voor zoeken in logboeken |Gebruik de API Voor logboekzoeken om programmatisch een computergroep te maken op basis van de resultaten van een logboekquery. |
| Active Directory |Scan automatisch het groepslidmaatschap van agentcomputers die lid zijn van een Active Directory-domein en maak een groep in Azure Monitor voor elke beveiligingsgroep. (Alleen Windows-machines)|
| Configuratiebeheer | Importeer verzamelingen uit Microsoft Endpoint Configuration Manager en maak voor elk een groep in Azure Monitor. |
| Windows Server Update Services |Scan automatisch WSUS-servers of -clients voor targetinggroepen en maak voor elk een groep in Azure Monitor. |

### <a name="log-query"></a>Logboekquery
Computergroepen die zijn gemaakt met een logboekquery bevatten alle computers die zijn geretourneerd door een query die u definieert.  Deze query wordt uitgevoerd elke keer dat de computergroep wordt gebruikt, zodat eventuele wijzigingen sinds het maken van de groep worden weergegeven.  

U elke query gebruiken voor een computergroep, maar deze `distinct Computer`moet een afzonderlijke set computers retourneren met behulp van.  Hieronder volgt een typische voorbeeldquery die u als computergroep gebruiken.

    Heartbeat | where Computer contains "srv" | distinct Computer

Gebruik de volgende procedure om een computergroep te maken vanuit een logboekzoekopdracht in de Azure-portal.

1. Klik **op Logboeken** in het **azure-monitormenu** in de Azure-portal.
1. Maak en voer een query uit die de gewenste computers in de groep retourneert.
1. Klik boven aan het scherm op **Opslaan.**
1. Wijzig **Opslaan als** **functie** en selecteer Deze query opslaan als **een computergroep**.
1. Geef waarden op voor elke eigenschap voor de computergroep die in de tabel wordt beschreven en klik op **Opslaan**.

In de volgende tabel worden de eigenschappen beschreven die een computergroep definiëren.

| Eigenschap | Beschrijving |
|:---|:---|
| Name   | Naam van de query die in de portal moet worden weergegeven. |
| Functiealias | Een unieke alias die wordt gebruikt om de computergroep in een query te identificeren. |
| Categorie       | Categorie om de query's in de portal te ordenen. |


### <a name="active-directory"></a>Active Directory
Wanneer u Azure Monitor configureert om Active Directory-groepslidmaatschappen te importeren, wordt het groepslidmaatschap van een Windows-domein samengevoegd met de agent Log Analytics geanalyseerd.  Voor elke beveiligingsgroep in Active Directory wordt een computergroep gemaakt die overeenkomt met de computergroepen die overeenkomen met de beveiligingsgroepen waarvan ze lid zijn.  Dit lidmaatschap wordt elke 4 uur voortdurend bijgewerkt.  

> [!NOTE]
> Geïmporteerde Active Directory-groepen bevatten alleen Windows-machines.

U configureert Azure Monitor om Active Directory-beveiligingsgroepen te importeren uit **geavanceerde instellingen** in uw Log Analytics-werkruimte in de Azure-portal.  Selecteer **Computergroepen,** **Active Directory**en importeer active **directory-groepslidmaatschappen vanaf computers**.  Er is geen verdere configuratie nodig.

![Computergroepen uit Active Directory](media/computer-groups/configure-activedirectory.png)

Wanneer groepen zijn geïmporteerd, wordt in het menu het aantal computers met groepslidmaatschap gedetecteerd en het aantal geïmporteerde groepen weergegeven.  U op een van deze koppelingen klikken om de **ComputerGroup-records** met deze informatie terug te sturen.

### <a name="windows-server-update-service"></a>Windows Server updateservice
Wanneer u Azure Monitor configureert om WSUS-groepslidmaatschappen te importeren, wordt het lidmaatschap van de doelgroep van alle computers geanalyseerd met de Log Analytics-agent.  Als u targeting aan clientzijde gebruikt, wordt op elke computer die is verbonden met Azure Monitor en deel uitmaakt van wsus-targetinggroepen, het groepslidmaatschap geïmporteerd naar Azure Monitor. Als u servertargeting gebruikt, moet de Log Analytics-agent op de WSUS-server worden geïnstalleerd om de groepslidmaatschapsgegevens te kunnen importeren in Azure Monitor.  Dit lidmaatschap wordt elke 4 uur voortdurend bijgewerkt. 

U configureert Azure Monitor om WSUS-groepen te importeren uit **geavanceerde instellingen** in uw Log Analytics-werkruimte in de Azure-portal.  Selecteer **Computergroepen,** **WSUS**en **importeer WSUS-groepslidmaatschappen**.  Er is geen verdere configuratie nodig.

![Computergroepen van WSUS](media/computer-groups/configure-wsus.png)

Wanneer groepen zijn geïmporteerd, wordt in het menu het aantal computers met groepslidmaatschap gedetecteerd en het aantal geïmporteerde groepen weergegeven.  U op een van deze koppelingen klikken om de **ComputerGroup-records** met deze informatie terug te sturen.

### <a name="configuration-manager"></a>Configuratiebeheer
Wanneer u Azure Monitor configureert om lidmaatschappen van configuration manager-verzameling te importeren, wordt voor elke verzameling een computergroep voor elke verzameling geconfigureerd.  De collectielidmaatschapsinformatie wordt elke 3 uur opgehaald om de computergroepen actueel te houden. 

Voordat u Configuration Manager-verzamelingen importeren, moet u [Configuration Manager verbinden met Azure Monitor](collect-sccm.md).  

![Computergroepen van SCCM](media/computer-groups/configure-sccm.png)

Wanneer verzamelingen zijn geïmporteerd, wordt in het menu het aantal computers met groepslidmaatschap gedetecteerd en het aantal geïmporteerde groepen weergegeven.  U op een van deze koppelingen klikken om de **ComputerGroup-records** met deze informatie terug te sturen.

## <a name="managing-computer-groups"></a>Computergroepen beheren
U computergroepen bekijken die zijn gemaakt op basis van een logboekquery of de Api voor logboekzoeken vanuit **geavanceerde instellingen** in uw Log Analytics-werkruimte in de Azure-portal.  Selecteer **Computergroepen** en vervolgens **Opgeslagen groepen**.  

Klik op de **x** in de kolom **Verwijderen** om de computergroep te verwijderen.  Klik op het pictogram **Leden weergeven** voor een groep om de logboekzoekopdracht van de groep uit te voeren die de leden retourneert.  U een computergroep niet wijzigen, maar in plaats daarvan verwijderen en vervolgens opnieuw maken met de gewijzigde instellingen.

![Opgeslagen computergroepen](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Een computergroep gebruiken in een logboekquery
U gebruikt een computergroep die is gemaakt op basis van een logboekquery in een query door de alias als functie te behandelen, meestal met de volgende syntaxis:

  `Table | where Computer in (ComputerGroup)`

U bijvoorbeeld het volgende gebruiken om UpdateSummary-records terug te sturen voor alleen computers in een computergroep genaamd mycomputergroep.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Geïmporteerde computergroepen en hun meegeleverde computers worden opgeslagen in de tabel **Computergroep.**  Met de volgende query wordt bijvoorbeeld een lijst met computers in de groep Domeincomputers teruggegeven uit Active Directory. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

Met de volgende query worden updateoverzichtrecords alleen voor computers in domeincomputers retourneert.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Computergroeprecords
Er wordt een record gemaakt in de werkruimte Log Analytics voor elk computergroepslidmaatschap dat is gemaakt vanuit Active Directory of WSUS.  Deze records hebben een type **ComputerGroep** en hebben de eigenschappen in de volgende tabel.  Records worden niet gemaakt voor computergroepen op basis van logboekquery's.

| Eigenschap | Beschrijving |
|:--- |:--- |
| `Type` |*Computergroep* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |Naam van de lidcomputer. |
| `Group` |Naam van de groep. |
| `GroupFullName` |Volledig pad naar de groep inclusief de bron- en bronnaam. |
| `GroupSource` |Bron van die groep werd verzameld. <br><br>Active Directory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |Naam van de bron waarvan de groep is verzameld.  Voor Active Directory is dit de domeinnaam. |
| `ManagementGroupName` |Naam van de beheergroep voor SCOM-agents.  Voor andere agents is dit\<AOI-werkplek-ID\> |
| `TimeGenerated` |Datum en tijd waarop de computergroep is gemaakt of bijgewerkt. |

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [logboekquery's](../log-query/log-query-overview.md) om de gegevens te analyseren die zijn verzameld uit gegevensbronnen en -oplossingen.  

