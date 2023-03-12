# Frontend Danis³h Capsules
**Danis³h Capsules** can be deployed anywhere. Here's how they are deployed on the Front End.

Standard notation for a capsule might be:

    Capsule_Examples:::CX_My_Capsule::Season:Spring##time:evening

On the *Front End*, within an HTML Document, the corresponding **Danis³h Capsule Reference** looks like this:

```html
<!--<[ <CX_My_Capsule (Capsule_Examples) Season="Spring" time="evening"> ]>-->
```

and may be created like this:

```js
const ashivaMenuNavigation = document.createComment('<[ <CX_My_Capsule (Capsule_Examples) Season="Spring" time="evening"> ]>');
```
