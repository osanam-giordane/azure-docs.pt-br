---
title: 'Tutorial: integração do Azure Active Directory ao Front | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Front.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88270b6d-2571-434a-b139-b6ccc3a2b19f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: d0bdf3ff282152f92e1b661bf19768489d1a029b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438606"
---
# <a name="tutorial-azure-active-directory-integration-with-front"></a>Tutorial: integração do Azure Active Directory ao Front

Neste tutorial, você aprenderá a integrar o Front ao Azure AD (Azure Active Directory).

A integração do Front ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Front.
- Você pode permitir que seus usuários façam logon automaticamente no Front (logon único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Front, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura de Front habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Front por meio da Galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-front-from-the-gallery"></a>Adição do Front por meio da Galeria
Para configurar a integração do Front ao Azure AD, você precisa adicionar o Front por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Front por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **Front**, selecione **Front** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Front na lista de resultados](./media/front-tutorial/tutorial_front_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Front, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Front é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Front.

No Front, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Front, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar de um usuário de teste do Front](#create-a-front-test-user)** : para ter um equivalente de Brenda Fernandes no Front que esteja vinculado à representação de usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo Front.

**Para configurar o logon único do Azure AD com o Front, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Front**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/front-tutorial/tutorial_front_samlbase.png)

1. Na seção **URLs e Domínio do Front**, execute as seguintes etapas:

    ![Configurar o logon único](./media/front-tutorial/tutorial_front_url1.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<companyname>.frontapp.com`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<companyname>.frontapp.com/sso/saml/callback`
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais, que são explicados adiante no tutorial ou contate a [equipe de suporte do Cliente Front](mailto:support@frontapp.com) para obter esses valores. 

1. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/front-tutorial/tutorial_front_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/front-tutorial/tutorial_general_400.png)
    
1. Na seção **Configuração do Front**, clique em **Configurar Front** para abrir a janela **Configurar Logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/front-tutorial/tutorial_front_configure.png) 

1. Faça logon no seu locatário do Front como administrador.

1. Acesse as **Configurações (ícone de engrenagem na parte inferior da barra lateral esquerda) > Preferências**.
   
    ![Configurar o logon único no lado do aplicativo](./media/front-tutorial/tutorial_front_000.png)

1. Clique no link **Logon Único** .
   
    ![Configurar o logon único no lado do aplicativo](./media/front-tutorial/tutorial_front_001.png)

1. Selecione **SAML** na lista suspensa do **Logon Único**.
   
    ![Configurar o logon único no lado do aplicativo](./media/front-tutorial/tutorial_front_002.png)

1. Na caixa de texto **Ponto de Entrada**, insira o valor da **URL de serviço de logon único** do assistente de configuração de aplicativo do Azure AD.
    
    ![Configurar o logon único no lado do aplicativo](./media/front-tutorial/tutorial_front_003.png)

1. Abra o arquivo de **Certificado (Base64)** baixado no bloco de notas, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **Certificado de autenticação**.
    
    ![Configurar o logon único no lado do aplicativo](./media/front-tutorial/tutorial_front_004.png)

1. Na seção de **Configurações de provedores de serviço**, execute as seguintes etapas:

    ![Configurar o logon único no lado do aplicativo](./media/front-tutorial/tutorial_front_005.png)

    a. Copie o valor da **ID da entidade** e cole-o na caixa de texto **Identificador**, na seção **Domínio e URLs do Front** no portal do Azure.

    b. Copie o valor do **URL do ACS** e cole-o na caixa de texto **URL de Resposta**, na seção **Domínio e URLs do Front** no portal do Azure.
    
1. Clique no botão **Salvar** .

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/front-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/front-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/front-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/front-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-front-test-user"></a>Criar um usuário de teste do Front

Nesta seção, você criará uma usuária chamada Britta Simon no Front. Trabalhe com a [equipe de suporte ao Cliente do Front](mailto:support@frontapp.com) para adicionar os usuários à plataforma Front. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Britta Simon use o logon único do Azure concedendo-lhe acesso ao Front.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Front, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **Fuse**.

    ![O link do Front na lista de Aplicativos](./media/front-tutorial/tutorial_front_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Front no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Front. 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/front-tutorial/tutorial_general_01.png
[2]: ./media/front-tutorial/tutorial_general_02.png
[3]: ./media/front-tutorial/tutorial_general_03.png
[4]: ./media/front-tutorial/tutorial_general_04.png

[100]: ./media/front-tutorial/tutorial_general_100.png

[200]: ./media/front-tutorial/tutorial_general_200.png
[201]: ./media/front-tutorial/tutorial_general_201.png
[202]: ./media/front-tutorial/tutorial_general_202.png
[203]: ./media/front-tutorial/tutorial_general_203.png

