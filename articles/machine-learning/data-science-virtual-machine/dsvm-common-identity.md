---
title: Een gemeenschappelijke identiteit instellen
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over het maken van algemene gebruikersaccounts die kunnen worden gebruikt voor meerdere virtuele gegevensmachines van Data Science. U Azure Active Directory of een on-premises Active Directory gebruiken om gebruikers te verifiëren bij de Virtual Machine data science.
keywords: deep learning, AI, data science tools, data science virtual machine, geospatial analytics, team data science proces
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 44f1f7ae3b290e1dbf01877f3881e1d95a238446
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70208144"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Een gemeenschappelijke identiteit instellen op een virtuele machine van Data Science

Op een virtuele Microsoft Azure-machine (VM), inclusief een Virtual Machine (Data Science Virtual Machine), maakt u lokale gebruikersaccounts terwijl u de VM indient. Gebruikers verifiëren vervolgens naar de VM met behulp van deze referenties. Als u meerdere VM's hebt die uw gebruikers moeten openen, kan het beheren van referenties erg omslachtig worden. Een uitstekende oplossing is het implementeren van gemeenschappelijke gebruikersaccounts en -beheer via een op standaarden gebaseerde identiteitsprovider. Via deze benadering u één set referenties gebruiken om toegang te krijgen tot meerdere bronnen op Azure, waaronder meerdere DSVMs.

Active Directory is een populaire identiteitsprovider en wordt ondersteund op Azure, zowel als cloudservice als als on-premises directory. U Azure Active Directory (Azure AD) of on-premises Active Directory gebruiken om gebruikers te verifiëren op een zelfstandige DSVM of een cluster van DSVMs in een Azure-set voor virtuele machine. U doet dit door de DSVM-exemplaren toe te voegen aan een Active Directory-domein.

Als u al Active Directory hebt, u deze gebruiken als uw gemeenschappelijke identiteitsprovider. Als u geen Active Directory hebt, u een beheerd Active Directory-exemplaar op Azure uitvoeren via [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS).

De documentatie voor [Azure AD](https://docs.microsoft.com/azure/active-directory/) bevat gedetailleerde [beheerinstructies,](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)waaronder richtlijnen voor het verbinden van Azure AD met uw on-premises directory als u er een hebt.

In dit artikel wordt beschreven hoe u een volledig beheerde Active Directory-domeinservice inStelt op Azure met Azure AD DS. U vervolgens uw DSVMs aansluiten bij het beheerde Active Directory-domein. Met deze aanpak kunnen gebruikers toegang krijgen tot een pool van DSVM's (en andere Azure-bronnen) via een gemeenschappelijk gebruikersaccount en referenties.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Een volledig beheerd Active Directory-domein instellen op Azure

Azure AD DS maakt het eenvoudig om uw identiteit te beheren door een volledig beheerde service op Azure te bieden. In dit Active Directory-domein beheert u gebruikers en groepen. Voer de volgende stappen uit om een Active Directory-domein en gebruikersaccounts met Azure in Te stellen in uw map:

1. Voeg de gebruiker in de Azure-portal toe aan Active Directory: 

   1. Meld u aan bij het [Azure Active Directory-beheercentrum](https://aad.portal.azure.com) met een account dat een globale beheerder is voor de directory.
    
   1. Selecteer **Azure Active Directory** en vervolgens **Gebruikers en groepen**.
    
   1. Selecteer **alle gebruikers**in gebruikers **en groepen**en selecteer Vervolgens **Nieuwe gebruiker**.
   
           The **User** pane opens:
      
      ![Het deelvenster Gebruiker](./media/add-user.png)
    
   1. Voer de details voor de gebruiker in, zoals **Naam** en **Gebruikersnaam**. Het domeinnaamgedeelte van de gebruikersnaam moet de oorspronkelijke standaarddomeinnaam "[domeinnaam].onmicrosoft.com" zijn of een geverifieerde, niet-federatieve [aangepaste domeinnaam](../../active-directory/add-custom-domain.md) zoals 'contoso.com'.
    
   1. Kopieer of noteer het gegenereerde gebruikerswachtwoord zodat u dit aan de gebruiker kunt doorgeven nadat dit proces is voltooid.
    
   1. U kunt er ook voor kiezen om de informatie bij **Profiel**, **Groepen** of **Directory-rol** in te vullen voor de gebruiker. 
    
   1. Selecteer **onder Gebruiker**de optie **Maken**.
    
   1. Distribueer het gegenereerde wachtwoord veilig naar de nieuwe gebruiker, zodat deze zich kan aanmelden.

1. Een Azure AD DS-exemplaar maken. Volg de instructies in [Azure Active Directory Domain Services inschakelen met de Azure-portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (de sectie 'Een instantie maken en basisinstellingen configureren'). Het is belangrijk om de bestaande gebruikerswachtwoorden in Active Directory bij te werken, zodat het wachtwoord in Azure AD DS wordt gesynchroniseerd. Het is ook belangrijk om DNS toe te voegen aan Azure AD DS, zoals beschreven onder 'De velden in het venster Basisbeginselen van de Azure-portal voltooien om een Azure AD DS-exemplaar te maken' in die sectie.

1. Maak een apart DSVM-subnet in het virtuele netwerk dat is gemaakt in de sectie 'Het virtuele netwerk maken en configureren' van de vorige stap.
1. Maak een of meer DSVM-exemplaren in het DSVM-subnet.
1. Volg de [instructies](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) om de DSVM toe te voegen aan Active Directory. 
1. Stel een Azure Files-share in om uw huis- of notitieblokmap te hosten, zodat uw werkruimte op elke machine kan worden gemonteerd. (Als u strikte machtigingen op bestandsniveau nodig hebt, moet network file system [NFS] worden uitgevoerd op een of meer VM's.)

   1. [Maak een Azure-bestandenshare](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Monteer dit aandeel op de Linux DSVM. Wanneer u **Verbinding** voor het Azure-bestandenaandeel selecteert in uw opslagaccount in de Azure-portal, wordt de opdracht weergegeven die moet worden uitgevoerd in de bashshell op de Linux DSVM. De opdracht ziet er als volgt uit:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Stel dat u uw Azure-bestandenaandeel hebt gemonteerd in /data/workspace. Maak nu mappen voor elk van uw gebruikers in het aandeel: /data/workspace/user1, /data/workspace/user2, enzovoort. Maak `notebooks` een map in de werkruimte van elke gebruiker. 
1. Symbolische koppelingen `notebooks` maken `$HOME/userx/notebooks/remote`voor in .   

U hebt nu de gebruikers in uw Active Directory-exemplaar gehost in Azure. Met Active Directory-referenties kunnen gebruikers zich aanmelden bij elke DSVM (SSH of JupyterHub) die is verbonden met Azure AD DS. Omdat de gebruikerswerkruimte zich op een Azure-bestanden-share bevindt, hebben gebruikers toegang tot hun notitieblokken en ander werk vanaf elke DSVM wanneer ze JupyterHub gebruiken.

Voor automatisch schalen u een virtuele machineschaalset gebruiken om een groep VM's te maken die allemaal op deze manier en met de gedeelde schijf zijn verbonden met het domein. Gebruikers kunnen zich aanmelden bij elke beschikbare machine in de virtuele machineschaalset en toegang hebben tot de gedeelde schijf waar hun notitieblokken worden opgeslagen. 

## <a name="next-steps"></a>Volgende stappen

* [Veilige referenties opslaan om toegang te krijgen tot cloudbronnen](dsvm-secure-access-keys.md)



