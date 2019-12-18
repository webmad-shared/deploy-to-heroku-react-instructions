# Deploy to Heroku: React instructions

The purpose of this document is to explain how to deploy the MERN application you have created for your third project to Heroku.

In order to deploy to Heroku your React app there will be some elements you will have to take into account:

1. The project root folder **must have** two folders inside
   1. `server`. This will contain your backend using Express, Mongoose models, etc.
   2. `client`. This will contain your React app.
2. You will have to create a **seed file** so you can tear down dirty data everytime you need and repopulate your DB with clean data.
3. A **your database running in MongoDB Atlas**.
4. A running **account in Heroku** for your app.
5. Environment files (and Heroku variables) both for your server and your React app.
6. The Heroku command line tools.
   
## Backend - local environment config
1. Instalar y configurar el módulo `dotenv` . Acordaos de requerir el módulo donde sea necesario.
2. En el fichero `.env`, añadir la URL de la base de datos local. También tenemos que añadir otrosdatos que no queremos que se suban al repositorio (credenciales, claves API...).
3. En app.js poner antes del module.exports cual es el index que tiene que enviar  `app.use((req, res) => {res.sendFile(__dirname + "/public/index.html");});`

## Frontend - local environment config
1. Confirmar que está instalado el `dotenv-cli` en la carpeta client. De lo contrario instalarlo: `npm install dotenv-cli`.
2. Crear los dos ficheros de variables de entorno en la raíz de la carpeta client: `touch .env.dev .env.prod`
3. Poner las variables de entornos.  También tenemos que añadir otros datos que no queremos quese suban al repositorio y pertenecen al front (credenciales, claves API...)
    1. `.env.dev` -> `REACT_APP_URL=http://localhost:5000/api`
    2. `.env.prod` -> `REACT_APP_URL= https://name-of-your-app.Herokuapp.com/api`
4. Dentro del archivo `package.json` de la parte de client hay que configurar los scripts:
```
    "start": "dotenv -e .env.dev react-scripts start"
    "build-dev": "dotenv -e .env.dev react-scripts build"
    "build-prod": "dotenv -e .env.prod react-scripts build"
```

Hacer `npm run build-prod` en la carpeta de client y mover todo el CONTENIDO de la carpeta build dentro de public de server. You can use this alias to make things easier. `alias cb="rm -rf ./server/public; mkdir ./server/public;  cp  -r ./client/build/. ./server/public"` . You can add this alias to your ~/.zshrc file so you can use it everytime you launch a new terminal.

## Heroku configuration

### Without account
1. Crear cuenta.
2. Crear app. Región: Europa 
3. Descargar e instalar Heroku CLI: https://devcenter.Heroku.com/articles/Heroku-cli .
4. Ejecutar en la terminal Heroku login e introducir las credenciales en la ventana del navegador.
5. Añadimos un repositorio remoto al repositorio local con el siguiente comando Heroku `git:remote-a nombre-de-la-app` (lo ejecutamos en la misma carpeta donde está el repositorio local).
6. Comprobamos que lo ha añadido correctamente ejecutando `git remote -v`

### With account
1. Ejecutar en la terminal Heroku login.
2. Añadimos un repositorio remoto al repositorio local con el siguiente comando `Heroku git:remote-a nombre-de-la-app` (lo ejecutamos en la misma carpeta donde está el repositorio local).
3. Comprobamos que lo ha añadido correctamente ejecutando `git remote -v`
4. Guardar variables de entorno necesarias en Heroku.

## Deploy to Heroku
1. Guardar los cambios con `git add .; git commit -m 'mensaje'`
2. Hacer un push a Heroku indicando con el subtree cual es la carpeta donde tiene que mirar. 

**HACER DESDE LA RAÍZ DEL PROYECTO**
`git subtree push --prefix=server Heroku master`

1. Ejecutamos `heroku logs  --tail` para ver si hay errores.
2. En la pestaña desplegable More si seleccionamos View logs podemos seguir el estado deldeploy.

## Deploy to the database (MongoDB Atlas)
NOTA : en la learning nos explica cómo hacer la subida a Mlab, pero debido a que MongoDB ha adquirido Mlab, ya no están admitiendo nuevas altas de usuarios.

### Without account
1. Vamos a https://www.mongodb.com/cloud/atlas/register  y creamos una cuenta.
2. Elegimos un servidor que esté en europa y sea gratuito.
3. Si nos sale un asistente  Get Started  pulsamos en  No Thanks!
4. Vamos a la pestaña Security a la opción  `IP Whitelist`  y pulsamos en  ADD IP ADDRESS. Marcamos la opción `Allow access` from Anywhere.
5. Dentro de  Security, vamos a  MongoDB Users  y creamos un usuario con los permisos  Atlasadmin . Apuntamos las credenciales de ese usuario, pues serán las que usemos paraconectarnos
6. Vamos a la pestaña  Overview  y pulsamos en  CONNECT . Elegimos  Connect your Application .
   1. Nos aseguramos que el  DRIVER  sea Node y copiamos el texto de  Connection StringOnly.
   2. Guardamos la cadena de conexión teniendo cuidado de sustituir por la contraseña delusuario de la base de datos.
   3. En la cadena de conexión, podemos sustituir  test  por el nombre que queramos que tenganuestra base de datos.

### With account
1. Accedemos con nuestra cuenta y en la pestaña  Collections  pulsamos el boton de  CreateDatabase  y nos creamos la nueva base de datos.
2. Vamos a la pestaña  Overview  y pulsamos en  CONNECT . Elegimos  Connect your Application .
   1. Nos aseguramos que el  DRIVER  sea Node y copiamos el texto de  Connection StringOnly.
   2. Guardamos la cadena de conexión teniendo cuidado de sustituir por la contraseña delusuario de la base de datos.
   3. En la cadena de conexión, debemos sustituir  test  por el nombre que le dimos a la basede datos creada

## Connect Heroku to the database
En Heroku, vamos a la pestaña Settings y pulsamos en  Reveal Config Vars. Creamos una variable con elmismo nombre que la variable que creamos para la base de datos en el fichero `.env`  y pegamos lacadena de conexión. También tenemos que crear variables para el resto de variables que tengamos en el `.env`.

## Run the seeds
1. En el archivo `.env` podemos:
   1. Modificar la url de la base de datos local por la cadena de conexión remota.
   2. Crear una variable local con la cadena remota, y cambiar la variable en `mongoose.connect(...)`.
2. En  `mongoose.connect(...)` , ponemos la cadena de conexión.
3. Ejecutamos el script en local.

## Connect to the remote database using Compass
1. Copiamos al portapapeles la cadena de conexión remota.
2. Abrimos Compass. Compass detectará que tenemos la cadena de conexión en el portapapeles y nos preguntará si queremos usarla. Decimos que sí
3. Tenemos que poner  admin en el campo  Authentication Database  para que nos deje conectar.