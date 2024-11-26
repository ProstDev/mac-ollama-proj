# MuleSoft AI Chain (MAC) + Ollama

Simple Mule application making use of the [MAC](https://mac-project.ai/) (MuleSoft AI Chain) project to connect to [Ollama](https://ollama.com/) (`llama3` model).

Full tutorial: [How to connect your local Ollama AI using the MAC project and ACB](https://www.prostdev.com/post/mac-project-ollama-ai)

## Similar repos

[![](https://github-readme-stats.vercel.app/api/pin/?username=alexandramartinez&repo=ollama-llm-provider&theme=graywhite)](https://github.com/alexandramartinez/ollama-llm-provider)

## Steps

### 1 - Install Ollama locally

- Go to [ollama.com](https://ollama.com/) and follow the prompts to install in your local computer
- Make a note of which version you started running (like `llama3` or `llama3.2`)
- To verify ollama is running, you should either be able to interact with it in the terminal or you should be able to run `ollama list` or `ollama ps`
- We will verify the local installation in MuleSoft before deploying to CloudHub

### 2 - Create the API specification

- In ACB > Design an API
- Name it `MAC-Ollama-API`
- REST API / RAML 1.0
- Create project
- Use the [mac-ollama-api.raml](/specification/mac-ollama-api.raml) file
- Publish to Exchange

### 3 - Implement the API

- Select Yes to implement this API in ACB
- Name it `mac-ollama-proj`, select the folder where you want to keep this, select Mule Runtime 4.8 and Java 17
- Once the project finishes loading, open the `mac-ollama-proj.xml` file under `src/main/mule`
- Take a look at the contents from [mac-ollama-proj.xml](/mule-project/src/main/mule/mac-ollama-proj.xml) to configure your own
- Create a new file called `llm-config.json` under `src/main/resources` with the following:

```json
{
    "OLLAMA": {
        "OLLAMA_BASE_URL": "http://localhost:11434"
    }
}
```

- This will let us test Ollama locally first

### 4 - Test the app locally

- Run the app locally
- Send a **POST** request to `localhost:8081/api/chat` with a JSON body including your question

```shell
curl -H 'Content-Type: application/json' -X POST http://localhost:8081/api/chat -d '"hello"'
```

- If everything was successful, continue to the next step. Otherwise, please troubleshoot before continuing
- Stop the app

### 5 - Use ngrok for the public endpoint

- Download and install [ngrok](https://download.ngrok.com/mac-os) to make your Ollama endpoint publicly available from the internet. This way, CloudHub will be able to access the URL since it's not only in your local (localhost:11434)
- Run the following from your Termina/cmd:

```shell
ngrok http 11434 --host-header="localhost:11434"
```

- Copy the address from the **Forwarding** field
- Paste it in your `llm-config.json` file under `src/main/resources`
- Save the file and run the app again to verify everything still works correctly
- Stop the app once you verify it works

### 6 - Deploy to CloudHub

- Go to your `pom.xml` file and change the version to `1.0.0` (remove the SNAPSHOT)
- Save the file
- Head to your `mac-ollama-proj.xml` file and click on the Deploy to CloudHub button
- Select the options you want (you can leave the defaults) and deploy
- Once the deployment is done, get the Public Endpoint from your application and call it to verify the app works

```shell
curl -H 'Content-Type: application/json' -X POST https://mac-ollama-proj-ny7z.5sy6-1.usa-e2.cloudhub.io/api/chat -d '"hello"'
```

> [!NOTE]
> If you experience a lot of issues with the deployment from ACB, you can also get the JAR from the `target` folder and deploy manually in Runtime Manager

