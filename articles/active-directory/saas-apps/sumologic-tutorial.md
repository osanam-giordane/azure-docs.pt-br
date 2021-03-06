---
title: 'Tutorial: Integração do Azure Active Directory ao SumoLogic | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SumoLogic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: dbebf2605fb214a167a276ec8dc344ff450ae5c0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420063"
---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Tutorial: Integração do Azure Active Directory ao SumoLogic

Neste tutorial, você aprenderá a integrar o SumoLogic ao Azure AD (Azure Active Directory).

A integração do SumoLogic ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao SumoLogic
- Você pode permitir que seus usuários façam logon automaticamente no SumoLogic (logon único) com as contas do Azure AD deles
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SumoLogic, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do SumoLogic

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando SumoLogic pela galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-sumologic-from-the-gallery"></a>Adicionando SumoLogic pela galeria
Para configurar a integração do SumoLogic ao Azure AD, você precisará adicionar o SumoLogic pela galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SumoLogic pela galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **SumoLogic**.

    ![Criação de um usuário de teste do AD do Azure](./media/sumologic-tutorial/tutorial_sumologic_search.png)

1. No painel de resultados, selecione **SumoLogic** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/sumologic-tutorial/tutorial_sumologic_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o SumoLogic, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do SumoLogic é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SumoLogic.

No SumoLogic, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o SumoLogic, você precisará concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criação de um usuário de teste do SumoLogic](#creating-a-sumologic-test-user)** – para ter um equivalente de Brenda Fernandes no SumoLogic que esteja vinculado à representação do usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo SumoLogic.

**Para configurar o logon único do Azure AD com o SumoLogic, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **SumoLogic**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/sumologic-tutorial/tutorial_sumologic_samlbase.png)

1. Na seção **URLs e Domínio do SumoLogic**, execute as seguintes etapas:

    ![Configurar o logon único](./media/sumologic-tutorial/tutorial_sumologic_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenantname>.SumoLogic.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:
    | |
    |--|
    | `https://<tenantname>.us2.sumologic.com` |
    | `https://<tenantname>.sumologic.com` |
    | `https://<tenantname>.us4.sumologic.com` |
    | `https://<tenantname>.eu.sumologic.com` |
    | `https://<tenantname>.au.sumologic.com` |

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do Cliente do SumoLogic](https://www.sumologic.com/contact-us/) para obter esses valores. 
 
1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/sumologic-tutorial/tutorial_sumologic_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/sumologic-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do SumoLogic**, clique em **Configurar o SumoLogic** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção Referência Rápida.**

    ![Configurar o logon único](./media/sumologic-tutorial/tutorial_sumologic_configure.png) 

1. Em outra janela do navegador da Web, faça logon em seu site de empresa SumoLogic como um administrador.

1. Vá para **Gerenciar \> Segurança**.
   
    ![Gerenciar](./media/sumologic-tutorial/ic778556.png "Gerenciar")

1. Clique em **SAML**.
   
    ![Configurações de segurança global](./media/sumologic-tutorial/ic778557.png "Configurações de segurança global")

1. Na lista **Selecionar uma configuração ou criar uma nova**, selecione **Azure AD** e clique em **Configurar**.
   
    ![Configurar o SAML 2.0](./media/sumologic-tutorial/ic778558.png "Configurar o SAML 2.0")

1. No diálogo **Configurar SAML 2.0** , realize as seguintes etapas:
   
    ![Configurar o SAML 2.0](./media/sumologic-tutorial/ic778559.png "Configurar o SAML 2.0")
   
    a. Na caixa de texto **Nome da Configuração**, digite **Azure AD**. 

    b. Selecione **Modo de Depuração**.

    c. Na caixa de texto **Emissor**, cole o valor da **ID de Entidade do SAML** que você copiou do Portal do Azure. 

    d. Na caixa de texto **URL de Solicitação de Autenticação**, cole o valor da **URL do Serviço de Logon Único SAML** copiado do portal do Azure.

    e. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole todo o Certificado na caixa de texto **Certificado X.509** .

    f. Para **Atributo de Email**, selecione **Usar entidade do SAML**.  

    g. Selecione **Configuração de Logon iniciada pelo SP**.

    h. Na caixa de texto **Caminho de Logon**, digite **Azure** e clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/sumologic-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/sumologic-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/sumologic-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/sumologic-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-sumologic-test-user"></a>Criar um usuário de teste do SumoLogic

Para permitir que os usuários do AD do Azure façam logon no SumoLogic, eles deverão ser provisionados no SumoLogic.  

* No caso do SumoLogic, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **SumoLogic** .

1. Vá para **Gerenciar \> Usuários**.
   
    ![Usuários](./media/sumologic-tutorial/ic778561.png "Usuários")

1. Clique em **Adicionar**.
   
    ![Usuários](./media/sumologic-tutorial/ic778562.png "Usuários")

1. No diálogo **Novo Usuário** , realize as seguintes etapas:
   
    ![Novo Usuário](./media/sumologic-tutorial/ic778563.png "Novo Usuário") 
 
    a. Digite as informações relacionadas da conta do Azure AD que você deseja provisionar nas caixas de texto **Nome**, **Sobrenome** e **Email**.
  
    b. Selecione uma função.
  
    c. Para **Status**, selecione **Ativo**.
  
    d. Clique em **Salvar**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do SumoLogic ou as APIs fornecidas pelo SumoLogic para provisionar as contas de usuário do AAD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao SumoLogic.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao SumoLogic, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **SumoLogic**.

    ![Configurar o logon único](./media/sumologic-tutorial/tutorial_sumologic_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SumoLogic no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo SumoLogic.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sumologic-tutorial/tutorial_general_01.png
[2]: ./media/sumologic-tutorial/tutorial_general_02.png
[3]: ./media/sumologic-tutorial/tutorial_general_03.png
[4]: ./media/sumologic-tutorial/tutorial_general_04.png

[100]: ./media/sumologic-tutorial/tutorial_general_100.png

[200]: ./media/sumologic-tutorial/tutorial_general_200.png
[201]: ./media/sumologic-tutorial/tutorial_general_201.png
[202]: ./media/sumologic-tutorial/tutorial_general_202.png
[203]: ./media/sumologic-tutorial/tutorial_general_203.png

