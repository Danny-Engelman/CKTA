# CKTA - Clark Kents' TypeScript Adventures

*These code examples are mainly taken from [SPFx Examples](https://github.com/SharePoint/sp-dev-fx-webparts) submitted by MVPs and others*

### Are you Superman?

You might **feel like Superman** with TypeScript, but if you take off your cape, **you are coding JavaScript** ES6  
And if you don't master ES6 you will still be writing oldskool ES3, long and error prone code  
These are live examples where Clark Kent refactored Superman code  

*All code fragments are for educational purposes, to explain ES6 concepts, and are not necessarily production ready code*

*Links to Original code can be broken*

*If you see your own code and feel ashamed. Good! You were writing oldskool ES3 code.*

![](http://i.imgur.com/1xT1HKV.jpg)


<hr>

# Forget For!

A For loop and break to find something in an Array should be something of the past

### [Original code:](https://github.com/waldekmastykarz/spfx-react-fabric-trendinginthissite/blob/master/src/webparts/trendingInThisSite/components/TrendingInThisSite.tsx)

```typescript
    private getValueFromResults(key: string, results: ISearchResultValue[]): string {
      let value: string = '';

      if (results != null && results.length > 0 && key != null) {
        for (let i: number = 0; i < results.length; i++) {
          const resultItem: ISearchResultValue = results[i];
          if (resultItem.Key === key) {
            value = resultItem.Value;
            break;
          }
        }
      }

      return value;
    }
```

### Clark Kents' code:

```typescript
    private getValueFromResults(key: string, results: ISearchResultValue[]): string {
      let item = results.find( result => result.Key === key );
      return item ? item.Value : 'empty string';
    }
```

<hr>

# .map() and .reduce() methods are powerful! 

And easily replace the too often used (or only one learned) .forEach method

### [Original Code:](https://github.com/SharePoint/sp-dev-fx-webparts/pull/219/files#diff-23)

```js
  .then((response: { PrimaryQueryResult: IPeopleDataResult }): void => {
          let relevantResults: any = response.PrimaryQueryResult.RelevantResults;
          let resultCount: number = relevantResults.TotalRows;
          let people = [];
          let persona: IPersonaProps = {};
          if (resultCount > 0) {
            relevantResults.Table.Rows.forEach(function (row) {
              row.Cells.forEach(function (cell) {
                //person[cell.Key] = cell.Value;
                if (cell.Key === 'JobTitle')
                  persona.secondaryText = cell.Value;
                if (cell.Key === 'PictureURL')
                  persona.imageUrl = cell.Value;
                if (cell.Key === 'PreferredName')
                  persona.primaryText = cell.Value;
              });
              people.push(persona);
            });
          }
          resolve(people);
        }, (error: any): void => {
          reject(this._peopleList = []);
        }));
```

### Clark Kents' code:

```typescript
.then((response: { PrimaryQueryResult: IPeopleDataResult }): void => {
          resolve(response.PrimaryQueryResult.RelevantResults.Table.Rows.map(row =>
            row.Cells.reduce( (persona, cell) => {
                if (cell.Key === 'JobTitle') persona.secondaryText = cell.Value;
                if (cell.Key === 'PictureURL') persona.imageUrl = cell.Value;
                if (cell.Key === 'PreferredName') persona.primaryText = cell.Value;
                return persona;
            }, {}) // start with empty {} persona object
        ));
        }, (error: any): void => reject(this._peopleList = []));
```

<hr>

# Let your code _flow_ logical

Resolve and Returns statements, it is hard to see the flow of this functions

### [Original code:](https://github.com/davidhartman/sp-dev-fx-webparts/blob/9fedac83a71499b25bf624087003e9e17f27b9a9/samples/angular-msgraph/setBaseUrlTask.js)

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


### Clark Kents' code:

```js
    return new Promise((resolve, reject) => {
        function taskerror(err){
            error(err);
            reject(err);
        }

      config.uniqueTasks
      .filter( task => task.name === 'writemanifests')
      .forEach( task => {
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
 
  }
```

<hr>

# Again, For loops can be replaced with (many) Array functions

Objective: Fade in (set opacity) N of M DOM elements

![](http://i.imgur.com/EOCGbZt.jpg)

### [Original code:](https://github.com/sebastienlevert/spfx-riot-list/blob/fd1d77933ea0450dbd771e0a2f388decec627bd2/src/office-ui-fabric-riot/Spinner/Spinner.ts)

*see source for all code,  with some more refactoringit can be reduced to half the size*

```typescript
  private _initializeOpacities(): void {
    let i = 0;
    let j = 1;
    let opacity;
    this.fadeIncrement = 1 / this.numCircles;
    for (i; i < this.numCircles; i++) {
      let circleObject = this.circleObjects[i];
      opacity = (this.fadeIncrement * j++);
      this._setOpacity(circleObject.element, opacity);
    }
  }
  
  private _setOpacity(element: HTMLElement, opacity: number): void {
    element.style.opacity = opacity.toString();
  }  
```

### Clark Kents'code:

```typescript
  private _initializeOpacities(): void {
    [...this.circleObjects].some( (circle, idx) => {
      circle.style.opacity = ++idx / this.numCircles;
      return idx === this.numCircles;  //end .some() method when true
    });
  }
```

some() executes the callback function once for each element present in the array until it finds one where callback returns a truthy value (a value that becomes true when converted to a Boolean). **If such an element is found, some() immediately returns true**. Otherwise, some() returns false.

<hr>

# Make you code read like it flows!

Traditionaly we had to declare strings etc. up front before we could use them.  
The one drawback is that your code no longer reads the same as it flows  
With some careful refactoring you can not only save on used variables (and thus performance) but also shorten your code

### [Original Code:](https://github.com/SharePoint/sp-dev-fx-webparts/blob/c4e72f15840db659d973d32bf348d03d09bd6b8b/samples/aad-api-spo-cookie/src/webparts/latestOrders/LatestOrdersWebPart.ts)

```typescript
private renderData(): void {
    if (this.orders === null) {
      return;                     //CK: Why does a void function return an undefined value?
    }
    let ordersString: string = ""; //CK:String concation is oldskool since Microsoft (finally) implemented the .map /.reduce methods in IE9
    this.orders.forEach(order => {
      ordersString += `
<tr>
  <td class="${styles.number}">${order.id}</td>
  <td class="${styles.number}">${new Date(order.orderDate).toLocaleDateString()}</td>
  <td>${order.region.toString()}</td>
  <td>${order.rep}</td>
  <td>${order.item}</td>
  <td class="${styles.number}">${order.units}</td>
  <td class="${styles.number}">$${order.unitCost.toFixed(2)}</td>
  <td class="${styles.number}">$${order.total.toFixed(2)}</td>
</tr>`;
    });
    const table: Element = this.domElement.querySelector(".data");
    table.removeAttribute("style");
    table.querySelector("tbody").innerHTML = ordersString;
  }
```
  
### Clark Kents' code:

```typescript
private renderData(): void {
    if (this.orders) {
        const className = styles.number; // GLOBAL!
        const table: Element = this.domElement.querySelector(".data");
        table.removeAttribute("style");  //could use standard HTML5 'hidden' attribute instead of whole Style
        table.querySelector("tbody").innerHTML = this.orders.map(order =>
                `<tr>
                    <td class="${className}">${order.id}</td>
                    <td class="${className}">${new Date(order.orderDate).toLocaleDateString()}</td>
                    <td>${order.region.toString()}</td>
                    <td>${order.rep}</td>
                    <td>${order.item}</td>
                    <td class="${className}">${order.units}</td>
                    <td class="${className}">$${order.unitCost.toFixed(2)}</td>
                    <td class="${className}">$${order.total.toFixed(2)}</td>
                  </tr>`
            ).join('');
    }
}
```

<hr>

# Make you code read like it flows! - The Sequel


### [Original code:](https://github.com/SharePoint/sp-dev-fx-webparts/pull/205/files#diff-673ca12ef1dd8a20bb7f0b211df4fce1)

```TypeScript
 public componentWillReceiveProps(newProps: IPanelProps) {
     if (newProps.isOpen === this.props.isOpen)
         return;

	 clearTimeout(this._onCloseTimer);

     if (newProps.isOpen) {
         if (!this.state.isOpen) {
             this.setState({
                 isOpen: true
             });
         }
         else {
             this.setState({
                 isVisible: true
             });
         }
     }

     if (!newProps.isOpen && this.state.isOpen) {
         this._close();
     }
 }
```

Function returns ``undefined`` if it is not the currect property-pane, alter the code to only execute the body

Negations ``!this.state.isOpen`` are hard to read, since there IS an else branch, the then/else can be reversed

**The first condition of the last (3rd) IF is actually the else branch, so no need to check something twice**

### Refactored code

```TypeScript
public componentWillReceiveProps(newProps: IPanelProps) {
    if (newProps.isOpen !== this.props.isOpen) {
        clearTimeout(this._onCloseTimer);
        if (newProps.isOpen) {
            if (this.state.isOpen)
                this.setState({
                    isVisible: true
                });
            else
                this.setState({
                    isOpen: true
                });
        } else if (this.state.isOpen) {
            this._close();
        }
    }
}
```

<hr>

# Condense that code! It will minify better! And is maintainable

*Note: In the above example you can replace 'this' with a constant to make it minifiy better!*

### [Original code:](https://github.com/SharePoint/sp-dev-fx-webparts/pull/231/files#diff-56)

```javascript
private setButtonsEventHandlers(): void {
	const webPart: JsomCrudWithBatchWebPart = this;
	this.domElement.querySelector('button.create-Button').addEventListener('click', () => { webPart.createItem(); });
	this.domElement.querySelector('button.readall-Button').addEventListener('click', () => { webPart.readItems(); });
	this.domElement.querySelector('button.update-Button').addEventListener('click', () => { webPart.updateItem(); });
	this.domElement.querySelector('button.delete-Button').addEventListener('click', () => { webPart.deleteItem(); });
}
```

### Clark Kents' code:

```javascript
private setButtonsEventHandlers(): void {
    const webPart: JsomCrudWithBatchWebPart = this;
    let addClick = (crud,func)=> webPart.domElement.querySelector(`button.${crud}-Button`).addEventListener('click', () => func);
    addClick('create', webPart.createItem );
    addClick('readall', webPart.readItems );
    addClick('update', webPart.updateItem );
    addClick('delete', webPart.deleteItem );
}

```

# By now you should understand the For loop is eval

### [Original code:](https://github.com/SharePoint/sp-dev-fx-webparts/pull/231/files#diff-56)

```javascript
private updateItemsHtml(items: IListItem[]): void {
	const itemsHtml: string[] = [];
	for (let i: number = 0; i < items.length; i) {
		itemsHtml.push(`<li>${items[i].Title} (${items[i].Id})</li>`);
	}

	this.domElement.querySelector('.items').innerHTML = itemsHtml.join('');
}
```

### Clark Kents' code:

```javascript
private updateItemsHtml(items: IListItem[]): void {
	this.domElement.querySelector('.items').innerHTML = 
		items.map(item=> `<li>${items.Title} (${items.Id})</li>` ).join('');
}
```

# Learn .map

### [Original code:](https://github.com/SharePoint/sp-dev-fx-webparts/pull/231/files#diff-56)

```javascript
var stringOfId='';
data.forEach(element => {
	stringOfId+=element.Id +','; // this will add a trailing ,
});
beforeCallback.updateStatus(`Item with IDs: ${stringOfId} successfully updated`);
```

### Clark Kents' code:

```javascript
var stringOfId=data.map(element => element.Id).join(',');
beforeCallback.updateStatus(`Item with IDs: ${stringOfId} successfully updated`);
```
	
<hr>

# When you hit Ctrl-C/V alarmbells should go off

Classic example where TypeScript strong Typing forces you to write duplicate code

Abstract endpoint calling into one method..  
just think how many references you need to check when the HttpClient is upgraded to V2

### [Original Code:](https://github.com/eoverfield/sp-dev-fx-webparts/blob/b3d3ba9ba7e35cb97f9cc6128ecdba216058f733/samples/react-custompropertypanecontrols/src/webparts/dropdownWithRemoteData/services/ListService.ts)

```typescript
public getLists(): Promise<IList[]> {
    var httpClientOptions : ISPHttpClientOptions = {};

httpClientOptions.headers = {
    'Accept': 'application/json;odata=nometadata',
    'odata-version': ''
};

return new Promise<IList[]>((resolve: (results: IList[]) => void, reject: (error: any) => void): void => {
    this.context.spHttpClient.get(this.context.pageContext.web.serverRelativeUrl + `/_api/web/lists?$select=id,title`,
        SPHttpClient.configurations.v1,
        httpClientOptions
    )
        .then((response: SPHttpClientResponse): Promise<{ value: IList[] }> => {
            return response.json();
        })
        .then((lists: { value: IList[] }): void => {
            resolve(lists.value);
        }, (error: any): void => {
            reject(error);
        });
});
}

public getList(listName: string): Promise<IListItem[]> {
    var httpClientOptions : ISPHttpClientOptions = {};

httpClientOptions.headers = {
    'Accept': 'application/json;odata=nometadata',
    'odata-version': ''
};

return new Promise<IListItem[]>((resolve: (results: IListItem[]) => void, reject: (error: any) => void): void => {
    this.context.spHttpClient.get(this.context.pageContext.web.serverRelativeUrl + `/_api/web/lists('${listName}')/items?$select=Id,Title`,
        SPHttpClient.configurations.v1,
        httpClientOptions
    )
        .then((response: SPHttpClientResponse): Promise<{ value: IListItem[] }> => {
            return response.json();
        })
        .then((listItems: { value: IListItem[] }): void => {
            resolve(listItems.value);
        }, (error: any): void => {
            reject(error);
        });
});
}
```


### Clark Kents' code:


```typescript
public getEndpoint(endpoint:string): Promise<IList[]> {
    var httpClientOptions : ISPHttpClientOptions = {};
    httpClientOptions.headers = {
        'Accept': 'application/json;odata=nometadata',
        'odata-version': ''
    };
    const context=this.context;
    return new Promise((resolve: (results: any) => void, reject: (error: any) => void): void => {
        context.spHttpClient.get(context.pageContext.web.serverRelativeUrl + endpoint,
            SPHttpClient.configurations.v1,
            httpClientOptions
        ).then(response  => response.json())
            .then(listdata => resolve(listdata.value))
            .catch( error => reject(error));
    })
}

public getLists(): Promise<IList[]> {
    return getEndpoint('/_api/web/lists?$select=id,title');
}

public getList(listName: string): Promise<IListItem[]> {
    return getEndpoint(`/_api/web/lists('${listName}')/items?$select=Id,Title`);
}

```

<hr>

![](http://i.imgur.com/l65o93Z.jpg)

