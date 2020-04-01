Cifrado RSA Javascript
Datapower tiene su propio motor de ECMAScript 5 lo cual nos permite correr código javascript como una transformación. Para hacer esto se debe agregar un GatewayScript a la regla como se muestra a continuación:

 ![Test Image ](https://github.com/SPLAYER-HD/Datapower/blob/master/CypherRSAJavascript/img/RulePipeline.png)

 Se debe configurar como cualquier xsl y cargar el archivo .js correspondiente:

 ![Test Image ](https://github.com/SPLAYER-HD/Datapower/blob/master/CypherRSAJavascript/img/ConfigGatewayScript.png)


Para cifrar en HTML debe usar el código en el folder  "rsa" en la ruta  "/test/ test_rsa.html", donde está el ejemplo de cómo cifrar usando llave pública para luego de-cifrar en Datapower con llave privada.

Para de-cifrar en JavaScript bajo el algoritmo RSA debe usar el archivo adjunto decryptDP.js, este archivo tiene todo el código de los archivos .js que se usan para cifrar en la versión HTML. Tiene algunas modificación que funcionan en un navegador pero no en el motor JavaScript de Datapower, adicionalmente se dejó todo en un solo archivo debido a que algunos archivos js no están codificados de manera que Datapower pueda importarlos entre sí con la función required(‘file’).

Al final de este archivo, desde la línea 2851 veremos la lógica de cifrar y de-cifrar, donde se usa la función session.INPUT.setVariable('passwordDecrypt',plaintext); que deja el texto de-cifrado en una variable de contexto, otra solución sería escribir OUTPUT para la siguiente transformación con el comando session.output.write(x); donde x es el contenido con el texto de-cifrado y lo que se requiera adicionalmente.

Para generar las llaves públicas y privadas use openssl con estos comandos:

openssl genrsa -out privateKey.txt 1024

openssl rsa -in privateKey.txt -out publicKey.txt –pubout