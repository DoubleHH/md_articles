
# Coding中常见Crash

Coding中常见Crash大致分为两类：

1. 方法中明确规定不为nil;
2. 系统升级导致某个api在低版本系统中不能使用

## 方法中明确规定不为nil

有以下这些情况：

1. 字典`setObject:forKey:`中object不能为nil，包括用`@{ key: obj, }`形式声明的，否则崩；（可用`setValue:forKey:`或者类型 `obj?:@""` 的形式）
2. 数组`addObject:`不能为空，包括用`@{ obj1, obj2, }`形式声明的，否则崩；
3. `[NSString stringByAppendingString:aString]`，aString为nil会崩；



## 系统升级导致某个api在低版本系统中不能使用

目前外卖支持低版本到iOS7，所以仅iOS7以上支持的API都会导致崩溃，有以下这些API：

1. `[NSString containsString:]` iOS8.0及以上；




## 后记

本wiki会持续不断更新，大家平时注意检查自己的代码，要有崩溃意识~