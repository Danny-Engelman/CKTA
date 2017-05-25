source:
https://github.com/sebastienlevert/spfx-riot-list/blob/fd1d77933ea0450dbd771e0a2f388decec627bd2/src/office-ui-fabric-riot/Spinner/Spinner.ts

Objective: Fade in (set opacity) N of M DOM elements

![](http://i.imgur.com/EOCGbZt.jpg)

### Original code:

*see source or all code,  with some more refactoringit can be reduced to half the size*

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
```

### Clark Kents'code:

```typescript
  private _initializeOpacities(): void {
    [...this.circleObjects].some((circle, idx) => {
      circle.style.opacity = ++idx / numCircles;
      return idx == numCircles;  //end .some() method when true
    });
  }
```
