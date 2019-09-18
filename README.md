# Datapower
Autenticación con LDAP
Datapower tiene funciones nativas para comunicarse con el LDAP. Esta función está en las extensiones de datapower, para importar estas extensiones se debe cargar el siguiente namespace:

xmlns:dp="http://www.datapower.com/extensions"

Para consultas y autenticación se necesita autenticar con "distinguishedName" completo en lugar de solo el username, por lo tanto lo primero que se debe hacer es consultar el "distinguishedName" del usuario que intenta autenticar, para esto debe tener un usuario con privilegios de búsqueda configurado en el LDAP y conocer su "distinguishedName".

la función para consultar datos de un usuario es la siguiente:

    <xsl:variable name="ldap-authen-search" 
        select="dp:ldap-search(
                  IP,
                  PORT,
                  USER,
                  PASSWORD,
                  TARGET_DN,
                  PARAMS,
                  SEARCH_FILTER,
                  SCOPE,
                  SSL_PROXY_PROFILE,
                  LDAP_LB_GROUP,
                  VERSION
                )"
      />

Detalle de cada parametros:

1. IP: URL o IP del servidor LDAP.
2. PORT: Puerto del servidor LDAP, por defecto 389.
3. USER: "distinguishedName" con privilegios en el LDAP de búsqueda, si va vacío significa que es un acceso anónimo.
4. PASSWORD: Contraseña del Usuario con privilegios en el LDAP.
5. TARGET_DN: Es la base del "distinguishedName" para la búsqueda, Se consultan todos los subárboles que están enlazados en esta base.
6. PARAMS: parámetros separados por comas, para traer cada parámetro que quiere consultar en el LDAP del usuario buscado.
7. SEARCH_FILTER: Es una expresión de filtro LDAP tal como se define en RFC 2254.La búsqueda devuelve los valores de los atributos para cada expresión en el subárbol que satisfaga la expresión de filtro. Esto depende de como este configurado el LDAP pero un ejemplo donde los usuarios tienen una propiedad objectClass y sea de tipo person el filtro seria como este: "(&amp;(objectClass=person)(sAMAccountName=#user))", donde #user se debe reemplazar por el username del usuario buscado.
8. SCOPE: Especifica la profundidad de la búsqueda en el LDAP. sus opciones son= 1.base: Coincide solo con la entrada de función 2.one: Coincide con la entrada de función y cualquier objeto de un nivel inferior y 3.sub: Coincide con la entrada de función y cualquier descendiente
9. SSL_PROXY_PROFILE: Identifica un Perfil Proxy SSL existente. Si se proporciona un Perfil Proxy SSL, el perfil SSL (reenvío) del cliente incluido en ese perfil proxy se usa para establecer una conexión segura con el servidor LDAP. En ausencia de un perfil de proxy SSL especificado, la función proporciona un valor predeterminado de una cadena vacía.
10. LDAP_LB_GROUP: requerido en ausencia de una dirección explícita, y de lo contrario opcional, especifica el nombre de un grupo de equilibrador de carga LDAP.
Si se especifica un equilibrador de carga LDAP, use una cadena vacía para los parámetros de dirección y puerto.
Si no se especifica un equilibrador de carga LDAP, la función proporciona un valor predeterminado de una cadena vacía.
11. VERSION: Especifica la versión LDAP. El valor predeterminado es v2. Se puede usar v2 o v3.

Si en las variables de contexto la variable var://context/INPUT/_extension/errorid tiene el codigo de error '0x80e005b1' después de lanzar la función dp:ldap-search, es porque no es posible conectarse al LDAP.

Si la variable ldap-authen-search queda vacia es por que el usuario no fue encontrado.

Si la variable var://context/INPUT/_extension/error contiene "data XXX" donde XXX es el código de error enviado por el LDAP, por ejemplo 'data 52e' es porque la contraseña del usuario principal está mal o 'data 775' es porque el usuario está bloqueado.

Si no se presenta ninguno de los anteriores casos, se debe buscar en la variable ldap-authen-search el atributo "distinguishedName", por ejemplo $ldap-authen-search/LDAP-search-results/result/attribute-value[@name='distinguishedName']. Con este parámetro ya se puede intentar la autenticacion

          <xsl:variable name="ldap-authen-result" 
              select="dp:ldap-authen(
                        distinguishedName, 
                        PASSWORD,
                        concat(IP,':',PORT), 
                        SSL_PROXY_PROFILE,
                        LDAP_LB_GROUP,
                        VERSION,
                        TIMEOUT
                      )"
            />

Detalle de cada parametros:

1. distinguishedName: URL o IP del servidor LDAP.
2. PASSWORD: contraseña de usuario que se quiere autenticar.
3. TIMEOUT: Timeout de conexión para el intento de autenticación.

los demás parámetros son los mismos que para la consulta.

Si en las variables de contexto la variable var://context/INPUT/_extension/errorid tiene el codigo de error '0x80e005b1' después de lanzar la función dp:ldap-authen, es porque no es posible conectarse al LDAP.

Si la variable ldap-authen-result queda vacia es porque la autenticación fallo.

Si la variable var://context/INPUT/_extension/error contiene "data XXX" donde XXX es el código de error enviado por el LDAP, por ejemplo 'data 52e' es porque la contraseña del usuario principal está mal o 'data 775' es porque el usuario está bloqueado.

Si la variable ldap-authen-result no esta vacía se debe comparar con el distinguishedName, si son iguales es porque la autenticación fue exitosa de lo contrario falló la autenticación.
