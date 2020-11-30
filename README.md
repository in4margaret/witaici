![lights](./images/christmas_lights.gif)

# Configuring Wit.ai CI using Wit.ai CLI with GitHub Actions or Azure DevOps

## Overview

Using this tutorial, you will be able to:

- Create a production-ready solution using Wit.ai
- Implement Continuous Integration/DevOps best practices using GitHub Actions and/or Azure DevOps
- Work together with other engineers and data scientist on the natural language understanding model at the same time
- Track the changes
- Test the model
- Choose the best model based on test results
- Iterate to improve Wit.ai model

## Prerequisites

- [Wit.ai](https://wit.ai/) account
- [GitHub](https://github.com/) account or [Azure DevOps](https://azure.microsoft.com/en-us/services/devops/) account
- Get familiar with [wittycli](https://www.npmjs.com/package/wittycli) - Wit.ai cli that was created for this tutorial

### Train the Wit.ai model

Let's create a sample Wit.ai model first.
We have only two intents for demo purposes and use 5 utterances for training:
| Intent | #1 utterance | #2 utterance  | #3 utterance|
| ------- | --- | --- | ---|
| get_temperature | Is it hot? | Tell me the temperature | What's the current temperature? |
| set_temperature | Set temperature to 70 degrees|lower the temperature by 10 degrees||

### Add model to the repo

Let's commit our model to the repo now.
To do that we can use [wittycli](https://www.npmjs.com/package/wittycli) to download the existing model (.zip file) or you can use the [model](./model/model.zip) from this repo.
We add zip model and unzipped files like app.json to the repo to see the diff with the main branch (it will be easier for PR reviewers to compare changes, we still need to keep the zipped model in the repo).
Each time you push new files to your branch (zipped and extracted model) it will trigger the CI pipeline that will run the set of test cases against that model and show you the test results.

### Test Wit.ai model

Let's create a file with test cases following the format specified in [wittycli](https://github.com/ShyykoSerhiy/wittycli).
It is a [./test/wit_test.json](./test/wit_test.json) file that you can find in this repo.
This file should contain a JSON array with objects in the following format:

- text: utterance that you will use to query you Wit.ai model
- expected intent: an intent that you expect your model should return
(if the model didn't return the expected intent maybe you will need to provide more utterances to test it with)
- confidence: you can specify a threshold for each test case. The value should be between 0 and 1. For example, 0.9 means that want your model to be 90% (or more) sure about the correct intent or entity.
- entities: list of expected entities (optional). For each entity you can specify additional parameters that you want to test such as confidence, entity value, start and end position in an utterance.

```js
{
    "text": "Get temperature",
    "intents": [
        {
            "name": "get_temperature",
            "confidence": 0.75
        }
    ]
}

{
    "text": "Set temperature to 70 degrees and then to 80 degrees", // <- utterance
    "intents": [
        {
            "name": "set_temperature", // <- expected intent
            "confidence": 0.8  // <- min confidence
        }
    ],
    "entities": { // <- optional
        "wit$temperature:temperature": [ // <- expected entity
            {
                "name": "wit$temperature",
                "role": "temperature",
                "start": 19,
                "end": 29,
                "body": "70 degrees",
                "confidence": 0.95, // <- min confidence, optional
                "unit": "degree",
                "type": "value",
                "value": 70
            }
        ]
    }
}
```

Now we are ready to build CI using Wit.ai CLI and GitHub Actions workflows and/or Azure DevOps.

## [CI using GitHub Actions workflows](docs/github_actions.md)

## [CI using Azure DevOps pipelines](docs/azure_devops.md)

## Next Steps

For demonstration purposes, we’ve created the GitHub Actions workflow and have recently released Azure DevOps pipeline, but you can use Wit.AI CLI locally.

We look forward to seeing what you will develop using our samples of GitHub Actions workflow, Azure DevOps pipeline and wittycli. Feel free to create issues or PRs in both repos:

* [Witty CLI GitHub repo](https://github.com/ShyykoSerhiy/wittycli)
* [Wit.ai CI](https://github.com/in4margaret/witaici)

## Related Content

* [Witty CLI GitHub repo](https://github.com/ShyykoSerhiy/wittycli)
* [Wit GitHub](https://github.com/wit-ai)
* [Wit Blog](https://wit.ai/blog)
* [GitHub Actions](https://docs.github.com/en/free-pro-team@latest/actions)
* [Azure DevOps](https://docs.microsoft.com/en-us/azure/devops/?view=azure-devops)

## License

[LICENSE](LICENSE) file.

![lights](./images/christmas_lights.gif)
