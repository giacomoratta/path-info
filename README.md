# PathInfo

NPM package for reading information about absolute paths, which can also be imported/exported via JSON. Both sync and async classes are provided.

#### Install
```
npm i path-info
```

## Create an object and read path info

The following examples:
1. create an empty object (because constructor cannot be async, we need an explicit method for 'set');
2. read and set the path information:
   1. `absolutePath` is a required parameter with the absolute path we need to read information for;
   2. `relRootPath` is an optional parameter which states what is the root path for this object; this data is quite important, for example, if we want to handle groups of paths which belong to different root directories;
3. if something goes wrong, a `PathInfoError` will be thrown.

#### Default class (async)
```
const { PathInfo } = require('path-info')
const myPath = new PathInfo()
await myPath.set({
    absolutePath: '/user/example012/documents/file1.txt',
    relRootPath: '/user/example012/documents/' /* optional */
})
```

#### Sync class
```
const { PathInfoSync } = require('path-info')
const myPath = new PathInfoSync()
myPath.set({
    absolutePath: '/user/example012/documents/file1.txt',
    relRootPath: '/user/example012/documents/' /* optional */
})
```

## Getters

After being instantiated a `PathInfo` object, we can finally get all the information.

```
// Let's take the following as example
await myPath.set({
    absolutePath: '/user/example012/documents/file1.txt',
    relRootPath: '/user/example012'
})
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


## Setters

| Name            | Description   |
| --------------- | ------------- |
| `relRoot(root)` | Set the relative root for the path. It does not change anything but the internal reference about the root which the path we want to belong to. In other words, this would be useful if we want to handle groups of paths which belong to different root directories. |
| `size`          | Set the size for the path, in bytes. |

## Other methods

| Name            | Description   |
| --------------- | ------------- |
| `isSet(): boolean` | Returns `true` if the object has been initialized and set without errors. |
| `clone(): PathInfo` | Return a new object with same data. |
| `fromJson(): undefined` | Set path info from a json object. |
| `toJson(): object` | Return a json object for any exporting operation. |
