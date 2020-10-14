# PathInfo

NPM package for reading information about absolute paths, which can also be imported/exported via JSON. Both sync and async classes are provided.

[![Version npm](https://img.shields.io/npm/v/path-info-stats.svg?style=flat-square)](https://www.npmjs.com/package/path-info-stats) [![js-standard-style](https://img.shields.io/badge/code%20style-standard-brightgreen.svg?style=flat)](http://standardjs.com/)

#### Install
```shell script
npm i path-info-stats
```

## Create an object and read path info

#### The method `set({ absolutePath, relRootPath })`

Arguments:
1. `absolutePath` is a required parameter with the absolute path we need to read information for (and it must exist);
1. `relRootPath` is an optional parameter which states what is the root path for this object; this data is quite important, for example, if we want to handle groups of paths which belong to different root directories;
1. both arguments **must exist** otherwise a `PathInfoError` will be thrown.

This is the main difference between `PathInfo` and `PathInfoSync` because it is the only `sync/async` method in the library.

The `set` method it is not just a _setter_, but it makes some strict checks in order to have reliable path information. So, after running this method (successfully), `PathInfo` will have:
- valid and normalized `absolutePath`
- valid and normalized `relRootPath` (if present)
- verified *existent* `absolutePath`
- verified *existent* `relRootPath` (if present)

> **Is `fromJson()` an alternative to `set()`?** Methods `fromJson` and `toJson` have been created for importing/exporting purposes, and for that reason they should be fast. Therefore, `fromJson` will not run the same checks of `set()`. So, no, it is not an alternative!

The following examples:
1. create an empty object;
2. read and set the path information:
3. if something goes wrong, a `PathInfoError` will be thrown.

#### Default class (async)
```javascript
const { PathInfo } = require('path-info-stats')
const myPath = new PathInfo()
await myPath.set({
    absolutePath: '/user/example012/documents/file1.txt',
    relRootPath: '/user/example012/documents/' /* optional */
})
```

#### Sync class
```javascript
const { PathInfoSync } = require('path-info-stats')
const myPath = new PathInfoSync()
myPath.set({
    absolutePath: '/user/example012/documents/file1.txt',
    relRootPath: '/user/example012/documents/' /* optional */
})
```
N.B. documentation below will refer to `PathInfo` only. However, getter, setters, and other methods are not async and are the same for both `PathInfo` and `PathInfoSync`.


## _Getters_

After being instantiated a `PathInfo` object, we can finally get all the information.

```javascript
// Let's take the following as example object
const myPath = new PathInfo()
await myPath.set({
    absolutePath: '/user/example012/documents/file1.txt',
    relRootPath: '/user/example012'
})

// getter usage examples
console.log('File name =', myPath.name) // output: 'File name = file1'
console.log('File size =', myPath.sizeString) // output: 'File size = 43 KB'
```

| Name          | Description   | Output        |
| ------------- | ------------- | ------------- |
| `base`        | File name with extension (or directory name) | `"file1.txt"` |
| `createdAt`   | Timestamp for creation date | `1602568566563` |
| `dir`         | Absolute path of parent directory | `"/user/example012/documents/"` |
| `ext`         | File extension | `"txt"` |
| `isDirectory` | True if it is a directory | `false` |
| `isFile`      | True if it is a file | `true` |
| `level`       | Level of nesting (0 for the root) | `3` |
| `modifiedAt`  | Timestamp for modification date | `1602568577594` |
| `name`        | File or directory name | `"file1"` |
| `path`        | Full path (basically the same of `absolutePath` argument) | `"/user/example012/documents/file1.txt"` |
| `relPath`     | Relative path (in according to `relRoot`) | `"documents/file1.txt"` |
| `relRoot`     | Relative root path (see `relRootPath` argument) | `"/user/example012"` |
| `root`        | System root for the path | `"/"` |
| `size`        | File size in bytes | `43008` |
| `sizeString`  | File size in human-readable string | `"43 KB"` |


## _Setters_

```javascript
// new PathInfo object without relative root
const myPath = new PathInfo()
await myPath.set({
    absolutePath: '/user/example012/documents/file1.txt'
})

// ...after some operations, we set the relative root path
myPath.relRoot('/user/example012')
myPath.size(8612548) // bytes
```

| Name            | Description   |
| --------------- | ------------- |
| `relRoot(root)` | Set the relative root for the path. It does not change anything but the internal reference about the root which the path we want to belong to. In other words, this would be useful if we want to handle groups of paths which belong to different root directories. |
| `size(value)`   | Set the size for the path, in bytes. |

## Other methods

#### `isEqualTo(PathInfo obj): boolean`
Returns `true` if the objects have the same paths and roots.
```javascript
const myPath1 = new PathInfo()
const myPath2 = new PathInfo()
await myPath1.set({
    absolutePath: '/user/example012/documents/file1.txt',
    relRootPath: '/user/example012'
})
await myPath2.set({
    absolutePath: '/user/example012/documents/file1.txt',
    relRootPath: '/user/example012'
})

console.log('path1 == path2:', myPath1.isEqualTo(myPath2)) // output: 'path1 == path2: true'

myPath2.relRoot('/user/example012/documents/')
console.log('path1 == path2:', myPath1.isEqualTo(myPath2)) // output: 'path1 == path2: false'
```

#### `isSet(): boolean`
Returns `true` if the object has been initialized and set without errors.
```javascript
const myPath = new PathInfo()
try {
  await myPath.set({
    absolutePath: '/wrong-path'
  })
  console.log('Is myPath set?', myPath.isSet()) // output: 'Is myPath set? true'
} catch(e) {
  console.log('Is myPath set?', myPath.isSet()) // output: 'Is myPath set? false'
}
```

#### `clone(): PathInfo`
Return a new object with same data.

```javascript
const myPath1 = new PathInfo()
let myPath2
await myPath1.set({
    absolutePath: '/user/example012/documents/file1.txt',
    relRootPath: '/user/example012'
})
myPath2 = myPath1.clone()
console.log('path1 == path2:', myPath1.isEqualTo(myPath2)) // output: 'path1 == path2: true'
```


#### `toJson(): object`
Return a json object for any exporting operation.

```javascript
const myPath1 = new PathInfo()
await myPath1.set({
    absolutePath: '/user/example012/documents/directory1/directory2',
    relRootPath: '/user/example012/documents'
})
console.log(myPath1.toJson())
/* 
  OUTPUT:
  {
     root: '/',
     dir: '/user/example012/documents/directory1',
     base: 'directory2',
     ext: '',
     name: 'directory2',
     path: '/user/example012/documents/directory1/directory2',
     level: 3,
     size: 4096,
     createdAt: 1602568566563,
     modifiedAt: 1602568566563,
     isFile: false,
     isDirectory: true,
     relPath: 'directory1/directory2',
     relRoot: '/user/example012/documents'
  }
*/
```

#### `fromJson(object): undefined`
Set path info from a json object.

> **Is `fromJson()` an alternative to `set()`?** Methods `fromJson` and `toJson` have been created for importing/exporting purposes, and for that reason they should be fast. Therefore, `fromJson` will not run the same checks of `set()`. So, no, it is not an alternative!

```javascript
const myPath1 = new PathInfo()
console.log('Is myPath1 set?', myPath1.isSet()) // output: 'Is myPath1 set? false'
myPath1.fromJson({
   root: '/',
   dir: '/user/example012/documents/directory1',
   base: 'directory2',
   ext: '',
   name: 'directory2',
   path: '/user/example012/documents/directory1/directory2',
   level: 3,
   size: 4096,
   createdAt: 1602568566563,
   modifiedAt: 1602568566563,
   isFile: false,
   isDirectory: true,
   relPath: 'directory1/directory2',
   relRoot: '/user/example012/documents'
})
console.log('Is myPath1 set?', myPath1.isSet()) // output: 'Is myPath1 set? true'
```