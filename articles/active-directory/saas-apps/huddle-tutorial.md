---
title: 'Zelfstudie: Azure Active Directory-integratie met kruipen dicht | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en kruipen dicht.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8389ba4c-f5f8-4ede-b2f4-32eae844ceb0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: d67dbcef1b287ed9552d96338a2591b5f8319532
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434778"
---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>Zelfstudie: Azure Active Directory-integratie met kruipen dicht

In deze zelfstudie leert u hoe u kruipen dicht integreert met Azure Active Directory (Azure AD).

Kruipen dicht integreren met Azure AD biedt u de volgende voordelen:

- U kunt beheren in Azure AD die toegang tot kruipen dicht heeft
- U kunt uw gebruikers automatisch ophalen aangemeld bij kruipen dicht (Single Sign-On) met hun Azure AD-accounts inschakelen
- U kunt uw accounts in één centrale locatie - Azure portal beheren

Als u wilt graag meer informatie over de integratie van de SaaS-app met Azure AD, Zie [wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met kruipen dicht, moet u de volgende items:

- Een Azure AD-abonnement
- Een kruipen dicht eenmalige aanmelding ingeschakeld abonnement

> [!NOTE]
> Als u wilt testen van de stappen in deze zelfstudie, raden we niet met behulp van een productie-omgeving.

Als u wilt testen van de stappen in deze zelfstudie, moet u deze aanbevelingen volgen:

- Gebruik uw productie-omgeving, niet als dat nodig is.
- Als u geen een proefversie Azure AD-omgeving hebt, krijgt u een proefversie van één maand [hier](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie test u de Azure AD eenmalige aanmelding in een testomgeving. Het scenario in deze zelfstudie bestaat uit twee belangrijkste bouwstenen:

1. Toe te voegen kruipen dicht in de galerie
1. Configureren en testen van Azure AD eenmalige aanmelding

## <a name="adding-huddle-from-the-gallery"></a>Toe te voegen kruipen dicht in de galerie
Voor het configureren van de integratie van kruipen dicht bij Azure AD, moet u kruipen dicht in de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

**Als u wilt toevoegen kruipen dicht in de galerie, moet u de volgende stappen uitvoeren:**

1. In de  **[Azure-portal](https://portal.azure.com)**, klik in het navigatievenster aan de linkerkant op **Azure Active Directory** pictogram. 

    ![Active Directory][1]

1. Navigeer naar **bedrijfstoepassingen**. Ga vervolgens naar **alle toepassingen**.

    ![Toepassingen][2]
    
1. Nieuwe toepassing toevoegen, klikt u op **nieuwe toepassing** knop boven aan het dialoogvenster.

    ![Toepassingen][3]

1. Typ in het zoekvak **kruipen dicht**.

    ![Het maken van een Azure AD-testgebruiker](./media/huddle-tutorial/tutorial_huddle_search.png)

1. Selecteer in het deelvenster resultaten **kruipen dicht**, en klik vervolgens op **toevoegen** om toe te voegen van de toepassing.

    ![Het maken van een Azure AD-testgebruiker](./media/huddle-tutorial/tutorial_huddle_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configureren en testen van Azure AD eenmalige aanmelding

In deze sectie kunt u configureren en testen Azure AD eenmalige aanmelding met kruipen dicht op basis van een testgebruiker met de naam "Britta Simon."

Voor eenmalige aanmelding om te werken, moet Azure AD om te weten wat de gebruiker equivalent in kruipen dicht is aan een gebruiker in Azure AD. Met andere woorden, moet een koppeling relatie tussen een Azure AD-gebruiker en de gerelateerde gebruiker in kruipen dicht tot stand worden gebracht.

In het kruipen dicht, wijs de waarde van de **gebruikersnaam** in Azure AD als de waarde van de **gebruikersnaam** de relatie van de koppeling tot stand brengen.

Als u wilt configureren en testen van Azure AD eenmalige aanmelding met kruipen dicht, u nodig hebt voor de volgende bouwstenen:

1. **[Configureren van Azure AD eenmalige aanmelding](#configuring-azure-ad-single-sign-on)**  : als u wilt dat uw gebruikers kunnen deze functie gebruiken.

1. **[Het maken van een Azure AD-testgebruiker](#creating-an-azure-ad-test-user)**  - voor het testen van Azure AD eenmalige aanmelding met Britta Simon.

1. **[Het maken van een testgebruiker kruipen dicht](#creating-a-huddle-test-user)**  : als u wilt een equivalent van Britta Simon in kruipen dicht die is gekoppeld aan de Azure AD-weergave van de gebruiker hebben.

1. **[Toewijzen van de Azure AD-testgebruiker](#assigning-the-azure-ad-test-user)**  - Britta Simon gebruik van Azure AD eenmalige aanmelding inschakelen.

1. **[Eenmalige aanmelding testen](#testing-single-sign-on)**  : als u wilt controleren of de configuratie werkt.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD eenmalige aanmelding configureren

In deze sectie maakt u Azure AD eenmalige aanmelding in de Azure-portal inschakelen en configureren van eenmalige aanmelding in uw toepassing kruipen dicht.

**Voor het configureren van Azure AD eenmalige aanmelding met kruipen dicht, moet u de volgende stappen uitvoeren:**

1. In de Azure-portal op de **kruipen dicht** toepassingspagina integratie, klikt u op **eenmalige aanmelding**.

    ![Eenmalige aanmelding configureren][4]

1. Op de **eenmalige aanmelding** dialoogvenster, selecteer **modus** als **SAML gebaseerde aanmelding** eenmalige aanmelding inschakelen.
 
    ![Eenmalige aanmelding configureren](./media/huddle-tutorial/tutorial_huddle_samlbase.png)

1. Op de **kruipen dicht domein en URL's** sectie, voert u de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/huddle-tutorial/tutorial_huddle_url.png)

    In de **aanmeldings-URL** tekstvak, een URL met behulp van het volgende patroon: `http://<company name>.huddle.com`

    > [!NOTE] 
    > Deze waarde is niet echt. Deze waarde bijwerken met de werkelijke aanmeldings-URL. Neem contact op met [kruipen dicht Client ondersteuningsteam](https://huddle.zendesk.com) deze waarde op te halen. 

1. Op de **SAML-handtekeningcertificaat** sectie, klikt u op **Certificate(Base64)** en slaat u het certificaatbestand op uw computer.

    ![Eenmalige aanmelding configureren](./media/huddle-tutorial/tutorial_huddle_certificate.png) 

1. Klik op **opslaan** knop.

    ![Eenmalige aanmelding configureren](./media/huddle-tutorial/tutorial_general_400.png)

1. Op de **kruipen dicht configuratie** sectie, klikt u op **configureren kruipen dicht** openen **aanmelding configureren** venster. Kopiëren de **SAML entiteit-ID en Single Sign-On Service URL voor SAML-** uit de **Naslaggids sectie.** 

    ![Eenmalige aanmelding configureren](./media/huddle-tutorial/tutorial_huddle_configure.png) 
    
1. Voor het configureren van eenmalige aanmelding op kruipen dicht zijde, moet u voor het verzenden van de gedownloade **certificaat**, **Single Sign-On Service URL voor SAML**, en **SAML entiteit-ID** naar [ Client-ondersteuningsteam kruipen dicht](https://huddle.zendesk.com). Ze stelt u deze optie om de SAML SSO-verbinding instellen goed aan beide zijden.  
   
    >[!NOTE]
    > Eenmalige aanmelding moet worden ingeschakeld door het ondersteuningsteam kruipen dicht. U ontvangt een melding wanneer de configuratie is voltooid. 
    > 

> [!TIP]
> U kunt nu een beknopte versie van deze instructies binnen lezen de [Azure-portal](https://portal.azure.com), terwijl het instellen van de app!  Na het toevoegen van deze app uit de **Active Directory > bedrijfstoepassingen** sectie, klikt u op de **Single Sign-On** tabblad en toegang tot de ingesloten documentatie via de  **Configuratie** sectie aan de onderkant. U kunt meer lezen over de documentatie voor embedded-functie: [embedded-documentatie voor Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 
   
### <a name="creating-an-azure-ad-test-user"></a>Het maken van een Azure AD-testgebruiker

Het doel van deze sectie is het maken van een testgebruiker in Azure portal Britta Simon genoemd.

![Azure AD-gebruiker maken][100]

**Als u wilt een testgebruiker maken in Azure AD, moet u de volgende stappen uitvoeren:**

1. In de **Azure-portal**, klik op het navigatiedeelvenster links **Azure Active Directory** pictogram.

    ![Het maken van een Azure AD-testgebruiker](./media/huddle-tutorial/create_aaduser_01.png) 

1. Als u wilt weergeven in de lijst met gebruikers, gaat u naar **gebruikers en groepen** en klikt u op **alle gebruikers**.
    
    ![Het maken van een Azure AD-testgebruiker](./media/huddle-tutorial/create_aaduser_02.png) 

1. Om te openen de **gebruiker** dialoogvenster, klikt u op **toevoegen** boven aan het dialoogvenster.
 
    ![Het maken van een Azure AD-testgebruiker](./media/huddle-tutorial/create_aaduser_03.png) 

1. Op de **gebruiker** dialoogvenster pagina, voert u de volgende stappen uit:
 
    ![Het maken van een Azure AD-testgebruiker](./media/huddle-tutorial/create_aaduser_04.png) 

    a. In de **naam** tekstvak, type **BrittaSimon**.

    b. In de **gebruikersnaam** tekstvak, type de **e-mailadres** van BrittaSimon.

    c. Selecteer **wachtwoord weergeven** en noteer de waarde van de **wachtwoord**.

    d. Klik op **Create**.
 
### <a name="creating-a-huddle-test-user"></a>Het maken van een testgebruiker kruipen dicht

Als u wilt dat Azure AD-gebruikers zich aanmelden bij kruipen dicht, moeten ze worden ingericht voor het kruipen dicht. In het geval van kruipen dicht is inrichten een handmatige taak.

**Als u wilt inrichten van gebruikers configureren, moet u de volgende stappen uitvoeren:**

1. Meld u aan bij uw **kruipen dicht** bedrijf site als administrator.
1. Klik op **werkruimte**.
1. Klik op **mensen \> anderen uitnodigen**.
   
   ![Mensen](./media/huddle-tutorial/IC787838.png "personen")

1. In de **maken van een uitnodiging voor een nieuwe** sectie, voert u de volgende stappen uit:
   
   ![Nieuwe uitnodiging](./media/huddle-tutorial/IC787839.png "nieuwe uitnodiging")
   
   a. In de **kiest u een team dat anderen uitnodigen** in de lijst met **team**.

   b. Type de **e-mailadres** van een geldige Azure AD-account die u inrichten wilt **Enter e-mailadres voor mensen die u wilt uitnodigen** tekstvak.

   c. Klik op **uitnodigen**.   
   
    >[!NOTE]
    > De houder van Azure AD-account ontvangt een e-mailbericht ook een koppeling voor het account te bevestigen voordat deze geactiveerd wordt. 
    > 

>[!NOTE]
>U kunt een andere kruipen dicht gebruiker-account maken-hulpprogramma's of API's die worden geleverd door kruipen dicht gebruiken voor het inrichten van gebruikersaccounts van de Azure AD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Toewijzen aan de gebruiker van de test Azure AD

In deze sectie maakt inschakelen u Britta Simon gebruiken Azure eenmalige aanmelding door toegang te verlenen aan kruipen dicht.

![Gebruiker toewijzen][200] 

**Als u wilt Britta Simon aan kruipen dicht toewijst, moet u de volgende stappen uitvoeren:**

1. Open de weergave toepassingen in de Azure-portal en gaat u naar de mapweergave en Ga naar **bedrijfstoepassingen** klikt u vervolgens op **alle toepassingen**.

    ![Gebruiker toewijzen][201] 

1. Selecteer in de lijst met toepassingen, **kruipen dicht**.

    ![Eenmalige aanmelding configureren](./media/huddle-tutorial/tutorial_huddle_app.png) 

1. Klik in het menu aan de linkerkant op **gebruikers en groepen**.

    ![Gebruiker toewijzen][202] 

1. Klik op **toevoegen** knop. Selecteer vervolgens **gebruikers en groepen** op **toevoegen toewijzing** dialoogvenster.

    ![Gebruiker toewijzen][203]

1. Op **gebruikers en groepen** dialoogvenster, selecteer **Britta Simon** in de lijst gebruikers.

1. Klik op **Selecteer** op knop **gebruikers en groepen** dialoogvenster.

1. Klik op **toewijzen** op knop **toevoegen toewijzing** dialoogvenster.
    
### <a name="testing-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie maakt testen u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster.

Wanneer u op de tegel kruipen dicht in het toegangsvenster, moet u de aanmeldingspagina van kruipen dicht toepassing automatisch beschikt.
Zie voor meer informatie over het toegangsvenster, [Inleiding tot het toegangsvenster](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Aanvullende resources

* [Lijst met zelfstudies over het integreren van SaaS-Apps met Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

<!--Image references-->

[1]: ./media/huddle-tutorial/tutorial_general_01.png
[2]: ./media/huddle-tutorial/tutorial_general_02.png
[3]: ./media/huddle-tutorial/tutorial_general_03.png
[4]: ./media/huddle-tutorial/tutorial_general_04.png
[100]: ./media/huddle-tutorial/tutorial_general_100.png
[200]: ./media/huddle-tutorial/tutorial_general_200.png
[201]: ./media/huddle-tutorial/tutorial_general_201.png
[202]: ./media/huddle-tutorial/tutorial_general_202.png
[203]: ./media/huddle-tutorial/tutorial_general_203.png
