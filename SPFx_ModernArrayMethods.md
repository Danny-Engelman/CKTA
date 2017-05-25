https://github.com/SharePoint/sp-dev-fx-webparts/pull/219/files#diff-23

The .map() and .reduce() methods are very powerful 

And easily replace the too often used (or only one learned) .forEach method

### Original Code:

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
            row.Cells.reduce(function (persona, cell) {
                if (cell.Key === 'JobTitle') persona.secondaryText = cell.Value;
                if (cell.Key === 'PictureURL') persona.imageUrl = cell.Value;
                if (cell.Key === 'PreferredName') persona.primaryText = cell.Value;
            }, {}) // start with empty {} persona object
        ));
        }, (error: any): void => {
          reject(this._peopleList = []);
        }));
```
