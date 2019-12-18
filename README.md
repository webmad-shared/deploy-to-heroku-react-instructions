# Deploy to Heroku: React instructions

The purpose of this document is to explain how to deploy the MERN application you have created for your third project to Heroku.

At some point specified by your lead teacher, you will have to upload your MERN third project to Heroku. Good news are that after running all the required steps you will find that republishing your latest changes is very easy. Remember that after deploying you will continue working locally and only when your new features are finished and stable you will have to redeploy to see your latest changes online.

In order to deploy to Heroku your React app there will be some elements you will have to take into account:

1. The project root folder **must have** two folders inside
   1. `server`. This will contain your backend using Express, Mongoose models, etc.
   2. `client`. This will contain your React app.
2. A **your database running in MongoDB Atlas**.
3. You will have to create and maintain throughout the project a **seed file** so you can tear down dirty data everytime you need and repopulate your DB with clean data.
4. Environment files containing the environment variables (and these variables in Heroku) both for your server and your React app.
5. A running **account in Heroku** for your app and its command line tools.

## 1. Create your root project
1. Create your empty project folder
2. Issue `git init` so you initialize control version with git for your project.
3. Create the `server` folder using `irongenerate server`
4. Create the `client` folder using `npx create-react-app client`
5. In the root folder issue a `git add .` and then `git commit -m "first commit"`

Now you have the basic skeleton for your project commited and you are now ready to start coding.

## 2. Database setup in MongoDB Atlas
We will be using MongoDB Atlas for this project. More info at https://www.mongodb.com/cloud/atlas/ 

In order to make things right, we will start this documentation by creating your project's database, populate it with your seed's data (next chapter of this document) and setting it up as the default DB for both your local and Heroku project.

### Creating an account
1. Visit https://www.mongodb.com/cloud/atlas/register and create an account.
2. Choose a server that is in Europe and that is free.
3. If you see an assistant "Get Started" click the "No Thanks!" button.
4. Go to the security tab. Then in the security tab choose `IP Whitelist` and select "ADD IP ADDRESS". Marcamos la opción `Allow access` from Anywhere.
5. Inside of "Security", navigate to "MongoDB Users" and create an user with the Atlasadmin permissions. Write down the credentials as these will be ones used to connect later on.

And then:

1. Access with you account and in the "Collections" tab press the button "Create Database" and create a new database.
2. Navigate to the "Overview" tab and press "CONNECT". Choose "Connect your Application".
   1. Ensure that the DRIVER is Node and copy the text "Connection String Only".
   2. Store the connection string. Be sure that you substitute the password with the real password you specified.
   3. In the connection string substitute `test` with the name we gave to the created database.

## 3. Run the seeds
1. In the `.env` file we must:
   1. Replace the local database url with the remote database connection string.
   2. Create a local variable with the remote database connection string and place it inside the `mongoose.connect(...)`.
2. In  `mongoose.connect(...)` we must specify the connection string.
3. Run the script locally.

### Connect to the remote database using Compass
1. Copy to the clipboard the remote connection string.
2. Open Compass. Compass will detect that we have the remote connection string the clipboard and will ask whether we want to use it. Answer "YES".
3. We have to set admin in the Authentication Database field so we are allowed to connect.
   
## 4. Environment variables
### Backend - local environment config
1. Install and configure the `dotenv` module. Remember to require the module wherever necesary.
2. In the `.env` file add the URL to the local database. Also add other variable that we don't wand to have in our repo for safety reasons (credentials, API keys...).
3. In the server `app.js` add this line **after** all the routes  `app.use((req, res) => {res.sendFile(__dirname + "/public/index.html");});`. You have to place it there so all the routes of your API **are detected first** and **only** if no requested route matches your API then the React app must be served.

### Frontend - local environment config
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

## 5. Heroku setup and Heroku's command line tools install

### Connect Heroku to the database
In Heroku, visit the "Settings" tab and press "Reveal Config Vars". Create all the variables of your `.env` file and remember to paste the Mongo Atlas connection string.

### Creating an account
1. Create account.
2. Create app. Region: Europe.
3. Download and install the Heroku command line tools: https://devcenter.Heroku.com/articles/Heroku-cli.
   
And then:
1. Issue in the terminal `heroku login`.
2. Add the remote repo to the local repo issuingAñadimos un repositorio remoto al repositorio local con el siguiente comando `Heroku git:remote-a nombre-de-la-app` (we run it in the same folder where is the local repository).
3. Verify that it has been properly added by issuing `git remote -v`.
4. Save the necesary environment variables in Heroku.

### Deploy to Heroku
1. Commit your changes by issuing `git add .; git commit -m 'your commit message'`.
2. Make a push to Heroku specifying with the `subtree` flag which folder has to look for.

**From your project's root folder:** `git subtree push --prefix=server heroku master`

Issue `heroku logs  --tail` to check for errors.