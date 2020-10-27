# SSKeychain

SSKeychain is a simple wrapper for accessing accounts, getting passwords, setting passwords, and deleting passwords using the system Keychain on Mac OS X and iOS.

## Adding to Your Project

Simply add the following to your Podfile if you're using CocoaPods:

``` ruby
pod 'SSKeychain'
```

or Cartfile if you're using Carthage:

```
github "soffes/SSKeychain"
```

To manually add to your project:

1. Add `Security.framework` to your target
2. Add `SSKeychain.h`, `SSKeychain.m`, `SSKeychainQuery.h`, and `SSKeychainQuery.m` to your project.

SSKeychain requires ARC.

Note: Currently SSKeychain does not support Mac OS 10.6.

## Working with the Keychain

SSKeychain has the following class methods for working with the system keychain:

```objective-c
+ (NSArray *)allAccounts;
+ (NSArray *)accountsForService:(NSString *)serviceName;
+ (NSString *)passwordForService:(NSString *)serviceName account:(NSString *)account;
+ (BOOL)deletePasswordForService:(NSString *)serviceName account:(NSString *)account;
+ (BOOL)setPassword:(NSString *)password forService:(NSString *)serviceName account:(NSString *)account;
```

Easy as that. (See [SSKeychain.h](https://github.com/soffes/sskeychain/blob/master/SSKeychain/SSKeychain.h) and [SSKeychainQuery.h](https://github.com/soffes/sskeychain/blob/master/SSKeychain/SSKeychainQuery.h) for all of the methods.)

## Documentation

### Use prepared documentation

Read the [online documentation](http://cocoadocs.org/docsets/SSKeychain).

## Debugging

If your saving to the keychain fails, use the NSError object to handle it. You can invoke `[error code]` to get the numeric error code. A few values are defined in SSKeychain.h, and the rest in SecBase.h.

```objective-c

#import "SSKeychain.h"

#define keychain_service @"xxxKeyChain_Service"
#define keychain_account @"xxxKeyChain_Account"

// - MARK:获取唯一标示符uuid
+ (NSString *)uuid{
    NSString *udid = [SSKeychain passwordForService:keychain_service account:keychain_account];
    NSError *error=nil;
    
    if (udid == nil || [udid isEqualToString:@""] || udid.length == 0)
    {
        udid = [[[UIDevice currentDevice] identifierForVendor] UUIDString];
        BOOL succcess= [SSKeychain setPassword:udid
                                    forService:keychain_service
                                       account:keychain_account
                                         error:&error];
        if (succcess)
        {
            NSLog(@"获取的UUID is %@", udid);
        }
        
        return udid;
    }
    else {
        return udid;
    }
}
```

Obviously, you should do something more sophisticated. You can just call `[error localizedDescription]` if all you need is the error message.

## Disclaimer

Working with the keychain is pretty sucky. You should really check for errors and failures. This library doesn't make it any more stable, it just wraps up all of the annoying C APIs.


## Thanks

This was originally inspired by EMKeychain and SDKeychain (both of which are now gone). Thanks to the authors. SSKeychain has since switched to a simpler implementation that was abstracted from [SSToolkit](http://sstoolk.it).

A huge thanks to [Caleb Davenport](https://github.com/calebd) for leading the way on version 1.0 of SSKeychain.
