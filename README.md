# tutorial-web-app

This web application provides the front door into the Inetgreatly initiative. It houses the various Tutorials (aka Steel Threads) as well as a dashboard of installed products/services.

# local development

```
yarn install;yarn start:dev
```

This will install dependencies & start the server.
Mock data will be used for any calls to OpenShift.

To configure local development with a running OpenShift cluster, do the following.

Create an OAuthClient:

```
yarn run oauthclient
```

Start the server, specifying the OpenShift host (This will disable mock data).

```
OPENSHIFT_HOST=localhost:8443 yarn start:dev
```

# local development with containers

Build the image

```
docker build -t webapp .
```

Start the image, then visit http://localhost:5001. This setup will use mock data.

To use real data (i.e. integrate with an openshift cluster), set the required env vars accordingly. For example:

```
docker run -it --rm -e OPENSHIFT_HOST=192.168.42.1:8443 -e OPENSHIFT_OAUTHCLIENT_ID=tutorial-webapp -e REDIRECT_URI=http://localhost:5001/oauth/callback -e NODE_ENV=production -p 5001:5001 webapp
```

Ensure you have created the OAuthClient in the OpenShift cluster too:

```
yarn run oauthclient
```

# remote development

```
oc new-project integreatly-web-app
find . | grep openshiftio | grep application | xargs -n 1 oc apply -f
oc new-app --template react-demo-app -p SOURCE_REPOSITORY_URL=https://github.com/integr8ly/tutorial-web-app -p SOURCE_REPOSITORY_REF=architecture
```

# Ascii Doc Support

This project requires [asciidoctor](https://github.com/asciidoctor/asciidoctor) and [yq](https://github.com/kislyuk/yq) for translating Ascii Docs to JSON.

Pre-requisites:

```
brew install asciidoctor
brew install python-yq
```

To convert ascii doc to HTML fragments, run the following:

```
cd ./public/asciidocs/en
asciidoctor [INPUT-ASCII-DOC-FILE] -s
```

To convert ascii doc files to JSON, you can run the following:

```
cd ./public/asciidocs/en
asciidoctor -b docbook [INPUT-ASCII-DOC-FILE] [OUTPUT-XML-FILE]
xq . [OUTPUT-XML-FILE] > [OUTPUT-JSON-FILE]
```

This JSON can then be referenced in the appropriate language locale under `public/locales/*.json`.
