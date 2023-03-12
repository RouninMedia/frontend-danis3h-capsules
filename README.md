# Danis³h Capsules on the Frontend
**Danis³h Capsules** can be deployed *anywhere*.

Here's how they are deployed on the Front End.

## Danis³h Capsule Standard Notation
Standard notation for a capsule might be:

    Capsule_Examples:::CX_My_Capsule::Season:Spring##time:evening
    
_____

## Danis³h Capsule Reference on the Frontend
On the *Front End*, within an HTML Document, the corresponding **Danis³h Capsule Reference** looks like this:

```html
<!--<[ <CX_My_Capsule (Capsule_Examples) Season="Spring" time="evening"> ]>-->
```
______

## Creating a Danis³h Capsule Reference on the Frontend
A **Danis³h Capsule Reference** on the front-end may be created in javascript using *either* of two approaches.

 - via a ***Capsule Reference Literal***
 - via a ***Capsule Reference Object***

### Capsule Reference Literal

A single line of JavaScript will create a **Danis³h Capsule Reference** from a ***Capsule Reference Literal***:

```js
const myCapsuleReference = document.createComment(
  '<[ <CX_My_Capsule (Capsule_Examples) Season="Spring" time="evening"> ]>'
);
```

### Capsule Reference Object



or by using a ***Capsule Reference Object***, like this:

```js
let ashivaMenuNavigation = {
  publisher: 'Ash',
  imprint: [],
  capsuleName: 'Ash_Toggle_Input',
  strongModifiers: [],
  attributes: {
    position: 'on'
  },
  dataset: {},
  directives: {},
  lightModifiers: []
};
```
