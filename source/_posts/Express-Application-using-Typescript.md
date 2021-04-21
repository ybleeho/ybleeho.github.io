---
title: Express Application using Typescript
date: 2019-12-22 14:58:28
tags:
---



Install dependencies

```
yarn add @types/node @types/express ts-node typescript nodemon --dev
yarn add express
```

Create a `tsconfig.json`  file in root of your application or run `npx tsc --init`

```
//more about compile options
//https://www.typescriptlang.org/docs/handbook/compiler-options.html
{
"compilerOptions":
  {
  "target": "es6",
  "module": "commonjs",
  "allowJs": true,
  "outDir": "./build",
  "rootDir": "./src",
  "esModuleInterop": true
  }
}
```

Add scripts to package.json file.

```
 "scripts": {
    "dev": "nodemon src/server.ts",
    "start": "ts-node dist/server.js",
    "build": "tsc -p ."
  },
```

File structure

```
- dist
- node_modules
- src
package.json
tsconfig.json


//src
- controllers
- interfaces
- middleware
app.ts
server.ts
```

Edit app.ts

```
import * as express from 'express'
import {Application} from 'express'

class App {
    public app: Application
    public port: number

    constructor(appInit: {port: number; middleWares: any; controllers: any;}) {
        this.app = express()
        this.port = appInit.port
        this.middlewares(appInit.middleWares)
        this.routes(appInit.controllers)
    }

    private middlewares(middleWares: { forEach: (arg0: (middleWare: any) => void) => void; }) {
        middleWares.forEach(middleWare => {
            this.app.use(middleWare)
        })
    }

    private routes(controllers: { forEach: (arg0: (controller: any) => void) => void; }) {
        controllers.forEach(controller => {
            this.app.use('/', controller.router)
        })
    }

    public listen() {
        this.app.listen(this.port, () => {
            console.log(`App listening on the    http://localhost:${this.port}`)
        })
    }

}

export default App

```

Edit server.ts

```
import App from './app'

import * as bodyParser from 'body-parser'
import loggerMiddleware from './middleware/logger';
import AuthController from './controllers/auth.controller'

const app = new App({
    port: 3000,
    controllers: [
        new AuthController()
    ],
    middleWares: [
        bodyParser.json(),
        bodyParser.urlencoded({extended: true}),
        loggerMiddleware
    ]
})

app.listen()
```

Add middleware

```
import {Request, Response} from "express"

const loggerMiddleware = (req: Request, res: Response, next) => {
    console.log('Request logged:', req.method, req.path)
    next()
}

export default loggerMiddleware

```

Add base  controller interface

```
interface IControllerBase {
    initRoutes(): any
}

export default IControllerBase
```

Add controller

```
import * as express from 'express'
import { Request, Response } from 'express'
import IControllerBase from '../interfaces/IControllerBase.interface'

class AuthController implements IControllerBase {
    public path = '/login'
    public router = express.Router()

    constructor() {
        this.initRoutes()
    }

    public initRoutes() {
        this.router.get(this.path, this.login)
    }

    login = (req: Request, res: Response) => {
        res.json({message: 'ok'})
    }

}

export default AuthController
```

Run the app

```
yarn run dev
```

> TODO:
>
> 1.Connect to postgressql & Create model with Typescript definitations
>
> [https://sequelize.org/v5/manual/typescript.html](https://sequelize.org/v5/manual/typescript.html)
>
> use sequelize-cli to generate typescript model is better
>
> 2.Controllers => Seperation of  route and logic


