---
title: Tabblad Testdrive voor virtuele machines in de Cloud Partner Portal voor azure marketplace
description: Beschrijft het tabblad Teststation dat wordt gebruikt bij het maken van een Azure Marketplace VM-aanbieding.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: d4fc8762e25825b21637b16b751d57a0dcbf369e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288798"
---
# <a name="virtual-machine-test-drive-tab"></a>Tabblad Teststation voor virtuele machine

Met het tabblad **Testdrive** van de pagina **Nieuwe aanbieding** u uw potentiële klanten een hands-on, self-guided demonstratie geven van de belangrijkste functies en voordelen van uw product, gedemonstreerd in een gestandaardiseerd scenario.  Testdrive is een optionele functie voor de aanbiedingstypen die teststation ondersteunen.  Test Drive vereist dat ondersteunende assets correct worden geïmplementeerd.  Zie het artikel [Azure Marketplace Test Drive](https://azure.microsoft.com/blog/azure-marketplace-test-drive/)voor meer informatie .  <!--TD: Replace with migrated version of Test Drive article! -->

Als u deze functie wilt inschakelen, klikt u op het tabblad **Teststation** op de optie **Ja** op **Een teststation inschakelen**.  Op het tabblad **Teststation** worden de velden weergegeven die beschikbaar zijn voor bewerking.  Een toegevoegd sterretje (*) op de veldnaam geeft aan dat dit vereist is.

![Tabblad Teststation op het formulier Nieuwe aanbieding voor virtuele machines](./media/publishvm_007.png)


## <a name="field-values"></a>Veldwaarden

In de volgende tabel worden het doel en de inhoud van deze velden beschreven.  Vereiste velden worden aangeklaagd door een sterretje (*).


|    Veld                  |       Beschrijving                                                            |
|  ---------                |     ---------------                                                          |
|  *Details*   |  |
| **Beschrijving\***           | Geef een overzicht van uw teststationscenario. Deze tekst wordt aan de gebruiker getoond terwijl de testdrive wordt ingericht. Dit veld ondersteunt basis-HTML als u opgemaakte inhoud wilt bieden.  |
| **Gebruikershandleiding\***           | Upload een gedetailleerde gebruikershandleiding (.pdf) waarmee gebruikers van Test Drive weten hoe ze uw oplossing moeten gebruiken.  |
| **Demovideo teststation** | Upload een video waarin uw oplossing wordt beschreven.  Als je deze optie hebt gekozen, moet je een naam, URL aan de video (gehost op YouTube of Vimeo) en een (533x324 pixel) thumbnail voor de video opgeven. |
| *Technische configuratie* |  |
| **exemplaren\***             | Geef de beschikbaarheid van het gebied op en de relatief beschikbaarheid van de vm-instantie (klik op het infopictogram voor meer informatie).  <br/>Potentiële gelijktijdige Test Drive-sessies mogen de quotumlimiet voor uw abonnement niet overschrijden.  Het eerste wordt berekend als: [Aantal geselecteerde regio's] x [Hot instances] + [Aantal geselecteerde regio's] x [Warme instanties] + [Aantal geselecteerde regio's] x [Koude instanties] |
| **Duur testrit\***   | Maximale sessieduur in uren. De Test Drive-sessie wordt automatisch beëindigd nadat deze periode is overschreden.  |
|**Arm-sjabloon teststation\***| Upload de azure resource manager-sjabloon die aan dit teststation is gekoppeld. Zie [Implementatiesjabloon virtuele machine transformeren voor teststation voor](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)meer informatie. |
| **Toegangsgegevens\***    | Azure Resource Manager toegangs- en proefinloggegevens, geschreven als platte tekst of eenvoudige HTML. |
| *Abonnementsgegevens voor implementatie van teststation* |  |
| **Azure-abonnements-id\*** | U zich aanmelden bij de [Microsoft Azure-portal](https://ms.portal.azure.com) en op **Abonnementen** op de linkermenubalk klikken. (Voorbeeld: "a83645ac-1234-5ab6-6789-1h234g764ghty")    Deze id moet een GUID `a83645ac-1234-5ab6-6789-1h234g764ghty`van het formulier zijn .|
| **Azure AD-tenant-id\***    | Azure Active Directory-tenant-id.  Kan worden verkregen door u aan te melden bij de [Microsoft Azure-portal](https://ms.portal.azure.com) en op **Azure Active Directory** op de linkermenubalk te klikken en vervolgens op **Eigenschappen** in de middelste menubalk te klikken en vervolgens de **Directory-id** uit het formulier te kopiëren.  Deze id moet ook een GUID zijn.  Als deze leeg is, moet u een tenant-id voor uw organisatie maken. |
| **Azure AD-app-id\***       | Id voor uw geregistreerde Azure VM-oplossing  |
| **Azure AD-app-sleutel\***      | Verificatiesleutel voor uw geregistreerde oplossing |
|   |   |


## <a name="next-steps"></a>Volgende stappen

Op het volgende [tabblad Marketplace](./cpp-marketplace-tab.md) geeft u marketing- en juridische informatie over uw oplossing.
