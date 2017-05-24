source:
https://github.com/waldekmastykarz/spfx-react-fabric-trendinginthissite/blob/master/src/webparts/trendingInThisSite/components/TrendingInThisSite.tsx

A For loop and break to find something in an Array should be something of the past

### Original code:

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
      let item = results.find( result => return result.Key === key );
      return item ? item.Value : 'empty string';
    }
```
