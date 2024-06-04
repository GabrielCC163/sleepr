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