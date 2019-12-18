# Deploy to Heroku: React instructions

The purpose of this document is to explain how to deploy the MERN application you have created for your third project to Heroku.

At some point specified by your lead teacher, you will have to upload your project to Heroku. After running all the required steps you will find that republishing your latest changes is very easy. Remember that after deploying you will continue working locally and only when your implementations are finished and stable you will redeploy.

In order to deploy to Heroku your React app there will be some elements you will have to take into account:

1. The project root folder **must have** two folders inside
   1. `server`. This will contain your backend using Express, Mongoose models, etc.
   2. `client`. This will contain your React app.
2. You will have to create a **seed file** so you can tear down dirty data everytime you need and repopulate your DB with clean data.
3. A **your database running in MongoDB Atlas**.
4. A running **account in Heroku** for your app.
5. Environment files containing the environment vairables (and these variables in Heroku) both for your server and your React app.
6. The Heroku command line tools.

## Database setup in MongoDB Atlas
We will be using MongoDB Atlas for this project. More info at https://www.mongodb.com/cloud/atlas/ 

In order to make things right, we will start this documentation by creating your project's database, populate it with your seed's data and setting it up as the default db for both your local and Heroku project.

### Without account
1. Visit https://www.mongodb.com/cloud/atlas/register and create an account.
2. Choose a server that is in Europe and that is free.
3. If you see an assistant "Get Started" click the "No Thanks!" button.
4. Vamos a la pestaña Security a la opción  `IP Whitelist`  y pulsamos en  ADD IP ADDRESS. Marcamos la opción `Allow access` from Anywhere.
5. Inside of "Security", navigate to "MongoDB Users" and create an user with the Atlasadmin permissions. Write down the credentials as these will be ones used to connect later on.
6. Vamos a la pestaña  Overview  y pulsamos en  CONNECT . Elegimos Connect your Application .
   1. Nos aseguramos que el DRIVER sea Node y copiamos el texto de Connection StringOnly.
   2. Guardamos la cadena de conexión teniendo cuidado de sustituir por la contraseña delusuario de la base de datos.
   3. En la cadena de conexión, podemos sustituir test por el nombre que queramos que tenga nuestra base de datos.

### With account
1. Access with you account and in the "Collections" tab press the button "Create Database" and create a new database.
2. Navigate to the "Overview" tab and press "CONNECT". Choose "Connect your Application".
   1. Ensure that the DRIVER is Node and copy the text "Connection String Only".
   2. Store the connection string. Be sure that you substitute the password with the real password you specified.
   3. In the connection string substitute `test` with the name we gave to the created database.

## Connect Heroku to the database
In Heroku, visit the "Settings" tab and press "Reveal Config Vars". Create all the variables of your `.env` file and remember to paste the Mongo Atlas connection string.

## Run the seeds
1. In the `.env` file we must:
   1. Replace the local database url with the remote database connection string.
   2. Create a local variable with the remote database connection string and place it inside the `mongoose.connect(...)`.
2. In  `mongoose.connect(...)` we must specify the connection string.
3. Run the script locally.

## Connect to the remote database using Compass
1. Copy to the clipboard the remote connection string.
2. Open Compass. Compass will detect that we have the remote connection string the clipboard and will ask whether we want to use it. Answer "YES".
3. We have to set admin in the Authentication Database field so we are allowed to connect.
   
## Backend - local environment config
1. Install and configure the `dotenv` module. Remember to require the module wherever necesary.
2. In the `.env` file add the URL to the local database. Also add other variable that we don't wand to have in our repo for safety reasons (credentials, API keys...).
3. In the server `app.js` add this line **after** all the routes  `app.use((req, res) => {res.sendFile(__dirname + "/public/index.html");});`. You have to place it there so all the routes of your API **are detected first** and **only** if no requested route matches your API then the React app must be served.

## Frontend - local environment config
1. Confirm that `dotenv-cli` is present in the `client` folder. Otherwise install it via `npm install dotenv-cli`.
2. Create the two variable files in the root of the `client` folder by issuing in the command line `touch .env.dev .env.prod`.
3. Write the different environments variables.  También tenemos que añadir otros datos que no queremos quese suban al repositorio y pertenecen al front (credenciales, claves API...)
    1. `.env.dev` -> `REACT_APP_URL=http://localhost:5000/api`
    2. `.env.prod` -> `REACT_APP_URL= https://name-of-your-app.Herokuapp.com/api`
4. Inside of the `package.json` file in the client part we have to configure these scripts:
```
    "start": "dotenv -e .env.dev react-scripts start"
    "build-dev": "dotenv -e .env.dev react-scripts build"
    "build-prod": "dotenv -e .env.prod react-scripts build"
```

Issue `npm run build-prod` in the `client` folder and move **all the contents** of the `build` folder inside the `public` server folder. You can use this alias to make things easier. `alias cb="rm -rf ./server/public; mkdir ./server/public;  cp  -r ./client/build/. ./server/public"` . You can add this alias to your `~/.zshrc` file so you can use it everytime you launch a new terminal.

## Heroku configuration

### Without account
1. Create account.
2. Create app. Region: Europe.
3. Download and install the Heroku command line tools: https://devcenter.Heroku.com/articles/Heroku-cli.
4. Issue in the terminal `heroku login` and write your credentials in the automatically opened browser window.
5. Añadimos un repositorio remoto al repositorio local con el siguiente comando Heroku `git:remote-a nombre-de-la-app` (lo ejecutamos en la misma carpeta donde está el repositorio local).
6. Verify that this new remote has correctly been added issuing `git remote -v`.

### With account
1. Issue in the terminal `heroku login`.
2. Añadimos un repositorio remoto al repositorio local con el siguiente comando `Heroku git:remote-a nombre-de-la-app` (lo ejecutamos en la misma carpeta donde está el repositorio local).
3. Comprobamos que lo ha añadido correctamente ejecutando `git remote -v`.
4. Save the necesary environment variables in Heroku.

## Deploy to Heroku
1. Commit your changes by issuing `git add .; git commit -m 'mensaje'`.
2. Make a push to Heroku specifying with the `subtree` flag which folder has to look for.

**From your project's root folder:** `git subtree push --prefix=server heroku master`

1. Issue `heroku logs  --tail` to check for errors.
2. En la pestaña desplegable More si seleccionamos View logs podemos seguir el estado del deploy.

