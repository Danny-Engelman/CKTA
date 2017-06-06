# CKTA - Clark Kents' TypeScript Adventures

You might feel Superman with TypeScript, but if you take off your cape, you **are** coding JavaScript ES6  
And if you don't master ES6 you will still be writing oldskool ES3, long and error prone code  
These are live examples where Clark Kent refactored Superman code  

*All code fragments are for educational purposes, to explain ES6 concepts, and are not necessarily production ready code*

![](http://i.imgur.com/1xT1HKV.jpg)

<hr>

![](http://i.imgur.com/l65o93Z.jpg)

<hr>

# Condense that code! It will minify better! And is maintainable

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

