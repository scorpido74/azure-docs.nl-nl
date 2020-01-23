---
title: Computer groepen in Azure Monitor-logboek query's | Microsoft Docs
description: Met computer groepen in Azure Monitor kunt u logboek query's op een bepaalde set computers bereiken.  In dit artikel worden de verschillende methoden beschreven die u kunt gebruiken voor het maken van computer groepen en het gebruik ervan in een logboek query.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/05/2019
ms.openlocfilehash: eedf04a2168c67449f97d8e462d4ff82653a22b3
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513695"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Computer groepen in Azure Monitor-logboek query's
Met computer groepen in Azure Monitor kunt u [logboek query's](../log-query/log-query-overview.md) op een bepaalde set computers bereiken.  Elke groep wordt gevuld met computers met behulp van een query die u definieert of door het importeren van groepen van verschillende bronnen.  Wanneer de groep is opgenomen in een logboek query, zijn de resultaten beperkt tot records die overeenkomen met de computers in de groep.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Het maken van een computergroep
U kunt een computer groep maken in Azure Monitor met behulp van een van de methoden in de volgende tabel.  In de onderstaande secties vindt u meer informatie over elke methode. 

| Methode | Beschrijving |
|:--- |:--- |
| Logboek query |Een logboek query maken waarmee een lijst met computers wordt geretourneerd. |
| API voor zoeken in logboeken |Gebruik de API voor zoeken in Logboeken om programmatisch een computer groep te maken op basis van de resultaten van een logboek query. |
| Active Directory |Automatisch het groepslid maatschap scannen van alle agent computers die lid zijn van een Active Directory domein en een groep in Azure Monitor maken voor elke beveiligings groep. (Alleen Windows-computers)|
| Configuration Manager | Importeer verzamelingen van micro soft endpoint Configuration Manager en maak een groep in Azure Monitor voor elk. |
| Windows Server updateservices |Automatisch WSUS-servers of-clients scannen voor doel groepen en een groep maken in Azure Monitor voor elk. |

### <a name="log-query"></a>Logboek query
Computer groepen die zijn gemaakt op basis van een logboek query bevatten alle computers die worden geretourneerd door een query die u definieert.  Deze query wordt uitgevoerd telkens als groep op de computer wordt gebruikt, zodat alle wijzigingen die sinds de groep is gemaakt is doorgevoerd.  

U kunt een query voor een computergroep, maar er moet een afzonderlijke set computers geretourneerd met behulp van `distinct Computer`.  Hier volgt een typisch voor beeld van een query die u als computer groep zou kunnen gebruiken.

    Heartbeat | where Computer contains "srv" | distinct Computer

Gebruik de volgende procedure een computergroep maken vanuit een zoeken in Logboeken in Azure portal.

1. Klik in het menu **Azure monitor** van de Azure Portal op **Logboeken** .
1. Maak een query en voer deze uit om de computers te retour neren die u in de groep wilt gebruiken.
1. Klik boven aan het scherm op **Opslaan** .
1. Wijzig **Opslaan als** in **functie** en selecteer **deze query opslaan als een computer groep**.
1. Geef waarden op voor elke eigenschap voor de computer groep die in de tabel wordt beschreven en klik op **Opslaan**.

De volgende tabel beschrijft de eigenschappen die een computergroep definiëren.

| Eigenschap | Beschrijving |
|:---|:---|
| Name   | De naam van de query die moet worden weer gegeven in de portal. |
| Functiealias | Een unieke alias die wordt gebruikt voor het identificeren van de groep van de computer in een query. |
| Category       | Categorie voor het ordenen van de query's in de portal. |


### <a name="active-directory"></a>Active Directory
Wanneer u Azure Monitor configureert om Active Directory groepslid maatschappen te importeren, analyseert het groepslid maatschap van alle Windows-domein computers die zijn toegevoegd aan de Log Analytics-agent.  Er wordt een computer groep gemaakt in Azure Monitor voor elke beveiligings groep in Active Directory en elke Windows-computer wordt toegevoegd aan de computer groepen die zijn gekoppeld aan de beveiligings groepen waarvan ze lid zijn.  Dit lidmaatschap wordt voortdurend bijgewerkt voor elke 4 uur.  

> [!NOTE]
> Geïmporteerde Active Directory groepen bevatten alleen Windows-machines.

U configureert Azure Monitor om Active Directory beveiligings groepen te importeren uit **Geavanceerde instellingen** in uw log Analytics-werk ruimte in de Azure Portal.  Selecteer **computergroepen**, **Active Directory**, en vervolgens **importeren Active Directory-groepslidmaatschappen van computers**.  Er is geen verdere configuratie nodig.

![Computergroepen uit Active Directory](media/computer-groups/configure-activedirectory.png)

Wanneer groepen zijn geïmporteerd, wordt het menu geeft het aantal computers met groepslidmaatschappen gedetecteerd en het aantal groepen geïmporteerd.  U kunt klikken op een van deze koppelingen om terug te keren de **ComputerGroup** records met deze informatie.

### <a name="windows-server-update-service"></a>Windows Server Update Service
Wanneer u Azure Monitor configureert om WSUS-groepslid maatschappen te importeren, analyseert het het lidmaatschap van de doel groep van alle computers met de Log Analytics agent.  Als u doel items aan de client zijde gebruikt, wordt voor elke computer die is verbonden met Azure Monitor en die deel uitmaakt van een WSUS-doel groep, het groepslid maatschap geïmporteerd in Azure Monitor. Als u doel items aan de server zijde gebruikt, moet de Log Analytics-agent worden geïnstalleerd op de WSUS-server om de gegevens van het groepslid maatschap te importeren in Azure Monitor.  Dit lidmaatschap wordt voortdurend bijgewerkt voor elke 4 uur. 

U configureert Azure Monitor voor het importeren van WSUS-groepen uit **Geavanceerde instellingen** in uw log Analytics-werk ruimte in de Azure Portal.  Selecteer **computergroepen**, **WSUS**, en vervolgens **importeren WSUS-groepslidmaatschappen**.  Er is geen verdere configuratie nodig.

![Computergroepen van WSUS](media/computer-groups/configure-wsus.png)

Wanneer groepen zijn geïmporteerd, wordt het menu geeft het aantal computers met groepslidmaatschappen gedetecteerd en het aantal groepen geïmporteerd.  U kunt klikken op een van deze koppelingen om terug te keren de **ComputerGroup** records met deze informatie.

### <a name="configuration-manager"></a>Configuration Manager
Wanneer u Azure Monitor configureert om Configuration Manager verzamelings lidmaatschappen te importeren, wordt er voor elke verzameling een computer groep gemaakt.  De lidmaatschapsgegevens van de verzameling wordt elke drie uur zodat de computergroepen huidige opgehaald. 

Voordat u Configuration Manager verzamelingen kunt importeren, moet u [Configuration Manager verbinden met Azure monitor](collect-sccm.md).  

![Computergroepen uit SCCM](media/computer-groups/configure-sccm.png)

Wanneer verzamelingen zijn geïmporteerd, wordt het menu geeft het aantal computers met groepslidmaatschappen gedetecteerd en het aantal groepen geïmporteerd.  U kunt klikken op een van deze koppelingen om terug te keren de **ComputerGroup** records met deze informatie.

## <a name="managing-computer-groups"></a>Computergroepen beheren
U kunt computer groepen weer geven die zijn gemaakt op basis van een logboek query of de API voor zoeken in Logboeken van **Geavanceerde instellingen** in uw log Analytics-werk ruimte in de Azure Portal.  Selecteer **computergroepen** en vervolgens **opgeslagen groepen**.  

Klik op de **x** in de **verwijderen** kolom te verwijderen van de groep op de computer.  Klik op de **leden weergeven** pictogram voor een groep voor het uitvoeren van zoeken in Logboeken van de groep die de leden als resultaat geeft.  U een computergroep niet wijzigen, maar in plaats daarvan moet verwijderen en vervolgens opnieuw te maken met de gewijzigde instellingen.

![Opgeslagen computergroepen](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Een computer groep gebruiken in een logboek query
U gebruikt een computer groep die is gemaakt op basis van een logboek query in een query door de alias te behandelen als een functie, meestal met de volgende syntaxis:

  `Table | where Computer in (ComputerGroup)`

U kunt bijvoorbeeld het volgende om terug te keren UpdateSummary records voor alleen computers in een computergroep mycomputergroup genoemd.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Geïmporteerde computergroepen en hun opgenomen computers worden opgeslagen in de **ComputerGroup** tabel.  Bijvoorbeeld, retourneert de volgende query een lijst van computers in de groep Domeincomputers uit Active Directory. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

De volgende query retourneert UpdateSummary records voor alleen computers in domeincomputers.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Computer groeperen van records
Een record wordt gemaakt in de werkruimte voor logboekanalyse voor elke computer groepslidmaatschap gemaakt op basis van Active Directory of WSUS.  Deze records zijn een type **ComputerGroup** en hebben de eigenschappen in de volgende tabel.  Er worden geen records gemaakt voor computer groepen op basis van logboek query's.

| Eigenschap | Beschrijving |
|:--- |:--- |
| `Type` |*ComputerGroup* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |De naam van de computer die lid is. |
| `Group` |Naam van de groep. |
| `GroupFullName` |Het volledige pad naar de groep met inbegrip van de bron en de naam van de gegevensbron. |
| `GroupSource` |Bron van die groep is verzameld uit. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |Naam van de bron die is verzameld, de groep.  Dit is de domeinnaam voor Active Directory. |
| `ManagementGroupName` |Naam van de beheergroep voor SCOM-agents.  Voor andere agents is dit AOI -\<werkruimte-ID\> |
| `TimeGenerated` |Datum en tijd groep op de computer is gemaakt of bijgewerkt. |

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [query's bijgehouden](../log-query/log-query-overview.md) om de gegevens die worden verzameld van gegevensbronnen en oplossingen te analyseren.  

