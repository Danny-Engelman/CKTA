source:
https://github.com/sebastienlevert/spfx-riot-list/blob/fd1d77933ea0450dbd771e0a2f388decec627bd2/src/office-ui-fabric-riot/Spinner/Spinner.ts

Objective: Fade in (set opacity) N of M DOM elements

![](http://i.imgur.com/EOCGbZt.jpg)

### Original code:

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
