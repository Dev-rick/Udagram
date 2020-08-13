# Udagram Image Filtering App 

Udagram is a simple cloud application developed alongside the Udacity Cloud Engineering Nanodegree. It allows users to register and log into a web client, post photos to the feed, and process photos using an image filtering microservice.

The project is split into three parts:
1. [The Simple Frontend](/frontend)
A basic Ionic client web application which consumes the RestAPI Backend. 
2. [The RestAPI Feed Backend](/restapi-feed), a Node-Express feed microservice.
3. [The RestAPI User Backend](/restapi-user), a Node-Express user microservice.

## Version 2: Locally usable with docker-compose

> _tip_: Not deployed to kubernetes yet, but already initialized the right environment variables

> _tip_: Pain points in the first place: define the right environment variables for restapi-feed as docker-compose does not access the credentials from /.aws even if mounted:

1. app/restapi-feed/src/config/config.ts

```ts
export const config = {
  "dev": {
   ...
    'access_key_id': process.env.AWS_ACCESS_KEY_ID,
    'secret_access_key': process.env.AWS_SECRET_ACCESS_KEY
  }
}

```
2. app/restapi-feed/src/aws.ts

```ts
# ADD TO BOTH GETSIGNEDURL FUNCTIONS:

const signedUrlExpireSeconds = 60 * 5

  s3.config.update({
    accessKeyId: c.access_key_id,
    secretAccessKey: c.secret_access_key
  })

const url = s3.getSignedUr...

```

3. deployment/docker/docker-compose.yaml

```yaml
 backend-feed:
    image: devrick1/restapi-feed
    environment:
        ...
        AWS_ACCESS_KEY_ID: ${AWS_ACCESS_KEY_ID}
        AWS_SECRET_ACCESS_KEY: ${AWS_SECRET_ACCESS_KEY}
```

4. deployment/docker/.env

```
POSTGRESS_USERNAME=XXX
POSTGRESS_PASSWORD=XXX
POSTGRESS_DATABASE=XXX
POSTGRESS_HOST=XXX.XXX.eu-west-3.rds.amazonaws.com
AWS_REGION=XXX
AWS_PROFILE=XXX
AWS_MEDIA_BUCKET=XXX
JWT_SECRET=XXX
HOME=XXX
URL=XXX
AWS_ACCESS_KEY_ID=XXX
AWS_SECRET_ACCESS_KEY=XXX
```

5. Run following command

```bash
sudo docker system prune -a #or $ docker builder prune(for only deletion of the cache)
sudo docker-compose -f deployment/docker/docker-compose-build.yaml build --parallel
sudo docker push devrick1/frontend
sudo docker push devrick1/restapi-user
sudo docker push devrick1/restapi-feed
sudo docker push devrick1/reverseproxy
cd deployment/docker
sudo docker-compose up
```

## Getting Setup
> _tip_: Make sure you have the right environment variables set 
> _tip_: This application is used to run the backend from a local server (other machine as the local client), so configure your localhost (http://192.168.0.80/) variable accordingly 
> _tip_: this frontend is designed to work with the RestAPI backends). It is recommended you stand up the backend first, test using Postman, and then the frontend should integrate.

### Installing Node and NPM
This project depends on Nodejs and Node Package Manager (NPM). Before continuing, you must download and install Node (NPM is included) from [https://nodejs.com/en/download](https://nodejs.org/en/download/).

### Installing Ionic Cli
The Ionic Command Line Interface is required to serve and build the frontend. Instructions for installing the CLI can be found in the [Ionic Framework Docs](https://ionicframework.com/docs/installation/cli).

### Installing project dependencies
This project uses NPM to manage software dependencies. NPM Relies on the package.json file located in the root of this repository. After cloning, open your terminal and run:
```bash
npm install
```
>_tip_: **npm i** is shorthand for **npm install**

### Setup Backend Node Environment
You'll need to create a new node server. Open a new terminal within the project directory and run:
Run `npm run dev` to start the server


### Configure The Backend Endpoint
Ionic uses enviornment files located in `./src/enviornments/enviornment.*.ts` to load configuration variables at runtime. By default `environment.ts` is used for development and `enviornment.prod.ts` is used for produciton. The `apiHost` variable should be set to your server url either locally or in the cloud.

***
### Running the Development Server
Ionic CLI provides an easy to use development server to run and autoreload the frontend. This allows you to make quick changes and see them in real time in your browser. To run the development server, open terminal and run:

```bash
ionic serve
```

### Building the Static Frontend Files
Ionic CLI can build the frontend into static HTML/CSS/JavaScript files. These files can be uploaded to a host to be consumed by users on the web. Build artifacts are located in `./www`. To build from source, open terminal and run:
```bash
ionic build
```
***

