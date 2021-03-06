---
title: Snelstartgids - gebruiksrechtovereenkomst moet zijn geaccepteerd voordat u toegang tot cloud-apps die zijn beveiligd met voorwaardelijke toegang van Azure Active Directory vereisen | Microsoft Docs
description: In deze snelstartgids leert u hoe u kunt vereisen dat de gebruiksrechtovereenkomst worden geaccepteerd voordat de toegang tot de geselecteerde cloud-apps door Azure Active Directory voor voorwaardelijke toegang wordt verleend.
services: active-directory
keywords: voorwaardelijke toegang tot apps, voorwaardelijke toegang met Azure AD, beveiligde toegang tot bedrijfsbronnen, beleid voor voorwaardelijke toegang, gebruiksvoorwaarden
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: conditional-access
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: a7aeecc84a3629b43f2c1eb40030866a941d0d3b
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627981"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Snelstartgids: Gebruiksvoorwaarden worden geaccepteerd voordat u toegang tot cloud-apps vereisen 

Voordat u toegang tot bepaalde cloud-apps in uw omgeving, is het raadzaam om op te halen van toestemming van gebruikers in de vorm van het accepteren van de gebruiksrechtovereenkomst (gebruiksvoorwaarden). Voorwaardelijke toegang van Azure Active Directory (Azure AD) beschikt u over: 

- Een eenvoudige methode voor het configureren van gebruiksvoorwaarden
- De optie voor het accepteren van de gebruiksrechtovereenkomst via beleid voor voorwaardelijke toegang vereisen  

Deze quickstart laat zien hoe u configureert een [beleid voor voorwaardelijke toegang van Azure AD](../active-directory-conditional-access-azure-portal.md) waarvoor een gebruiksrechtovereenkomst kunnen worden geaccepteerd voor een geselecteerde cloud-app in uw omgeving is vereist.

![Beleid maken](./media/require-tou/5555.png)


Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.



## <a name="prerequisites"></a>Vereisten 

Als u wilt het scenario in deze snelstartgids hebt voltooid, hebt u het volgende nodig:

- **Toegang tot een Azure AD Premium-editie** -voorwaardelijke toegang van Azure AD is een Azure AD Premium-capaciteit. 

- **Een testaccount met de naam Isabella Simonsen** : als u niet hoe ik een testaccount maakt weet, Zie [cloudgebruikers toevoegen](../fundamentals/add-users-azure-active-directory.md#add-cloud-based-users).


## <a name="test-your-sign-in"></a>Test de aanmelding

Het doel van deze stap is om een indruk van de ervaring aanmelden zonder een beleid voor voorwaardelijke toegang.

**Voor het testen van de aanmelding:**

1. Aanmelden bij uw [Azure-portal](https://portal.azure.com/) als Isabella Simonsen.

2. Meld u af.




## <a name="create-your-terms-of-use"></a>Uw gebruiksvoorwaarden maken

In deze sectie biedt u de stappen voor het maken van een voorbeeld van een gebruiksvoorwaarden. Als u een gebruiksvoorwaarden maakt, selecteert u een waarde voor **afdwingen met sjablonen voor voorwaardelijke toegang voor**. Selecteren **aangepast beleid** opent u het dialoogvenster voor het maken van een nieuw beleid voor voorwaardelijke toegang zodra uw gebruiksvoorwaarden is gemaakt.


**Uw gebruiksvoorwaarden maken:**

1. Maak een nieuw document in Microsoft Word.

2. Type **mijn gebruiksvoorwaarden**, en sla het document op uw computer als **mytou.pdf**.

3. Aanmelden bij uw [Azure-portal](https://portal.azure.com) als hoofdbeheerder, beveiligingsbeheerder of een beheerder van voorwaardelijke toegang.

4. Klik in de Azure-portal op de navigatiebalk links op **Azure Active Directory**. 

    ![Azure Active Directory](./media/require-tou/02.png)

5. Op de **Azure Active Directory** pagina, in de **beheren** sectie, klikt u op **voorwaardelijke toegang**.

    ![Voorwaardelijke toegang](./media/require-tou/03.png) 

6. In de **beheren** sectie, klikt u op **gebruiksvoorwaarden**.

    ![Gebruiksvoorwaarden](./media/require-tou/04.png) 

7. Klik in het menu aan de bovenkant op **nieuwe voorwaarden**.

    ![Gebruiksvoorwaarden](./media/require-tou/05.png) 

8. Op de **nieuwe gebruiksvoorwaarden** pagina:

    ![Gebruiksvoorwaarden](./media/require-tou/112.png) 

    a. In de **naam** tekstvak, type **mijn gebruiksvoorwaarden**.

    b. In de **weergavenaam** tekstvak, type **mijn gebruiksvoorwaarden**.

    c. Upload de voorwaarden van de PDF-bestanden gebruiken.

    d. Als **taal**, selecteer **Engels**.

    e. Als **vereisen dat gebruikers om uit te breiden de gebruiksvoorwaarden**, selecteer **op**.

    f. Als **afdwingen met sjablonen voor voorwaardelijke toegang voor**, selecteer **aangepast beleid**.

    g. Klik op **Create**.
 


## <a name="create-your-conditional-access-policy"></a>Uw beleid voor voorwaardelijke toegang maken 

Deze sectie wordt beschreven hoe u de vereiste voorwaardelijk toegangsbeleid maken. Maakt gebruik van het scenario in deze Quick Start:

- De Azure-portal als tijdelijke aanduiding voor een cloud-app waarvoor uw gebruiksvoorwaarden worden geaccepteerd. 
- De voorbeeldgebruiker in voor het testen van het beleid voor voorwaardelijke toegang.  

Stel in het beleid:

|Instelling |Waarde|
|---     | --- |
|Gebruikers en groepen | Isabella Simonsen |
|Cloud-apps | Beheer van Microsoft Azure |
|Toegang verlenen | Mijn gebruiksvoorwaarden |
 

![Beleid maken](./media/require-tou/1234.png)

 


**Uw beleid voor voorwaardelijke toegang configureren:**

1. Op de **nieuw** pagina, in de **naam** tekstvak, type **vereisen gebruiksvoorwaarden voor Isabella**.

    ![Naam](./media/require-tou/71.png)

2. In de **toewijzing** sectie, klikt u op **gebruikers en groepen**.

    ![Gebruikers en groepen](./media/require-tou/06.png)

3. Op de **gebruikers en groepen** pagina:

    ![Gebruikers en groepen](./media/require-tou/24.png)

    a. Klik op **gebruikers en groepen selecteren**, en selecteer vervolgens **gebruikers en groepen**.

    b. Klik op **Selecteren**.

    c. Op de **Selecteer** pagina, selecteert u **Isabella Simonsen**, en klik vervolgens op **Selecteer**.

    d. Op de **gebruikers en groepen** pagina, klikt u op **gedaan**.

4. Klik op **Cloud-apps**.

    ![Cloud-apps](./media/require-tou/08.png)

5. Op de **Cloud-apps** pagina:

    ![Cloud-apps selecteren](./media/require-tou/26.png)

    a. Klik op **apps selecteren**.

    b. Klik op **Selecteren**.

    c. Op de **Selecteer** pagina, selecteert u **Microsoft Azure Management**, en klik vervolgens op **Selecteer**.

    d. Op de **Cloud-apps** pagina, klikt u op **gedaan**.


6. In de **besturingselementen voor toegang** sectie, klikt u op **verlenen**.

    ![Besturingselementen voor toegang](./media/require-tou/10.png)

7. Op de **verlenen** pagina:

    ![Verlenen](./media/require-tou/111.png)

    a. Selecteer **toegang verlenen**.

    a. Selecteer **mijn gebruiksvoorwaarden**.

    b. Klik op **Selecteren**.

8. In de **beleid inschakelen** sectie, klikt u op **op**.

    ![Beleid inschakelen](./media/require-tou/18.png)

9. Klik op **Create**.


## <a name="evaluate-a-simulated-sign-in"></a>Een gesimuleerde aanmelding evalueren

Nu dat u uw beleid voor voorwaardelijke toegang hebt geconfigureerd, wilt u waarschijnlijk weet of deze werkt zoals verwacht. Gebruik als een eerste stap de voorwaardelijke toegang beleid hulpprogramma what-if om te simuleren een aanmelding van uw testgebruiker. De simulatie maakt een schatting van het effect dat aanmelding heeft op uw beleid en genereert een simulatierapport.  

Initialiseren van de wat als hulpprogramma voor het evalueren van beleid is ingesteld:

- **Isabella Simonsen** als gebruiker 
- **Microsoft Azure Management** als cloud-app


Te klikken op **wat gebeurt er als** maakt u een simulatierapport waarin wordt weergegeven:

- **Gebruiksvoorwaarden vereisen voor Isabella** onder **beleidsregels die worden toegepast** 
- **Mijn gebruiksvoorwaarden** als **verlenen besturingselementen**.

![Wat gebeurt er als hulpprogramma voor beleid](./media/require-tou/79.png)



**Uw beleid voor voorwaardelijke toegang evalueren:**

1. Op de [voorwaardelijke toegang - beleid](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) in het menu bovenaan op de pagina, klikt u op **wat gebeurt er als**.  
 
    ![What If](./media/require-tou/14.png)

2. Klik op **gebruikers**, selecteer **Isabella Simonsen**, en klik vervolgens op **Selecteer**.

    ![Gebruiker](./media/require-tou/15.png)

2. Selecteer een cloud-app:

    ![Cloud-apps](./media/require-tou/16.png)

    a. Klik op **Cloud-apps**.

    b. Op de **pagina voor Cloud-apps**, klikt u op **apps selecteren**.

    c. Klik op **Selecteren**.

    d. Op de **Selecteer** pagina, selecteert u **Microsoft Azure Management**, en klik vervolgens op **Selecteer**.

    e. Klik op de pagina van de cloud-apps op **gedaan**.

3. Klik op **wat gebeurt er als**.


## <a name="test-your-conditional-access-policy"></a>Testen van uw beleid voor voorwaardelijke toegang

In de vorige sectie hebt u geleerd hoe u een gesimuleerde aanmelding evalueren. Naast een simulatie, moet u ook testen van uw beleid voor voorwaardelijke toegang om ervoor te zorgen dat deze naar verwachting werkt. 

Als u wilt testen van uw beleid, willen aanmelden bij uw [Azure-portal](https://portal.azure.com) met behulp van uw **Isabella Simonsen** test-account. U ziet een dialoogvenster waarin u moet de gebruiksvoorwaarden accepteren.

![Gebruiksvoorwaarden](./media/require-tou/57.png)


## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet meer nodig hebt, verwijdert u de testgebruiker en het beleid voor voorwaardelijke toegang:

- Als u niet hoe u een Azure AD-gebruiker verwijdert weet, Zie [gebruikers verwijderen uit Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-users-from-azure-ad).

- Als u wilt verwijderen van uw beleid, selecteert u uw beleid en klik vervolgens op **verwijderen** in de werkbalk Snelle toegang.

    ![Multi-Factor Authentication](./media/require-tou/33.png)

- Als u wilt verwijderen van uw gebruiksvoorwaarden, selecteert u deze en klik vervolgens op **voorwaarden verwijderen** in de werkbalk bovenaan. 

    ![Multi-Factor Authentication](./media/require-tou/29.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [MFA vereisen voor specifieke apps](app-based-mfa.md)
> [toegang blokkeren als er een risico voor de sessie wordt gedetecteerd](app-sign-in-risk.md)

