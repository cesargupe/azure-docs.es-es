---
title: Asignación de roles de directorio de Azure AD en PIM | Microsoft Docs
description: Aprenda a asignar los roles de directorio de Azure AD en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 10/30/2018
ms.author: rolyon
ms.openlocfilehash: 5f0b5d1695603a7cd2a3c7ac1dbc484e44257d88
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249618"
---
# <a name="assign-azure-ad-directory-roles-in-pim"></a>Asignación de roles de directorio de Azure AD en PIM

Con Azure Active Directory (Azure AD), un administrador global puede realizar asignaciones de roles de directorio **permanentes**. Estas asignaciones de roles se pueden crear mediante [Azure Portal](../users-groups-roles/directory-assign-admin-roles.md) o mediante [comandos de PowerShell](/powershell/module/azuread#directory_roles).

El servicio Azure AD Privileged Identity Management (PIM) permite también a los administradores de roles con privilegios realizar asignaciones de roles de directorio permanentes. Además, los administradores de rol con privilegios pueden hacer que los usuarios sean **aptos** para roles de directorio. Un administrador apto puede activar el rol cuando lo necesite y, cuando termina, sus permisos caducan. Para información acerca de los roles que puede administrar mediante PIM, consulte [Roles de directorio que pueden administrar con Azure AD PIM](pim-roles.md).

## <a name="make-a-user-eligible-for-a-role"></a>Hacer que un usuario sea apto para un rol

Siga estos pasos para hacer que un usuario sea apto para un rol de directorio de Azure AD.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con un usuario que sea miembro del [con Administrador de rol con privilegios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

    Para obtener información acerca de cómo conceder otro acceso de administrador para administrar PIM, consulte [Concesión de acceso para administrar Azure AD Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Abra **Azure AD Privileged Identity Management**.

    Si aún no ha iniciado PIM en Azure Portal, vaya a [Primer uso de PIM](pim-getting-started.md).

1. Haga clic en **Roles de directorio de Azure AD**.

1. Haga clic en **Roles** o en **Miembros**.

    ![Roles de directorio de Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Haga clic en **Agregar miembro** para abrir Agregar miembros administrados.

1. Haga clic en **Seleccionar un rol**, haga clic en un rol que desee administrar y luego haga clic en **Seleccionar**.

    ![Seleccione un rol.](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Haga clic en **Seleccionar miembros**, seleccione los usuarios que desea asignar al rol y, a continuación, haga clic en **Seleccionar**.

    ![Seleccione un rol.](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. En Agregar miembros administrados, haga clic en **Aceptar** para agregar el usuario al rol.

1. En la lista de roles, haga clic en el que acaba de asignar para ver la lista de miembros.

     Cuando el rol esté asignado, el usuario que ha seleccionado aparecerá en la lista de miembros como **Elegible** para el rol.

    ![Usuario apto para un rol](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Ahora que el usuario es apto para el rol, hágale saber que puede activarlo de acuerdo con las instrucciones que se describen en [Activación y desactivación de roles en Privileged Identity Management de Azure AD](pim-how-to-activate-role.md).

    A los administradores aptos se les pide que se registren en Azure Multi-factor Authentication (MFA) durante la activación. Si un usuario no puede registrarse en MFA o usa una cuenta de Microsoft (normalmente @outlook.com), deberá establecerlo como permanente en todos sus roles.

## <a name="make-a-role-assignment-permanent"></a>Hacer que una asignación de roles sea permanente

De forma predeterminada, los usuarios nuevos solo son aptos para un rol de directorio. Siga estos pasos si desea hacer que una asignación de roles sea permanente.

1. Abra **Azure AD Privileged Identity Management**.

1. Haga clic en **Roles de directorio de Azure AD**.

1. Haga clic en **Miembros**.

    ![Lista de miembros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Haga clic en un rol **elegible** que desee convertir en permanente.

1. Haga clic en **Más** y luego en **Establecer como permanente**.

    ![Hacer que una asignación de roles sea permanente](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    El rol aparece ahora como **permanente**.

    ![Lista de miembros con el cambio permanente](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Eliminación de un usuario de un rol

Puede quitar a los usuarios de las asignaciones de roles, pero asegúrese de que siempre haya al menos un usuario que sea un administrador global permanente. Si no está seguro de si los usuarios necesitan aún sus asignaciones de roles, puede [iniciar una revisión de acceso del rol](pim-how-to-start-security-review.md).

Siga estos pasos para quitar a un usuario específico de un rol diferente.

1. Abra **Azure AD Privileged Identity Management**.

1. Haga clic en **Roles de directorio de Azure AD**.

1. Haga clic en **Miembros**.

    ![Lista de miembros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Haga clic en una asignación de roles que desee quitar.

1. Haga clic en **Más** y después en **Quitar**.

    ![Quitar un rol](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. En el mensaje que le pide confirmación, haga clic en **Sí**.

    ![Quitar un rol](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    La asignación de rol se quita.

## <a name="authorization-error-when-assigning-roles"></a>Error de autorización al asignar roles

Si recientemente habilitó PIM para una suscripción y obtiene un error de autorización cuando intenta que un usuario pueda optar a un rol de directorio, es posible que la entidad de servicio MS-PIM aún no tenga los permisos adecuados. La entidad de servicio MS PIM debe tener el rol [Administrador de acceso de usuario](../../role-based-access-control/built-in-roles.md#user-access-administrator) para asignar roles a otros usuarios. En lugar de esperar hasta que se asigne a MS-PIM el rol de administrador de acceso de usuario, puede asignarlo manualmente.

Siga estos pasos para asignar el rol de administrador de acceso de usuario a la entidad de servicio de MS-PIM para una suscripción.

1. Inicie sesión en Azure Portal como administrador global.

1. Elija **Todos los servicios** y, después, **Suscripciones**.

1. Elija su suscripción.

1. Elija **Control de acceso (IAM)** para ver la lista actual de las asignaciones de roles en el ámbito de la suscripción.

   ![Hoja Control de acceso (IAM) para una suscripción](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Compruebe si la entidad de servicio **MS PIM** tiene asignado el rol **Administrador de acceso de usuario**.

1. Si no es así, elija **Agregar** para abrir el panel **Agregar permisos**.

1. En la lista desplegable **Rol**, seleccione el rol **Administrador de acceso de usuario**.

1. En la lista **Seleccionar**, busque y seleccione la entidad de servicio **MS PIM**.

   ![Adición de permisos para MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Elija **Guardar** para asignar el rol.

   Después de unos momentos, se asignará a la entidad de servicio MS-PIM el rol Administrador de acceso de usuario en el ámbito de la suscripción.

   ![Rol Administrador de acceso de usuario para MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)


## <a name="next-steps"></a>Pasos siguientes

- [Configuración de roles de directorio de Azure AD en PIM](pim-how-to-change-default-settings.md)
- [Asignación de roles de recursos de Azure en PIM](pim-resource-roles-assign-roles.md)
