# Creating a Hello World Application

---

## Example Structure of an Angular 2 App

![Angular 2 App Structure](content/images/angular2-generic-application-structure.png)

Notes:

- Functionality is grouped using `NgModule`s (or "modules")
- An application may contain many modules
- An application may define only one _bootstrap_ (entry point) module
- Angular itself defines numerous modules that you can import to gain access to core functionality
- A module defines a collection of components, directives, pipes and services
- A module can import other modules to gain access to the components, directives and services within it

---

## Decorators / imperative vs declarative

- The majority of functionality inside an Angular application is defined _declaratively_ in metadata
- Metadata is attached to `class` definitions using _decorators_
- Examples of Angular decorators are `@NgModule()`, `@Component()`, `@Directive()`, `@Pipe()`, etc.
- Angular retrieves and parses this metadata and uses it to create an executable application
- This contrasts with the old way of building applications using imperative statements (eg. jQuery)

---

## Application building blocks

The core building blocks of Angular applications are:
- `NgModule` 
  - Group together related functionality into a module that can be imported by other modules
- `Component`
  - A combination of a _template_ that _somewhat_ resembles HTML and a `class` definition
  - A template can refer to methods, properties and event handlers inside the `class`
  - The template and class combine to produce an HTML document that the browser can render
- Providers
  - A `class` or `function` that provides some functionality or logic, and can be injected into components and directives

---

## Structure of a module

```ts
import { NgModule } from '@angular/core';

@NgModule({
  imports:      [ ... ],
  declarations: [ ... ],
  providers:    [ ... ],
  exports:      [ ... ],
  bootstrap:    [ ... ]
})
export class MyModule {}
```

- `imports`: Other modules required by our module (built-in, feature or third party)
- `declarations`: Components, directives and pipes defined in our module
- `providers`: Services defined in our module
- `exports`: Components, directives or pipes available for other modules or unit tests
- `bootstrap`: The root component of the application

---

## Typical root module

```ts
@NgModule({
  imports:      [ BrowserModule ],
  declarations: [ RootComponent, OtherComponent, MyPipe, MyDirective ],
  providers:    [ MyService ],
  bootstrap:    [ RootComponent ]
})
export class AppModule {}
```

- There's only one root module in an application, which is passed to `bootstrapModule()`
- Only the root module defines a `bootstrap` component which is the first thing rendered on the screen
- Applications running inside a browser must import `BrowserModule`
- `BrowserModule` automatically imports `CommonModule` which contains critical directives (`NgIf`, `NgFor`, `ngSwitch`, etc.)

---

## Example feature module

```ts
@NgModule({
  imports:      [ CommonModule, OtherFeatureModule ],
  declarations: [ MyComponent, MyPipe, MyDirective ],
  providers:    [ MyService ],
  exports:      [ MyComponent, MyDirective ]
})
export class MyFeatureModule {}
```

- All components that you wish to use in your templates must be added to `declarations`
- A component that appears in `declarations` but not `exports` is considered _private_, meaning modules that import this `NgModule` will not have access to that component
- A component that appears in `declarations` *and* `exports` is considered _public_, and can be freely used by modules that import this `NgModule`
- There's no root component in a feature module because application bootstrapping was already handled in the _root_ `NgModule`
- Anything appearing in the `providers` array (typically called _services_) is *public* and can be used by anything that imports this `NgModule`

---

## Root Module Example

_app.module.ts_

```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppComponent } from './app.component';

@NgModule({
  imports: [BrowserModule],
  declarations: [AppComponent],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

- Always follow the filename convention:
  - `AppModule` => _app.module.ts_
  - `SharedModule` => _shared.module.ts_

---

## Basic Structure of a Component

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'rio-widget',
  styles: ['p { color: red }'],
  template: '<p>I\'m a widget!</p>'
})
export class WidgetComponent { ... }
```

- `selector`: The HTML tag associated with the component

```html
<rio-widget></rio-widget>
```

- `template`: The user facing side of the component
  - Usually HTML but it can be other language depending on the platform
- `styles`: An array of styles to apply to the template
  - By default styles only apply to the component (~shadow root)

[View Example](https://plnkr.co/edit/oQt4n7r6droc2aczAFbO?p=preview)

---

## Comments on Components

- Always define a prefix for every component of your application
  - Avoid colisions with third party components and future native HTML elements
- The prefix has usually 3 letters:

```ts
selector: 'rio-widget' // rio = Rangle.io
```

- We can use backticks to define multiline templates and styles

```ts
template: `
  <h1>My Title</h1>
  <p>My Paragraph</p>
`
```

```ts
styles: [`
  p {
    color: red;
  }
`]
```

---

## A Simple Angular 2 Folder Structure

```sh
.
├── dist
│   └── ...
├── src
│   ├── app
│   │   ├── app.module.ts
│   │   ├── index.ts
│   │   └── root.component.ts
│   ├── index.html
│   └── main.ts
└── ... 
```

- Leave the root folder for configuration files
- Put all our code in the _src_ folder
- After a build, the bundle files will be created in the _dist_ folder
- Always create one folder per module
- Create import barrels with files called _index.ts_

---

## Folder Structure (With Config Files)

```sh
.
├── src
│   ├── app
│   │   └── ...
│   └── ...
├── package.json
├── tsconfig.json
└── webpack.config.js
```

- Config files needed for:
  - NPM => _package.json_
  - Webpack => _webpack.config.json_ 
  - Typescript => _tsconfig.json_

---

## Bootstrapping an Application

```ts
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import { enableProdMode } from '@angular/core';

import { AppModule } from './app/app.module';

if (process.env.NODE_ENV === 'production') {
  enableProdMode();
}

platformBrowserDynamic().bootstrapModule(AppModule);
```

- The function used to bootstrap an application depends on the target platform
- Other platforms can be the server (Angular Universal) or a smartphone (Nativescript)
- There are two types of bootstrapping:
  - Just in time compilation (JIT)
  - Ahead of time compilation (AOT)
- Calling `enableProdMode` modifies how the change detection works

---

## Explanation of JIT, AOT and SSR

Different builds to suit different needs (production vs development and such)

---

## JIT vs AOT

| Characteristic        | JIT          | AOT               |
| --------------------- | ------------ | ----------------- |
| Compilation target    | Browser      | Server            |
| Compilation context   | Runtime      | Build             |
| Bundle size           | Huge (~3 MB) | Smaller (~400 KB) |
| Execution Performance | -            | Better            |
| Startup time          | -            | Shorter           |

- AoT compiles component templates in the server
- With AoT the compiler is not shipped to the browser

---

## Loading an App Using Webpack

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Angular 2 App</title>
</head>
<body>
  <rio-app>Loading...</rio-app>
</body>
</html>
```

---

## Loading an App Using SystemJS

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Angular 2 App</title>
  <script src="https://unpkg.com/systemjs@0.19.38/dist/system.src.js"></script>
  <script src="https://code.angularjs.org/tools/typescript.js"></script>
  <script src="system.config.js"></script>
</head>
<body>
  <rio-app>Loading...</rio-app>
  <script>
    System.import('main.ts');
  </script>
</body>
</html>
```

---

## How to Define a Component

```ts
@Component({ // SystemJS & Angular CLI & AoT style
  selector: 'rio-app',
  styleUrls: ['app.component.css'],
  templateUrl: 'app.component.html'
})
```

[View Example](https://plnkr.co/edit/s1jcJH9YuODcMEDWPLYU?p=preview)

Notes:

- SystemJS style loads assets at runtime
- Webpack style bundle assets together
- Webpack style is similar to inline style

---

## Directory structure

Create folders to create conceptual functionality groups

```sh
src
├── app
│   ├── app.module.ts
│   ├── index.ts
│   └── login
│       ├── login.component.css
│       ├── login.component.html
│       └── login.component.ts
├── index.html
└── main.ts
```

---

## Registering Components in the Module

```ts
import {
  RootComponent,
  HeaderComponent,
  BodyComponent,
  MessageComponent,
} from './components';

@NgModule({
  declarations: [
    RootComponent,
    HeaderComponent,
    BodyComponent,
    MessageComponent
  ],
  bootstrap: [
    RootComponent
  ]
})
export class AppModule {}
```

- All the components used have to be defined in the module
