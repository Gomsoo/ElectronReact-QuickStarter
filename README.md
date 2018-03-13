# ElectronReact-QuickStarter

React project using `create-react-app` and `Electron`.<br>
This project was created based on [Building an Electron application with create-react-app](https://medium.freecodecamp.org/building-an-electron-application-with-create-react-app-97945861647c)

이 프로젝트는 `create-react-app`으로 만든 프로젝트에 `Electron`을 사용할 수 있도록 반영한 프로젝트 입니다.<br>
[Building an Electron application with create-react-app](https://medium.freecodecamp.org/building-an-electron-application-with-create-react-app-97945861647c)을 참고하여 만들어졌습니다.<br>
원문 글대로 `ejecting`을 하지 않았으므로, `webpack`을 비롯한 각종 프로젝트 세팅을 직접 커스터마이징 하고 싶지 않은 분들에게 적합합니다.


여기부터는 원문 글에서 현 프로젝트에 적용한 부분을 그대로 가져온 내용입니다.

#### Basic Recipe
> 1. run `create-react-app` to generate a basic React application
> 2. run `npm install --save-dev electron`
> 3. add `main.js` from `electron-quick-start` (we'll rename it to `electron-starter.js`, for clarity)
> 4. modify call to `mainWindow.loadURL` (in `electron-starter.js`) to use `localhost:3000` (webpack-dev-server)
> 5. add a main entry to `package.json` for `electron-starter.js`
> 6. add a run target to start Electron to `package.json`
> 7. `npm start` followed by `npm run electron`

This works okay for development, but has two shortcomings:
  * Production won't use `webpack-dev-server`. It needs to use the static file from building the React project
  * (small) nuisance to run both npm commands

#### Specifying the loadURL in Production and Dev
In development, an environment variable can specify the url for `mainWindow.loadURL` (in `electron-starter.js`). If the env var exists, we'll use it; else we'll use the production static HTML file.

We'll add a npm run target (to `package.json`) as follows:

```
"electron-dev": "ELECTRON_START_URL=http://localhost:3000 && electron ."
```

In `electron-starter.js`, we'll modify the `mainWindow.loadURL` call as follows:

```javaScript
const startUrl = process.env.ELECTRON_START_URL || url.format({
  pathname: path.join(__dirname, '/../build/index.html'),
  protocol: 'file:',
  slashes: true
});
mainWindow.loadURL(startUrl);
```

There is a problem with this: `create-react-app` (by default) builds an `index.html` that uses absolute paths. This will fail when loading it in Electron. Thankfully, there is a config option to change this: set a `homepage` property in `package.json`. (Facebook documentation on the property is here.)

So we can set this property to the current directory and `npm run build` will use it as a relative path.

```
"homepage": "./",
```
