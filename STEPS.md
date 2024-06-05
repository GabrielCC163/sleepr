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
