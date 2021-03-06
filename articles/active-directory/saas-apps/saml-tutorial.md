---
title: 'Tutorial: Integración de Azure Active Directory con una aplicación de línea de negocio (LOB) habilitada para el token SAML 1.1 | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y una aplicación de línea de negocio (LOB) habilitada para el token SAML 1.1.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ced1d88d-0e48-40d5-9aea-ef991cd9d270
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: edabc09f820093d088ec0b8ed1222fb26c800bee
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426435"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Tutorial: Integración de Azure Active Directory con una aplicación de línea de negocio (LOB) habilitada para el token SAML 1.1

En este tutorial aprenderá a integrar una aplicación de línea de negocio (LOB) habilitada para el token SAML 1.1 con Azure Active Directory (Azure AD).

La integración de la aplicación LOB habilitada para el token SAML 1.1 con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a la aplicación LOB habilitada para el token SAML 1.1.
- Puede permitir que los usuarios inicien sesión automáticamente en la aplicación LOB habilitada para el token SAML 1.1 (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con la aplicación LOB habilitada para el token SAML 1.1, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único de la aplicación LOB habilitada para el token SAML 1.1

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de la aplicación LOB habilitada para el token SAML 1.1 desde la galería
1. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-saml-11-token-enabled-lob-app-from-the-gallery"></a>Adición de la aplicación LOB habilitada para el token SAML 1.1 desde la galería
Para configurar la integración de la aplicación LOB habilitada para el token SAML 1.1 en Azure AD, tiene que agregar la aplicación LOB habilitada para el token SAML 1.1 desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar la aplicación LOB habilitada para el token SAML 1.1 desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

1. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
1. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

1. En el cuadro de búsqueda, escriba **SAML 1.1 Token enabled LOB App** (Aplicación LOB habilitada para el token SAML 1.1), seleccione **SAML 1.1 Token enabled LOB App** (aplicación LOB habilitada para el token SAML 1.1) desde el panel de resultados y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Aplicación LOB habilitada para el token SAML 1.1 en la lista de resultados](./media/saml-tutorial/tutorial_saml_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con la aplicación LOB habilitada para el token SAML 1.1 con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, es preciso que Azure AD sepa cuál es el usuario homólogo de la aplicación LOB habilitada para el token SAML 1.1 para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de la aplicación LOB habilitada para el token SAML 1.1.

Para configurar y probar el inicio de sesión único de Azure AD con la aplicación LOB habilitada para el token SAML 1.1, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
1. **[Creación de un usuario de prueba de la aplicación LOB habilitada para el token SAML 1.1](#create-a-saml-11-token-enabled-lob-app-test-user)**: para tener un homólogo de Britta Simon en la aplicación LOB habilitada para el token SAML 1.1 vinculado a la representación del usuario de Azure AD.
1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación LOB habilitada para el token SAML 1.1.

**Para configurar el inicio de sesión único de Azure AD con la aplicación LOB habilitada para el token SAML 1.1, siga estos pasos:**

1. En la página de integración de la **aplicación LOB habilitada para el token SAML 1.1** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

1. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/saml-tutorial/tutorial_saml_samlbase.png)

1. En la sección **Dominio y direcciones URL de la aplicación LOB habilitada para el token SAML 1.1**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de la aplicación LOB habilitada para el token SAML 1.1](./media/saml-tutorial/tutorial_saml_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://your-app-url`

    b. En el cuadro de texto **Identificador (id. de entidad)**, escriba una dirección URL con el siguiente patrón: `https://your-app-url`
     
    > [!NOTE] 
    > Estos valores no son reales. Reemplace estos valores por direcciones URL específicas de la aplicación.  

1. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/saml-tutorial/tutorial_saml_certificate.png) 

1. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/saml-tutorial/tutorial_general_400.png)
    
1. En la sección **SAML 1.1 Token enabled LOB App Configuration** (Configuración de la aplicación LOB habilitada para el token SAML 1.1), haga clic en **Configure SAML 1.1 Token enabled LOB App** (Configurar la aplicación LOB habilitada para el token SAML 1.1) para abrir la ventana **Configurar inicio de sesión** . Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de la aplicación LOB habilitada para el token SAML 1.1](./media/saml-tutorial/tutorial_saml_configure.png) 

1. Para configurar el inicio de sesión único en **SAML 1.1 Token enabled LOB App** (Aplicación LOB habilitada para el token SAML 1.1), es preciso enviar los datos descargados de **certificado (Base64), dirección URL de inicio de sesión, identificador de identidad de SAML y dirección URL del servicio de inicio de sesión único de SAML** al equipo de soporte técnico de la aplicación. Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/saml-tutorial/create_aaduser_01.png)

1. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/saml-tutorial/create_aaduser_02.png)

1. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/saml-tutorial/create_aaduser_03.png)

1. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/saml-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-saml-11-token-enabled-lob-app-test-user"></a>Creación de un usuario de prueba de la aplicación LOB habilitada para el token SAML 1.1

En esta sección, creará el usuario Britta Simon en la aplicación LOB habilitada para el token SAML 1.1. Trabaje con el equipo de soporte técnico de la aplicación para crear el usuario en la aplicación. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, concederá acceso a Britta Simon a la aplicación LOB habilitada para el token SAML 1.1 para que use el inicio de sesión único de Azure.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a la aplicación LOB habilitada para el token SAML 1.1, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

1. En la lista de aplicaciones, seleccione **SAML 1.1 Token enabled LOB App** (Aplicación LOB habilitada para el token SAML 1.1).

    ![El vínculo a la aplicación LOB habilitada para el token SAML 1.1 en la lista Aplicaciones](./media/saml-tutorial/tutorial_saml_app.png)  

1. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

1. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

1. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

1. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de la aplicación LOB habilitada para el token SAML 1.1 en el panel de acceso, debería iniciar sesión automáticamente en la aplicación LOB habilitada para el token SAML 1.1.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/saml-tutorial/tutorial_general_01.png
[2]: ./media/saml-tutorial/tutorial_general_02.png
[3]: ./media/saml-tutorial/tutorial_general_03.png
[4]: ./media/saml-tutorial/tutorial_general_04.png

[100]: ./media/saml-tutorial/tutorial_general_100.png

[200]: ./media/saml-tutorial/tutorial_general_200.png
[201]: ./media/saml-tutorial/tutorial_general_201.png
[202]: ./media/saml-tutorial/tutorial_general_202.png
[203]: ./media/saml-tutorial/tutorial_general_203.png
