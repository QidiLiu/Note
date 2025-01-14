#done

- Official doc (install): [Continue - Install](https://docs.continue.dev/getting-started/install)
- Official doc (model): [Continue - DeepSeek](https://docs.continue.dev/customize/model-providers/deepseek)

Prerequisite:
- Visual Studio Code
- A Chinese mobile phone number (the Chinese mainland)

## Set up step by step

Search and install VSCode extension "Continue".

Open Continue in side bar and click "Get started using our API keys", and allow it to sign in with GitHub account.

Log in [deepseek](https://www.deepseek.com/) and create an API Key

Back to VSCode and open Continue in side bar again, click the gear symbol to open config file of Continue, and add DeepSeek-V3 in models:

```json
{  
	"title": "DeepSeek-V3",  
	"provider": "deepseek",  
	"model": "deepseek-chat",  
	"apiKey": "[API_KEY]"  
},
```

Also, change the "tabAutocompleteModel" part:
```json
"tabAutocompleteModel": {
    "title": "DeepSeek-V3",
    "provider": "deepseek",
    "model": "deepseek-chat",
    "apiKey": "[API_KEY]"
},
```

## Test

Try it in VSCode.