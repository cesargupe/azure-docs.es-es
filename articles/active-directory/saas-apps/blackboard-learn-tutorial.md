---
title: 'Tutorial: Integración de Azure Active Directory con Blackboard Learn | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Blackboard Learn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 0b8ca505-61ea-487c-9a3e-fa50c936df0c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: jeedes
ms.openlocfilehash: 9b7e7a84059f8393e8f900733602e32ca3ad833b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423664"
---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn"></a>Tutorial: Integración de Azure Active Directory con Blackboard Learn

En este tutorial, obtendrá información sobre cómo integrar Blackboard Learn con Azure Active Directory (Azure AD).

La integración de Blackboard Learn con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Blackboard Learn.
- Puede permitir que los usuarios inicien sesión automáticamente en Blackboard Learn (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Blackboard Learn, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único de Blackboard Learn

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Blackboard Learn desde la galería
1. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-blackboard-learn-from-the-gallery"></a>Adición de Blackboard Learn desde la galería
Para configurar la integración de Blackboard Learn en Azure AD, es preciso agregar Blackboard Learn desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Blackboard Learn desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

1. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![APLICACIONES][2]
    
1. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![APLICACIONES][3]

1. En el cuadro de búsqueda, escriba **Blackboard Learn**.

    ![Creación de un usuario de prueba de Azure AD](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_search.png)

1. En el panel de resultados, seleccione **Blackboard Learn** y, después, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Blackboard Learn con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Blackboard Learn para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Blackboard Learn.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como valor del **nombre de usuario** en Blackboard Learn.

Para configurar y probar el inicio de sesión único de Azure AD con Blackboard Learn, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
1. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
1. **[Creación de un usuario de prueba de Blackboard Learn](#creating-a-blackboard-learn-test-user)**: para tener un homólogo de Britta Simon en Blackboard Learn que esté vinculado a la representación del usuario en Azure AD.
1. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Blackboard Learn.

**Para configurar el inicio de sesión único de Azure AD con Blackboard Learn, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Blackboard Learn**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

1. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_samlbase.png)

1. En la sección **Dominio y direcciones URL de Blackboard Learn**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.blackboard.com/`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.blackboard.com/auth-saml/saml/SSO/entity-id/SAML_AD`
    
    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con [el equipo de soporte al cliente de Blackboard Learn](https://www.blackboard.com/support/index.aspx) para obtener estos valores. 

1. La aplicación Blackboard Learn espera que las aserciones SAML estén en un formato concreto. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección **Atributos de usuario** de la página de integración de aplicaciones.
 La siguiente captura de pantalla muestra un ejemplo al respecto.

    ![Configurar inicio de sesión único](./media/blackboard-learn-tutorial/tutorial_attribute.png)

1. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo token de SAML como muestra la imagen y siga estos pasos. Aquí asignamos el atributo Userprincipalname como el atributo de usuario único, pero puede asignarlo al valor adecuado que distinga de manera única el usuario de la organización y que se asigne al campo de nombre de usuario de Blackboard Learn.
           
    | Nombre del atributo | Valor de atributo |   
    | ---------------| ----------------|
    | urn:oid:1.3.6.1.4.1.5923.1.1.1.6 |user.userprincipalname |

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/blackboard-learn-tutorial/tutorial_attribute_04.png)
    
    ![Configurar inicio de sesión único](./media/blackboard-learn-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**.

1. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo XML en el equipo.

    ![Configurar inicio de sesión único](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_certificate.png)

1. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/blackboard-learn-tutorial/tutorial_general_400.png)

1. En la sección **Configuración de Blackboard Learn**, haga clic en **Configurar Blackboard Learn** para abrir la ventana **Configurar inicio de sesión**. Copie el valor de **Identificador de entidad de SAML** de la **sección Referencia rápida**

    ![Configurar inicio de sesión único](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_configure.png) 

1. Para configurar el inicio de sesión único en **Blackboard Learn**, debe enviar el **XML de metadatos** descargado y el **identificador de entidad de SAML** al [soporte técnico de Blackboard Learn](https://www.blackboard.com/support/index.aspx).

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/blackboard-learn-tutorial/create_aaduser_01.png) 

1. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/blackboard-learn-tutorial/create_aaduser_02.png) 

1. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/blackboard-learn-tutorial/create_aaduser_03.png) 

1. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/blackboard-learn-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="creating-a-blackboard-learn-test-user"></a>Creación de un usuario de prueba de Blackboard Learn
En esta sección, creará un usuario llamado Britta Simon en Blackboard Learn. 

Compatibilidad de aplicaciones de Blackboard Learn para el aprovisionamiento de usuarios justo a tiempo. Asegúrese de que ha configurado las notificaciones, tal y como se describe en la sección **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)**
### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Blackboard Learn.

![Asignar usuario][200] 

**Para asignar Britta Simon a Blackboard Learn, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

1. En la lista de aplicaciones, seleccione **Blackboard Learn**.

    ![Configurar inicio de sesión único](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_app.png) 

1. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

1. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

1. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

1. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Blackboard Learn en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Blackboard Learn. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/blackboard-learn-tutorial/tutorial_general_01.png
[2]: ./media/blackboard-learn-tutorial/tutorial_general_02.png
[3]: ./media/blackboard-learn-tutorial/tutorial_general_03.png
[4]: ./media/blackboard-learn-tutorial/tutorial_general_04.png

[100]: ./media/blackboard-learn-tutorial/tutorial_general_100.png

[200]: ./media/blackboard-learn-tutorial/tutorial_general_200.png
[201]: ./media/blackboard-learn-tutorial/tutorial_general_201.png
[202]: ./media/blackboard-learn-tutorial/tutorial_general_202.png
[203]: ./media/blackboard-learn-tutorial/tutorial_general_203.png

