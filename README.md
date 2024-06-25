# Flitx::LogManager

Flitx::LogManager es una aplicación que desarrollé en mi tiempo libre para llevar un mejor registro de logs, errores, eventos y análisis de datos especializados para un Logger que luego subiré. Me ayudó muchísimo a encontrar bugs y errores, y espero que te sea útil también. Por eso publico este repositorio para que cualquiera que lo necesite pueda usarlo.

## Índice

1. [Características](#características)
2. [Requisitos del Sistema](#requisitos-del-sistema)
3. [Instalación](#instalación)
4. [Uso](#uso)
5. [Personalización](#personalización)
6. [Mejoras Futuras](#mejoras-futuras)
7. [Contribuciones](#contribuciones)
8. [Licencia](#licencia)
9. [Changelog](#changelog)
10. [Contacto](#contacto)

## Características

- **Gestión de Logs**: Visualización detallada de logs con filtros por tipo, fecha y prioridad.
- **Notificaciones**: Alertas personalizadas para diferentes eventos y estados de logs.
- **Desencriptación**: Soporte para desencriptar datos sensibles almacenados en los logs.
- **Colores Personalizados**: Configuración de colores personalizados para diferentes tipos de logs y estados.
- **Filtros Avanzados**: Filtros avanzados para visualizar logs por períodos de tiempo específicos (día, semana, mes).
- **Botón de Copia**: Funcionalidad para copiar información de logs con un solo clic.
- **Resumen de Logs**: Vista de resumen con conteo de logs por tipo.
- **Seguridad**: Encriptación de datos sensibles para mayor seguridad.

## Requisitos del Sistema

- PHP 7.4 o superior
- MySQL 5.7 o superior
- Servidor web (Apache, Nginx, etc.)

## Instalación

1. Clona el repositorio:
   ```bash
   git clone https://github.com/FranCh00/Flitx-LogManager.git
   ```

2. Navega al directorio del proyecto:
   ```bash
   cd Flitx-LogManager
   ```

3. Configura la base de datos en `config/config.php`. Aquí tienes un ejemplo de cómo debería lucir el archivo:
   ```php
   <?php
   return [
       'db_host' => 'localhost',
       'db_name' => 'club_canabico',
       'db_user' => 'root',
       'db_pass' => '',
       'db_charset' => 'utf8mb4',
       'logger_enabled' => true,
       'debug_show_context' => true,
       'debug_show_resolved' => true,
       'debug_show_resolved_duration' => true,
       'debug_columns_to_show' => ['id', 'code', 'type', 'message', 'file', 'status', 'created_at', 'resolved_at', 'resolved_duration'],
       'debug_columns_to_hide' => ['context'],
       'encryption_password' => 'tu_contraseña_secreta',
       'log_colors' => [
           'error' => '#e74c3c',
           'critical' => '#c0392b',
           'alert' => '#f39c12',
           'info' => '#3498db',
           'debug' => '#27ae60',
           'warning' => '#f1c40f',
           'notice' => '#9b59b6',
           'notification' => '#1abc9c',
           'event' => '#34495e',
           'audit' => '#8e44ad',
           'task' => '#16a085',
           'metric' => '#2ecc71',
           'trace' => '#95a5a6',
           'security' => '#2980b9',
           'session' => '#e67e22',
           'validation' => '#f39c12',
       ],
       'status_colors' => [
           'resolved' => '#27ae60',
           'in_progress' => '#3498db',
           'pending' => '#f39c12',
           'under_review' => '#8e44ad',
           'not_resolved' => '#e74c3c',
       ],
       'enable_copy_button' => false,
       'notifications' => [
           'log_resolved' => 'El log ha sido resuelto exitosamente.',
           'log_not_resolved' => 'El log ha sido marcado como no resuelto.',
           'many_not_resolved' => 'Hay muchos logs no resueltos. Por favor, revise.',
       ],
       'time_periods' => [
           'day' => '1 day',
           'week' => '1 week',
           'month' => '1 month',
       ],
   ];
   ```


4. Configura tu servidor web apuntando al directorio `public`.

## Uso

1. Accede a al directorio donde tienes el script, por ejemplo a  ```http://localhost/Flitx-LogManager/```.
2. Utiliza la navegación para ver y gestionar los logs.
3. Aplica filtros y utiliza las opciones de copiado para gestionar los datos de los logs.


## Personalización

LogManager permite una gran cantidad de personalizaciones a través del archivo `config/config.php`. Acá te explico cada una:

### Configuración de la Base de Datos
Define los parámetros de conexión a la base de datos:
```php
'db_host' => 'localhost',           // Dirección del host de la base de datos
'db_name' => 'LogManager_prod',       // Nombre de la base de datos
'db_user' => 'root',                // Usuario de la base de datos
'db_pass' => '',                    // Contraseña del usuario de la base de datos
'db_charset' => 'utf8mb4',          // Conjunto de caracteres a utilizar en la conexión
```
Scripts para crear la base de datos:
```sql
SET SQL_MODE = "NO_AUTO_VALUE_ON_ZERO";
START TRANSACTION;
SET time_zone = "+00:00";

CREATE TABLE `logs` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `code` varchar(10) NOT NULL,
  `type` varchar(20) NOT NULL,
  `message` text NOT NULL,
  `file` varchar(255) DEFAULT NULL,
  `function` varchar(255) DEFAULT NULL,
  `line` int(11) DEFAULT NULL,
  `context` longtext CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL CHECK (json_valid(`context`)),
  `created_at` timestamp NOT NULL DEFAULT current_timestamp(),
  `resolved_at` timestamp NULL DEFAULT NULL,
  `resolved_duration` int(11) DEFAULT NULL,
  `status` varchar(20) DEFAULT NULL,
  PRIMARY KEY (`id`),
  INDEX `idx_code` (`code`),
  INDEX `idx_type` (`type`),
  INDEX `idx_status` (`status`),
  INDEX `idx_created_at` (`created_at`),
  INDEX `idx_resolved_at` (`resolved_at`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

```
Datos de prueba si necesitan:
```sql
-- Volcado de datos de prueba para la tabla `logs`
--
INSERT INTO `logs` (`id`, `code`, `type`, `message`, `file`, `function`, `line`, `context`, `created_at`, `resolved_at`, `resolved_duration`, `status`) VALUES
(1, 'E001', 'ERROR', 'Argumento no válido: $average_times no es un array', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\inventory\\utils\\UserFetcher.php', 'fetchAverageForUserDash', 150, '{"provided_type": "string", "value": "1.0000", "msg_0cu1t0": "3n 31 p45o ..- .-.. - . .--. ---"}', '2024-06-24 16:40:16', '2024-06-24 20:08:38', 12502, 'resolved'),
(2, 'E002', 'ERROR', 'Argumento no válido: Datos de tiempos promedio no válidos: no es un array', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\inventory\\utils\\UserFetcher.php', 'fetchAverageForUserDash', 176, '{"expected_type": "array", "value": "[]", "msg_0cu1t0": "y l4 v1d4 ..- . ...- .- --.- ..- ."}', '2024-06-24 16:40:16', '2024-06-24 20:19:47', 13171, 'resolved'),
(3, 'C001', 'CRITICAL', 'Error crítico ocurrió en el sistema', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\core\\ErrorHandler.php', 'handleCriticalError', 50, '{"error_code": "500", "system_status": "down", "msg_0cu1t0": "bu5c4m0s 53n71d0 ..- -. -.-. .- -.. .-"}', '2024-06-24 17:00:00', '2024-06-24 22:02:08', NULL, 'resolved'),
(4, 'A001', 'ALERT', 'Alta carga del servidor detectada', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\utils\\ServerMonitor.php', 'checkServerLoad', 80, '{"cpu_usage": "95%", "memory_usage": "90%", "msg_0cu1t0": "3n c4d4 d14, ..- -. -.-. .- -.. .-"}', '2024-06-24 17:15:00', '2024-06-24 18:47:19', NULL, 'not_resolved'),
(5, 'I001', 'INFO', 'Usuario inició sesión con éxito', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\controllers\\UserController.php', 'loginUser', 100, '{"user_id": 123, "login_status": "successful", "msg_0cu1t0": "m1r4m05 h4c14 .- -.-. .- ...- -..."}', '2024-06-24 17:30:00', NULL, NULL, 'not_resolved'),
(6, 'D001', 'DEBUG', 'Información de depuración: valores de variables', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\utils\\Debugger.php', 'logDebugInfo', 120, '{"variable_name": "value", "debug_level": "high", "msg_0cu1t0": "c0n n0574lg14 ..-. .-. --. -..-"}', '2024-06-24 17:45:00', NULL, NULL, 'resolved'),
(7, 'W001', 'WARNING', 'Advertencia: Espacio en disco bajo', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\utils\\DiskMonitor.php', 'checkDiskSpace', 150, '{"disk": "/dev/sda1", "space_available": "10 GB", "msg_0cu1t0": "r3c0rd4nd0 .-. . -.-. --- .-. -.."}', '2024-06-24 18:00:00', '2024-06-24 18:09:02', NULL, 'resolved'),
(8, 'N001', 'NOTICE', 'Aviso: Uso de función obsoleta', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\utils\\DeprecatedFunctions.php', 'handleDeprecatedFunction', 180, '{"function_name": "oldFunction", "replacement": "newFunction", "msg_0cu1t0": "qu3 n0 v0lv3r4n ..--- -...- -.-."}', '2024-06-24 18:15:00', '2024-06-24 18:09:06', 11513, 'resolved'),
(9, 'N002', 'NOTIFICATION', 'Notificación: Nuevo registro de usuario', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\controllers\\UserController.php', 'registerNewUser', 200, '{"user_id": 456, "registration_status": "successful", "msg_0cu1t0": "3l r3l0j ..-. .. --.- .- -..- .-"}', '2024-06-24 18:30:00', '2024-06-24 18:09:05', 12062, 'resolved'),
(10, 'EVT001', 'EVENT', 'Evento: Usuario cerró sesión', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\controllers\\UserController.php', 'logoutUser', 220, '{"user_id": 123, "logout_status": "successful", "msg_0cu1t0": "s1gu3 5u .- -.-. --- ..- .-. ... ."}', '2024-06-24 18:45:00', NULL, NULL, 'resolved'),
(11, 'AU001', 'AUDIT', 'Registro de auditoría: Acceso a datos sensibles', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\utils\\AuditTrail.php', 'logAccess', 240, '{"user_id": 789, "action": "view_sensitive_data", "msg_0cu1t0": "1mpl4c4bl3 -. . .- --. . .-. ---"}', '2024-06-24 19:00:00', '2024-06-24 18:02:32', NULL, 'resolved'),
(12, 'T001', 'TASK', 'Tarea: Limpiar archivos temporales', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\utils\\FileCleanup.php', 'cleanupTempFiles', 260, '{"task": "cleanup", "status": "completed", "msg_0cu1t0": "m13ntr45 n057r05 .-.. --- -..- --- --."}', '2024-06-24 19:15:00', NULL, NULL, 'resolved'),
(13, 'M001', 'METRIC', 'Métrica: Número de usuarios activos', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\utils\\Metrics.php', 'trackActiveUsers', 280, '{"active_users": 100, "time_frame": "24h", "msg_0cu1t0": "s0ñ4m0s c0n ..-. ..-. --- - .-..-"}', '2024-06-24 19:30:00', '2024-06-24 18:02:31', 7003, 'resolved'),
(14, 'TR001', 'TRACE', 'Rastro: Traza de ejecución detallada', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\utils\\Tracer.php', 'traceExecution', 300, '{"trace_id": 12345, "execution_time": "200ms", "msg_0cu1t0": "un m4ñ4n4 ..- -. -- .- -- --- .-."}', '2024-06-24 19:45:00', NULL, NULL, 'resolved'),
(15, 'SEC001', 'SECURITY', 'Alerta de seguridad: Intento de acceso no autorizado', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\security\\SecurityMonitor.php', 'monitorSecurityEvents', 320, '{"ip_address": "192.168.1.100", "attempt_type": "brute_force", "msg_0cu1t0": "m45 l1g3r0 .-.. .. --. .-. ---"}', '2024-06-24 20:00:00', '2024-06-24 18:58:13', 5208, 'resolved'),
(16, 'S001', 'SESSION', 'Alerta de sesión: Sesión expirada', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\utils\\SessionManager.php', 'checkSessionExpiration', 340, '{"session_id": "abc123", "expiration_time": "30m", "msg_0cu1t0": "l45 h0r45 ... . ...- .- -.-. .- .-"}', '2024-06-24 20:15:00', '2024-06-24 19:04:41', 4295, 'resolved'),
(17, 'V001', 'VALIDATION', 'Error de validación: Datos de entrada no válidos', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\validators\\InputValidator.php', 'validateInput', 360, '{"input_errors": ["Field is required", "Invalid format"], "msg_0cu1t0": "5e 35c4p4n ..--- -...- -.-. .- -.."}', '2024-06-24 20:30:00', '2024-06-24 18:02:29', 3383, 'resolved');

-- Volcado de datos adicional para la tabla `logs`
--

INSERT INTO `logs` (`id`, `code`, `type`, `message`, `file`, `function`, `line`, `context`, `created_at`, `resolved_at`, `resolved_duration`, `status`) VALUES
(18, 'E003', 'ERROR', 'Error de conexión a la base de datos', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\database\\DBConnector.php', 'connect', 45, '{"connection_status": "failed", "db_type": "MySQL", "msg_0cu1t0": "3ntr3 r1545 ..-. .. -. --- ..- -"}', '2024-06-24 21:00:00', NULL, NULL, 'not_resolved'),
(19, 'W002', 'WARNING', 'Memoria del servidor al 90%', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\utils\\MemoryMonitor.php', 'checkMemoryUsage', 75, '{"memory_usage": "90%", "threshold": "85%", "msg_0cu1t0": "y ll4nt05 ..-. --- .-. . .-. ... ."}', '2024-06-24 21:15:00', NULL, NULL, 'under_review'),
(20, 'I002', 'INFO', 'Usuario cambió su contraseña', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\controllers\\UserController.php', 'changePassword', 110, '{"user_id": 789, "change_status": "successful", "msg_0cu1t0": "y 3n l4 .- .--. .-. .. ...- --- .-."}', '2024-06-24 21:30:00', NULL, NULL, 'pending'),
(21, 'A002', 'ALERT', 'Número elevado de intentos de inicio de sesión fallidos', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\security\\LoginMonitor.php', 'monitorLoginAttempts', 130, '{"attempts": 50, "threshold": 30, "msg_0cu1t0": "pr154 ..-. .. -. --- .-. ... ."}', '2024-06-24 21:45:00', NULL, NULL, 'in_progress'),
(22, 'N003', 'NOTICE', 'Actualización de software disponible', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\utils\\Updater.php', 'checkForUpdates', 150, '{"software_version": "1.2.3", "update_status": "available", "msg_0cu1t0": "0lvid4m05 ..-. .. ...- ..-. ---"}', '2024-06-24 22:00:00', NULL, NULL, 'resolved'),
(23, 'D002', 'DEBUG', 'Variable inesperada encontrada en el análisis', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\utils\\Analyzer.php', 'analyzeData', 170, '{"variable_name": "unexpected_value", "analysis_status": "pending", "msg_0cu1t0": "v1v1r 31 .-.. .. -. -.-. .- -. - ---"}', '2024-06-24 22:15:00', NULL, NULL, 'not_resolved'),
(24, 'C002', 'CRITICAL', 'Fallo crítico del sistema', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\core\\System.php', 'criticalFailure', 190, '{"system_status": "down", "error_code": "502", "msg_0cu1t0": "3nc4nt0 ..-. .. .-. .- -.-. - .-"}', '2024-06-24 22:30:00', '2024-06-24 23:00:00', 1800, 'resolved'),
(25, 'SEC002', 'SECURITY', 'Intento de acceso desde IP no autorizada', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\security\\IPMonitor.php', 'monitorIPAccess', 210, '{"ip_address": "203.0.113.1", "attempt_type": "unauthorized", "msg_0cu1t0": "4pr0v3ch4 .- .--. .-. --- ...- .-"}', '2024-06-24 22:45:00', NULL, NULL, 'under_review'),
(26, 'T002', 'TASK', 'Ejecución de tarea programada: Limpieza de caché', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\utils\\CacheCleaner.php', 'runScheduledTask', 230, '{"task": "cache_cleanup", "status": "completed", "msg_0cu1t0": "c4d4 1n574nt3 ..-. .. -.-. .- -.. - .-"}', '2024-06-24 23:00:00', '2024-06-24 23:15:00', 900, 'resolved'),
(27, 'M002', 'METRIC', 'Métrica: Tiempo de respuesta del servidor', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\utils\\Metrics.php', 'trackResponseTime', 250, '{"response_time_ms": 200, "status": "normal", "msg_0cu1t0": "n0 d315 .-. . . ...- -...- .- .-."}', '2024-06-24 23:15:00', NULL, NULL, 'in_progress'),
(28, 'N004', 'NOTICE', 'Mantenimiento programado a las 02:00 AM', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\utils\\Maintenance.php', 'scheduleMaintenance', 270, '{"maintenance_time": "02:00 AM", "status": "scheduled", "msg_0cu1t0": "n4d4 4l .-.. .- -- .- -.. --- .-."}', '2024-06-24 23:30:00', NULL, NULL, 'pending'),
(29, 'A003', 'ALERT', 'Se detectó un posible ataque DDoS', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\security\\DDoSMonitor.php', 'detectDDoSAttack', 290, '{"attack_type": "DDoS", "severity": "high", "msg_0cu1t0": "4z4r ..-. .. -.-. .- -.. .-."}', '2024-06-24 23:45:00', NULL, NULL, 'under_review'),
(30, 'I003', 'INFO', 'Se realizó una copia de seguridad completa', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\utils\\BackupManager.php', 'performBackup', 310, '{"backup_id": 456, "status": "successful", "msg_0cu1t0": "pue5 .--. .. -. -.-. .- -.. .-"}', '2024-06-25 00:00:00', NULL, NULL, 'resolved'),
(31, 'E004', 'ERROR', 'Tiempo de espera de la solicitud agotado', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\network\\RequestHandler.php', 'handleRequest', 330, '{"request_id": 789, "timeout": "30s", "msg_0cu1t0": "3n un 4br1r -. .- -.-. ..-. ---"}', '2024-06-25 00:15:00', NULL, NULL, 'not_resolved'),
(32, 'W003', 'WARNING', 'La temperatura del servidor es alta', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\utils\\TemperatureMonitor.php', 'checkTemperature', 350, '{"temperature": "85C", "status": "high", "msg_0cu1t0": "y cerr4r .--. .. .- -- ..-. .-. ---"}', '2024-06-25 00:30:00', NULL, NULL, 'in_progress'),
(33, 'N005', 'NOTICE', 'Se añadió una nueva funcionalidad', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\utils\\FeatureManager.php', 'addNewFeature', 370, '{"feature_name": "NewFeature", "status": "added", "msg_0cu1t0": "de .-.. .. ..-. .- -...- .. .-"}', '2024-06-25 00:45:00', NULL, NULL, 'pending'),
(34, 'C003', 'CRITICAL', 'Error crítico de seguridad detectado', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\security\\SecurityScanner.php', 'scanForThreats', 390, '{"threat_level": "high", "action_taken": "none", "msg_0cu1t0": "0j05 l4 ...- .. -.. .- -.-. .- -..-"}', '2024-06-25 01:00:00', '2024-06-25 01:30:00', 1800, 'resolved'),
(35, 'A004', 'ALERT', 'Actividad sospechosa en la cuenta de usuario', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\security\\AccountMonitor.php', 'monitorUserAccount', 410, '{"user_id": 123, "suspicious_activity": "true", "msg_0cu1t0": "v1d4 pue63 ..-. .. -.-. .- -.. -.-."}', '2024-06-25 01:15:00', NULL, NULL, 'under_review'),
(36, 'I004', 'INFO', 'Actualización de perfil de usuario exitosa', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\controllers\\UserController.php', 'updateUserProfile', 430, '{"user_id": 789, "update_status": "successful", "msg_0cu1t0": "c4mb14r -.-. .- -- -...- .. .-. .-. .-"}', '2024-06-25 01:30:00', NULL, NULL, 'resolved'),
(37, 'E005', 'ERROR', 'Fallo al cargar el módulo', 'D:\\xampp\\htdocs\\FranChoo\\Scripts\\core\\ModuleLoader.php', 'loadModule', 450, '{"module": "PaymentGateway", "error_code": "404", "msg_0cu1t0": "C4d4 .-.. . ...- -- .- -. -.-."}', '2024-06-25 01:45:00', NULL, NULL, 'not_resolved');

```
### Habilitar o Deshabilitar el Logger
Permite activar o desactivar el registro de logs:
```php
'logger_enabled' => true,           // true para habilitar el logger, false para deshabilitarlo
```

### Opciones de Visualización de Logs
Controla qué aspectos de los logs se muestran en la interfaz:
```php
'debug_show_context' => true,       // true para mostrar el contexto del log, false para ocultarlo
'debug_show_resolved' => true,      // true para mostrar logs resueltos, false para ocultarlos
'debug_show_resolved_duration' => true, // true para mostrar la duración de resolución de los logs, false para ocultarla
```

### Columnas a Mostrar y Ocultar en la Vista de Logs
Define qué columnas se deben mostrar u ocultar en la tabla de logs:
```php
'debug_columns_to_show' => ['id', 'code', 'type', 'message', 'file', 'status', 'created_at', 'resolved_at', 'resolved_duration'],
// Array de nombres de columnas que se deben mostrar

'debug_columns_to_hide' => ['context'],
// Array de nombres de columnas que se deben ocultar
```

### Contraseña para Desencriptar Datos Sensibles
Configura la contraseña para desencriptar datos sensibles almacenados en los logs:
```php
'encryption_password' => 'tu_contraseña_secreta',
// Contraseña utilizada para la desencriptación de datos sensibles
```

### Colores Personalizados para los Tipos de Logs y Estados
Define colores específicos para distintos tipos de logs y sus estados:
```php
'log_colors' => [
    'error' => '#e74c3c',            // Color para logs de tipo error
    'critical' => '#c0392b',         // Color para logs de tipo crítico
    'alert' => '#f39c12',            // Color para logs de tipo alerta
    'info' => '#3498db',             // Color para logs de tipo informativo
    'debug' => '#27ae60',            // Color para logs de tipo

 depuración
    'warning' => '#f1c40f',          // Color para logs de tipo advertencia
    'notice' => '#9b59b6',           // Color para logs de tipo aviso
    'notification' => '#1abc9c',     // Color para logs de tipo notificación
    'event' => '#34495e',            // Color para logs de tipo evento
    'audit' => '#8e44ad',            // Color para logs de tipo auditoría
    'task' => '#16a085',             // Color para logs de tipo tarea
    'metric' => '#2ecc71',           // Color para logs de tipo métrica
    'trace' => '#95a5a6',            // Color para logs de tipo traza
    'security' => '#2980b9',         // Color para logs de tipo seguridad
    'session' => '#e67e22',          // Color para logs de tipo sesión
    'validation' => '#f39c12',       // Color para logs de tipo validación
],
'status_colors' => [
    'resolved' => '#27ae60',         // Color para logs resueltos
    'in_progress' => '#3498db',      // Color para logs en progreso
    'pending' => '#f39c12',          // Color para logs pendientes
    'under_review' => '#8e44ad',     // Color para logs bajo revisión
    'not_resolved' => '#e74c3c',     // Color para logs no resueltos
],
```

### Habilitar Botón de Copia
Permite activar o desactivar el botón de copia en la interfaz de usuario:
```php
'enable_copy_button' => false,      // true para habilitar el botón de copia, false para deshabilitarlo
```

### Notificaciones Personalizadas
Define mensajes personalizados para diferentes eventos y estados de logs:
```php
'notifications' => [
    'log_resolved' => 'El log ha sido resuelto exitosamente.',       // Mensaje para logs resueltos
    'log_not_resolved' => 'El log ha sido marcado como no resuelto.',// Mensaje para logs no resueltos
    'many_not_resolved' => 'Hay muchos logs no resueltos. Por favor, revise.', // Mensaje para muchos logs no resueltos
],
```

### Períodos de Tiempo para la Visualización de Logs
Configura los periodos de tiempo para filtrar logs:
```php
'time_periods' => [
    'day' => '1 day',                 // Período de un día
    'week' => '1 week',               // Período de una semana
    'month' => '1 month',             // Período de un mes
],
```

## Estructura de Archivos

```plaintext
Flitx-LogManager\
├── config\
│   └── config.php               # Configuración principal del sistema
├── controllers\
│   └── DebugController.php      # Controlador para manejar la lógica de depuración de logs
├── db\
│   └── connection.php           # Configuración y gestión de la conexión a la base de datos
├── models\
│   └── Debug.php                # Modelo que interactúa con la base de datos para gestionar los logs
├── public\
│   ├── assets\
│   │   ├── css\
│   │   │   └── styles.css        # Estilos CSS para la interfaz de usuario
│   │   ├── img\                 # Directorio para imágenes utilizadas en la interfaz
│   │   └── js\
│   │       └── scripts.js       # Scripts JavaScript para la funcionalidad del frontend
│   ├── debug.php                # Vista principal para la depuración y gestión de logs
│   ├── index.php                # Página principal de la aplicación
│   └── layout.php               # Plantilla de diseño común para las vistas
├── utils\
│   └── Logger.php               # Utilidad para el manejo y registro de logs
└── views\
    └── debug_view.php           # Vista específica para la visualización detallada de logs
```

## Mejoras Futuras

- Implementar un panel de administración en `index.php` para configurar todas las opciones desde una interfaz gráfica.
- Añadir soporte para exportar logs a diferentes formatos (CSV, JSON, XML).
- Mejorar el sistema de notificaciones para incluir opciones de envío por correo electrónico y SMS.
- Integrar con servicios de terceros para análisis de logs (por ejemplo, ELK Stack).
- Crear una API REST para gestionar logs de manera programática.

## Contribuciones

Las contribuciones son bienvenidas. Por favor, sigue los siguientes pasos:

1. Haz un fork del repositorio.
2. Crea una nueva rama (`git checkout -b feature/nueva-caracteristica`).
3. Realiza tus cambios y haz commits (`git commit -am 'Añadir nueva característica'`).
4. Empuja tus cambios a la rama (`git push origin feature/nueva-caracteristica`).
5. Abre un Pull Request.

## Licencia

Este proyecto está bajo la licencia [MIT](LICENSE).

## Changelog

### [1.7.4] - 2024-06-24
- **Added**: Soporte para períodos de tiempo en la visualización de logs (`config.php`, `DebugController.php`, `Debug.php`).
- **Added**: Nuevos colores para los tipos de logs y estados (`config.php`).
- **Added**: Función `formatDuration` para mostrar duración de resolución en un formato legible (`debug_view.php`).
- **Fixed**: Correcciones en la lógica de desencriptación de campos en los logs (`Debug.php`).

### [1.7.0] - 2024-06-10
- **Added**: Opción para filtrar logs por período de tiempo (`config.php`, `DebugController.php`, `debug_view.php`).
- **Added**: Nueva notificación para múltiples logs no resueltos (`config.php`).
- **Changed**: Mejorada la documentación en comentarios del código (`Debug.php`, `Logger.php`).
- **Fixed**: Corrección de errores menores en la visualización de logs (`debug_view.php`).

### [1.6.0] - 2024-05-15
- **Added**: Implementación de botones de copia en la interfaz de usuario (`debug_view.php`).
- **Added**: Función para desencriptar datos de logs (`Debug.php`).
- **Changed**: Refactorización de la función `getLogs` para soportar desencriptación (`Debug.php`).
- **Changed**: Actualización del estilo de los badges para logs y estados (`layout.php`, `config.php`).
- **Fixed**: Corrección de errores en la actualización de estado de los logs (`DebugController.php`).

### [1.5.0] - 2024-03-27
- **Added**: Configuración de colores personalizados para tipos de logs y estados (`config.php`).
- **Added**: Configuración para mostrar u ocultar columnas específicas en la vista de logs (`config.php`).
- **Changed**: Mejoras en la interfaz de usuario para la vista de logs (`debug_view.php`).
- **Changed**: Actualización del método `updateLogStatus` para manejar nuevas condiciones de estado (`Debug.php`).
- **Fixed**: Corrección de bugs relacionados con la sanitización de entradas (`DebugController.php`).

### [1.4.0] - 2024-03-24
- **Added**: Soporte para múltiples tipos de notificaciones (`config.php`).
- **Added**: Función `getLogsCount` para obtener el conteo de logs por tipo (`Debug.php`).
- **Changed**: Refactorización de la lógica de filtrado y ordenamiento de logs (`Debug.php`).
- **Changed**: Mejoras en el manejo de excepciones y errores (`Debug.php`, `Logger.php`).
- **Fixed**: Corrección de errores en la vista de resumen de logs (`debug_view.php`).

### [1.3.0] - 2024-02-11
- **Added**: Configuración de habilitación del logger (`config.php`, `Logger.php`).
- **Added**: Función para obtener los tipos de logs (`Debug.php`).
- **Changed**: Actualización de `DebugController.php` para manejar nuevas configuraciones de filtro y orden (`DebugController.php`).
- **Fixed**: Corrección de bugs en la sanitización de datos en el controlador de depuración (`DebugController.php`).

### [1.2.0] - 2024-01-15
- **Added**: Implementación de métodos adicionales en el modelo `Debug` (`Debug.php`).
- **Added**: Mejoras en la lógica de conexión a la base de datos (`connection.php`).
- **Changed**: Refactorización del controlador de depuración para mejorar la legibilidad y mantenimiento (`DebugController.php`).
- **Fixed**: Corrección de errores menores en la interfaz de usuario (`debug_view.php`).

### [1.1.0] - 2023-12-12
- **Added**: Configuraciones iniciales para la aplicación de gestión de logs (`config.php`).
- **Added**: Implementación inicial del modelo `Debug` para manejo de datos de logs (`Debug.php`).
- **Changed**: Mejoras en la

 estructura y documentación del código (`DebugController.php`, `Logger.php`).
- **Fixed**: Corrección de errores iniciales en la implementación del logger (`Logger.php`).

### [1.0.1] - 2023-12-10
- **Added**: Manejo de excepciones personalizadas y mejorada la documentación (`Debug.php`, `Logger.php`, `config.php`).
- **Fixed**: Corrección de errores menores en la lógica de registros de logs (`Logger.php`).

### [1.0.0] - 2023-12-9
- **Added**: Versión inicial de la aplicación de gestión de logs.
- **Added**: Implementación básica de la configuración, controlador, modelo y vistas (`config.php`, `DebugController.php`, `Debug.php`, `debug_view.php`).

## Contacto

- Autor: [FranChoo](https://github.com/FranCh00/)

---
