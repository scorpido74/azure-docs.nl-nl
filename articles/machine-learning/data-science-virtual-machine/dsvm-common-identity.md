---
title: Instellen van een gemeenschappelijke identiteit voor de Data Science Virtual Machine - Azure | Microsoft Docs
description: Informatie over het maken van algemene gebruikersaccounts die kunnen worden gebruikt voor meerdere virtuele Machines voor Datatechnologie. U kunt Azure Active Directory of een on-premises Active Directory gebruiken voor verificatie van gebruikers aan de Data Science Virtual Machine.
keywords: deep learning, AI, hulpprogramma's voor data science, virtuele machine voor datatechnologie, georuimtelijke analyses, team data science process
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: vijetaj
ms.openlocfilehash: 34303c8832a58e4d97ba4e712d624edf321b84a1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558273"
---
# <a name="set-up-a-common-identity-on-the-data-science-virtual-machine"></a>Instellen van een gemeenschappelijke identiteit voor de Data Science Virtual Machine

Op een Azure-machine (VM), met inbegrip van de Data Science Virtual Machine (DSVM), kunt u lokale gebruikersaccounts maken tijdens het inrichten van de virtuele machine. Gebruikers worden vervolgens geverifieerd aan de virtuele machine met behulp van deze referenties. Als u meerdere virtuele machines die u nodig hebt voor toegang tot hebt, deze benadering kunt snel aan het omslachtig beheren van referenties. Algemene gebruikersaccounts en -beheer via een op standaarden gebaseerde id-provider, kunt u op een enkele set referenties voor toegang tot meerdere resources in Azure, met inbegrip van meerdere Dsvm. 

Active Directory is een populaire id-provider en op Azure wordt ondersteund als een service en on-premises. U kunt Azure Active Directory (Azure AD) of het on-premises Active Directory om gebruikers op een zelfstandige DSVM of een Datatechnologie-cluster in een schaalset voor virtuele machine van Azure te verifiëren. U doen dit door de DSVM-exemplaren toevoegen aan een Active Directory-domein. 

Als u al Active Directory om de identiteiten te beheren, kunt u deze gebruiken als uw algemene id-provider. Als u geen Active Directory, kunt u een beheerd exemplaar van Active Directory op Azure uitvoeren via een service met de naam [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS). 

De documentatie voor [Azure AD](https://docs.microsoft.com/azure/active-directory/) biedt gedetailleerde [management instructies](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution), met inbegrip van Azure AD verbinden met uw on-premises directory als u die hebt. 

Dit artikel beschrijft de stappen voor het instellen van een volledig beheerde Active Directory domain services op Azure met behulp van Azure AD DS. Vervolgens kunt u uw Dsvm toevoegen aan het beheerde domein met Active Directory om gebruikers toegang krijgen tot een groep van Dsvm (en andere Azure-resources) met behulp van een algemene gebruikersaccount en referenties. 

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Een volledig beheerde Active Directory-domein in Azure instellen

Azure AD DS, kunt u eenvoudig uw identiteiten beheren door te voorzien in een volledig beheerde service in Azure. Op deze Active Directory-domein beheert u gebruikers en groepen. De stappen voor het instellen van een wordt gehost op Azure Active Directory-domein en de gebruikersaccounts in uw directory zijn:

1. In de Azure-portal, moet u de gebruiker toevoegen aan Active Directory: 

   a. Meld u aan bij het [Azure Active Directory-beheercentrum](https://aad.portal.azure.com) met een account met globale beheerdersrechten voor de directory.
    
   b. Selecteer **Azure Active Directory** en vervolgens **Gebruikers en groepen**.
    
   c. Selecteer in **Gebruikers en groepen** de optie **Alle gebruikers**. Selecteer vervolgens **Nieuwe gebruiker**.
   
      De **gebruiker** deelvenster wordt geopend.
      
      ![Het deelvenster 'Gebruiker'](./media/add-user.png)
    
   d. Voer de details voor de gebruiker in, zoals **Naam** en **Gebruikersnaam**. Het domeingedeelte van de gebruikersnaam moet de eerste instantie domein naam '[naam].onmicrosoft.com' of een geverifieerde, niet-gefedereerde [aangepaste domeinnaam](../../active-directory/add-custom-domain.md) zoals 'contoso.com'.
    
   e. Kopieer of noteer het gegenereerde gebruikerswachtwoord zodat u dit aan de gebruiker kunt doorgeven nadat dit proces is voltooid.
    
   f. U kunt er ook voor kiezen om de informatie bij **Profiel**, **Groepen** of **Directory-rol** in te vullen voor de gebruiker. 
    
   g. Selecteer bij **Gebruiker** de optie **Maken**.
    
   h. Distribueer het gegenereerde wachtwoord op een veilige manier naar de nieuwe gebruiker zodat deze zich kan aanmelden.

1. Maak een Azure AD DS-exemplaar. Volg de instructies in het artikel [inschakelen Azure Active Directory Domain Services met behulp van de Azure-portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (taken 1 tot en met 5). Het is belangrijk om bij te werken van de bestaande gebruikerswachtwoorden in Active Directory, zodat het wachtwoord in Azure AD DS is gesynchroniseerd. Het is ook belangrijk om toe te voegen DNS met Azure AD DS, zoals beschreven in taak 4 van het artikel. 

1. Maak een apart DSVM-subnet in het virtuele netwerk in taak 2 van de vorige stap hebt gemaakt.
1. Een of meer exemplaren van de Data Science VM maken in het subnet DSVM. 
1. Ga als volgt de [instructies](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) DSVM toevoegen aan Active Directory. 
1. Een Azure Files-share voor het hosten van uw directory thuis of op het notitieblok om in te schakelen voor het koppelen van uw werkruimte op elke machine koppelen. (Als u nauwe op bestandsniveau machtigingen nodig hebt, moet u NFS die worden uitgevoerd op een of meer virtuele machines.)

   a. [Maak een Azure Files-share](../../storage/files/storage-how-to-create-file-share.md).
    
   b. Koppelen op de Linux-DSVM. Wanneer u selecteert de **Connect** knop voor de Azure-bestanden delen in uw opslagaccount in Azure portal, de opdracht om uit te voeren in de Bash shell op de Linux-DSVM wordt weergegeven. De opdracht ziet er als volgt:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Wordt ervan uitgegaan dat uw Azure-bestandsshare in /data/workspace, bijvoorbeeld is gekoppeld. Nu directory's maken voor elk van uw gebruikers in de share: /data/workspace/user1, /data/workspace/user2, enzovoort. Maak een `notebooks` map in de werkruimte van elke gebruiker. 
1. Symbolische koppelingen maken voor `notebooks` in `$HOME/userx/notebooks/remote`.   

U hebt nu de gebruikers in uw Active Directory-exemplaar die wordt gehost in Azure. Gebruikers kunnen met behulp van de Active Directory-referenties aanmelden bij een DSVM (SSH of JupyterHub) die gekoppeld aan Azure AD DS. Omdat de werkruimte voor gebruikers op een Azure-bestandsshare is, hebben gebruikers toegang tot hun laptops en andere werkzaamheden van een DSVM wanneer ze JupyterHub. 

Voor automatisch schalen, kunt u een virtuele-machineschaalset maken van een pool van virtuele machines die zijn gekoppeld aan het domein op deze manier en met de gedeelde schijf die is gekoppeld. Gebruikers kunnen aanmelden bij alle beschikbare machine in de virtuele-machineschaalset en toegang hebben tot de gedeelde schijf waar hun laptops worden opgeslagen. 

## <a name="next-steps"></a>Volgende stappen

* [Veilig opslaan van referenties voor toegang tot cloud-bronnen](dsvm-secure-access-keys.md)



