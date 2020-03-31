---
title: Databaserollen en -gebruikers beheren in Azure Analysis Services | Microsoft Documenten
description: Meer informatie over het beheren van databaserollen en gebruikers op een Analysis Services-server in Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 51c01869e6152d8e9357644457df11f4fcf5ec5f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273703"
---
# <a name="manage-database-roles-and-users"></a>Databaserollen en -gebruikers beheren

Op het niveau van de modeldatabase moeten alle gebruikers tot een rol behoren. Rollen definiëren gebruikers met bepaalde machtigingen voor de modeldatabase. Elke gebruiker of beveiligingsgroep die aan een rol is toegevoegd, moet een account hebben in een Azure AD-tenant in hetzelfde abonnement als de server. 

Hoe u rollen definieert, is afhankelijk van het gereedschap dat u gebruikt, maar het effect is hetzelfde.

Functiemachtigingen zijn onder andere:
*  **Beheerder** - Gebruikers hebben volledige machtigingen voor de database. Databaserollen met beheerdersmachtigingen verschillen van serverbeheerders.
*  **Proces** - Gebruikers kunnen verbinding maken met en procesbewerkingen uitvoeren in de database en modeldatabasegegevens analyseren.
*  **Lees** - Gebruikers kunnen een clienttoepassing gebruiken om verbinding te maken met en modeldatabasegegevens te analyseren.

Wanneer u een tabelmodelproject maakt, maakt u rollen en voegt u gebruikers of groepen toe aan die rollen met behulp van Rolbeheer in Visual Studio met analysis services-projecten. Wanneer u wordt geïmplementeerd op een server, gebruikt u SQL Server Management Studio (SSMS), [Analysis Services PowerShell-cmdlets](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference)of [TMSL (Tabular Model Scripting Language)](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) om rollen en gebruikersleden toe te voegen of te verwijderen.

**Beveiligingsgroepen** moeten [per e-mail](https://docs.microsoft.com/exchange/recipients-in-exchange-online/manage-mail-enabled-security-groups) zijn ingeschakeld met de `MailEnabled` eigenschap ingesteld op `True`. Bij het opgeven van een `obj:groupid@tenantid`groep per e-mailadres gebruik .


## <a name="to-add-or-manage-roles-and-users-in-visual-studio"></a>Rollen en gebruikers toevoegen of beheren in Visual Studio  
  
1.  Klik in **Tabelmodelverkenner**met de rechtermuisknop **op Rollen**.  
  
2.  Klik **in Rolbeheer**op **Nieuw**.  
  
3.  Typ een naam voor de rol.  
  
     Standaard wordt de naam van de standaardrol incrementeel genummerd voor elke nieuwe rol. Het wordt aanbevolen een naam te typen die het type lid duidelijk identificeert, bijvoorbeeld Finance Managers of Human Resources Specialists.  
  
4.  Selecteer een van de volgende machtigingen:  
  
    |Machtiging|Beschrijving|  
    |----------------|-----------------|  
    |**Geen**|Leden kunnen het modelschema niet lezen of wijzigen en kunnen geen gegevens opvragen.|  
    |**Lezen**|Leden kunnen gegevens opvragen (op basis van rijfilters), maar kunnen het modelschema niet wijzigen.|  
    |**Lezen en verwerken**|Leden kunnen gegevens opvragen (op basis van filters op rijniveau) en Alle bewerkingen verwerken en verwerken, maar het modelschema niet wijzigen.|  
    |**Proces**|Leden kunnen Proces- en procesbewerkingen uitvoeren. Kan het modelschema niet lezen of wijzigen en kan geen gegevens opvragen.|  
    |**Beheerder**|Leden kunnen het modelschema wijzigen en alle gegevens opvragen.|   
  
5.  Als de rol die u maakt, lees- of lees- en procesmachtigingen heeft, u rijfilters toevoegen met behulp van een DAX-formule. Klik op het tabblad **Rijfilters,** selecteer een tabel, klik vervolgens op het veld **DAX-filter** en typ een DAX-formule.
  
6.  Klik **Members** > **op Leden Extern toevoegen**.  
  
8.  Voer **in Extern lid**toevoegen gebruikers of groepen in uw tenant Azure AD in op e-mailadres. Nadat u op OK hebt geklikt en Rolbeheer hebt gesloten, worden rollen en rolleden weergegeven in Tabelmodelverkenner. 
 
     ![Rollen en gebruikers in Tabelmodel Explorer](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Implementeren op uw Azure Analysis Services-server.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Rollen en gebruikers toevoegen of beheren in SSMS

Als u rollen en gebruikers wilt toevoegen aan een geïmplementeerde modeldatabase, moet u als serverbeheerder of al in een databaserol met beheerdersmachtigingen met de server zijn verbonden.

1. Klik in Object Exporer met de rechtermuisknop op**Nieuwe rol** **Rollen** > .

2. Voer **in Rol maken**een rolnaam en -beschrijving in.

3. Selecteer een machtiging.

   |Machtiging|Beschrijving|  
   |----------------|-----------------|  
   |**Volledige controle (beheerder)**|Leden kunnen het modelschema wijzigen, verwerken en alle gegevens opvragen.| 
   |**Database verwerken**|Leden kunnen Proces- en procesbewerkingen uitvoeren. Kan het modelschema niet wijzigen en kan geen gegevens opvragen.|  
   |**Lezen**|Leden kunnen gegevens opvragen (op basis van rijfilters), maar kunnen het modelschema niet wijzigen.|  
  
4. Klik **op Lidmaatschap**en voer vervolgens een gebruiker of groep in uw tenant Azure AD in op e-mailadres.

     ![Gebruiker toevoegen](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Als de rol die u maakt leestoestemming heeft, u rijfilters toevoegen met behulp van een DAX-formule. Klik **op Rijfilters,** selecteer een tabel en typ een DAX-formule in het veld **DAX-filter.** 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Rollen en gebruikers toevoegen met een TMSL-script

U een TMSL-script uitvoeren in het XMLA-venster in SSMS of met PowerShell. Gebruik de opdracht [CreateOrReplace](https://docs.microsoft.com/analysis-services/tmsl/createorreplace-command-tmsl) en het object [Rollen.](https://docs.microsoft.com/analysis-services/tmsl/roles-object-tmsl)

**Voorbeeld van TMSL-script**

In dit voorbeeld worden een Externe B2B-gebruiker en een groep toegevoegd aan de rol Analist met Leesmachtigingen voor de SalesBI-database. Zowel de externe gebruiker als de groep moeten zich in dezelfde tenant Azure AD bevinden.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Analyst"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@adventureworks.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="to-add-roles-and-users-by-using-powershell"></a>Rollen en gebruikers toevoegen met PowerShell

De [SqlServer-module](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) biedt taakspecifieke databasebeheercmdlets en de cmdlet voor algemene doeleinden Invoke-ASCmd die een TMSL-query of -script (Tabular Model Scripting Language) accepteert. De volgende cmdlets worden gebruikt voor het beheren van databaserollen en gebruikers.
  
|Cmdlet|Beschrijving|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Een lid toevoegen aan een databaserol.| 
|[Verwijder-Rollid](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Een lid uit een databaserol verwijderen.|   
|[Aanroepen-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Een TMSL-script uitvoeren.|

## <a name="row-filters"></a>Rijfilters  

Rijfilters bepalen welke rijen in een tabel kunnen worden opgevraagd door leden van een bepaalde rol. Rijfilters worden voor elke tabel in een model gedefinieerd met DAX-formules.  
  
Rijfilters kunnen alleen worden gedefinieerd voor rollen met machtigingen lezen en lezen en verwerken. Als een rijfilter niet is gedefinieerd voor een bepaalde tabel, kunnen leden standaard alle rijen in de tabel opvragen, tenzij kruisfiltering van toepassing is vanuit een andere tabel.
  
 Rijfilters vereisen een DAX-formule, die moet worden geëvalueerd tot een WAARDE WAAR/ONWAAR, om de rijen te definiëren die kunnen worden opgevraagd door leden van die specifieke rol. Rijen die niet in de DAX-formule zijn opgenomen, kunnen niet worden opgevraagd. De tabel Klanten met de volgende rijfiltersexpressie *=Klanten [Land] = 'VS'*, leden van de rol Verkoop kunnen bijvoorbeeld alleen klanten in de VS zien.  
  
Rijfilters zijn van toepassing op de opgegeven rijen en gerelateerde rijen. Wanneer een tabel meerdere relaties heeft, wordt met filters beveiliging toegepast voor de relatie die actief is. Rijfilters worden doorsneden met andere rijbestanden die zijn gedefinieerd voor gerelateerde tabellen, bijvoorbeeld:  
  
|Tabel|DAX-expressie|  
|-----------|--------------------|  
|Regio|=Regio[Land]="USA"|  
|ProductCategory|=Productcategorie[Naam]="Fietsen"|  
|Transacties|=Transacties[Jaar]=2016|  
  
 Het netto-effect is dat leden rijen met gegevens kunnen opvragen waar de klant zich in de VS bevindt, de productcategorie fietsen is en het jaar 2016. Gebruikers kunnen geen transacties buiten de VS, transacties die geen fietsen zijn, of transacties niet in 2016 opvragen, tenzij ze lid zijn van een andere rol die deze machtigingen verleent.
  
 U het filter *=FALSE()* gebruiken om toegang tot alle rijen voor een hele tabel te weigeren.

## <a name="next-steps"></a>Volgende stappen

  [Serverbeheerders beheren](analysis-services-server-admins.md)   
  [Azure Analysis Services beheren met PowerShell](analysis-services-powershell.md)  
  [TMSL-naslaggids (TMSL)](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference)

