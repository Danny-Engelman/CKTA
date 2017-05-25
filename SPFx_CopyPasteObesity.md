Source:
https://github.com/eoverfield/sp-dev-fx-webparts/blob/b3d3ba9ba7e35cb97f9cc6128ecdba216058f733/samples/react-custompropertypanecontrols/src/webparts/dropdownWithRemoteData/services/ListService.ts

Classic example where TypeScript strong Typing forces you to write duplicate code

Abstract endpoint calling into one method..  
just think how many references you need to check when the HttpClient is upgraded to V2

### Original Code:

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

*refactored by hand, not tested*

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