# JHOSSGuard Security v2 (Candidato de Parche)

> **Aviso importante:** Este parche es un **candidato realista** con diffs AOSP
(Java + SELinux). Necesita adaptaciones a tu rama exacta de Android y **no está
garantizado** que compile sin ajustes. Úsalo como base sólida para revisión técnica,
integración y pruebas en un entorno de desarrollo (AOSP + emulador/dispositivo de prueba).

## Qué hace
- Añade un guard extra en `ActivityManagerService` que bloquea **interacciones entre usuarios**
  (cross-user) desde apps que no tengan `INTERACT_ACROSS_USERS_FULL`, registrando un warning.
- Incluye reglas **SELinux (private)** para reforzar que apps `untrusted_app/priv_app` no puedan
  resolver servicios sensibles relacionados con usuarios sin privilegio explícito.

## Ramas / rutas objetivo
- Java: `frameworks/base/services/core/java/com/android/server/am/ActivityManagerService.java`
- SELinux private: `system/sepolicy/private/`

## Cómo aplicarlo (AOSP)
1. Copia el archivo `JHOSSGuard_Security_v2.patch` a la raíz de tu checkout AOSP.
2. Asegúrate de estar en una rama limpia: `repo forall -c 'git status'`.
3. Aplica el parche:
   ```bash
   git am JHOSSGuard_Security_v2.patch
   ```
   o
   ```bash
   git apply --3way JHOSSGuard_Security_v2.patch
   ```
4. Resuelve conflictos si aparecen (depende de tu branch).
5. Compila los targets afectados:
   ```bash
   source build/envsetup.sh
   lunch aosp_arm64-eng
   m services sepolicy -j$(nproc)
   ```
6. Flashea o instala el build en **dispositivo de pruebas** o emulador.

## Pruebas rápidas
- **Cross-user**: desde una app sin `INTERACT_ACROSS_USERS_FULL` intenta `startService()` con `EXTRA_USER_HANDLE`
  hacia otro usuario. Debe lanzar `SecurityException` y dejar log con tag `JHOSSGuard`.
- **SELinux**: revisa `dmesg`/`logcat` para verificar que no hay denials inesperados
  (los `neverallow` pueden fallar si no coinciden con tu árbol).

## Notas de integración
- Las firmas de métodos y utilidades (`checkPermission`, `EventLogTags`) pueden variar entre
  ramas AOSP. Ajusta imports y helpers si el compilador los pide.
- Las reglas de SELinux aquí son conservadoras y pueden necesitar afinarse con `audit2allow`
  y pruebas CTS/VTS.

## Autor
- JHOSSGuard — contacto: jimenezjhossmith46@gmail.com

