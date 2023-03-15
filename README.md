# Danis³h Capsules on the Frontend
**Danis³h Capsules** can be deployed *anywhere*.

Here's how they are deployed on the Front End.

## Danis³h Capsule Standard Text Notation
**Standard Text Notation** for a capsule looks like this:

    Capsule_Examples:::CX_My_Capsule::Season:Spring##time:evening
    
_____

## Danis³h Capsule Reference on the Frontend
On the *Front End*, within an HTML or SVG Document, the corresponding **Danis³h Capsule Reference** looks like this:

```html

<!--[<CX_My_Capsule (Capsule_Examples) Season="Spring" time="evening">]-->
```

**N.B.** Note that there is *more* to this **Capsule Reference** than there appears to be:

 1) the reference contains an *implicit* inline **CapsuleManifest**:

     `[#][Markup="CX_My_Capsule", Styles="CX_My_Capsule", Scripts="CX_My_Capsule", Data="CX_My_Capsule"]`
     
 2) the reference *also* contains an *implicit* **Named Cell** (which essentially acts as a *PrimeCell*):
     
     `[@]CX_My_Capsule` // *points to:* `/.assets/capsules/cx-my-capsule/code/markup/cx-my-capsule--html.json`
______

## Creating a Danis³h Capsule Reference on the Frontend
A **Danis³h Capsule Reference** on the front-end may be created in javascript using *either* of two approaches.

 - via a ***Capsule Reference Literal*** e.g. `[<CX_My_Capsule (Capsule_Examples) Season="Spring" time="evening">]`
 - via a ***Capsule Reference Object***

### Capsule Reference Literal

A single line of JavaScript will create a **Danis³h Capsule Reference** from a ***Capsule Reference Literal***:

```js
const myCapsuleReference = document.createComment(
  '[<CX_My_Capsule (Capsule_Examples) Season="Spring" time="evening">]'
);
```

### Capsule Reference Object

***Capsule Reference Objects*** present the information from the **standard notation** as a JavaScript object.

Note that, with the (current) exception of the Danis³h entry:

    "Capsule": true
    
this JS object _corresponds exactly_ to its Capsule declaration equivalent within a hardcoded Danis³h Markup Cell.

Each can be transformed into the other via the methods `JSON.parse()` and `JSON.stringify()`.

#### Example 1:

    Capsule_Examples:::CX_My_Capsule

becomes:

```js
let myCapsuleReferenceObject = {
  CapsuleName: 'CX_My_Capsule',
  CapsulePublisher: 'Capsule_Examples'
};
```
or, including all keys:

```js
let myCapsuleReferenceObject = {
  CapsuleName: 'CX_My_Capsule',
  CapsulePublisher: 'Capsule_Examples',
  CapsuleImprint: [],
  StrongModifiers: [],
  classList: [],
  attributes: {},
  dataSet: {},
  Directives: {},
  LightModifiers: []
};
```

#### Example 2:

    Capsule_Examples:::CX_My_Capsule::Season:Spring##time:evening
    
becomes:


```js
let myCapsuleReferenceObject = {
  CapsuleName: 'CX_My_Capsule',
  CapsulePublisher: 'Capsule_Examples',
  StrongModifiers: {Season: 'Spring'},
  LightModifiers: {time: 'evening'}
};
```
or, including all keys:

```js
let myCapsuleReferenceObject = {
  CapsuleName: 'CX_My_Capsule',
  CapsulePublisher: 'Capsule_Examples',
  CapsuleImprint: [],
  
  StrongModifiers: {
    Season: 'Spring'
  },
  
  classList: [],
  attributes: {},
  dataSet: {},
  Directives: {},
  
  LightModifiers: {
    time: 'evening'
  }
};
```

#### Example 3:

    Ash:::Standard_UI:::TouchScreen:::Ash_Toggle_Input::Position:Off++class:test-class-1:test-class-2:test-class-3:test-class-4++test-attribute-1:test-value-1++test-attribute-2:test-value-2++test-attribute-3:test-attribute-3++test-attribute-4:++test-attribute-5++data-test-data-1:test-value-1++data-test-data-2:test-value-2++data-test-data-3:data-test-data-3++data-test-data-4:++data-test-data-5++data-test-data-6&&pagecontext:pagefix&&settingslisted:(StrongModifiers|classList|attributes|dataSet|Directives|LightModifiers)##theme:light

becomes:

```js
let myCapsuleReferenceObject = {
  CapsuleName: 'Ash_Toggle_Input',
  CapsulePublisher: 'Ash',
  CapsuleImprint: ['Standard_UI', 'TouchScreen'],
  
  StrongModifiers: {
    Position: 'Off'
  },
  
  classList: [
    'test-class-1',
    'test-class-2',
    'test-class-3',
    'test-class-4'
  ],
  
  attributes: {
    'test-attribute-1': 'test-value-1'
    'test-attribute-2': 'test-value-2'
    'test-attribute-3': 'test-attribute-3'
    'test-attribute-4': '',
    'test-attribute-5': '⊤'
  },
  
  dataSet: {
    'test-data-1': 'test-value-1',
    'test-data-2': 'test-value-2',
    'test-data-3': 'data-test-data-3'
    'test-data-4': '',
    'test-data-5': '⊤',
    'test-data-6': '⊤'
  },
  
  Directives: {
    pagecontext: 'pagefix',
    settingslisted: [
      StrongModifiers,
      classList,
      attributes,
      dataSet,
      Directives,
      LightModifiers
    ]
  },
  
  LightModifiers: {
    theme: 'light'
  }
};
```
