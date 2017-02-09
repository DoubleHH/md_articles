# JSPatch注意事项

本篇文章记录JSPatch热修复时遇到的问题，不定时更新~

## 1. block传递及执行

JSPatch的脚本中Block结构的定义如下:

~~~javascript
block('ParameterAType1, ParameterBType1...', function(ParameterA, ParameterB...) {
	// 函数体
})
~~~

`block(parameterString, functionImpl)`本身就是JavaScript函数。

<mark>Block变量在Objective-C代码经过编译后是一个函数C语言函数的结构体指针。从JSPatch表达上也看出Block最后执行的还是函数。

JSPatch把函数参数中的Block直接转成了函数。

举个改动过的JavaScript Convert原生栗子

~~~objective-c
@implementation SampleClass
- (void)requestUrl:(NSString *)url param:(NSDictionary *)dict callback:(void (^)(int code, NSString *msg))callback {
    [super requestUrl:url param:dict callback:callback];
    JPRequest *obj = [[JPRequest alloc] initWithUrl:url param:dict];
    obj.successBlock = ^(id data, NSError *err) {
        NSString *content = [JPParser parseData:data];
        if (callback) callback(@{
            @"content": content
        }, err);
        [self.dataSource refresh];
        self.callback = callback;
        self.handleRequestSuccess(data);
    };
}
@end
~~~

~~~javascript
require('JPRequest,JPParser');
defineClass('SampleClass', {
    requestUrl_param_callback: function(url, dict, callback) {
        self.super().requestUrl_param_callback(url, dict, callback);
        var obj = JPRequest.alloc().initWithUrl_param(url, dict);
        obj.setSuccessBlock(block('id,NSError*', function(data, err) {
            var content = JPParser.parseData(data);
            if (callback) callback({"content": content}, err);
            self.dataSource().refresh();
            self.setCallback(block('int, NSString *', callback));
            self.handleRequestSuccess()(data);
        }));
    },
});
~~~

这里我们主要关注Block的处理。

### callback block变量
作为参数，因为JS是弱类型，直接用名字放在函数参数变量中，代表JS函数。`function(url, dict, callback)`  
作为执行代码，JS代码中直接作为函数体执行`callback({"content": content}, err)`。  
作为Block传递，`self.setCallback(block('int, NSString *', callback));`，该句JS代码分为两部分，前半部分是setter方法self.setCallback(x)，x部分是将JS函数转化为JS中的block样式。

### handleRequestSuccess block变量
再看`self.handleRequestSuccess`block，JS代码`self.handleRequestSuccess()(data);`，咱们可以拆解成两部分，前半部分`self.handleRequestSuccess()`是一个属性获取方法，JS中获取后直接是函数，所以后面紧跟着就是函数的参数列表（如对JS函数不熟悉，可以看看[JavaScript 函数定义方式](http://www.ibm.com/developerworks/cn/web/1406_dengxb_jsfunction/)）。

## 2. setter方法转义
因为JS中不能直接获取只能通过方法获取变量，所以在hook setter方法时需要通过Key-Value Coding的方式。

举个栗子：

~~~objective-c
@implementation WMPatchTest

- (void)setPayCompletion:(void (^)(BOOL, NSInteger, NSString *))payCompletion {
    _payCompletion = [payCompletion copy];
}

@end
~~~

~~~javascript
defineClass('WMPatchTest', {
    setPayCompletion: function(payCompletion) {
        self.setValue_forKey(block('BOOL, int, NSString *', payCompletion), "_payCompletion");
    },
});
~~~

## 3. 实例函数与类函数的写法
实例函数在第一个大括号里写函数体，类函数第一个是空，第二个是函数体

~~~javascript
defineClass(‘Class', {
     xxx
})
defineClass(‘Class', {}, {
     xxx
})
~~~

## 4. block中使用到的self不能直接使用
~~~javascript
var weakSelf = __weak(self)
block('BOOL', function(successed) {
	weakSelf.reloadDataWithRelocation(YES)
})
~~~

## 5. static变量不能用

### 若是局部static变量；
两种解决办法：  

1. NSUserDefaults 保存static变量
2. 为某个单例类添加属性`self.setProp_forKey("value", "propertyName")， self.getProp("propertyName")`

### 若是全局的，则需要在OC代码有方法可以返回该变量，否则没法用；