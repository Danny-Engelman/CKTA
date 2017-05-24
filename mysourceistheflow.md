
Source:

https://github.com/SharePoint/sp-dev-fx-webparts/blob/c4e72f15840db659d973d32bf348d03d09bd6b8b/samples/aad-api-spo-cookie/src/webparts/latestOrders/LatestOrdersWebPart.ts

### Original Code:

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
  ```ts
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
