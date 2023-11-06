# Lab 9 Errata

## Step 0: Prepare your project

Due to some [recent change at Railway](https://help.railway.app/project-help/f9v3gkPQRy4UShk5SnoPDH/getting-your-create-react-app-running-on-railway/qHRsgxa5n57xLp1yVgk9fP), this part is completely obsolete.

* First, go into the `client` directory and `npm install serve`

* Rename the current `start` script to `dev`. In the future, run `npm run dev` for local development
* Add a new `start` script:

  ```js
  "start": "serve build -s -n -L -p $PORT"
  ```

* Your `scripts` section should now look like:

  ```js
  "scripts": {
    "dev": "react-scripts start",
    "start": "serve build -s -n -L -p $PORT",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  ```

(Onthefly appears to have been converted from a `create-react-app` app. If using Vite from scratch, the `start` script should be: `erve dist -s -n -L -p $PORT`).

## Step 1: Deploy the server

* If you get an error message from Railway that says "Failed to Deploy! Invalid Service Name", it means your github repo's name [is too long](https://stackoverflow.com/questions/76665256/railway-github-repository-deploy-error-invalid-service-name). Try shortening it! Max 27 characters, as far as I can tell.

## Step 2: Deploy the client

* Use this line for `API_URL`

  ```js
  const API_URL = process.env.NODE_ENV === 'production' ? 'https://?????.up.railway.app' : 'http://localhost:3001'
  ```

  * Replace `?????` with your server domain of course

* Add the new `API_URL` variable to the `useEffect()` dependency list in `App.js`. This prevents warnings about missing variables in the dependency list:

  ```js
  useEffect(() => {
    // ... getUser and fetchTrips function definitions ...
    getUser()
    fetchTrips()
  }, [API_URL]);
  ```

## Step 4: Update server configuration

* In `server/server.js`, refactor it to use a `CLIENT_URL` constant, and update the `origin` key for `cors` and the homepage redirect as follows:

  ```js
  const CLIENT_URL = process.env.NODE_ENV === 'production' ? 'https://?????.up.railway.app' : 'http://localhost:3000'
  ```

  * Replace `?????` with your client domain of course

  ```js
  app.use(cors({
    origin: CLIENT_URL,
    methods: 'GET,POST,PUT,DELETE,PATCH',
    credentials: true
  }))
  ```

  ```js
  app.get('/', (req, res) => {
    res.redirect(CLIENT_URL)
  })
  ```

* In `server/config/auth.js`, update the `successRedirect` and `failureRedirect` values to simply `'/'`

