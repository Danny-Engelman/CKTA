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

Function returns ``undefined`` if it is not the currect property-pane, alter the code to only execute the body

Negations ``!this.state.isOpen`` are hard to read, since there IS an else branch, the then/else can be reversed

The first condition of the last if is actually the else branch, so no need to check something twice

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
