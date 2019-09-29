## electron的打包

electron有两种打包方式：electron-packager及electron-builder。electron-builder更加高效方便。这里只介绍该打包方式。  

```
yarn add electron-builder --save-dev

"build": {
    "appId": "com.xxx.app",
    "mac": {
      "target": ["dmg","zip"]
    },
    "win": {
      "target": ["nsis","zip"]
    }
},
"scripts": {
    "dist": "electron-builder --win --x64"
},
```