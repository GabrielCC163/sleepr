## Create a new library
- nest generate library common
Check nest-cli.json and libs folder

## DB
- npm i @nestjs/mongoose mongoose
- npm i @nestjs/config (enable to read .env)
- nest generate module database -p common
- npm i joi (schema validation)

## App reservations
- nest g app reservations
- nest g resource reservations

## Validation
main.ts
- app.useGlobalPipes(new ValidationPipe({ whitelist: true })) (class validator, class transformer)
- whitelist is to block user from sending another fields rather then the decorated fields

## Docker
- cd apps/reservations && docker build ../../ -f Dockerfile -t sleepr_reservations
- or docker-compose up


## App auth
- nest g app auth
- nest g module users (select auth)
- nest g controller users (select auth)
- nest g service users (select auth)

## Passport Authentication and Authorization
- npm i @nestjs/passport passport passport-local @nestjs/jwt passport-jwt bcryptjs express
- npm i -D @types/passport-local @types/passport-jwt @types/bcryptjs
- npm i cookie-parser
- npm i -D @types/cookie-parser
- Setup cookieParser in auth/src/main.ts
- Import JwtModule in auth.module.ts
- Local strategy (login with user email and password)
    - create local.strategy.ts
    - create apps/auth/src/guards/local-auth.guard.ts (references local strategy)
    - Put @UseGuards(LocalAuthGuard) as decorator in login controller
- JWT Strategy (to validate the JWT)
    - create jwt.strategy.ts
    - create apps/auth/src/guards/jwt-auth.guard.ts

## Connecting reservations with auth microservice
- npm i @nestjs/microservices
- Add into apps/auth/src/main.ts:
    - app.connectMicroservice({ transport: Transport.TCP })
    - await app.startAllMicroservices()


## Payment service
- nest g app payments
- npm i stripe

## Notification service (NODEMAILER and GMAIL)
- nest g app notifications
- Dockerfile and .env
- add service into docker-compose.yml
- setup main.ts
- npm i nodemailer
- npm i -D @types/nodemailer

- Access console.cloud.google.com
- new Project -> Sleepr
-> APIs and services > OAuth consent screen > select External + Create
- Add app name, support email and developer email address. + Test user (email).
- Credentials > Create credentials > OAuth client ID > URIs: https://developers.google.com/oauthplayground > Create.
- Copy credentials and paste into .env
- Access https://developers.google.com/oauthplayground/
- Settings > Use your own OAuth credentials > Paste client id and secret. > Close.
- Select Gmail API v1 > Click Authorize APIs > Click Exchange authorization code for tokens



## Rule
- send -> MessagePattern
- emit -> EventPattern


## Endpoints
- Create user :: POST http://localhost:3001/users
    - email and password

- Login :: POST http://localhost:3001/auth/login
    - email and password
    - after request, check token in Cookies

- Create reservation :: POST http://localhost:3000/reservations
 ```json
  {
    "startDate": "02-01-2023",
    "endDate": "02-05-2023",
    "placeId": "123",
    "charge": {
        "amount": 5,
        "card": {
            "cvc": "413",
            "exp_month": 12,
            "exp_year": 2027,
            "number": "4242 4242 4242 4242"
        }
    }
  }
 ```
 
## Google Cloud
- Access console.cloud.google.com
- Activate free trial
- Artifact Registry API > Enable
    - Create repository > reservations > Docker > us-east4 > Create.
    - Same for auth | notifications | payments.
    - Click in one of them > SETUP INSTRUCTIONS > Google Cloud SDK
        - quickstart > install it.
        - run: gcloud config configurations create sleepr
        - run: gcloud config set project sleepr-<projectId>
    - Click in one of them > SETUP INSTRUCTIONS > Application Default Credentials
        - run: gcloud auth application-default login
        - run: gcloud artifacts repositories list
        - run: gcloud auth configure-docker us-east4-docker.pkg.dev 
        - cp apps/reservations > run docker build -t reservations -f . ../../
        - back in gcloud console > artifacts registry > reservations > copy (URI)
        - run: docker tag reservations <gcloud_repository_uri>/production
        - run: docker image push <gcloud_repository_uri>/production
        - refresh the page and see the image created.
        - repeat for auth | notifications | payment

- Search for Google Container Registry API > Enable ?? don't know if this is required.

## Tip: build docker image
- cd auth
- docker build -t auth -f . ../../

## Google Cloud CI/CD
- Create cloudbuild.yaml
- Access console.cloud.google.com > Cloud Build API > Enable
    - Setup build trigger
    - name: sleepr-push | push to a branch | 1st generation | repo: github | branch: ^main$ | autodetect