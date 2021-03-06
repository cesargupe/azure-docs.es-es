---
title: 'Tutorial: Integración de Azure Active Directory con Direct | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Direct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 7c2cd1f0-d14c-42f0-94a8-9b800008b285
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: jeedes
ms.openlocfilehash: 7e693a721e5556970607fafd8ff187d3b06c913e
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092893"
---
# <a name="tutorial-azure-active-directory-integration-with-direct"></a>Tutorial: Integración de Azure Active Directory con Direct

En este tutorial aprenderá a integrar Direct con Azure Active Directory (Azure AD).

La integración de Direct con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Direct.
- Puede permitir que los usuarios inicien sesión automáticamente en Direct (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Direct, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Direct

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba.
El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Direct desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-direct-from-the-gallery"></a>Adición de Direct desde la galería

Para configurar la integración de Direct en Azure AD, tiene que agregar Direct desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Direct desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![APLICACIONES][2]

3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![APLICACIONES][3]

4. En el cuadro de búsqueda, escriba **Direct**. En el panel de resultados, seleccione **Direct** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/direct-tutorial/tutorial_direct_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD

En esta sección va a configurar y probar el inicio de sesión único de Azure AD con Direct con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD ha de saber cuál es el usuario homólogo de Direct para un usuario de Azure AD. Es decir, hay que establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de Direct.

Para establecer la relación de vínculo, en Direct, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Direct, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Direct](#creating-a-direct-test-user)**: para tener un homólogo de Britta Simon en Direct que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Direct.

**Para configurar el inicio de sesión único de Azure AD con Direct, realice los pasos siguientes:**

1. En la página de integración de la aplicación **Direct** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/direct-tutorial/tutorial_direct_samlbase.png)

3. En la sección **Dominio y direcciones URL de Direct**, si quiere configurar la aplicación en modo iniciado por **IDP**:

    ![Configurar inicio de sesión único](./media/direct-tutorial/tutorial_direct_url.png)

    En el cuadro de texto **Identificador**, escriba la dirección URL: `https://direct4b.com/`

4. Active **Mostrar configuración avanzada de URL**, si quiere configurar la aplicación en modo iniciado por **SP**.

    ![Configurar inicio de sesión único](./media/direct-tutorial/tutorial_direct_url1.png)

     En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://direct4b.com/sso` 

5. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/direct-tutorial/tutorial_direct_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/direct-tutorial/tutorial_general_400.png)

7. Para configurar el inicio de sesión único en **Direct**, necesita enviar el archivo **XML de metadatos** descargado al [equipo de soporte técnico de Direct](https://direct4b.com/ja/support.html#inquiry).

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/direct-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/direct-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.

    ![Creación de un usuario de prueba de Azure AD](./media/direct-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:

    ![Creación de un usuario de prueba de Azure AD](./media/direct-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).

### <a name="creating-a-direct-test-user"></a>Creación de un usuario de prueba de Direct

En esta sección creará un usuario llamado Britta Simon en Direct. Colabore con el [equipo de soporte técnico de Direct](https://direct4b.com/ja/support.html#inquiry) para agregar los usuarios a la plataforma de Direct. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Direct.

![Asignar usuario][200] 

**Para asignar Britta Simon a Direct, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Direct**.

    ![Configurar inicio de sesión único](./media/direct-tutorial/tutorial_direct_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

1. Si desea realizar pruebas en **modo a iniciado por IDP**:

    Al hacer clic en el icono de **Direct** en el panel de acceso, debería iniciar sesión automáticamente en su aplicación **Direct**.

2. Si desea realizar pruebas en **modo iniciado por SP**:

    a. Haga clic en el icono **Direct** en el panel de acceso y se le redirigirá a la página de inicio de sesión de la aplicación.

    b. Escriba su `subdomain` en el cuadro de texto que se muestra y presione "次へ (Siguiente)". Iniciará sesión automáticamente en su aplicación **Direct**.

Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/direct-tutorial/tutorial_general_01.png
[2]: ./media/direct-tutorial/tutorial_general_02.png
[3]: ./media/direct-tutorial/tutorial_general_03.png
[4]: ./media/direct-tutorial/tutorial_general_04.png

[100]: ./media/direct-tutorial/tutorial_general_100.png

[200]: ./media/direct-tutorial/tutorial_general_200.png
[201]: ./media/direct-tutorial/tutorial_general_201.png
[202]: ./media/direct-tutorial/tutorial_general_202.png
[203]: ./media/direct-tutorial/tutorial_general_203.png