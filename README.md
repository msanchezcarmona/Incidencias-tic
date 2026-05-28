# Incidencias TIC (Cloud)

## Enlaces
- Repo: https://github.com/msanchezcarmona/Incidencias-tic.git
- GitHub Pages: https://msanchezcarmona.github.io/Incidencias-tic/

## Evidencias (capturas)
<!-- Puedes añadir una imagen (captura de pantalla) del siguiente modo (sustituye "alt text" por un texto alternativo
para que tu documento gane accesibilidad. El ejemplo supone que las imágenes las guardas en la carpeta "res"): ![alt text](res/image.png) -->
<!-- En Visual Studio Code puedes previsualizar un archivo markdown pulsando con botón derecho del ratón y seleccionando "Open preview"-->
1) Tabla `incidencias` (estructura):
   <img width="1557" height="749" alt="Image" src="https://github.com/user-attachments/assets/07c5434e-ca75-4363-8dda-87e6e241f744" />
   
2) RLS activado (se puede mirar en el editor de tablas de Supabase, pregunta a tu LLM favorito):
   <img width="730" height="329" alt="Image" src="https://github.com/user-attachments/assets/e747ac01-655e-45ed-8053-d186c67f480d" />
   
3) Policy (SELECT/INSERT/UPDATE) (se puede mirar donde mismo, pregunta a tu LLM favorito):
   <img width="1569" height="556" alt="Image" src="https://github.com/user-attachments/assets/5d246d2f-fd94-4022-9959-fa2d1e7fb8bf" />
   
4) App funcionando (crear y listar):  
5) App funcionando (cerrar incidencia):
   <img width="901" height="926" alt="Image" src="https://github.com/user-attachments/assets/2f9935fa-e11a-410c-a9a0-45abac902ceb" />

## CE.f — Procedimiento de almacenaje cloud
- Servicio cloud usado: Supabase (Postgres, Auth, RLS)
- Estructura de tabla:  La tabla incidencias almacena los datos técnicos. Cuenta con una clave primaria (id), la fecha de registro (created_at), el identificador del usuario (user_id vinculado a auth.users), y campos de texto/estado (aula, equipo, tipo, descripcion, estado)
- Autenticación: En Supabase Auth mediante registro e inicio de sesión por correo electrónico y contraseña. Al autenticarse, Supabase genera un token JWT (sesión) que el cliente envía en cada petición
- Permisos (RLS + policies):  RLS bloquea por defecto todos los accesos directos a la tabla. Se han creado políticas donde el acceso a SELECT, INSERT y UPDATE solo se permite si el user_id de la fila coincide con el auth.uid() del usuario autenticado en ese momento
- Conexión desde la app (URL + ANON KEY, supabase-js): Se usa el SDK @supabase/supabase-js. La aplicación se conecta usando la URL del proyecto y la ANON KEY pública (una clave segura para el frontend siempre que RLS esté bien configurado)


## CE.g — Importancia del cloud (beneficios)
- Productividad: Permite desplegar un backend completo y funcional en minutos sin necesidad de aprovisionar ni mantener servidores físicos, bases de datos o sistemas operativos
- Seguridad: Supabase gestiona la criptografía de las contraseñas de forma nativa, provee certificados SSL automáticos y permite aplicar reglas de seguridad (RLS) a nivel de la propia base de datos
- Coste: Se han usaado modelos gratuitos
- Escalabilidad y disponibilidad: Alta disponibilidad garantizada por la infraestructura en la nube, el sistema puede absorber picos de peticiones sin necesidad de reconfiguración manual por nuestra parte

## RA5 — Riesgos y medidas
### Riesgos (3)
1) Exposición de claves secretas: Accidentalmente subir la "Service Role Key" (que salta el RLS) al frontend o a GitHub
2) Filtración de datos por accesos no autorizados: Que un usuario malintencionado pueda leer incidencias de otros centros o usuarios manipulando el cliente
3) Inclusión de datos sensibles: Que los usuarios utilicen el campo de "descripción" para anotar datos personales

### Medidas (5)
1) Mínimo Privilegio (Frontend): Utilizar exclusivamente la ANON_KEY pública en el código cliente
2) Seguridad a nivel de fila (RLS): Asegurar que la base de datos restrinja las operaciones lógicas, no dependiendo de validaciones únicamente en el frontend
3) Sanitización de Salidas: Implementación de la función escapeHtml() en JS antes de inyectar datos en el DOM para evitar la ejecución de scripts maliciosos
4) Educación y directrices UI: Añadir avisos explícitos en el formulario para indicar a los usuarios que no introduzcan datos personales
5) Exclusión en el repositorio: Utilizar un archivo .gitignore si se usaran variables de entorno locales en el futuro para que ninguna clave sensible quede registrada en el historial público de Git
