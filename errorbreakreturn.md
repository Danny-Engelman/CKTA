#If there a re multiple exits, where do we go?

[Source](https://github.com/davidhartman/sp-dev-fx-webparts/blob/9fedac83a71499b25bf624087003e9e17f27b9a9/samples/angular-msgraph/setBaseUrlTask.js)

#### Original code

```js

    return new Promise((resolve, reject) => {

      var writeManifestsTask = undefined;
      for (var i = 0; i < config.uniqueTasks.length; i++) {
        if (config.uniqueTasks[i].name === 'writemanifests') {
          writeManifestsTask = config.uniqueTasks[i];
          break;
        }
      }

      if (!writeManifestsTask) {
        var errorMsg = 'Couldn\'t retrieve the writeManifests task.';
        error(errorMsg);
        reject(errorMsg);
        return;
      }

      var url = config.production ? `${writeManifestsTask.taskConfig.cdnBasePath}` : `${writeManifestsTask.taskConfig.debugBasePath}dist/`;

      var webPartCodePath = `${config.libFolder}/webparts/angularMsGraph/AngularMsGraphWebPart.js`;
      var webPartCode = fs.readFileSync(webPartCodePath, 'utf-8');
      webPartCode = webPartCode.replace('$BASEURL$', url);
      fs.writeFile(webPartCodePath, webPartCode, (err) => {
        if (err) {
          error(err);
          reject(err);
          return;
        }

        log(`Base URL successfully set to ${url}`);
        resolve();
      });
    });
  }
```


#### Clark Kents code:

```js

    return new Promise((resolve, reject) => {
      config.uniqueTasks
      .filter( (task) => task.name==='writemanifests')
      .forEach( (task) => {
          let url =  config.production ? task.taskConfig.cdnBasePath : task.taskConfig.debugBasePath + "dist/";
          let webPartCodePath = config.libFolder + "/webparts/angularMsGraph/AngularMsGraphWebPart.js";
          let webPartCode = fs.readFileSync( webPartCodePath , 'utf-8' ).replace('$BASEURL$', url);
          fs.writeFile(webPartCodePath, webPartCode, (err) => {
            if (err) taskerror( err );
            log(`Base URL successfully set to ${url}`);
            resolve();
          });
      });
  
    taskerror("Couldn't retrieve the writeManifests task.");
 
    function taskerror(err){
        error(err);
        reject(err);
    }
  }
```
