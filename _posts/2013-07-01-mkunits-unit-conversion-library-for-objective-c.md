---
layout: post
title: "Implementing Unit conversion library for Objective-C"
date: 2013-07-01 18:19:29 +0100
keywords: "design patterns, ios, open-source"
---

Last week I decided to implement unit conversion library for Objective-C called [MKUnits][MKUnits]. I was fully aware that there were many open-sourced implementation already available, but unfortunately none of them fitted my needs.

[MKUnits]:https://github.com/michalkonturek/MKUnits

I required a library that:

* is easily extendable
* has built-in rounding functionality
* is precise up to at least 10 decimal places
* allows manipulation between units of the same group, i.e. kg and pounds, without the need of conversion


### Quantity Pattern

Before I started coding, I had a quick read through a chapter of [Analysis Patterns][analysis-patterns] by [Martin Fowler][martin-fowler], which describes the **Quantity Pattern**. This pattern is extremely useful when you want to represent dimensioned quantities.

[analysis-patterns]:http://www.amazon.co.uk/Analysis-Patterns-Reusable-Object-Models/dp/0201895420
[martin-fowler]:http://martinfowler.com/

The idea behind this pattern is straightforward; it is a class that represents both the `amount` and the `unit`, and allows arithmetic behaviour, e.g. addition and subtraction, and conversion to other quantities.

The initial interface for the `MKQuantity` was:

```objc
@class MKUnit;

@interface MKQuantity : NSObject

@property (nonatomic, copy) NSDecimalNumber *amount;
@property (nonatomic, strong) MKUnit *unit;

- (id)initWithAmount:(NSNumber *)amount withUnit:(MKUnit *)unit;

- (instancetype)add:(MKQuantity *)other;
- (instancetype)subtract:(MKQuantity *)other;

- (instancetype)convertTo:(MKUnit *)unit;

- (NSComparisonResult)compare:(MKQuantity *)other;

@end
```

Also, it's worth to mention that `MKQuantity` is a **value object**. 

> A **value object** is a simple object whose equality is not based on identity.

In other words, two value objects are equal if all their attributes are equal. In this case, it is `amount` and `unit`.

```objc
- (BOOL)isEqual:(id)object {
    if (![object isKindOfClass:[self class]]) return NO;
    if (![[object unit] isEqual:self.unit]) return NO;
    return [self.amount isEqual:[object amount]];
}

- (NSUInteger)hash {
    return [[NSString stringWithFormat:@"%@%@%@",
             [self class], self.unit.symbol, self.amount] hash];
}
```


`MKQuantity` is associated with `MKUnit`:

```objc
@interface MKUnit : NSObject<NSCopying>

@property (nonatomic, strong) NSString *name;
@property (nonatomic, strong) NSString *symbol;
@property (nonatomic, strong) NSDecimalNumber *ratio;

- (id)initWithName:(NSString *)name
        withSymbol:(NSString *)symbol withRatio:(NSDecimalNumber *)ratio;

@end
```

The `ratio` property is used to convert unit to base unit, eg. for Length a base unit is `meter`, therefore, the ratio for `kilometer` unit is 1000 as `amount_in_meters = 1000 * amount_in_kilometers`.


### Unit conversion

Since this is a unit conversion library, the first type of a behaviour to be added is conversion.


```objc
- (instancetype)convertTo:(MKUnit *)unit {
    [self _assert_that_is_convertible_with_unit:unit];

    id converted = [self.unit convertAmount:self.amount to:unit];
    return [[self class] createWithAmount:converted withUnit:unit];
}
```

The conversion process of unit A to unit B consists of: 

* asserting that unit A is convertible with unit B

```objc
- (void)_assert_that_is_convertible_with_unit:(MKUnit *)unit {
    NSAssert([self.unit isConvertibleWith:unit], UNITS_NOT_CONVERTIBLE);
}
```

The aim is to perform conversion and arithmetic operations not only on the same units, but also on the other units of the same quantity, eg. adding 500 pounds to 2.5 kilograms should be allowed, but adding 500 grams to 1 meter should not.

```objc
- (BOOL)isConvertibleWith:(MKUnit *)unit {
    return [unit isMemberOfClass:[self class]];
}
```

* converting `amount` of unit A to `amount` of unit B

Firstly the `amount` of unit A must be converted to `amount` of base unit, and then the result is converted to `amount` of unit B.

```objc
- (NSNumber *)convertAmount:(NSNumber *)amount to:(MKUnit *)unit {
    return [self convertAmount:amount from:self to:unit];
}

- (NSNumber *)convertAmount:(NSNumber *)amount from:(MKUnit *)from to:(MKUnit *)to {
    NSAssert([from isConvertibleWith:to], UNITS_NOT_CONVERTIBLE);
    
    id baseAmount = [from convertToBaseUnit:amount];
    id converted = [to convertFromBaseUnit:baseAmount];
    return converted;
}
```

* creating new `Quantity` object of unit B with converted `amount`

This is due general consensus that a `Value Object` should be entirely immutable. 


### Arithmetic

Addition and subtraction operations of two quantities are allowed as long as their units are convertible.

```objc
- (instancetype)add:(MKQuantity *)other {
    MKQuantity *converted = [other convertTo:self.unit];
    id amount = [self.amount decimalNumberByAdding:converted.amount];
    return [[self class] createWithAmount:amount withUnit:self.unit];
}

- (instancetype)subtract:(MKQuantity *)other {
    MKQuantity *converted = [other convertTo:self.unit];
    id amount = [self.amount decimalNumberBySubtracting:converted.amount];
    return [[self class] createWithAmount:amount withUnit:self.unit];
}
```

<!-- As this is a unit conversion library, we should only allow multiplication and division by scalar numbers.

```objc

``` -->

## 

If you would like to know more, please visit [MKUnits GitHub repository][MKUnits].

I am [@michalkonturek][twitter] on Twitter. 

Looking forward to your feedback.

[twitter]:http://twitter.com/michalkonturek

