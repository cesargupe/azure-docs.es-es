---
title: Inicio de sesión de Azure MFA con la verificación en dos pasos | Microsoft Docs
description: Esta página proporciona instrucciones sobre dónde debe ir para ver los distintos métodos de inicio de sesión disponibles con Azure MFA.
keywords: autenticación de usuario, experiencia de inicio de sesión, inicio de sesión con el teléfono móvil, inicio de sesión con el teléfono del trabajo
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.component: user-help
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: e7886d3c8aa1ef9ee1e392030aabf0d187568e24
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343073"
---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>La experiencia de inicio de sesión con Azure Multi-Factor Authentication
> [!NOTE]
> El propósito de este artículo es recorrer una experiencia de inicio de sesión típica. Para obtener ayuda sobre el inicio de sesión o resolver problemas, consulte [Problemas con Azure Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>¿Cómo se presenta la experiencia de inicio de sesión?
La experiencia de inicio de sesión depende de qué elija como segundo factor: una llamada de teléfono, una aplicación de autenticación o mensajes de textos. Elija la opción que mejor describa lo que hace:

| ¿Cómo se puede iniciar sesión? |
| --- |
| [Con una llamada a mi móvil o teléfono de trabajo](#signing-in-with-a-phone-call) |
| [Con un mensaje de texto a mi móvil](#signing-in-with-a-text-message)
| [Con notificaciones de la aplicación Microsoft Authenticator](#signing-in-with-the-microsoft-authenticator-app-using-notification) |
| [Con códigos de verificación de la aplicación Microsoft Authenticator](#signing-in-with-the-microsoft-authenticator-app-using-verification-code) |
| [Con un método alternativo, ya que ahora no puedo usar mi método preferido](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Inicio de sesión con una llamada de teléfono
La siguiente información describe la experiencia de verificación de dos pasos con una llamada a un móvil o teléfono de trabajo.

1. Inicie sesión en una aplicación o servicio como Office 365 con su nombre de usuario y contraseña.  
2. Microsoft lo llamará.  
3. Responda al teléfono y pulse la tecla #.  

## <a name="signing-in-with-a-text-message"></a>Inicio de sesión con un mensaje de texto
La siguiente información describe la experiencia de verificación de dos pasos con mensaje de texto a un móvil:

1. Inicie sesión en una aplicación o servicio como Office 365 con su nombre de usuario y contraseña.
2. Microsoft envía un mensaje de texto que contiene un código numérico.
3. Escriba el código en el cuadro que aparece en la página de inicio de sesión.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Inicio de sesión con la aplicación Microsoft Authenticator
La siguiente información describe la experiencia de uso de la aplicación Microsoft Authenticator para las verificaciones en dos pasos. Hay dos maneras diferentes de utilizar la aplicación. Puede recibir notificaciones push en el dispositivo o puede abrir la aplicación para obtener un código de comprobación.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Para iniciar sesión con una notificación de la aplicación Microsoft Authenticator
1. Inicie sesión en una aplicación o servicio como Office 365 con su nombre de usuario y contraseña.
2. Microsoft envía una notificación a la aplicación Microsoft Authenticator de su dispositivo.

  ![Microsoft envía una notificación](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Abra la notificación en el teléfono y seleccione la clave de **verificación**. Si su empresa requiere un PIN, escríbalo aquí.
4. Con esto debe haber iniciado sesión.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Para iniciar sesión en la aplicación Microsoft Authenticator con un código de verificación

Si usa la aplicación Microsoft Authenticator para obtener códigos de comprobación, al abrir la aplicación, podrá ver un número debajo de su nombre de cuenta. Este número cambia cada 30 segundos para que no use el mismo número dos veces. Cuando se le solicite un código de verificación, abra la aplicación y use cualquier número que se muestre.

1. Inicie sesión en una aplicación o servicio como Office 365 con su nombre de usuario y contraseña.
2. Microsoft le pedirá un código de verificación.

  ![Escribir el código de verificación](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Abra la aplicación Microsoft Authenticator en el teléfono y escriba el código en el cuadro donde está iniciando sesión.

## <a name="signing-in-with-an-alternate-method"></a>Inicio de sesión con un método alternativo
En ocasiones, no tiene el teléfono o el dispositivo que configuró como método de verificación preferida. Esta situación es el motivo por el que se recomienda configurar métodos de copia de seguridad para la cuenta. En la siguiente sección se mostrará cómo iniciar sesión con un método alternativo cuando el método principal no esté disponible.

1. Inicie sesión en una aplicación o servicio como Office 365 con su nombre de usuario y contraseña.
2. Seleccione **Usar otra opción de comprobación**. Verá distintas opciones de comprobación en función del número de ellas que haya configurado.
3. Elija un método alternativo e inicie sesión.

  ![Usar método alternativo](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Pasos siguientes

Si tiene problemas para iniciar sesión con la verificación en dos pasos, obtenga más información en [Problemas con Azure Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

Aprenda a [administrar la configuración de la verificación en dos pasos](multi-factor-authentication-end-user-manage-settings.md).

Descubra cómo [empezar a trabajar con la aplicación Microsoft Authenticator](microsoft-authenticator-app-how-to.md) para que pueda utilizar las notificaciones para iniciar sesión, en lugar de textos y llamadas de teléfono.
