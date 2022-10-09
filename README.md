# Luban

`Luban`（鲁班） —— `Android`图片压缩工具 升级

[原库地址](https://github.com/Curzibn/Luban/releases)


# 导入

```sh
implementation 'io.github.tianshaokai:luban:1.0.0'
```

# 使用

### 方法列表

方法 | 描述
---- | ----
load | 传入原图
filter | 设置开启压缩条件
ignoreBy | 不压缩的阈值，单位为K
setFocusAlpha | 设置是否保留透明通道 
setTargetDir | 缓存压缩图片路径
setCompressListener | 压缩回调接口
setRenameListener | 压缩前重命名接口

### 异步调用

`Luban`内部采用`IO`线程进行图片压缩，外部调用只需设置好结果监听即可：

```java
Luban.with(this)
        .load(photos)
        .ignoreBy(100)
        .setTargetDir(getPath())
        .filter(new CompressionPredicate() {
          @Override
          public boolean apply(String path) {
            return !(TextUtils.isEmpty(path) || path.toLowerCase().endsWith(".gif"));
          }
        })
        .setCompressListener(new OnCompressListener() {
          @Override
          public void onStart() {
            // TODO 压缩开始前调用，可以在方法内启动 loading UI
          }

          @Override
          public void onSuccess(File file) {
            // TODO 压缩成功后调用，返回压缩后的图片文件
          }

          @Override
          public void onError(Throwable e) {
            // TODO 当压缩过程出现问题时调用
          }
        }).launch();
```

### 同步调用

同步方法请尽量避免在主线程调用以免阻塞主线程，下面以rxJava调用为例

```java
Flowable.just(photos)
    .observeOn(Schedulers.io())
    .map(new Function<List<String>, List<File>>() {
      @Override public List<File> apply(@NonNull List<String> list) throws Exception {
        // 同步方法直接返回压缩后的文件
        return Luban.with(MainActivity.this).load(list).get();
      }
    })
    .observeOn(AndroidSchedulers.mainThread())
    .subscribe();
```

# License

    Copyright 2022 tianshaokai
    
    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at
    
        http://www.apache.org/licenses/LICENSE-2.0
    
    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
