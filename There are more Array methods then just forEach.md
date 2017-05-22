https://github.com/SharePoint/sp-dev-fx-webparts/pull/219/files#diff-23

The .map() and .reduce() methods are very powerful 

And easily replace the too often used (or only one learned) .forEach method

### Original Code


```js
function getPeople(response) {
    let relevantResults = response.PrimaryQueryResult.RelevantResults;
    let resultCount = relevantResults.TotalRows;
    let people = [];
    let persona = {};
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
    return (people);
}
```

### Clark Kents' code

```js
function getPeople(response) {
    return response.PrimaryQueryResult.RelevantResults.Table.Rows.map(function (row) {
        return row.Cells.reduce(function (persona, cell) {
            if (cell.Key === 'JobTitle') persona.secondaryText = cell.Value;
            if (cell.Key === 'PictureURL') persona.imageUrl = cell.Value;
            if (cell.Key === 'PreferredName') persona.primaryText = cell.Value;
        }, {}); // start with empty {} persona object
    })
}
```
 
