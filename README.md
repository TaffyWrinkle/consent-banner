# Consent Banner

## Overview

Consent banner is the library which will generate a banner at the specified position for asking the cookie preferences. 

It contains two buttons, `accept all` and `more info`. If the user clicks `more info` button, A dialog will pop up so that the user can set the cookie categories that he/she wants to share with us.

## Building and running on localhost

First install dependencies:

```sh
npm i
```

To create a production build:

```sh
npm run build-prod
```

To create a development build:

```sh
npm run build
```

## Running

```sh
npm run start
```

## Testing

```sh
npm run test
```

## Example use

This is part of your `html` page.

```HTML
<body>
    ...
    <div id="app"></div>
    ...
</body>
```

If you want to insert a banner into the `<div id="app"></div>`, you can use the following example.

```TypeScript

let cookieCategories: ICookieCategory[] = 
[
    {
        id: "c0",
        name: "1. Essential cookies",
        descHtml: "We use this cookie, read more <a href='link'>here</a>.",
        isUnswitchable: true
    },
    {
        id: "c1",
        name: "2. Performance & analytics",
        descHtml: "We use this cookie, read more <a href='link'>here</a>."
    },
    {
        id: "c2",
        name: "3. Advertising/Marketing",
        descHtml: "Blah"
    },
    {
        id: "c3",
        name: "4. Targeting/personalization",
        descHtml: "Blah"
    }
];

let textResources: ITextResources = {
    bannerMessageHtml = "We use optional cookies to provide... read <a href='link'>here</a>.",
    acceptAllLabel = "Accept all",
    moreInfoLabel = "More info",
    preferencesDialogCloseLabel = "Close",
    preferencesDialogTitle = "Manage cookie preferences",
    preferencesDialogDescHtml = "Most Microsoft sites...",
    acceptLabel = "Accept",
    rejectLabel = "Reject",
    saveLabel = "Save changes",
    resetLabel = "Reset all"
};

let callBack = function(obj: any) { console.log(obj); };

let cc = new ConsentControl("app", "en", callBack, cookieCategories, textResources);
let cookieCategoriePreferences: ICookieCategoriesPreferences = { "c1": undefined, "c2": false, "c3": true };

cc.showBanner(cookieCategoriePreferences);
```

## Developer Guide

`ConsentControl` consists of 2 main elements: **Banner** and **Preferences Dialog**. Use `containerElementOrId`, `culture`, `onPreferencesChanged`, `cookieCategories`, `textResources` to create an instance. `culture` will be used to determine the direction of the banner and preferences dialog.

```JavaScript
var cc = new ConsentControl(
    containerElementOrId: string | HTMLElement,   // here the banner will be inserted, can be HTMLElement or element id
    culture: string,                // culture can be just language "en" or language-country "en-us". Based on language RTL should be applied (https://www.w3.org/International/questions/qa-scripts.en)
    onPreferencesChanged: (cookieCategoriesPreferences: ICookieCategoriesPreferences) => void,   // callback function, called on preferences changes (via "Accept All" or "Save changes"), must pass cookieCategoriePreferences, see ICookieCategoriesPreferences in Data types
    cookieCategories?: ICookieCategory[],       // optional, see ICookieCategory in Data types
    textResources?: ITextResources          // optional, see ITextResources in Data types
);
```

1. `setTextResources(textResources: ITextResources)` can be used to set the texts.

2. `setContainerElement(containerElementOrId: string | HTMLElement)` can be used to set the container element for the banner and preferences dialog. 

    If you pass `HTMLElement`, it will be used as the container. If you pass `string`, the method will use it as the `id` to find the container element.

    ```JavaScript
    cc.setContainerElement("app");
    ```

    or

    ```JavaScript
    let container = document.getElementById('app');
    cc.setContainerElement(container);
    ```
    
    If the container can not be found, it will throw an error. `new Error("Container not found error")`
    
3. `getContainerElement()` will return the current container element.

4. You can set the direction manually by using `setDirection(dir?: string)`. `dir` can be `"ltr"` or `"rtl"`. If `dir` is not passed, it will determine the direction by `dir` attribute in `html`, `body` or `culture`.
    
    ```JavaScript
    // There are 3 cases. dir="rtl", dir="ltr", empty

    // Set direction to rtl (right-to-left)
    cc.setDirection("rtl");

    // Set direction to ltr (left-to-right)
    cc.setDirection("ltr");

    // It will use "dir" attribute in "html" or "body"
    // If the "dir" attribute is not specified, it will apply the direction based on "culture"
    cc.setDirection();
    ```
    
5. `getDirection()` will return the current direction.

### Data types

There are three data types: `ICookieCategory`, `ITextResources` and `ICookieCategoriesPreferences`.

+ `ICookieCategory` is used to create cookie categories that will be showed in the preferences dialog.
+ `ITextResources` is the texts that will be used in the banner and preferences dialog.
+ `ICookieCategoriesPreferences` is used to store the preferences in each cookie categories.

```TypeScript
// All categories should be replaced with the passed ones in the control
interface ICookieCategory {
    id: string;
    name: string;
    descHtml: string;
    isUnswitchable?: boolean;       // optional, prevents toggling the category. True only for categories like Essential cookies.
}

// only the passed text resources should be replaced in the control. If any string is not passed the control should keep the default value
interface ITextResources {
    bannerMessageHtml?: string;
    acceptAllLabel?: string;
    moreInfoLabel?: string;
    preferencesDialogCloseLabel?: string;
    preferencesDialogTitle?: string;
    preferencesDialogDescHtml?: string;
    acceptLabel?: string;
    rejectLabel?: string;
    saveLabel?: string;
    resetLabel?: string;
}

interface ICookieCategoriesPreferences {
    [key: string]: boolean | undefined;
}
```

### Provided methods

+ Methods related to **banner**: `showBanner(cookieCategoriesPreferences: ICookieCategoriesPreferences)` and `hideBanner()`
+ Methods related to **Preferences Dialog**: `showPreferences(cookieCategoriesPreferences: ICookieCategoriesPreferences)` and `hidePreferences()`

```JavaScript
// Insert all necessary HTML code and shows the banner. Until this method is called there should be no HTML elements of the Consent Control anywhere in the DOM. Only one banner will be created. If call it many times, it will only display the last one and remove all previous banners.
cc.showBanner(
    cookieCategoriesPreferences: ICookieCategoriesPreferences      // see ICookieCategoriesPreferences in Data types
);

// Hides the banner and the Preferences Dialog. Removes all HTML elements of the Consent Control from the DOM
cc.hideBanner();

// Shows Preferences Dialog. Leaves banner state unchanged. If there is a preferences dialog, it will show the dialog instead of creating a new one.
cc.showPreferences(
    cookieCategoriesPreferences: ICookieCategoriesPreferences      // see ICookieCategoriesPreferences in Data types
);

// Hides Preferences Dialog. Removes all HTML elements of the Preferences Dialog from the DOM. Leaves banner state unchanged
cc.hidePreferences();
```

### Custom settings

1. Change the width of buttons in banner: Change `$bannerBtnWidth` in `styles.scss`

2. `webpack.config.js` file is for development purpose, and `webpack.production.config.js` is for production. The only difference between `webpack.config.js` and `webpack.production.config.js` is `localIdentName` in `use/options/modules` under `module/rules`. 

## Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
