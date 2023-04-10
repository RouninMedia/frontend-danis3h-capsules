# Danis³h Capsules on the Frontend
**Danis³h Capsules** can be deployed *anywhere*.

Here's how they are deployed on the Front End.

## Danis³h Capsule Attribute Notation
The **Attribute Notation** uses continuous plaintext to describe a capsule, like this:

    Capsule_Examples:::CX_My_Capsule::Season:Spring||time:evening
    
This reveals the following information:

 - **CapsulePublisher:** `Capsule_Examples`
 - **CapsuleImprint:** none
 - **CapsulePublisher ShortName:** `CX`
 - **CapsuleName:** `CX_MyCapsule`
 - **StrongModifiers:** `{Season: 'Spring'}`
 - **LightModifiers:** `{time: 'evening'}`

_____

## Aside: To match the format / capabilities of Danis³h Capsules on the Frontend, there is a new format for Danis³h Capsules on the Server Side

To complement the new front-end reference syntax...

_______

## First Draft *(deprecated)*

  - Redesigned **CodeSheet References** in **CapsuleManifests**
      1. There are now *three* parameters (instead of two):
         1. *CodeSheet Name*
         2. *CodeSheet Source FileName* + *CodeSheet Source FileType*
         3. *CodeSheet Source FilePath*
      2. Of these, *if* the *CodeSheet Name* ends in `Styles`:
         1. the *FileType* will be assumed to be `__CSS`
         2. the *FilePath* assumed to be `Styles`
      3. If the *FileType* is `.scss` and / or the *FilePath* is `New_Styles___2023___Feb` then one and / or both will need to be explicitly stated
      4. Alternatively, if the *CodeSheetName* does not end in a recognised suffix, then *FileType* and *FilePath* will need to be explicitly stated
      5. **N.B.** the *CodeSheetName* (and *CodeSheet Filename*) are *not obliged* to end in a recognised suffix; but the *Capsule `CodeCell` Name* is
      6. To clarify terminology:
         1. the **CapsuleManifest** imports the named *CodeSheet Source File* (which usually includes a suffix and may include a filepath or filetype)
         2. the **CapsuleManifest** names and builds the *CodeSheet* (from the static or dynamic *CodeSheet SourceFile* and any *Transformers*)
         3. the **CapsuleManifest** saves the named, built *CodeSheet* as a namespaced `CodeCell`
         4. the **CapsuleManifest** locks the `CodeCell` into the **Capsule**
_____

## Second Draft

 - Decided, on reflection, that the redesigned **CodeSheet References** in **CapsuleManifests** were too complex and needed to be re-simplified:
     1. Resolved that all assumed / implicit / hinted-at data (e.g. *FilePath* / *FileType* etc.) needs to be explicitly written out
     2. Instead of (*CellAlias*, *FileName* + *FileType*, *FilePath*), changed the three new parameters to (*FileName*, *FileType*, *FilePath*)
     3. Permitted the third parameter to be optional, given that there may not be a *FilePath*
     4. Derived the *CellAlias* automatically by removing any *CapsuleName* prefix from the start and adding a *CellType* suffix to the end
     5. Thus kept the feature that every Cell added to a Capsule must have a unique CellName ending in `Markup`, `Styles`, `Scripts`, `Vectors` or `Data`
     6. To bring everything into line with front-end inline Capsule Manifests, turned `$Capsule['Markup']`, `$Capsule['Styles']` etc. into `arrays` containing CapsuleCells, each identified by its own *CapsuleEntry* key; e.g. `$Capsule['Styles'] = $Button_Styles` becomes `$Capsule['Styles']['Button_Styles'] = $Button_Styles`
     7. Required that two *CapsuleCells* of the same *CellType* MAY NOT have the same name, regardless of *FilePath* and *FileType* - this is so every *CellAlias* remains unique

_____

## Notes:

SIGNIFICANT STEP FORWARD: In front-end capsule references, I wondered if I could use something similar to theCellName Reference syntax used when inspecting individual Cells (like: <Markup[@]SB_nextPage>). But then I realised that this might require a block of multiple references (eg. one for markup, one for styles etc.) or else redundant loading, before seeing that a much more configurable shorthand would be an Inline CapsuleManifest (using xHan) which plays the same role on the front-end as played by the actual CapsuleManifest file on the server-side filesystem for when Capsules are invoked server-side

_____

## Further Notes

   - Changed `CustomComponents` to **Namespace-Suffixed CodeCells** to standardise "named cells" & enable formerly unavailable functionality
     
     e.g. *before*, a Capsule might have had `['Markup']`, `['Styles']`, `['Scripts']` and then `['Markup'['CustomComponents']['Menu']`
          *but now*, a Capsule can have `['Markup']['Button_Markup']`, `['Markup']['Menu_Markup']`, `['Styles']['Button_Styles']`, `['Styles']['Menu_Styles']`, `['Scripts']['Button_Scripts']`, `['Scripts']['Menu_Scripts']`, `['Vectors']['Vectors']`
    
     Amongst other things, this enables a much more flexible, more sophisticated setup, where, what used to be two separate Capsules:

       - `Ashiva_Open_Control_Pad` for the inital button; and then
       - `Ashiva_Control_Pad` for the asynchronously loaded interface
      
     can now be a *single* Capsule, where, e.g.:

       - `Ashiva_Menu` first loads the intial `Button_Markup`, `Button_Styles` and `Button_Scripts` *CapsuleCells* (according to the initial, static, server-side CapsuleManifest); and later
       - when the user interacts with the button, the `Button_Scripts` *CapsuleCell* of `Ashiva_Menu` dynamically inserts a `CapsuleReference` into the page (which contains its own inline `CapsuleManifest`) and then parses that `CapsuleReference` which leads to the asynchronous loading of several new `CapsuleCells` from the *same Capsule* (`CapsuleCells` which weren't initially loaded): *menu markup*, *menu styles* and *menu scripts*

  Not least, this can take a lot of pressure off the initial page load - the precise issue which led to `Ashiva_Open_Control_Pad` and `Ashiva_Control_Pad` being separated in the first place.
  
_____

## Danis³h Capsule Reference on the Frontend
On the *Front End*, within an HTML or SVG Document, the **Danis³h CapsuleReference** described by the *Attribute Notation* above would look like this:

```html
<!--[<CX_My_Capsule (Capsule_Examples) Season="Spring" time="evening">]-->
```

**N.B.** Importantly this **CapsuleReference** contains *two more* non-visible but implicit pieces of data:

 1) Firstly, the reference contains an *implicit* inline **CapsuleManifest**:
 
   #### Shorthand
    [#][Markup, Styles, Scripts, Data]
    
   #### Longhand
    [#][Markup="CX_My_Capsule", Styles="CX_My_Capsule", Scripts="CX_My_Capsule", Data="CX_My_Capsule"]
     
 2) Secondly, the reference *also* contains an *implicit* **PrimeCell** (or single-item **PrimeCellSet**).

    The **PrimeCell** is generally the Cell (usually a `Markup` Cell or a `Vectors` Cell) upon which any Styles, Scripts and Data act, but is occasionally     represented by an actual `Data`, `Scripts` or `Styles` Cell. 

    These four **PrimeCell** references:
     
    - `[@]Markup`,
    - `[@]CX_My_Capsule`
    - `[@]Markup="CX_My_Capsule"`
    - `[@]Markup="CX_My_Capsule__HTML"`
   
    all point to this *relative-to-root URL* in **Ashiva**: `/.assets/capsules/cx-my-capsule/code/markup/cx-my-capsule--html.json`
    
Hence, written out in full, the **Danis³h Capsule Reference** above looks like this:

```html
<!--[
  <CX_My_Capsule (Capsule_Examples) Season="Spring" time="evening"

    [#][
      Markup="CX_My_Capsule__HTML",
      Styles="CX_My_Capsule__CSS",
      Scripts="CX_My_Capsule__JS",
      Data="CX_My_Capsule__JSON"
    ]

    [@]Markup="CX_My_Capsule__HTML"
  >
]-->
```

In **Attribute Notation** (continuous plaintext), the entire **CapsuleReference** above would be:

`Capsule_Examples:::CX_My_Capsule::Season:Spring||time:evening@@Markup:CX_My_Capsule__HTML##Markup:CX_My_Capsule__HTML##Styles:CX_My_Capsule__CSS##Scripts:CX_My_Capsule__JS##Data:CX_My_Capsule__JSON`

Though, normally, there's really *no need* to include the _Implicit Data_ and that's why it's enough to write:

    Capsule_Examples:::CX_My_Capsule::Season:Spring||time:evening
    
Not unusually, there's no need *even* to include any explicit **LightModifiers**, so the standard *Attribute Notation* would simply be:

    Capsule_Examples:::CX_My_Capsule::Season:Spring
    

### The `scan` Directive
The default **CellName** used in the implicit data is identical to the **CapsuleName** (`CX_My_Capsule`), the default can be changed via the `scan` **Directive**:

**e.g.**

```html
[&]scan="CX_Button"
```
will yield the following *implicit* inline **CapsuleManifest**:

    ```html
    [#][Markup="CX_Button", Styles="CX_Button", Scripts="CX_Button", Data="CX_Button"]
    ```

and the following *implicit* **PrimeCell**:

    - `[@]Markup="CX_Button"`

Thus, instead of:

    <!--[<CX_My_Capsule (Capsule_Examples) [@]CX_Button [#][Markup="CX_Button", Styles="CX_Button", Scripts="CX_Button"]>]-->
    
It will suffice to write:
    
    <!--[<CX_My_Capsule (Capsule_Examples) [&]scan="CX_Button">]-->

### Negation in front-end CapsuleReferences (`!`)
Since any omission to declare **PrimeCell** or **CapsuleManifest** values leads to default values being implied, we need an explicit way to indicate that values don't actually exist and are not being invoked at all.

In ***CapsuleReference** Syntax* a non-existent value is indicated via a `!` prefix.

This enables a **CapsuleReference** which explicitly has no **PrimeCell** to be written out like this:

```html
<!--[<CX_My_Capsule (Capsule_Examples) [!@]>]-->
```

If, instead, we wrote out:

```html
<!--[<CX_My_Capsule (Capsule_Examples)>]-->
```

this would be the equivalent of:

```html
<!--[<CX_My_Capsule (Capsule_Examples) [@]Markup="CX_My_Capsule__HTML">]-->
```

We can use the same negation syntax in an inline **CapsuleManifest**:

```html
<!--[<CX_My_Capsule (Capsule_Examples) [#][!Markup, Scripts="CX_My_Other_Capsule", !Data]>]-->
```

The line above references these two **CapsuleCells**, one explicitly, the other implicitly:

 - `Styles="CX_My_Capsule"`
 - `Scripts="CX_My_Other_Capsule"`


If, instead, we wrote out *only*:

```html
<!--[<CX_My_Capsule (Capsule_Examples) [#][Scripts="CX_My_Other_Capsule"]>]-->
```

this would be the equivalent of:

```html
<!--[<CX_My_Capsule (Capsule_Examples) [#][Markup="CX_My_Capsule", Styles="CX_My_Capsule", Scripts="CX_My_Other_Capsule", Data="CX_My_Capsule"]>]-->
```
    
______
    

### A short note about the differences between PrimeCells in *file-based* and *inline* CapsuleManifests
In inline **CapsuleManifests**, as in file-based **CapsuleManifests**, the **PrimeCell** will commonly have a value of _Markup_ or _Vectors_ or _Data_.

A key difference between the two, however, is that file-based **CapsuleManifests** are *free-floating*, standing alone from other files, while inline **CapsuleManifests** are declared within the context of an `HTML Document` or an `SVG Document`.

Therefore, unlike in file-based **CapsuleManifests**:

 - in an *HTML Document* a CapsuleReference with no declared **PrimeCell** will *assume* an implicit Markup **PrimeCell** (eg. `[@]Markup`)
 - in an *SVG Document* a CapsuleReference with no **PrimeCell** will *assume* an implicit Vectors **PrimeCell** (eg. `[@]Vectors`)

Consequently, in an inline **CapsuleManifest**, the **PrimeCell**  will need to be explicitly named, *only* if:

 1. something other than _Markup_ (in HTML documents); or
 2. something other than _Vectors_ (in SVG Documents); or
 3. has a *CellName* which doesn't echo the *CapsuleName*

That is, these three references (note the first example with no explicit **PrimeCellType**) are equivalent:
```html
<!--[<CX_My_Capsule (Capsule_Examples) Season="Spring" time="evening" [@]Button_Markup>]-->
<!--[<CX_My_Capsule (Capsule_Examples) Season="Spring" time="evening" [@]Markup="Button_Markup">]-->
<!--[<CX_My_Capsule (Capsule_Examples) Season="Spring" time="evening" [@]Markup="Button_Markup__HTML">]-->
```

And these three references (note the first example with no explicit **PrimeCellName**) are equivalent, too:

```html
<!--[<CX_My_Capsule (Capsule_Examples) Season="Spring" time="evening" [@]Data>]-->
<!--[<CX_My_Capsule (Capsule_Examples) Season="Spring" time="evening" [@]Data="CX_My_Capsule">]-->
<!--[<CX_My_Capsule (Capsule_Examples) Season="Spring" time="evening" [@]Data="CX_My_Capsule__JSON">]-->
```

And, finally, these four references (note the first example with no **PrimeCell** declaration at all) are equivalent:

```html
<!--[<CX_My_Capsule (Capsule_Examples) Season="Spring" time="evening">]-->
<!--[<CX_My_Capsule (Capsule_Examples) Season="Spring" time="evening" [@]Markup>]-->
<!--[<CX_My_Capsule (Capsule_Examples) Season="Spring" time="evening" [@]Markup="CX_My_Capsule">]-->
<!--[<CX_My_Capsule (Capsule_Examples) Season="Spring" time="evening" [@]Markup="CX_My_Capsule__HTML">]-->
```

Also note that, for the sake of brevity, if an inline **CapsuleManifest** is explicitly declared, the **PrimeCell** is implicitly included in that inline **CapsuleManifest**.

Further note that, if an inline **CapsuleManifest** *is* explicitly declared and a **PrimeCell** *isn't*, then the `Markup` or `Vectors` **PrimeCell** will be *assumed* to be the *first* `Markup` or `Vectors` Cell declared in the inline **CapsuleManifest**.

Thus all five of these references are functionally equivalent:

```html
<!--[<Ashiva_Control_Menu (Ashiva) [#][Markup="Button_Markup" Styles="Button_Markup", Scripts="Button_Markup"]>]-->
<!--[<Ashiva_Control_Menu (Ashiva) [@]Button_Markup [#][Styles="Button_Markup", Scripts="Button_Markup"]>]-->
<!--[<Ashiva_Control_Menu (Ashiva) [@]Markup="Button_Markup" [#][Styles="Button_Markup", Scripts="Button_Markup"]>]-->
<!--[<Ashiva_Control_Menu (Ashiva) [@]Button_Markup [#][Markup="Button_Markup" Styles="Button_Markup", Scripts="Button_Markup"]>]-->
<!--[<Ashiva_Control_Menu (Ashiva) [@]Markup="Button_Markup" [#][Markup="Button_Markup" Styles="Button_Markup", Scripts="Button_Markup"]>]-->
```

Each of which would be written like this in **Attribute Notation**:
    
    Ashiva:::Ashiva_Control_Menu##Markup:Button_Markup##Styles:Button_Markup##Scripts:Button_Markup
    Ashiva:::Ashiva_Control_Menu@@Button_Markup##Styles:Button_Markup##Scripts:Button_Markup
    Ashiva:::Ashiva_Control_Menu@@Markup:Button_Markup##Styles:Button_Markup##Scripts:Button_Markup
    Ashiva:::Ashiva_Control_Menu@@Button_Markup##Markup:Button_Markup##Styles:Button_Markup##Scripts:Button_Markup
    Ashiva:::Ashiva_Control_Menu@@Markup:Button_Markup##Markup:Button_Markup##Styles:Button_Markup##Scripts:Button_Markup

Finally, if a front-end **CapsuleReference** references a **Capsule** for which a file-based **CapsuleManifest** has already been declared during page load, it may reference the already-declared **CapsuleManifest** using the syntax `[#]` (or, if need be, the already-declared **PrimeCell** using the syntax `[@]`):

```html
<!--[<Ashiva_Control_Menu (Ashiva) [#]>]-->
```

The syntax above will invoke on the client-side the same capsule in the same configuration as any of these on the server-side:

```php
echo danis3hCapsule(${'<Ashiva_Control_Menu (Ashiva) [#]>'});
echo danis3hCapsule(${'<Ashiva_Control_Menu (Ashiva)>'});
echo danis3hCapsule(${'<Ashiva_Control_Menu [#]>'});
echo danis3hCapsule(${'<Ashiva_Control_Menu>'});
```

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
  LightModifiers: [],
  CapsuleManifest: {},
  PrimeCell: {}
};
```

#### Example 2:

    Capsule_Examples:::CX_My_Capsule::Season:Spring||time:evening
    
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
  },
  
  CapsuleManifest: {},
  PrimeCell: {}
};
```

#### Example 3:

    Ash:::Standard_UI:::TouchScreen:::Ash_Toggle_Input::Position:Off++class:test-class-1:test-class-2:test-class-3:test-class-4++test-attribute-1:test-value-1++test-attribute-2:test-value-2++test-attribute-3:test-attribute-3++test-attribute-4:++test-attribute-5++data-test-data-1:test-value-1++data-test-data-2:test-value-2++data-test-data-3:data-test-data-3++data-test-data-4:++data-test-data-5++data-test-data-6&&pagecontext:pagefix&&settingslisted:(StrongModifiers|classList|attributes|dataSet|Directives|LightModifiers)||theme:light

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
  },
  
  CapsuleManifest: {},
  PrimeCell: {}
};
```
