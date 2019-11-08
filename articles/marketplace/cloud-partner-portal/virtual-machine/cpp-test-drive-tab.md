---
title: Het tabblad test station van de virtuele machine in de Cloud Partner-portal voor Azure Marketplace
description: Hierin wordt het tabblad test station beschreven dat wordt gebruikt voor het maken van een Azure Marketplace-VM-aanbieding.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 94ebbcca50916f1675ab990a4b45f3b90e069104
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808864"
---
# <a name="virtual-machine-test-drive-tab"></a>Het tabblad test station van de virtuele machine

Op het tabblad **test drive** van de pagina **nieuwe aanbieding** kunt u uw potentiële klanten voorzien van een praktijk gerichte, zelfgestuurde demonstratie van de belangrijkste functies en voor delen van uw product, zoals wordt getoond in een gestandaardiseerd scenario.  Test drive is een optionele functie voor de aanbiedings typen die ondersteuning bieden voor een test drive.  Voor een test drive moeten ondersteunende assets goed worden geïmplementeerd.  Zie het artikel [Azure Marketplace test drive](https://azure.microsoft.com/blog/azure-marketplace-test-drive/)voor meer informatie.  <!--TD: Replace with migrated version of Test Drive article! -->

Als u deze functie wilt inschakelen, klikt u op het tabblad **test station** op de optie **Ja** in **een test station inschakelen**.  Op het tabblad **test station** worden de velden weer gegeven die kunnen worden bewerkt.  Een toegevoegd sterretje (*) op de veld naam geeft aan dat deze vereist is.

![Het tabblad test station in het formulier voor de nieuwe aanbieding voor virtuele machines](./media/publishvm_007.png)


## <a name="field-values"></a>Veld waarden

In de volgende tabel worden het doel en de inhoud van deze velden beschreven.  Vereiste velden worden gedicteerd door een asterisk (*).


|    Veld                  |       Beschrijving                                                            |
|  ---------                |     ---------------                                                          |
|  *Details*   |  |
| **Beschrijving\***           | Geef een overzicht van het scenario test drive. Deze tekst wordt weer gegeven aan de gebruiker terwijl het test station wordt ingericht. Dit veld biedt ondersteuning voor eenvoudige HTML als u opgemaakte inhoud wilt leveren.  |
| **Gebruikers handleiding\***           | Upload een gedetailleerde gebruikers handleiding (. PDF) waarmee u kunt testen of gebruikers uw oplossing kunnen gebruiken.  |
| **Demo video testen** | Upload een video met een demonstratie van uw oplossing.  Als u deze optie kiest, moet u een naam opgeven, een URL naar de video (gehost op YouTube of Vimeo) en een (533x324 pixels) miniatuur voor de video. |
| *Technische configuratie* |  |
| **Instanties\***             | Geef de beschik baarheid van de regio en relatief Beschik baarheid van het VM-exemplaar op (Klik op het informatie pictogram voor meer informatie).  <br/>Mogelijke gelijktijdige sessies van de test drive mogen de quotum limiet voor uw abonnement niet overschrijden.  De eerste wordt berekend als: [aantal regio's geselecteerd] x [Hot instances] + [aantal geselecteerde regio's] x [warme instanties] + [aantal geselecteerde regio's] x [koude instanties] |
| **\* van de duur van het test station**   | De maximale sessie duur in uren. De sessie van het test station wordt automatisch beëindigd nadat deze periode is overschreden.  |
|**\* van ARM-sjabloon testen**| Upload de Azure Resource Manager sjabloon die is gekoppeld aan dit test station. Zie de [sjabloon voor de implementatie van virtuele machines transformeren voor een test station](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)voor meer informatie. |
| **Toegangs gegevens\***    | Azure Resource Manager toegang en aanmeldings gegevens voor de proef versie, geschreven als tekst zonder opmaak of eenvoudige HTML. |
| *Details van implementatie abonnement testen* |  |
| **Azure-abonnements-id\*** | Kan worden verkregen door u aan te melden bij de [Microsoft Azure-Portal](https://ms.portal.azure.com) en te klikken op **abonnementen** op de linker menu balk. (Bijvoorbeeld: "a83645ac-1234-5ab6-6789-1h234g764ghty")    Deze id moet een GUID zijn van het formulier `a83645ac-1234-5ab6-6789-1h234g764ghty`.|
| **Azure AD-Tenant-id\***    | Azure Active Directory Tenant-ID.  Kan worden verkregen door u aan te melden bij de [Microsoft Azure-Portal](https://ms.portal.azure.com) en op **Azure Active Directory** te klikken op de linker menu balk en vervolgens op **Eigenschappen** te klikken in de middelste menu balk. vervolgens wordt de **map-id** vanuit het formulier gekopieerd.  Deze id moet ook een GUID zijn.  Als dit leeg is, moet u een Tenant-ID voor uw organisatie maken. |
| **Azure AD-app-id\***       | Id voor de geregistreerde Azure VM-oplossing  |
| **Azure AD-app sleutel\***      | Verificatie sleutel voor uw geregistreerde oplossing |
|   |   |


## <a name="next-steps"></a>Volgende stappen

Op het volgende [Marketplace](./cpp-marketplace-tab.md) -tabblad geeft u marketing-en juridische informatie over uw oplossing.
