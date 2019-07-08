1. 问题一： gyp ERR! stack Error: EACCES: permission denied, mkdir '/usr/local/node/lib/node_modules/sqlite3/.node-gyp'

背景：在centos7的docker容器中安装sqlite3时提示的错误。
```shell
$ npm install sqlite3

> sqlite3@4.0.9 install /usr/local/node/lib/node_modules/sqlite3
> node-pre-gyp install --fallback-to-build

node-pre-gyp WARN Using request for node-pre-gyp https download 
node-pre-gyp WARN Pre-built binaries not installable for sqlite3@4.0.9 and node@10.16.0 (node-v64 ABI, glibc) (falling back to source compile with node-gyp) 
node-pre-gyp WARN Hit error EACCES: permission denied, mkdir '/usr/local/node/lib/node_modules/sqlite3/lib/binding' 
gyp WARN EACCES user "undefined" does not have permission to access the dev dir "/root/.node-gyp/10.16.0"
gyp WARN EACCES attempting to reinstall using temporary dev dir "/usr/local/node/lib/node_modules/sqlite3/.node-gyp"
gyp WARN install got an error, rolling back install
gyp WARN install got an error, rolling back install
gyp ERR! configure error 
gyp ERR! stack Error: EACCES: permission denied, mkdir '/usr/local/node/lib/node_modules/sqlite3/.node-gyp'
gyp ERR! System Linux 4.9.125-linuxkit
gyp ERR! command "/usr/local/node/bin/node" "/usr/local/node/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "configure" "--fallback-to-build" "--module=/usr/local/node/lib/node_modules/sqlite3/lib/binding/node-v64-linux-x64/node_sqlite3.node" "--module_name=node_sqlite3" "--module_path=/usr/local/node/lib/node_modules/sqlite3/lib/binding/node-v64-linux-x64" "--napi_version=4" "--node_abi_napi=napi" "--napi_build_version=0" "--node_napi_label=node-v64"
gyp ERR! cwd /usr/local/node/lib/node_modules/sqlite3
gyp ERR! node -v v10.16.0
gyp ERR! node-gyp -v v3.8.0
gyp ERR! not ok 
node-pre-gyp ERR! build error 
node-pre-gyp ERR! stack Error: Failed to execute '/usr/local/node/bin/node /usr/local/node/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js configure --fallback-to-build --module=/usr/local/node/lib/node_modules/sqlite3/lib/binding/node-v64-linux-x64/node_sqlite3.node --module_name=node_sqlite3 --module_path=/usr/local/node/lib/node_modules/sqlite3/lib/binding/node-v64-linux-x64 --napi_version=4 --node_abi_napi=napi --napi_build_version=0 --node_napi_label=node-v64' (1)
```

解决方案：

```shell
$ npm install sqlite3 --unsafe-perm=true --allow-root
```

