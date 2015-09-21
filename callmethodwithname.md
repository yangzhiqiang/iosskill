### 通过方法名称调用方法

```
            SEL sel = NSSelectorFromString(@"checkMethod:");
            NSMethodSignature * signiture = [self methodSignatureForSelector:sel];
            
            const char * sig = [signiture getArgumentTypeAtIndex:0];
            sig = [signiture getArgumentTypeAtIndex:1];
            sig = [signiture getArgumentTypeAtIndex:2];
            
            NSInvocation * invocation = [NSInvocation invocationWithMethodSignature:signiture];
            
            [ invocation setSelector: sel];
            
            // Argument 1 is at index 2, as there is self and _cmd before
            [ invocation setArgument: &obj atIndex: 2 ];
            [ invocation invokeWithTarget: self ];

            BOOL ret = NO;
            // If you need to get the return value
            [ invocation getReturnValue: &ret ];
```
```
- (BOOL) checkUUID: (id) UUID {
    
    return YES;
}
```
