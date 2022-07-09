[Deployment of static apps via azure cli](https://docs.microsoft.com/en-us/azure/static-web-apps/get-started-cli?tabs=vanilla-javascript)

Necessary changes in the created GH action:

```yaml
###### Repository/Build Configurations - These values can be configured to match your app requirements. ######
app_location: "/" # App source code path
api_location: "" # Api source code path - optional
output_location: "public" # Built app content directory - optional
app_build_command: "yarn build"
###### End of Repository/Build Configurations ######
```

Add `staticwebapp.config.json` file in the root directory:

```json
{
  "responseOverrides": {
    "404": {
      "rewrite": "/index.html",
      "statusCode": 200
    }
  }
}
```
