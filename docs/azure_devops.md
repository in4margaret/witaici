# CI using Azure DevOps pipelines

Let's use [Azure DevOps](https://azure.microsoft.com/en-us/services/devops/) to build CI for Wit.ai.

Add WIT_AUTH_TOKEN secret to Azure DevOps Variable Group.
![azdo_secret](../images/azdo_secret.png)
 It should have a value of the Server Access Token from any app in Wit.ai. ![server_access_token](../images/server_access_token.png)
 Now let's create a file [wit_ci.yml](../.azdo/wit_ci.yml) in your Azure DevOps repo that defines the CI. Let's have a look at what it does.

First, we install the wittycli npm package in our CI pipeline.

```yaml
- script: npm install -g wittycli
  displayName: 'wittycli install'
```

Second, using wittycli we import the model that we committed in the repo to Wit.ai using existing WIT_AUTH_TOKEN.
We save a new auth token for the model that we just imported as a NEW_WIT_AUTH_TOKEN environment variable.

```yaml
- script: |
    export NEW_WIT_AUTH_TOKEN=$(wittycli import --file ./model/model.zip --name "witcli$(BUILD.BUILDID)" --private --dot access_token --wait)
    echo $NEW_WIT_AUTH_TOKEN
    echo "##vso[task.setvariable variable=NEW_WIT_AUTH_TOKEN;isOutput=true]$NEW_WIT_AUTH_TOKEN"
  displayName: 'import model'
  name: 'importzip'
```

Internally wittycli uses the value of the WIT_AUTH_TOKEN env variable to authenticate all requests made to Wit.ai.

Third, on every new change you make to the model, it runs tests using wittycli functionality. To test we need to provide a file with the test cases, auth token for the model, also you can specify the level of parallelism.

```yaml
- script: |
    wittycli test --auth $(importzip.NEW_WIT_AUTH_TOKEN) --file ./test/wit_test.json --parallel 5
  displayName: 'test model'
```

Results of the test run you can find in the Azure DevOps build run:

![tests results](../images/azdo_test_result.png)

Azure DevOps pipeline is located in [../.azdo/wit_ci.yml](../.azdo/wit_ci.yml) in this repo.

## Review and continue improving your Wit.ai app in GitHub

From our Azure DevOps build  run we see that 3 tests fail, so let's add additional utterances to the model and train our model with more utterances. Generally, it's a good idea to not modify the existing prod version of the Wit.ai app. That's why we will create a duplicate app for improving of the model.
To do that we can import the existing model to the Wit.ai portal using [import_wit_model.yml](../.azdo/import_wit_model.yml) Azure DevOps pipeline that you can trigger manually.

![import model](../images/azdo_model_import.png)
Before you will trigger it manually you can specify Wit.ai model name.
After you imported it using manual workflow in GitHub Actions and modified it from the Wit.ai portal, you can perform the same steps as before:

- Download the model from the Wit.ai portal using wittlycli or Wit.ai UI
- Commit it to your repo.

CI automatically starts running the tests after the change and we can see if the changed model performs better and resolves intents with a higher score.

Azure DevOps pipelines are located in [.azdo](../.azdo/) folder in this repo.
