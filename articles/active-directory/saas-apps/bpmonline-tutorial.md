---
title: 'Tutorial: Integração do Azure Active Directory com o Bpm’online | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Bpm’online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 052db91d-ccff-4098-8ae3-2f76eca90539
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: b6fe50b24a20f81500ac1ed5008fcb6c59c0243a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449393"
---
# <a name="tutorial-azure-active-directory-integration-with-bpmonline"></a>Tutorial: Integração do Azure Active Directory ao Bpm’online

Neste tutorial, você aprenderá a integrar o Bpm’online ao Azure Active Directory (AD).

A integração do Bpm’online ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Bpm’online.
- Você pode permitir que usuários façam logon automaticamente no Bpm’online (logon único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Bpm’online, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Bpm’online

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Bpm’online a partir da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-bpmonline-from-the-gallery"></a>Adicionando o Bpm’online a partir da galeria
Para configurar a integração do Bpm’online ao Azure AD, você precisa adicionar o Bpm’online a partir da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Bpm’online a partir da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **Bpm’online**, selecione **Bpm’online** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Bpm’online na lista de resultados](./media/bpmonline-tutorial/tutorial_bpmonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Bpm’online, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Bpm’online é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Bpm’online.

No Bpm’online, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Bpm’online, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do Bpm’online](#create-a-bpmonline-test-user)** – para ter um equivalente de Brenda Fernandes no Bpm’online que esteja vinculado à representação do usuário do Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Bpm’online.

**Para configurar o logon único do Azure AD com o Bpm’online, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **Bpm’online**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/bpmonline-tutorial/tutorial_bpmonline_samlbase.png)

1. Na seção **Domínio e URLs do Bpm’online**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único de Domínio e URLs do Bpm’online](./media/bpmonline-tutorial/tutorial_bpmonline_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<client site name>.bpmonline.com/`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<client site name>.bpmonline.com/ServiceModel/AuthService.svc/SsoLogin`

1. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de Domínio e URLs do Bpm’online](./media/bpmonline-tutorial/tutorial_bpmonline_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<client site name>.bpmonline.com/`
     
    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Entre em contato com a [equipe de suporte do Cliente Bpm’online](mailto:support@bpmonline.com) para obter esses valores. 

1. Na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar a **URL de metadados de federação do aplicativo** e cole-a no bloco de notas.
    
    ![Configurar o logon único](./media/bpmonline-tutorial/tutorial_metadataurl.png)
     
1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/bpmonline-tutorial/tutorial_general_400.png)
    
1. Para configurar logon único no **Bpm’online**, é necessário enviar a **URL de metadados de federação do aplicativo** para a [Equipe de suporte do Bpm’online](mailto:support@bpmonline.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/bpmonline-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/bpmonline-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/bpmonline-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/bpmonline-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-bpmonline-test-user"></a>Criar um usuário de teste do Bpm’online

Nesta seção, você criará um usuário chamado Brenda Fernandes no Bpm’online. Trabalhe com a [equipe de suporte do Bpm’online](mailto:support@bpmonline.com) para adicionar os usuários à plataforma Bpm’online. Os usuários devem ser criados e ativados antes de usar o logon único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Bpm’online.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Bpm’online, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **Bpm’online**.

    ![O link do Bpm’online na lista de Aplicativos](./media/bpmonline-tutorial/tutorial_bpmonline_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Bpm’online no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Bpm’online.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bpmonline-tutorial/tutorial_general_01.png
[2]: ./media/bpmonline-tutorial/tutorial_general_02.png
[3]: ./media/bpmonline-tutorial/tutorial_general_03.png
[4]: ./media/bpmonline-tutorial/tutorial_general_04.png

[100]: ./media/bpmonline-tutorial/tutorial_general_100.png

[200]: ./media/bpmonline-tutorial/tutorial_general_200.png
[201]: ./media/bpmonline-tutorial/tutorial_general_201.png
[202]: ./media/bpmonline-tutorial/tutorial_general_202.png
[203]: ./media/bpmonline-tutorial/tutorial_general_203.png

