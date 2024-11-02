Trying to use nx Module Federation between Angular host and React remote and getting

How to run

Will serve host
```
cd my-angular
npm i
npm run serve

Will serve remote
cd my-react
npm i
npm run serve
```


Go to http://localhost:5200/ - angular host and expct it load react remote but it shows the error

```
main.ts:1 TypeError: fn is not a function
while loading "./Module" from 4916
    at handleFunction (index.cjs.js:69:35)
    at onInitialized (index.cjs.js:91:24)
    at handleFunction (index.cjs.js:80:32)
    at onExternal (index.cjs.js:87:35)
    at handleFunction (index.cjs.js:80:32)
    at index.cjs.js:116:17
    at Array.forEach (<anonymous>)
    at Object.remotes (index.cjs.js:39:31)
    at __webpack_require__.f.remotes (remotes loading:24:1)
    at ensure chunk:6:1
```

1. First step
Created React and Angular workspaces and create host and remote in angular and react.
Everything works.


2. Second step to load remote react component in angular host

Now trying to load react remote in Angular host

a) Updated my-angular/apps/angularHost/module-federation.config.ts

to contains react remote as 
```
  remotes: [ ['rremote','http://localhost:4201/remoteEntry.js']],
```
b) Added my-angular/apps/angularHost/src/remotes.d.ts
```
declare module 'rremote/Module';
```
c) Added react div for mounting to my-angular/apps/angularHost/src/index.html
```
    <div id="root-react"></div>
```
d) Added angular component that load remote react cmp
my-angular/apps/angularHost/src/app/react-remote.component.ts

```
import {AfterContentInit, Component} from '@angular/core';
import {mount} from "rremote/Module"

@Component({
  standalone: true,
  selector: 'react-remote',
  template: 'Hi from remote',
})
export class ReactRemoteComponent implements AfterContentInit{
    ngAfterContentInit(): void {
        mount();
    }
}
```
e) in React remote my-react/apps/rremote/src/remote-entry.ts change to
remote-app to include app where we boostrap react app

```
export { default } from './app/remote-app';
```

f) where my-react/apps/rremote/src/app/remote-app.tsx

```

import * as ReactDOM from "react-dom/client";
import App from './app';

function RemoteApp() {
  const root = ReactDOM.createRoot(
    document.getElementById('root-react') as HTMLElement
  );
  root.render(
    <App />
  );
}


export default RemoteApp;

```