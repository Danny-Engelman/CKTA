[source](https://github.com/SharePoint/sp-dev-fx-webparts/pull/205/files#diff-673ca12ef1dd8a20bb7f0b211df4fce1)

### Original code

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


### Refactored code
