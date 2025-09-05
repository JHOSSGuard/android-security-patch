Parche de seguridad JHOSSGuard para Android 13+

## Descripción general
Este parche presenta **JHOSSGuard**, un mecanismo de seguridad diseñado para mitigar los intentos de escalada de privilegios por parte de aplicaciones no confiables en Android 13+ (Tiramisu y posteriores).

## Características principales
- **Fortalecimiento de ActivityManagerService (AMS)**
  - Agrega el método `enforceNoPrivilegeEscalationLocked` para detectar y bloquear llamadas de servicio no autorizadas o entre usuarios sin el permiso `INTERACT_ACROSS_USERS_FULL`.
  - Integrado en `startService` (y se puede extender a otras llamadas sensibles como `broadcastIntentLocked`).

- **Refuerzo de políticas de SELinux**
  - Agrega nuevas reglas (`jhossguard.te`) en `system/sepolicy/private/`.
  - Bloquea las aplicaciones (`appdomain`) para que no resuelvan servicios críticos (`activity`, `package`, `usagestats`, `batterystats`, `procstats`).
  - Evita que aplicaciones no confiables agreguen nuevos servicios a `system_server`.

## Impacto
- Evita que aplicaciones tipo spyware o bloatware abusen de privilegios ocultos.
- Fortalece la separación usuario/sistema, reduciendo la superficie de ataque para la escalada de privilegios.
- Proporciona compatibilidad con Android 13+.

## Aplicación del parche
1. Clonar o sincronizar el árbol de origen de AOSP (Android 13+).
2. Copie el parche en la raíz de la fuente.
3. Ejecutar:
   ``golpe
   git apply --3way JHOSSGuard_Security_A13.patch
   ```
4. Reconstruir los módulos relevantes:
   ``golpe
   código fuente build/envsetup.sh
   almuerzo aosp_arm64-esp
   servicios m sepolicy -j$(nproc)
   ```

## Pruebas
- Implementar en un emulador o dispositivo de prueba que ejecute Android 13+.
- El intento de iniciar o transmitir servicios entre usuarios desde una aplicación sin privilegios debe bloquearse con `SecurityException`.
- Verificar las denegaciones de SELinux a través de `adb logcat` y `dmesg`.

## Notas
- Este parche está diseñado para **fines educativos y de investigación**.
- Requiere pruebas de integración antes de la implementación en producción.
- Se aceptan contribuciones a través de solicitudes de extracción de GitHub.

---
Autor: **JHOSSGuard**
