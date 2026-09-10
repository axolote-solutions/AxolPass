# 1. Requerimientos Funcionales (FR) - Autenticación y Autorización

## 1.1. Gestión de Identidad (Autenticación)**

* **Creación de Credenciales Administrativas:** El `CommunityAdmin` no se puede autoregistrar; Axolote Solutions le asigna un usuario y él debe generar su propia contraseña, o bien, acceder mediante una invitación enviada a su correo (por ejemplo, usando autenticación de Google/Gmail).
* **Autenticación de Residentes:** El acceso a la aplicación móvil para los residentes estará basado en contraseña, con soporte para biometría (huella/FaceID del dispositivo).
* **Validación de Identidad:** El sistema debe obligar a los residentes a validar su correo electrónico o teléfono (OTP) antes de poder generar invitaciones, para incrementar la seguridad y trazabilidad.

## 1.2. Control de Roles y Permisos (Autorización)**

* **Roles Estáticos del Sistema:** El sistema operará bajo un modelo de Control de Acceso Basado en Roles (RBAC) con 5 roles estrictamente definidos: `SystemAdmin`, `CommunityAdmin`, `ResidentMain`, `ResidentSecondary` y `SecurityGuard`.
* **Restricción de Delegación:** En esta primera fase, los roles son fijos y no se permitirá la creación de subroles o la delegación temporal de funciones (ej. nombrar a un suplente del administrador).
* **Aislamiento por Rol:** Cada rol tendrá permisos explícitos; por ejemplo, un `ResidentSecondary` puede generar invitaciones, pero un `CommunityAdmin` no puede ver los nombres de los invitados de los residentes, y el `SecurityGuard` tiene capacidades de registro de eventos operativos.

## 1.3. Gestión y Ciclo de Vida de la Sesión**

* **Expiración de Sesión:** El sistema debe implementar tiempos de expiración de sesión (timeouts) automáticos. Esta funcionalidad es de criticidad alta especialmente para los administradores de fraccionamientos para evitar accesos no autorizados en equipos compartidos.

---

# 2. Requerimientos No Funcionales (NFR) - Seguridad Avanzada

## 2.1. Evolución de la Seguridad (Roadmap)**

* **Autenticación en Dos Pasos (2FA):** El sistema debe estar diseñado para soportar 2FA en el futuro, reconociendo que aportará mayor confianza a los residentes, aunque su implementación deberá cuidar la experiencia de usuario para personas no tan familiarizadas con la tecnología.

## 2.2. Seguridad de la Contraseña**

* **Almacenamiento Seguro:** (Implícito como mejor práctica de la industria) Las contraseñas nunca deben guardarse en texto plano; el sistema debe utilizar algoritmos de hashing fuertes (ej. bcrypt o Argon2).

---

**El impacto en la arquitectura:**
Al tener este contexto separado, cuando un residente intente crear una invitación, el flujo real será:

1. El **Contexto IAM** verifica que el token de sesión es válido y que el usuario tiene el rol `ResidentMain` o `ResidentSecondary`.
2. El **Contexto de Comunidad** verifica que su casa no esté suspendida y que no haya superado su límite de invitaciones.
3. El **Contexto de Accesos** finalmente genera el código QR/PIN.

